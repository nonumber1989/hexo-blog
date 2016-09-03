---
title: Angular 总结之一
date: 2016-9-3 17:41:55
tags: [angularjs,javascript]
categories: javascript
---

## AngularJs  总结之一
很久都没有更新点自己的博客了，最近忙于工作，参与到两个项目中，其中之一就是继续搞搞angular，虽然感觉依旧是小打小闹，不过重新用起angular，还是收获满满，现在分享点自己的小总结和小经验吧。
### Angular http interceptors
  需要对所有的网络请求和响应进行拦截，统一处理
  比如500 401 403 等消息，我们多数情况不会在每一个fail回调中处理，而是统一给客户提示，这就需要http拦截器
  $httpProvider.interceptors.push('XXXInterceptor');
  该拦截器也是一个普通的service了

    return {
      request: function(config) {
        $rootScope.$broadcast('loading:show');
        return config;
      },
      response: function(response) {
        $rootScope.$broadcast('loading:hide');
        return response;
      },
      responseError: function(rejection) {
        $rootScope.$broadcast('loading:hide');
        console.log(JSON.stringify(rejection));
        if (rejection.data === null) {
          showDialog($rootScope);
        } else if (rejection.status === 401) {
          $injector.invoke(function($state) {
            console.log($state.current.name);
            $state.go('tachi.account.signIn');
          });
        } else if (rejection.status === 403) {
        } else if (rejection.status === 404) {
        } else if (rejection.status === 500) {
        }
      }
    };

### Angular with localstorage
   关于localstorage与sessionstorage的问题，其实挺让我伤心的，公司的UI5框架使用JQuery封装了web storage的操作但是却又出guidline不让使用，这很让人痛心啊
   首先 抛开业务谈及web storage的安全问题，这是html5的规范，符合同源策略，就算你怕用户localstorage保存下来有问题，可以考虑sessionstorage啊
   与其他同事沟通下来觉得公司禁用的原因可能在于，公司一个域名下会存在多个application，而多个application可能存在命名冲突问题，比如我命名user 你也命名user...

   抛开这些吧，我用angular选用的是ngStorage，仅仅是简单封装而已
   put get delete操作而已本身并没有什么其他的难度
### Angular with jwt
   应用中使用了JWT，那么token的存储呢？ 必然就放localstorage了

    jwtOptionsProvider.config({
    authPrefix: 'Bearer ',
    tokenGetter: ['jwtHelper', '$localStorage', function(jwtHelper, $localStorage) {
      var theUserToken = $localStorage.userToken;
      if (theUserToken instanceof Object) {
        var date = jwtHelper.getTokenExpirationDate(theUserToken.token);
        var bool = jwtHelper.isTokenExpired(theUserToken.token);
        return theUserToken.token;
      } else {
        //do nothing with rejected with 401 Unauthorized error
      }
    }],
    whiteListedDomains: ['http://127.0.0.1:3000']
      });
    $httpProvider.interceptors.push('jwtInterceptor');
### Angular event emit and listners
  采用$rootscope 进行事件发射和监听
  往购物车添加一件商品，需要更新下菜单栏的购物车badge数字
  购物车badge所在的view与对应的controller与进行添加动作的controller肯定不在一个中，则在添加时候才哟哦那个$rootscope发射事件和数据，在badge对应得controller监听

     function deleteBroadcast() {
         $rootScope.$broadcast('ShoppingCart', {"action": "delete"});
     }
     //----------------------------------------------------------------
     $scope.$on('ShoppingCart', function (event, data) {
        if (data.action === "add") {
          $scope.itemNumber++;
          console.log($scope.itemNumber);
        } else if (data.action === "delete") {
          $scope.itemNumber--;
        } else if (data.action === "clear") {
          $scope.itemNumber = 0;
        }});

### Angular with UI Router
  也许你用angular做了一些小的项目，采用angular自身提供的router机制，但是我觉得更多的人还是愿意选择更加强大和方便的UI-Router来帮助自己。
  [angular-ui/ui-router](https://github.com/angular-ui/ui-router)
   其中大名鼎鼎的ionic就把UI-Router作为默认的路由配置。

      $stateProvider
        .state('crafts', {
          url: "/crafts",
          abstract: true,
          templateUrl: "views/crafts.html",
          controller: 'MainController'
        })
        .state('crafts.introduction', {
          url: "/introduction",
          views: {
            'craftsContent': {
              templateUrl: "views/introduction.html",
              controller: 'IntroductionController'
            }
          }
        })
       .state('crafts.orders.details', {
          url: "/:id",
          views: {
            'craftsContent@crafts': {
              templateUrl: "views/orderDetails.html",
              controller: 'OrderDetailsController'
            }
          }
        })
        .state('home', {
          url: "/",
          templateUrl: "views/main.html",
          controller: "MainController"
        });
* one application multiple routers
   直白的讲就是把应用划分路由,比如上述代码把应用或分为tachi部分和introduction部分，表现在URL上为 http://localhost:9000/#/crafts 、http://localhost:9000/#/home  ，而且crafts部分又继续细分
   我home页面仅仅是展示一些信息，引导注册和登录，菜单栏上只有少部分内容
   而crafts想到于用户登录之后的整个功能应用，菜单栏齐全
   按照功能区分路由，crafts下面是功能部分个小小feature的路由
* abstract router
   把crafts设置为abstract可以把一些公用的数据放入controller控制,比如菜单栏点击事件（国际化切换中英文 $rootscope的事件监听）
* parameter
   可以URL传值 也可以直接采用state发送自定义数据进行router间传递，state parameter可以取得
* 如果子路由和父路由采用同一个view 

        views: {
            'craftsContent@crafts': {
              templateUrl: "views/orderDetails.html",
              controller: 'OrderDetailsController'
            }
    注意content路径的配置 上述代码为使用views配置下的绝对路径