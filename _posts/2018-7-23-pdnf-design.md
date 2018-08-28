---

layout:     post
title:      "搭建DNS服务(七)"
subtitle:   "powerdns内部机制分析"
date:       2018-7-23 09:00:00
author:     "Jht"
header-img: "img/powerdns-bg.jpg"
catalog: true
tags:
    - dns
    - powerdns
---

# PowerDNS内部机制分析

## 为什么研究？

根据他的主从策略。他说`Native replication`是默认就支持的，不需要额外的配置。

参考资料：[DNS Modes of Operation](https://doc.powerdns.com/authoritative/modes-of-operation.html#dns-modes-of-operation)

## 那么他是咋支持的？

通过看源码，看到了内部架构。

### 架构

![avatar](/img/in-post/pdns/pdns.jpg)

### 分析

#### 启动流程

##### guardian模式

如果启动参数中“–guardian=yes”，PowerDNS将启动guardian模式，即fork出一个子进程执行正常的启动流程，而父进程则一秒钟探测一次子进程是否活着，如果子进程非正常退出则重启动子进程。

代码片段如下：

```c
**`// receiver.cc`**

void daemonize(void)
{
    ......
    if(::arg().mustDo("guardian") && !isGuarded(argv)) {
      if(::arg().mustDo("daemon")) {
        g_log.toConsole(Logger::Critical);
        daemonize();
      }
      guardian(argc, argv);  
      # never get here, guardian will reinvoke process

      cerr<<"Um, we did get here!"<<endl;
    } 
    .......
}

static int guardian(int argc, char **argv)
{
    ........
    for(;;) {
        ........
        for(;;) {
            int ret=waitpid(pid,&status,WNOHANG);

            if(ret<0) {
            g_log<<Logger::Error<<"In guardian loop, waitpid returned error: "<<strerror(errno)<<endl;
            g_log<<Logger::Error<<"Dying"<<endl;
            exit(1);
            }
            else if(ret) // something exited

            break;
            else { // child is alive
           
            // execute some kind of ping here 

            if(DLQuitPlease())
                takedown(1); // needs a parameter
    
            setStatus("Child running on pid "+itoa(pid));
            sleep(1);
            }
        }

        pthread_mutex_lock(&g_guardian_lock);
        close(g_fd1[1]);
        fclose(g_fp);
        g_fp=0;

        if(WIFEXITED(status)) {
            int ret=WEXITSTATUS(status);

            if(ret==99) {
            g_log<<Logger::Error<<"Child requested a stop, exiting"<<endl;
            exit(1);
            }
            setStatus("Child died with code "+itoa(ret));
            g_log<<Logger::Error<<"Our pdns instance exited with code "<<ret<<", respawning"<<endl;

            sleep(1);
            continue;
        }
        if(WIFSIGNALED(status)) {
            int sig=WTERMSIG(status);
            setStatus("Child died because of signal "+itoa(sig));
            g_log<<Logger::Error<<"Our pdns instance ("<<pid<<") exited after signal "<<sig<<endl;
        #ifdef WCOREDUMP
            if(WCOREDUMP(status)) 
            g_log<<Logger::Error<<"Dumped core"<<endl;
        #endif

            g_log<<Logger::Error<<"Respawning"<<endl;
            sleep(1);
            continue;
        }
        g_log<<Logger::Error<<"No clue what happened! Respawning"<<endl;
        }
        else {
        g_log<<Logger::Error<<"Unable to fork: "<<strerror(errno)<<endl;
        exit(1);
        }
    }
    ........
}
```


##### 加载modules

根据`load-modules`给定的值，进行加载modules

代码片段如下：

```c
//receiver.cc

void daemonize(void)
{
    ......
    if(!::arg()["load-modules"].empty()) {
      vector<string> modules;

      stringtok(modules,::arg()["load-modules"], ", ");
      if (!UeberBackend::loadModules(modules, ::arg()["module-dir"])) {
        exit(1);
      }
    }
    BackendMakers().launch(::arg()["launch"]); // vrooooom!
    ......
}
```

##### 实例化DynListener

代码片段如下：

```c
//receiver.cc

void daemonize(void)
{
    ......
    if(isGuarded(argv)) {
      g_log<<Logger::Warning<<"This is a guarded instance of pdns"<<endl;
      dl=new DynListener; // listens on stdin 
    }
    else {
      g_log<<Logger::Warning<<"This is a standalone pdns"<<endl; 
      
      if(::arg().mustDo("control-console"))
        dl=new DynListener();
      else
        dl=new DynListener(s_programname);
      
      writePid();
    }
    ......
}
```

##### 实例化UDPNameServer

```c
//receiver.cc

void daemonize(void)
{
    .....
    N=std::make_shared<UDPNameserver>(); // this fails when we are not root, throws exception

    g_udpReceivers.push_back(N);

    size_t rthreads = ::arg().asNum("receiver-threads", 1);
    if (rthreads > 1 && N->canReusePort()) {
      g_udpReceivers.resize(rthreads);

      for (size_t idx = 1; idx < rthreads; idx++) {
        try {
          g_udpReceivers[idx] = std::make_shared<UDPNameserver>(true);
        }
        catch(const PDNSException& e) {
          g_log<<Logger::Error<<"Unable to reuse port, falling back to original bind"<<endl;
          break;
        }
      }
    }
    ......
}
```

##### 实例化TCPNameServer

```c
//receiver.cc

void daemonize(void)
{
    .....
    if(!::arg().mustDo("disable-tcp"))
      TN=new TCPNameserver; 
    ......
}
```

##### 启动DNSProxy

```c
//common_startup.cc

void mainthread()
{
    .....
    if(::arg().mustDo("resolver")){
        DP=new DNSProxy(::arg()["resolver"]);
        DP->go();
    }
    .....
}
```

##### 启动WebServer

```c
//common_startup.cc

void mainthread()
{
    .....
    if(::arg().mustDo("webserver") || ::arg().mustDo("api"))
        webserver.go();
    .....
}
```

##### 启动DynListener

```c
//common_startup.cc

void mainthread()
{
    .....
    dl->go();
    .....
}
```

##### 启动TCPNameServer

```c
//common_startup.cc

void mainthread()
{
    .....
    if(TN)
        TN->go(); // tcp nameserver launch
    .....
}
```

TCPNameServer会在接收数据包时创建工作线程。

```c
//tcpreceiver.cc

void TCPNameserver::thread()
{
    .......
    if(pthread_create(&tid, 0, &doConnection, reinterpret_cast<void*>(fd))) {
        g_log<<Logger::Error<<"Error creating thread: "<<stringerror()<<endl;
        d_connectionroom_sem->post();
        close(fd);
        decrementClientCount(remote);
    }
    .......
}

```

##### UDPNameServer分配工作线程

```c
//common_startup.cc

void mainthread()
{
    .....
    unsigned int max_rthreads= ::arg().asNum("receiver-threads", 1);
    g_distributors.resize(max_rthreads);
    for(unsigned int n=0; n < max_rthreads; ++n)
        pthread_create(&qtid,0,qthread, reinterpret_cast<void *>(n));
    .....
}
```

而每个接收线程则会创建若干自己的distributor线程。

```c
//common_startup.cc

void *qthread(void *number)
{
    .....
    DNSPacket *P;
    DNSDistributor *distributor = DNSDistributor::Create(::arg().asNum("distributor-threads", 1)); // the big dispatcher!
    .....
}
```

### 查询流程

#### TCP查询

```c
//tcpreceiver.cc

void *TCPNameserver::doConnection(void *data)
{
    .....
    // 第一步：在PacketCache查询

   // short circuit - does the PacketCache recognize this question?

    if(packet->couldBeCached() && PC.get(packet.get(), cached.get())) {

        if(logDNSQueries)
          g_log<<"packetcache HIT"<<endl;
        cached->setRemote(&packet->d_remote);

        cached->d.id=packet->d.id;

        cached->d.rd=packet->d.rd; // copy in recursion desired bit 

        cached->commitD(); // commit d to the packet  inlined

        sendPacket(cached, fd); // presigned, don't do it again

        continue;
      }
    .....

    // 第二步：backend中查询

      if(logDNSQueries)
          g_log<<"packetcache MISS"<<endl;  
      {
        Lock l(&s_plock);
        if(!s_P) {
          g_log<<Logger::Error<<"TCP server is without backend connections, launching"<<endl;

          s_P=new PacketHandler;

        }

        // we really need to ask the backend :-)
        
        reply=shared_ptr<DNSPacket>(s_P->doQuestion(packet.get())); 
      }

      if(!reply)  // unable to write an answer?

        break;

      sendPacket(reply, fd);

}
```

#### UDP查询

```c
// common_startup.cc

void *qthread(void *number)0
try
{
    ......
    // 第一步：查询PacketCache

        if((P->d.opcode != Opcode::Notify && P->d.opcode != Opcode::Update) && P->couldBeCached()) {
           
        // does the PacketCache recognize this question?
           
        bool haveSomething=PC.get(P, &cached);

        if (haveSomething) {
            if(logDNSQueries)
                g_log<<"packetcache HIT"<<endl;

            cached.setRemote(&P->d_remote);  // inlined

            cached.setSocket(P->getSocket()); // inlined

            cached.d_anyLocal = P->d_anyLocal;

            cached.setMaxReplyLen(P->getMaxReplyLen());

            cached.d.rd=P->d.rd; // copy in recursion desired bit

            cached.d.id=P->d.id;

            cached.commitD(); // commit d to the packet inlined

            NS->send(&cached); // answer it then inlined

            diff=P->d_dt.udiff();

            avg_latency=(int)(0.999*avg_latency+0.001*diff); // 'EWMA'

            continue;
        }
    }

    .......

    // 第二步：去backend中查询

    try {
      
      distributor->question(P, &sendout); // otherwise, give to the distributor

    }
    catch(DistributorFatal& df) { // when this happens, we have leaked loads of memory. Bailing out time.

      _exit(1);

    }
    ......
}

```

### UeberBackend

UeberBackend是一个特殊的Backend。启动时被加载的backends向它注册，添加到一个vector中。UeberBackend按注册顺序依次调用其他backend的相应成员方法。

#### 调用

```c
// ueberbackend.cc

// 第一步：UeberBackend的get回调d_handle.get(rr)
bool UeberBackend::get(DNSZoneRecord &rr)
{
  ......
  if(!d_handle.get(rr)) {
    ......
  }
    .......
}

bool UeberBackend::handle::get(DNSZoneRecord &r)
{
  .......
  // 第二步：d_handle.get首先调用第一个backend的get方法

  while(d_hinterBackend && !(isMore=d_hinterBackend->get(r))) { // this backend out of answers

  // 第三步：不成功 调用下一个backend的lookup和get方法，直到返回成功或者遍历完所有backend

    if(i<parent->backends.size()) {

      DLOG(g_log<<"Backend #"<<i<<" of "<<parent->backends.size()
           <<" out of answers, taking next"<<endl);
      
      d_hinterBackend=parent->backends[i++];
      d_hinterBackend->lookup(qtype,qname,pkt_p,parent->d_domain_id);
    }
    else 
      break; // 成功就返回

    DLOG(g_log<<"Now asking backend #"<<i<<endl);
  }
  if(!isMore && i==parent->backends.size()) {
    DLOG(g_log<<"UeberBackend reached end of backends"<<endl);
    return false;
  }

  DLOG(g_log<<"Found an answering backend - will not try another one"<<endl);
  i=parent->backends.size(); // don't go on to the next backend
  return true;
}
 
```

### Communicator

- slave：是不是supermastert的通知，包括创建对应的Slave的domain等；do AXFR from master,
- master：master检查master的domain是否有更新，有更新的化放入队列
- doNotifications：master 检查队列是否有通知并发送通知

```c
//communicator.cc

void CommunicatorClass::mainloop(void)
{
  try {
    .......

    // 每slave-cycle-interval检查一次

    d_tickinterval=::arg().asNum("slave-cycle-interval");

    .......
    for(;;) {

      slaveRefresh(&P); //包括创建对应的Slave的domain，do AXFR from master

      masterUpdateCheck(&P); //master检查master的domain是否有更新，包括通过api修改的更新

      tick=doNotifications(); //this processes any notification acknowledgements and actually send out our own notifications
      
      tick = min (tick, d_tickinterval);
      next=time(0)+tick;
      while(time(0) < next) {
        rc=d_any_sem.tryWait();

        if(rc) {
          bool extraSlaveRefresh = false;
          Utility::sleep(1);
          {
            Lock l(&d_lock);
            if (d_tocheck.size())
              extraSlaveRefresh = true;
          }
          if (extraSlaveRefresh)
            slaveRefresh(&P);
        }
        else { 
          break; // something happened

        }
        // this gets executed at least once every second

        doNotifications();
      }
    }
  ........
}
```

#### masterUpdateCheck 

```c
//mastercommunicator.cc

void CommunicatorClass::masterUpdateCheck(PacketHandler *P)
{
  .......
  // 查询所有的domain

  B->getUpdatedMasters(&cmdomains); 
  ........
  
  for(auto& di : cmdomains) {

    purgeAuthCachesExact(di.zone);

    queueNotifyDomain(di, B); //查询是否有通知，并放入队列

    di.backend->setNotified(di.id, di.serial); //将记录设置为已通知，set serial
  }
}
```



### 处理接收到的通知

```c
//packethandler.cc

int PacketHandler::processNotify(DNSPacket *p)
{
    ......
}
  
```






