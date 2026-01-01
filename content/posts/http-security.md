---
title: HTTP security summary
date: 2020-01-14 00:00:00.0
updated: 2021-07-27 19:05:56.546
summary: All sites should enable https.
categories: ["Tech"]
tags: ["https", "security"]
---


HTTPS {#https}
--------------

HTTPS: HyperText Transfer Protocol Secure（超文本安全传输协议）

HTTP 协议明文传输，通过路由节点可被拦截，不利于传输敏感或机密数据，为用户着想，网站请使用 HTTPS 协议，可参考 [Wildcard cert via Let's Encrypt](https://chuntung.com/lets-encrypt/) 生成浏览器信任的证书，或 [Self-signed certificate](/posts/self-signed-cert/) 生成自签名证书。

HTTPS 一句话概括：CA 证书公钥验证证书指纹，通过非对称加密算法协商对称加密的密钥等信息，通过对称加密传输数据，并对数据做签名认证。

MITM \& SSLStrip {#mitmsslstrip}
--------------------------------

HTTPS客户端与服务端的传输内容不可被拦截，但二者之间存在中间人时，还是有可能遭受攻击，如下两种：

### MITM: Man-In-the-Middle Attack（中间人攻击） {#mitmmaninthemiddleattack}

客户端未做证书信任校验（即CA证书公钥验证网站证书签名，校验证书有效期、吊销状态等），中间人使用自签证书与客户端交互，从而获取通信内容。

防范方式：

* 用户不要安装不信任的CA证书，不信任不安全的证书；
* 客户端请求时进行证书信任校验；
* 服务端保管好证书私钥，泄露及时吊销；
* 证书颁发机构不要参与伪造证书！

### SSLStrip（SSL剥离攻击） {#sslstripssl}

中间人将url替换为HTTP，以致客户端使用HTTP协议，中间人则使用SSL服务器交互，以此获得通信内容。

防范方式：服务端声明HSTS强制客户端使用SSL。

XSS {#xss}
----------

XSS: Cross-site scripting（跨站脚本攻击）

网站中被写入攻击脚本，录入内容未校验导致保存了脚本，浏览器打开后执行恶意脚本。

防范方式：页面显示时进行html转码。

CSRF {#csrf}
------------

CSRF: Cross-site request forgery（跨站请求伪造）

攻击者通过欺骗/诱惑等手段让用户在已认证的情况下执行一些操作，伪造成用户行为去请求服务器。  
本质是利用浏览器表单允许*跨站*提交数据，AJAX 不允许跨站，如果某个行为*只限定 AJAX 提交*，则不会被攻击。

GET 方式，点击本站链接（或嵌入图片链接），直接请求服务器；  
POST 方式，点击其他站点链接，通过跨站脚本构造表单内容后提交。

防范方式：

1. 检验 header referer，依赖浏览器追溯来源，本站被嵌入图片链接可能校验无效；
2. 检验 csrf token，服务器生成 csrf token，当作html元素传给客户端，客户端提交时，form 表单或 http header（ajax 方式）携带 csrf_token，不得存于 cookie 并传至服务器，浏览器会自动传递 cookie。

URL Redirect {#urlredirect}
---------------------------

利用知名网站跳转漏洞跳至钓鱼网站，诱导密码用户名后跳回。

防范方式：服务端跳转时校验网址合法性。