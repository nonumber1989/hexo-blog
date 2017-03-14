---
title: 重拾Angular的一些小思考
date: 2017-3-13 17:41:55
tags: [angularjs,javascript]
categories: javascript
---
# 重拾Angular的一些小思考

最近重拾Angular，准确的说是从AngularJs到Angular的迁移，已经相当长一段时间没有触碰前端，重走前端之路，多了几分思考和感慨。作为一个“伪前端”，以java为主业，node以辅助，机缘巧合入了前端开发的门，并搞了一些hybrid的移动开发，与勤恳耕耘前端的“专业前端”相比，自是羸弱很多，本文以此角度分享，末尾也将给出一些个人的总结。如有偏颇，网诸位指正。

 我不是一个狂热的google粉，使用Angular也是误打误撞，进入上一家公司的时候，恰逢一个项目部分重构，前端框架就是Angular（**Angular1.X + KendoUI + Customized Directives + Open Source Directives**） ，当时AngularJs正是如日中天，我也写的津津有味，后台配之RESTful API甚至Happy啊（当然现实并没有那么美好，其中的坑和历史原因的workaround也是挺扯的）。后来也参与了使用Ionic 进行mobile开发的部分工作，也打开了一扇新技术的大门，hybrid概念也风起云涌，大家跃跃欲试，自此 移动、Web、后台之路勉强打通。如今踏入Angular，不免怀念。

## Angular:群雄逐鹿中的高瞻远瞩
如今前端之火熊熊燃烧，前端的工程化之路欣欣向荣，各大厂、各路牛人乃至ES标准都各显神通：诸如Google的AngularJs、Facebook的React、尤雨溪大神的Vue.js、ES6的推进等无不说明这是一个群雄逐鹿的“大前端时代”。
   秦失其鹿，天下共逐之，而赢家并非只有一个。

+ **从AngularJs到Angular :谷歌的高瞻远瞩**
    >对于一个AngularJs的使用者来讲，Angular2的各种消息无疑会有一些打击：全新框架设计、不兼容1.X版本、推荐使用TypeScript开发等，天啊，难道手里的东西要丢掉重新开始？看下如今最红火的几个框架的目标：
 **AngularJS — Superheroic JavaScript MVW Framework**
**Angular: One framework**
**Vue:The Progressive JavaScript Framework**
**React:A JavaScript library for building user interfaces**
注意，Angular是想做One framework，打通移动、web、桌面，提供一站式服务，相比其他轻量级的解决方案，无疑更具前瞻性（为了这个美梦也是付出着努力啊）。

   Vue.js的作者尤雨溪在知乎的回答中也提到，这些框架在当下没有绝对的赢家！ 虽说Vue.js如今火爆，但是领域还是以互联网应用为主，轻量级、灵活；React作为一个Lib着重于View操作；AngularJs依然火爆企业级应用（Angular出来之前我也预测过，与诸位看法一致，Angular新版本并不会导致AngularJs的衰败，AngularJs经历了磨炼之后迎来真正的爆发：越来越多的企业级应用会选择AngularJs，github上55032的start足以表明）。
Angular的决策着实影响了Angular的进一步推进，虽然我也认同**Just do one thing and make it best**，但是有一个全面且优秀的方案也在长远来看更是美哉啊！

+ **从AngularJs到Angular之掣肘天下，纵横捭阖**
  动态语言一时爽，代码重构火葬场！JavaScript由于历史原因和其设计思想使得写出好的js代码、写出好测的代码变得困难，记得初识javascript，直觉它简单方便，等读了一些js的书籍，踩过js的坑之后方觉灵活伴随的陷阱（甚至有一本神书曰：the good part of JavaScript，捂脸逃...)。当Google和巨硬在Angular上达成合作的时候我是甚为惊叹的！两大厂做后盾，TypeScript加Angular，这是要掣肘天下的节奏啊！！！
  > TypeScript与ES6的争论我自是不言，但是从一个写java为生，兼做了前端的业余选手来讲，类型无疑是一个好东西！我想不是每个公司都可以招聘到BAT等大厂高水平的前端工程师、也不是每个项目能够严格推行前端的编码规范、做到严格的前后端分离也不是如描绘的那样容易。每个前端的水平不一致、代码风格不一致、代码库越来越庞大、人员更迭等等在JS这个弱类型语言面前被放大。阿里的一前端大牛都抱怨，写node烦人啊！况且一种兼职的伪前端的呢？
  有了类型有了编译，有了微软大厂（良心讲，微软好些东西设计挺好，若不是当年太过封闭不至于被黑，尤其被Java阵营的人黑！），写代码、重构代码乃至IDE提示都较js有大幅提升啊，不要纸上约束程序员，直接融到他的工具中是最稳妥的方案！

Angular与Typescrip的纵横之策，给繁复巨大的企业级应用带来福音，给非专业的前端开发更多的保障，给微软拥抱开源注入强心针，给谷歌推行思想一条更宽广的路。

+ **从AngularJs到Angular：润物有声，且行且珍惜**
溜一遍Angular的文档，你会发现，其实AngularJs的思想精髓都还在，或者说更加分明、清晰、优秀！Scope随被移除但思想还在，融入到了Componet中；Controller也顺势变成Componet让组件化意图更加明显；Observe改进性能，并删除了额外的directive；模块机制部分使用ES6并支持懒加载····，开发者呢，其实花费一些时间就能很快上手。Angular并没有放弃AngularJs的东西，在大创新的前提下还是为AngularJS的老开发们打开方便之门的！

生态环境上，Ionic也紧随步伐，设计基于material design简直不要太漂亮！虽说Angular的其他组件目前确实没有那么红火，但是我相信，在一段时间后将会大量增加（AngularJs到Angular的组件迁移，不算难事啊）。
Angular在国内似乎没有那么红火，普通企业级应用在享受AngularJs的便利下，Angular确实会变成一个观望选项，不过我相信当发觉了One Framework的好处以及明了TypeScript的便捷之后，讲很快迎来大的发展！

## 总结
ES6是标准，提供了模块化和类型的概念，我想专业的前端同学还是以ES6为主；Typescript是js的超集，提供了AOT，编译是个好东西啊，虽不是正统标准但是包含这些标准，如我这般伪前端可以入手；关于Bable和TC的争论我暂不论述，毕竟不是一个道路上的玩意儿，再次，大前端时代，没有唯一的赢家！怎么合适怎么来，每个框架有各自的场景，选择趁手兵器即可！

　