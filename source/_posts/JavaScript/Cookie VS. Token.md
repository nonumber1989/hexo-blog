---
title: Cookie 还是Token，这是一个问题
date: 2017-3-10 15:21:55
tags: [coding,javascript]
categories: javascript
---

# Cookie 还是Token，这是一个问题

>   现在的web应用已经不同以往，从传统的JSP、ASP到现在的SPA单页面应用，从以前的集群到现在火热的微服务，web开发似乎每年要搞点新意出来，作为一个小小码农，我觉得需要回归基础与本质，然后从基础与本质出发寻找新鲜的路子来解决问题。
    今天就从Cookie和Token这个点出发，思考下如何选择Web认证方式。

# Cookie：Web老兵
 > 说到Cookie就要说Session（这里指服务端session，不是泛化的session），作为web世界的老兵，Cookie和Session这对好兄弟解决了http请求无状态的问题。http的request-response一来一往，服务端并不知晓来者是谁，如何让服务器在迷糊状态下记住来者何人，我们需要引入Cookie和Session：举个栗子，小明（对，就是小明）要访问某付费小电影网站网站[www.shortAv.info](我瞎造的你也信?)，他输入自己的用户名、密码，此时小电影网站服务器根据输入的用户名密码到数据库load一遍，嗯，没错！是我们的注册付费用户，棒棒哒！然后服务器创建session，

    HttpSession session = HttpServletRequest.getSession(true);
> session可以把用户名、权限、首选项设置等信息存起来（这就是所谓的状态，内存里在会话期间维持一堆信息），同时通过设置Cookie返回sessionId给浏览器端，浏览器拿到Cookie，至此登录完成，浏览器之后的请求都自动带着Cookie（内含sessionId），服务器会根据sessionId拿到session的信息，验明正身，区分下VIP什么的。Cookie和Session通过一个sessionId建立起浏览器和服务器之间的连接。

     Cookie cookie = new Cookie("theKey",  theValue);
     //cookie.setMaxAge(24*60*60);

> 没有设置MaxAge的Cookie叫做Session Cookie，也就是这个Cookie驻留内存，一旦浏览器关闭就消亡了；设置了MaxAge就是persist Cookie，说白了就是可以存储在文件中被持久化的Cookie，关闭浏览器之后依然存在，有一个保质期。
   Cookie加Session就像月老在两个年轻人之间牵的红线，红线就是那个sessionId喽，每次交流都”冥冥之中“通过那根线，做到知己知彼。

# Token ：新战线利器

> Token这玩意儿其实不是新鲜东西，常见的CSRF token经常和Cookie勾肩搭背玩暧昧，一起保卫web的安全，关于CSRF与token，以后可以再深入讲，此处就偷个懒。既然我要讲的Web认证，那么这个Token就不仅仅是一个随机数、做防御的小机制那么简单了。下面就以[jwt-Json Web Token](https://jwt.io/)来讲解。

> 还是小明，这次他不从电脑上玩了，蹲厕所的时候打开手机APP，还是那个[www.shortAv.info](我瞎造的你也信?)网站做的，他输入用户名、密码，post到服务端之后同样根据数据库存储的信息对比下，发现是个validated的用户，这次不返回Cookie也不创建Session了，而是返回一个refreshToken和accessToken给请求者，请求者保存下来，至此”登录成功“，接下来的请求都带着accessToken，服务端只要验证accessToken的正确性（根据密钥）就可以使用token中的信息了，一旦accessToken过期，使用refreshToken换一个新的就是了。小明又可以继续欣赏了，哈哈。

> JWT的payload可以承载诸如用户名，权限等信息，这些信息可以存储在浏览器Local Storage或者Session Storage中，每次请求服务器时可以把JWT放在http header中，服务器端对每个请求的JWT进行解析，知道来着是谁，有何权限。（我知道您有很多问题，比如安全，少安毋躁，建议先了解下JWT）。
JWT和服务端资源就像钥匙和锁一样，服务端把东西锁好保护，并根据验证信息给用户发放钥匙（钥匙里带着很多信息，比如你是谁，能开哪个箱子等），以后用户要拿东西，带着钥匙开就是了，每次开，箱子根据钥匙就知道你是谁，能不能开这个箱子。

# Cookie Vs. Token
+ Cookie成熟，配合Session-Cookie的方案容易上手，Token作为一个新鲜方案，需要更多的考量
+ Cookie配合Session的机制，使得服务端维护”状态“，这与现在无状态后台设计思路相悖，比如RESTful设计，鼓励无状态；Session的存在也使得后端服务负载均衡相对麻烦，session粘连或者session复制都不是特别好的方案，个人觉得中小型项目，JWT配合RESTful相当方便。
+ Cookie存储4K数据，Token的payload可以更多（但是放多了也是麻烦事，毕竟你每次请求带这么大个header也不是个优秀的设计）
+ Cookie多用在浏览器Web应用上，Mobile上没用过Cookie，现在Mobile First的设计思路大行其道，一个API即满足浏览器调用还要满足Mobile甚至其他设备调用，Token就比Cookie方便了；而且，如果多终端大量用户调用API，Token的无状态将给服务器减轻巨大的负载压力，毕竟把状态推到了客户端分担了。
+ Cookie配合Session的方案，用户信息状态在服务端，服务端有过期时间，而且服务器端可以方便invalidate某个session，而JWT则把信息分摊到客户端，也存在过期时间，但是一旦token被发放，就不好在服务端进行revoke（具体折中的方法在下面提到）
+ 安全问题，讲真，我不太敢讲这块，因为一些大牛在争论Cookie和Token两方案的安全比较问题，我仅抛出自己的一些思考： Cookie存在CSRF隐患，当然可以通过添加CSRF-Token防御，JWT Token本身就是Token了，脱离了Cookie，自然就不用开了CSRF了；JWT呢，Token一旦暴露，payload的信息均可以被查看，增加了信息泄露的危险，JWT的密钥只能防范内容被篡改；Cookie有httpOnly设置，可以防止js读取Cookie内容，而JWT可以被js操纵，XSS带来的危害对JWT来说更大。但总体来讲，XSS更容易防范。

# 补充与总结
###小补充

 * Token只是一种机制，发放给客户端后可以被存储起来，LocalStorage和SessionStorage是比较合适的存储方案，当然，如果你喜欢，放Cookie里也行，但是这时候Cookie就是一个存储载体和传输载体（服务端不需要进行状态维护）
 * Token的过期时间一般不要过长，根据实际情况设定，Token过期后需要进行Token置换，可以考量使用refreshToken加accessToken的方案，RefreshToken过期时间可以长一些，而且可以进行revoke（最好别revoke accessToken，否则又走回维护状态的老路了）
 * Token的payload信息，比如用户名、头像url等可以存储起来并被前端展示，这比放入全局变量好（私以为）

### 小总结
  * 如果API设计成规范的无状态RESTful，考虑使用token
  * 如果API被不同终端消费，Cookie受限，考量token
  * 如果采用SPA，而且没有服务端渲染（或者很少），考量token
  * 如果没有考量这些玩意儿，选择Cookie Session机制吧，哥就是稳
