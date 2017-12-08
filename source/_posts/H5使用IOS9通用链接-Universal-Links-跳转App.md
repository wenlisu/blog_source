title: H5使用IOS9通用链接(Universal Links)跳转App
author: LS
categories: 手册
date: 2017-12-08 10:12:27
tags:
---
前言
=========

最近接到产品提的一个需求就是要求用户点击下载页时判断用户手机是否安装了App，安装的话打开App，没有安装则打开对于下载地址。

## 使用meta

通过在head标签内定义meta，这个标签是告诉iphone的safari浏览器，这个网站对应的app是什么，然后在页面上面显示一个下载banner。

```
<meta name='apple-itunes-app' content='app-id=yourAppId'>
```
![](http://osph37mc5.bkt.clouddn.com/0601.PNG)

## 使用iframe

这种方法不会引起页面可见的变化（例如页面内容变成一个新页面），不会导致浏览器历史记录的变化，不过只在IOS9以下才生效。

```
if (dom.isIos) {
    if(Boolean(navigator.userAgent.match(/OS [3-8]_\d[_\d]* like Mac OS X/i))){
        var ifr = document.createElement('iframe');
        ifr.src = urls.iosScheme;
        ifr.style.display = 'none';
        document.body.appendChild(ifr);
        var openTime = +new Date();
        window.setTimeout(function(){
            document.body.removeChild(ifr);
            if( (+new Date()) - openTime > 2500 ){
                window.location = urls.ios;
            }
        },2000);
    return;
}
```

## 直跳方式:window.location.href

通过直接跳转iOS/Android APP配置的URL scheme。

```
window.location.href = "URL scheme://[host]/[path]?[query];
```

- scheme：判别启动的App。 - 必填项
- host：适当记述。- 必填项
- path：传值时必须的key。 - 非必填项
- query：获取值的Key和Value。 - 非必填项

## 通用链接（universal link）

因为微信将唤起本地APP的接口给禁了，所以微信中是不能直接唤起APP的，一般做法是提示用户在浏览器中打开，之后的流程还是我们上面讲的内容。
但是，在iOS9中，这个限制是可以突破的，也就是说可以直接唤起APP。方法就是使用我们上文提到的universal link。

要满足universal link有以下几点前提条件：

- 支持https的服务器
- 服务器绑定域名
- 在服务器根目录上传apple-app-site-association文件（效果需要满足访问https:activities.domain.com/apple-app-site-association 能够下载或者打开json内容）
- 至少 iOS 9 beta 2 版本 [下载]，这很重要， 因为如果是之前的测试版本你需要做额外的操作。
- 至少 Xcode 7 beta 2 [下载]

##### apple-app-site-association

apple-app-site-association是没有任何后缀名，大小写敏感，上传到需要识别domain服务器根目录下，

```
{
    "applinks": {
        "apps": [],
        "details": [
            {
                "appID": "teamID.bundleId",
                "paths": [ "*", "NOT /videos/2017/*", ]
            },
            {
                "appID": "*******.com.queqianme.speed.app",
                "paths": [ "/iosSpeed/deaplink/*" ]
            },
             {
                "appID": "*******.com.queqianme.app",
                "paths": [ "/ios/deaplink/*" ]
            }
        ]
    }
}
```

appID: 格式为 teamID.bundleId形式。
paths: 实际上就是限制哪些路径可以唤醒app，哪些路径不能唤醒app。
你可能想知道appID从何而来， 基本上， 它是加入了你的团队标识的 bundle id。你可以从你的 [苹果开发账号页面](https://link.jianshu.com?t=https://developer.apple.com/cn/)，找到Membership选项卡，获取你的团队标识:

![](http://osph37mc5.bkt.clouddn.com/0603.png)

也可以使用[苹果的验证网站](https://search.developer.apple.com/appsearch-validation-tool/)，验证文件是否能被苹果请求到。如果是未上线的应用，使用验证网站时可能出现如下提示。

![](http://osph37mc5.bkt.clouddn.com/0604.png)

- 出现该提示为apple-app-site-association文件配置正确。
- 出现404错误码提示，则为apple-app-site-association文件未上传成功，或者使用https://yourdomain.com/apple-app-site-association路径无法访问。

不过即使报错了，还是不影响跳转的功能，最好配置后试试看能否跳转。

##### 项目配置

在项目的Capablities中开启Associated domains。注意domains可以添加多个，前缀必须为applinks:，applinks:后为你的需要识别服务器的域名。

![](http://osph37mc5.bkt.clouddn.com/0605.png)

##### 验证配置

快捷验证，在备忘录中输入https://activities.domain.com/apple-app-site-association，长按这个链接，出现下图提示则配置成功。

![](http://osph37mc5.bkt.clouddn.com/0606.png)