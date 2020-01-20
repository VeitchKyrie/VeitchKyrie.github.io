---
layout:     post
title:      遇到问题---ulimit open files number within process
subtitle:   问题分析及解决
date:       2019-12-08
author:     VK
catalog: true
tags:
    - linux
---



[change the number of open files limit](https://stackoverflow.com/questions/34588/how-do-i-change-the-number-of-open-files-limit-in-linux)

[increase open files limit](https://ro-che.info/articles/2017-03-26-increase-open-files-limit)

```shell
cat /proc/<pid>/limits
lsof -p <pid>
```

[diagnose too many open files issue](https://www.ibm.com/support/pages/how-diagnose-too-many-open-files-issues)



systemomap



I just put the line `ulimit -n 8192` inside the catalina.sh, so when I do the catalina start, java runs with the specified limit above.



**As a system administrator**: The <a href="http://linux.die.net/man/5/limits.conf">`/etc/security/limits.conf`</a> file controls this on most Linux installations; it allows you to set per-user limits. You'll want a line like `myuser - nofile 1000`.

**Within a process**: <a href="http://www.opengroup.org/onlinepubs/009695399/functions/getrlimit.html">The getrlimit and setrlimit calls</a> control most per-process resource allocation limits. `RLIMIT_NOFILE` controls the maximum number of file descriptors. You will need appropriate permissions to call it.