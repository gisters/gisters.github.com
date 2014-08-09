---
layout: post
title: "JavaScript 实现域名跳转"
description: ""
keywords: "javascript,重定向"
category: Internet
tags: [JavaScript]
---

偶尔发送个博客链接给朋友时，得到的回复基本都是，网页打不开，或者网页打开很慢。由于本博客是托管在 GitHub 上的，内地访问速度一直是问题。之前尝试过在内地配台 vps 用来反向代理。但是成本太高，用了段时间后放弃，重拾去年的 GitCafe，决定把博客迁移到 GitCafe 上。

之前没使用 GitCafe 来托管博客的原因，是其分配的两级域名无法设置301重定向到博客域名上。众所周知，两个域名网站含有一模一样的内容，对 SEO 来说不是好事。今天上午，写了个JavaScript 解决重定向问题。

<!-- more -->
当然，JavaScript 无法实现 301 redirects。这是因为 301 redirects 是服务器状态码。而 JavaScript 则是客户端运行的。

开始工作，首先将域名解析到 gitcafe 的 pages ip `117.79.146.98`

```
$ dig havee.me +nostats +nocomments +nocmd

; <<>> DiG 9.8.3-P1 <<>> havee.me +nostats +nocomments +nocmd
;; global options: +cmd
;havee.me.              IN          A
havee.me.       60      IN          A       117.79.146.98
```

JavaScript:

```html
<script>
  var url = "havee.me";
  var other = "havee.gitcafe.com";
  if( window.location.host == other ) {
      document.location.href = "\/\/" + url + this.location.pathname;
  }
</script>
```

#### 一个小技巧

测试 JavaScript 时，可以直接使用浏览器的控制台，譬如 Firefox 中 Web开发者中的 web控制台，就可以使用

![Web Console]({{ site.qiniudn }}/images/2014/03/web-console.png)

![Javascript]({{ site.qiniudn }}/images/2014/03/javascript.png)

打开 Firefox 的web控制台，可以随心所欲的输入测试，更棒的是，还有自动补全，不怕打错

![Completion]({{ site.qiniudn }}/images/2014/03/completion.png)

    print(window.location.host);
    "havee.me"
    print(this.location.pathname);
    "/internet/2014-03/domain-redirect-by-javascript.html"

#### Update 2014-03-14

今天下午，该问题已被 GitCafe 解决

```
$ curl -IL havee.gitcafe.com
HTTP/1.1 301 Moved Permanently
Server: nginx/1.4.5
Date: Fri, 14 Mar 2014 11:18:00 GMT
Content-Type: text/html
Content-Length: 184
Connection: keep-alive
Location: http://havee.me/
Cache-Control: public

HTTP/1.1 200 OK
Server: nginx/1.4.5
Date: Fri, 14 Mar 2014 11:18:01 GMT
Content-Type: text/html
Content-Length: 17697
Last-Modified: Fri, 14 Mar 2014 09:23:06 GMT
Connection: keep-alive
ETag: "5322ca7a-4521"
Cache-Control: public
Accept-Ranges: bytes
```
