---
layout:     post
title:      "git cherry-pick"
subtitle:   " \"分支的合并commit\""
date:       2019-11-07 00:00:00
author:     "Jht"
header-img: "img/post-bg-git.jpg"
catalog: true
tags:
    - git
---

## git cherry-pick

### 切换到要合并的分支

#### fork的

```bash
git fetch upstream release-0.2
git checkout -b release-0.2 upstream/release-0.2
git push origin release-0.2
```

#### 非fork的

```bash
# 没有的话先创建分支
git checkout release-0.2
```

### cherry-pick

找到你要合并的提交（一个或多个）

```bash
git cherry-pick -x xxxxxx # xxxxxx为commit id  -x 参数，表示保留原提交的作者信息进行提交
git cherry_pick <start-commit-id>…<end-commit-id> #它的范围就是 start-commit-id 到 end-commit-id 之间所有的 commit，但是它这是一个 (左开，右闭] 的区间，也就是说，它将不会包含 start-commit-id 的 commit。
git cherry-pick <start-commit-id>^...<end-commit-id> # 包含start-commit-id
```

#### 冲突

冲突的话,解决冲突

```bash
git commit -c 
git cherry-pick -x xxxxxx # xxxxxx为commit id
git cherry-pick --continue
```

### 其他命令

```bash
git cherry-pick --skip
git cherry-pick --quit
git cherry-pick --abort
```