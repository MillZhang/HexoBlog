---
title: JS获取上一页访问URL地址
tags: JavaScript
---

### document.referrer
概念:
> 获取浏览器上一页来源的url信息

返回上一页一般实现:

`history.go(-1)`或者`history.back()`

但如果上一页没有来源信息,返回时就比较捉急了,此时需要判断`document.referrer`为`''`再来做对应的处理
```js
if(document.referrer === ''){
  //do some business
}
```

<!--more-->

### 一些无法获取上一页referrer信息的场景
* 直接在浏览器地址栏中输入
* 使用`location.reload()`刷新(包括`location.href`或`location.replace`刷新页面);
* 扫码进入移动端浏览器
* 直接在新窗口打开页面
* 从https的网站直接进入一个http协议的网站(HTTPS turns off HTTP Referrers to HTTP websites. 默认是关闭的，安全原因;
开启的话在meta里设置，可能现在的流行的最新版浏览器兼容会好点！)
* `a`标签设置`rel="noreferrer"`（兼容IE7+）
* `meta`标签来控制不让浏览器发送`referer`
```html
<meta content="never" name="referrer">
```
