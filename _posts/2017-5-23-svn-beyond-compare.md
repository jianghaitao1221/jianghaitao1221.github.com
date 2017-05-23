---

layout:     post
title:      "TortoiseSVN设置比较工具为BeyondCompare"
subtitle:   "小乌龟升级"
date:       2017-5-23 08:00:00
author:     "Jht"
header-img: "img/util-bg.png"
catalog: true
tags:
    - Beyond Compare
---

## 配置

`"C:\Program Files (x86)\Beyond Compare x`为安装路径
### 第一步 

- 右键->TortoiseSVN
- 选择`Setting`
- 选择`Diff Viewer`
    - 填写`"C:\Program Files (x86)\Beyond Compare x\BComp.exe" %base %mine /title1=%bname /title2=%yname /leftreadonly`
    - 填写`"C:\Program Files (x86)\Beyond Compare 3\BCompare.exe"`
    - 填写`"C:\Program Files (x86)\Beyond Compare 3\BCompare.exe"`

![bc1](http://jianghaitao1221.github.io/img/in-post/svn-bc/svn-bc1.png)

### 第二步

- 选择`Merge Toole`
- 填写`"C:\Program Files (x86)\Beyond Compare x\BComp.exe" %mine %theirs %base %merged /title1=%yname /title2=%tname /title3=%bname /title4=%mname`

![bc2](http://jianghaitao1221.github.io/img/in-post/svn-bc/svn-bc2.png)

