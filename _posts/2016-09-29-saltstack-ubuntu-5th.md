---
layout:     post
title:      "salt-key命令介绍"
subtitle:   "\"saltstack学习五\""
date:       2016-09-29 00:00:00
author:     "Jht"
header-img: "img/home-bg-computer.jpg"
catalog: true
tags:
    - saltstack
---



## salt-key命令介绍

`英文水平一版，列位凑合看。`

```bash
#运行
salt-key -h
```

### Options

```bash
Options:
  --version             显示版本号
  --versions-report     显示程序的所有依赖包版本号
  -h, --help            帮助信息
  --saltfile=SALTFILE   指定Saltfile. 如果不合法, 在当前工作目录中查找.
  #文件内容
  #salt-key:
  #config_dir: /home/vagrant/salt-key
  #log_file: /home/vagrant/salt-key/log.txt
  -c CONFIG_DIR, --config-dir=CONFIG_DIR
                        Salt配置目录的位置。这个目录包含了Salt Master和Minion的配置文件。在大多数系统中，默认位置是``/etc/salt``。
  -u USER, --user=USER  指定用户运行salt-key.
  --hard-crash          捕捉original异常不退出，默认：False
  -q, --quiet           安静模式，不输出信息到控制台
  -y, --yes             对所有询问是否继续，回答yes,默认：False
  --rotate-aes-key=ROTATE_AES_KEY
                        设置成false，将使删除掉的minios还监听master发布的消息。慎用。默认：True

```

### Actions

```bash
Actions:
  -l ARG, --list=ARG      
                        打印公钥key. 可设置下面三个值
                        "pre", "un", and "unaccepted" 会显示 不许可/未签名 keys. 
                        "acc" or "accepted"会显示 许可/已签名 keys. 
                        "rej" or "rejected"会显示拒绝的 keys.  
                        "all" 会显示所有 keys.
  -L, --list-all        会显示所有公钥，相当月: "--list all"
  -a ACCEPT, --accept=ACCEPT 
                        许可指定的公钥(使用--include-all选项
                        可以指定除了挂起的key外的所有reject状态的公钥)
  -A, --accept-all      接受所有等待认证的key
  -r REJECT, --reject=REJECT 
                        拒绝指定的公钥 (使用--include-all选项
                        可以指定除了挂起的key外的所有accept状态的公钥)
  -R, --reject-all      拒绝所有等待认证的key
  --include-all         显示所有状态的key,配合 accepting/rejecting 选项使用，指定所有非pending状态的公钥
  -p PRINT, --print=PRINT 
                        打印指定的公钥
  -P, --print-all       打印所有的公钥
  -d DELETE, --delete=DELETE 
                        删除指定key
  -D, --delete-all      删除所有的key
  -f FINGER, --finger=FINGER 
                        显示指定key的指纹信息
  -F, --finger-all      显示所有key的指纹信息
```

### Logging Options

```bash
Logging Options
  --log-file=LOG_FILE
                        指定日志文件路径，默认: /var/log/salt/key.
  --log-file-level=LOG_LEVEL_LOGFILE
                        日志文件等级，可设置下面中的一个值 'all', 'garbage',
                        'trace', 'debug', 'info', 'warning', 'error', 'quiet'.
                        默认: 'warning'.
```

### Output Options

```bash
Output Options:
    Configure your preferred output format.

    --out=OUTPUT, --output=OUTPUT
                        指定输出的格式, The builtins are 'key', 'yaml',
                        'overstatestage', 'highstate', 'newline_values_only',
                        'txt', 'raw', 'no_return', 'virt_query', 'compact',
                        'json', 'nested', 'quiet', 'pprint'.
    --out-indent=OUTPUT_INDENT, --output-indent=OUTPUT_INDENT
                        设置输出行缩进的空格数. 
                        负数取消输出缩进编排.仅对使用的outputer有效.
    --out-file=OUTPUT_FILE, --output-file=OUTPUT_FILE
                        把显示输出到指定的文件
    --out-file-append, --output-file-append
                        把显示输出添加到指定的文件
    --no-color, --no-colour
                        关闭字体颜色
    --force-color, --force-colour
                        强制开启输出颜色渲染
    --state-output=STATE_OUTPUT, --state_output=STATE_OUTPUT
                        覆盖掉master配置文件中对minion 返回输出内容所定义的state_output值。针对本次命令起效。One of 'full', 'terse', 'mixed', 'changes' or
                        'filter'. Default: 'none'.
    --state-verbose=STATE_VERBOSE, --state_verbose=STATE_VERBOSE
                        覆盖掉master配置文件中对minion 返回输出内容所定义的state_verbose值， 针对本次命令起效。 Set to True or False. Default: none.

```

### Key Generation Options

```bash
Key Generation Options:

    --gen-keys=GEN_KEYS
                        生成的key设置一个salt使用的名称
                        #salt-key --gen-keys=minibar
                        #会生成
                        #minibar.pem minibar.pub
    --gen-keys-dir=GEN_KEYS_DIR
                        设置生成key对的放置目录，默认当前目录。 Default: '.'.
    --keysize=KEYSIZE   生成key设置位数, 仅跟--gen-keys选项配合时有效,
                        数值大小必须大于2048，否则会被提升至2048位，
                        Default: 2048.
    --gen-signature     Create a signature file of the masters public-key
                        named master_pubkey_signature. The signature can be
                        send to a minion in the masters auth-reply and enables
                        the minion to verify the masters public-key
                        cryptographically. This requires a new signing-key-
                        pair which can be auto-created with the --auto-create
                        parameter.
    --priv=PRIV         The private-key file to create a signature with.
    --signature-path=SIGNATURE_PATH
                        The path where the signature file should be written.
    --pub=PUB           The public-key file to create a signature for.
    --auto-create       Auto-create a signing key-pair if it does not yet
                        exist.
    #运行
    #salt-key --auto-create --gen-signature
    #Generating new signing key-pair master_sign.* in /etc/salt/pki/master
    #Using public-key /etc/salt/pki/master/master.pub
    #Using private-key /etc/salt/pki/master/master_sign.pem

```