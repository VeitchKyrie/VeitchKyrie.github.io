---
layout:     post
title:      遇到问题之git remote rejected
subtitle:   遇到问题
date:       2019-09-30
author:     VK
catalog: true
tags:
    - git
---



## git remote rejected 问题

> Counting objects: 38, done.
>
> Delta compression using up to 4 threads.
>
> Compressing objects: 100% (27/27), done.
>
> Writing objects: 100% (38/38), 6.61 KiB | 0bytes/s, done.
>
> Total 38 (delta 32), reused 15 (delta 11)
>
> remote: Resolving deltas: 100% (32/32)
>
> remote: Counting objects: 197659, done
>
> remote: Processing changes: refs: 1,done    
>
> remote: (W) ae78e28: commit subject >65characters; use shorter first paragraph
>
> remote: (W) d0e3f51: commit subject >65characters; use shorter first paragraph
>
> Tossh://uidq1459@gerrit-review:29418/FAW-VW-CRS3.0-MQB-Soc
>
> **! [remote rejected] HEAD ->refs/for/master (change <http://gerrit-review:8080/105579>closed)**
>
> **error: failed to push some refs to'ssh://uidq1459@gerrit-review:29418/FAW-VW-CRS3.0-MQB-Soc'**



## 解决

`gitk`找到最新的提交id

085f8895a237339c619553a4fef27d2fc0cdd0ce

```shell
git reset --hard 085f8895a237339c619553a4fef27d2fc0cdd0ce
```

然后解压备份的文件

```shell
git status
git push review
```

