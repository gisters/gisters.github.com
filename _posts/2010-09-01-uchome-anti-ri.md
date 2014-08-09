---
layout: post
title: "UChome 防注册机"
description: "uchome 防注册机"
keywords: "uchome, 注册机"
category: Internet
tags: [UChome, RI, Usage]
---

![uchome]({{ site.qiniudn }}/images/2010/09/uchome.png "uchome")

被注册机盯上了怎么办，特别是像 uchome 这样的 SNS 网站，批量注册一大堆用户，全部发些垃圾内容，整个网站一会就被弄的不成样。

解决这个问题的办法，直接采用的就是邮箱验证，为的就是提高用户质量，如果一个真正想注册用的户是不会在意去打开邮箱，拿到验证码的。

<!-- more -->

解决的过程很简单，这里就简单公布下代码和一个通用的邮件程序，即使你的服务器没装或没开通邮件服务，也可以借助像 `qq mail` 这样的第三方发送邮件，好了，废话不多说了，详细内容看下面：

#### 首先建一个验证邮箱的数据表，在 `mysql client` 或者 `phpmyadmin` 里执行，注意你自己的版本编码和数据库表前缀

```sql
CREATE TABLE IF NOT EXISTS `uh_checkusermail` (
`id` int(10) unsigned NOT NULL auto_increment,
`uid` int(10) unsigned NOT NULL default ‘0′,
`mail` varchar(100) NOT NULL,
`checknum` varchar(20) NOT NULL,
`statu` tinyint(1) unsigned NOT NULL default ‘0′,
`dateline` int(10) unsigned NOT NULL,
PRIMARY KEY (`id`)
) ENGINE=MyISAM DEFAULT CHARSET=gbk AUTO_INCREMENT=1 ;
```

#### 打开 uchome 根目录下的 `template\default\do_register.htm` 文件

在里面找到下面这句代码

```html
<tr><th>邮箱</th><td><input type=”text” id=”email” name=”email” value=”@” tabindex=”5″ />
 <br>请准确填入您的邮箱，在忘记密码，或者您使用邮件通知功能时，会发送邮件到该邮箱。</td></tr>
````

替换为

```html
<tr><th>邮箱</th><td><input type="text" id="email" name="email" value="@" tabindex="5" />&nbsp;&nbsp;<input type="button" onclick="sendcode()" value="发送邮箱验证码" />&nbsp;<span id="sendmsg"></span></td></tr>
 <tr><th>邮箱验证码</th><td><input type="text" id="mailcode" name="mailcode" value="" tabindex="6" /></td></tr>
```

#### 再在上面那个文件里找到

```html
<script>
function register(id, result) {
if(result) {
$(’registersubmit’).disabled = true;
window.location.href = “$jumpurl”;
} else {
updateseccode();
}
}
</script>
```

替换为

```html
<script>
function register(id, result) {
if(result) {
$(’registersubmit’).disabled = true;
window.location.href = “$jumpurl”;
} else {
updateseccode();
}
}
function sendcode() {
var mail = $(’email’).value;
ajaxget(’r_checkmail.php?mail=’+mail+’&time=’+new Date().getTime()+’&ajaxdiv=sendmsg’, ’sendmsg’);
}
</script>
```

#### 打开uchome根目录下的`source/do_register.php`文件

在里面找到下面这句代码

```js+php
//检查邮件
 if($_SCONFIG['checkemail']) {
 if($count = getcount(’spacefield’, array(’email’=>$email))) {
 showmessage(’email_has_been_registered’);
 }
 }
```

在这段代码下面加上

```js+php
$query = $_SGLOBAL['db']->query(”SELECT * FROM “.tname(’checkusermail’).” where mail=’”.$email.”‘ and checknum=’”.$_POST['mailcode'].”‘ and statu=0″);
 if(!$value = $_SGLOBAL['db']->fetch_array($query,1)) {
 showmessage(’邮箱验证码错误’);
 }
```

#### 再在上面那个文件里找到

```js+php
//开通空间
 include_once(S_ROOT.’./source/function_space.php’);
 $space = space_open($newuid, $username, 0, $email);
```

在这段代码下面加上

```js+php
//更新邮箱状态
 $_SGLOBAL['db']->query(”update “.tname(’spacefield’).” set emailcheck=1 where uid=’”.$newuid.”‘”);
```

#### 下载压缩包，解压后打开里面的 r_checkmail.php 文件

找到如下代码

```js+php
$mail->Host = “smtp.qq.com”; //邮件服务器
$mail->Port = “25″; //邮件服务器端口
$mail->SMTPAuth = true;
$mail->Username = “111111@qq.com“; //邮件帐号
$mail->Password = “123456789″; //邮件帐号密码
$mail->From = “111111@qq.com“; //发送邮件帐号
```

参考说明配置你的邮件发送程序，如果你选用的是 `qq mail` 的话，一定要在你的 `qq mail` 设置里开启 `smtp`。

#### 修改完以上步骤后，把压缩包的 `r_checkmail.php` 和 `phpmailer` 全都放到你的 `uchome` 根目录下

下载：[codefile]({{ site.qiniudn }}/images/2010/09/codefile.zip)

转自：[被注册机困扰的问题 - uchome吧](http://www.uchome8.com/127)
