---
title: HTML 基础学习与理解
date: 2016-01-04 11:21:55
tags: [coding,html]
categories: html
---

# HTML 基础的学习与理解

作为一个前端技术的小菜，之前虽然写html，却从来没有仔细的去理解html基础细节甚至是原理等，借此博客补习一下。

## 概览

```
<!DOCTYPE html>
 <html>
  <head>
  <meta charset="utf-8" />
  <meta name="keywords" content="html learning" />
  <meta name="description" content="it is learning blog" />
  <title>Steven HTML Blog</title>
  </head>
  <body>
   <div>
   </div>
  </body>
 </html>

```
### !DOCTYPE
可能这个是最容易被人忽略的一点吧，其实对于html来说还是挺重要的。
!DOCTYPE 放在<html>前面，告诉浏览器文档使用哪种html或者xhtml规范。 第一，它告诉用户代理和校验器这个文档是按照什么DTD写的。这个动作是被动的，也就是说，每次页面加载时，浏览器并不会下载DTD并检查合法性，只有当手动校验页面时才启用。
第二个，对于实际操作，最重要的目的是通知浏览器读取文档时用哪种解析算法。浏览器有三种方式解析HTML文档：非怪异（标准）模式、怪异模式、部分怪异（几乎标准）模式。
**如果缺少doctype 或者格式不正确，那么怪异模式会被触发，页面以老的并且不正确的方式渲染。**

### HEAD 细节
内部的meta提供页面元数据定义，元数据总是以名称/值的形式存储

  >  用不同的属性名来划分，这两种属性名为：name属性类型，http-equiv属性类型。
  >  `<meta name=”某个设置值” content=”对该设置值进行具体补充说明的信息”>`
  >  `<meta http-equiv=”某个设置值” content=”对该设置值进行具体补充说明的信息”>`
  > **name=”keywords”**: “keywords” 是一个经常被用到的名称。它为文档定义了一组关键字.content中有多个关键字的时候用逗号分隔
  > **name=”description”**:会把content中的内容最为网页文件的简要描述添加到搜索引擎当中。很多的搜索引擎都会把description中的内容显示在所述索引显示超链接的下面。
  > **name=”robots”**:content的设置值：
    + index（允许搜索引擎检索收录）
    + noindex（不允许搜索引擎检索收录）
    + follow（允许索引引擎检索收录并且顺着超链接继续查找）
    + nofollow（允许索引引擎检索收录但是不允许顺着超链接继续查找）
    + all（允许搜索引擎检索收录也能，也允许顺着超链接继续查找）
    + none（不允许搜索引擎查检索收录找也不允许顺着超链接继续查找）

  > **name=”generator”**:网页的编辑软件名称。
  > **name=”autor”**:网页的作者名称。
  > **name=”copyright”**:对版权声明

### 关于meta 中http-equiv
使用带有 http-equiv 属性的 <meta> 标签时，服务器将把名称/值对添加到发送给浏览器的内容头部。例如，添加：
  > `<meta http-equiv="charset" content="iso-8859-1">`
  > `<meta http-equiv="expires" content="31 Dec 2008">`

这样发送到浏览器的头部就应该包含：
  > content-type: text/html (默认一定会带有这个)
  > charset:iso-8859-1
  > expires:31 Dec 2008

```
<html>
   <head>
     <meta http-equiv="refresh" content="5;URL=http://www.zixun24.com">
   </head>
   <body>
          页面会5秒钟自动刷新并且跳转到url制定的地址
          无URL仅仅五秒钟刷新一次页面
   </body>
</html>
````
如果设置content=”0″表示禁止使用缓存。
`<meta http-equiv="expires" content="Wed, 20 Jun 2015 22:33:00 GMT">`
到期重新从服务器load页面

**http-equiv =”windows-target”：** 强制定义当前网页的打开窗口。用于防止页面被盗用。例如，要防止自己的网页被别人当成一个frame页面调用，可以使用如下：`<meta http-equiv=”windows-target” content=”_top”>`

**http-equiv =”pragma”：禁止浏览器缓存页面。**
例如：`<meta http-equiv=”pragma” content=”on-cache”>`
或者：`<meta http-equiv=”cache-control” content=”on-cache”>` 这个比上一个更可靠

**http-equiv =”page-Enter”、http-equiv =”page-exit”：**
浏览器刚打开页面时候的显示效果。`<meta http-equiv="page-enter" content="revealTrans(Transition=23,Duration=1.0)">`
浏览器退出页面时候的显示效果。`<meta http-equiv="page-exit" content="revealTrans(Transition=23,Duration=1.0)">`

#### 连接
 [meta 学习](http://www.zixun24.com/storage/2015/05/web-html-mate-06.html)
