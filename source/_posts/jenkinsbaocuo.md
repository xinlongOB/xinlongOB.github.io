---
title: jenkins报错--秘钥认证失败
tags:
  - jenkins
  - linux
categories: 运维
date: 2020-06-01 16:45:44
---
测试服更换机器后，新服务器把旧服务器ip顶替，但是秘钥不同。构建的时候报错

报错日志：

    Pseudo-terminal will not be allocated because stdin is not a terminal.
    @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
    @    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
    @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
    IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
    Someone could be eavesdropping on you right now (man-in-the-middle attack)!
    It is also possible that a host key has just been changed.
    The fingerprint for the RSA key sent by the remote host is
    SHA256:y0IuKOX1vqISQPtpFZ3zbC+DtBqRMQfd1dXj8foo3Fs.
    Please contact your system administrator.
    Add correct host key in /var/lib/jenkins/.ssh/known_hosts to get rid of this message.
    Offending ECDSA key in /var/lib/jenkins/.ssh/known_hosts:7
    Password authentication is disabled to avoid man-in-the-middle attacks.
    Keyboard-interactive authentication is disabled to avoid man-in-the-middle attacks.
    Permission denied (publickey,gssapi-keyex,gssapi-with-mic,password).
    Build step 'Execute shell' marked build as failure
    Finished: FAILURE

解决办法：

    vim  /var/lib/jenkins/.ssh/known_hosts

找到对应的IP数据删除整行的秘钥

重新执行构建就ok了