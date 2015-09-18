---
layout: post
title: "Telnet 发信测试"
category: Linux
tags: [Telnet]
---

```
> telnet localhost
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
220 mail.demoslice.com ESMTP Postfix (Gentoo)
> HELO yeah.net
250 yeah.net
> MAIL FROM:<root@localhost>
250 2.1.0 Ok
> RCPT TO:<mail@yeah.net>
250 2.1.5 OK
> DATA
354 End data with <CR><LF>.<CR><LF>
> Subject: test
this is test email
.
250 2.0.0 Ok: queued as 9620FF0087
> QUIT

```