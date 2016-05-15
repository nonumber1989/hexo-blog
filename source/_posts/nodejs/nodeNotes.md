---
title: Node 学习小笔记
date: 2016-05-13 15:21:55
tags: [nodejs,javascript]
categories: javascript
---


# Why 
学习nodejs的过程中总会碰到一些小的问题，希望把这些小点滴记录下来

### Node 设计本身
  1.  node中根据相对路径读取文件。
      **Relative paths work, but they are relative to process.cwd(), not the currently executing module.**
      *use **__dirname** to solve this problem*
  2.  node中的文件分隔符
      *一直使用   \    就可以，这个分隔符目前涵盖所有的os*
  
  3.  promise 学习 ，直接上一段bluebird的代码吧，继续深入。
        
            var Promise = require("bluebird");
            var forPromise = function(param, callback) {
            // callback(null,param);
                if (param === "nonumber1989") {
                    callback(null, param);
                } else {
                    var error = new Error("nonumber1989 error !");
                    callback(error);
                }
            };
            var thePromise = Promise.promisify(forPromise);
            thePromise("nonumber1989").then(function(value) {
                console.log(value);
                return value + "--for next step";
            }).then(function(value) {
                console.log(value);
            })
            .catch(function(err) {
                console.log(err);
            });

### javascript 基础
  1.  javascript callback return value.
      首先这就是一个误区！ javascript回调中是不能返回值给外面使用的(不借助promise)。因为回调函数的执行时间不确定，后续的值会先被使用。
