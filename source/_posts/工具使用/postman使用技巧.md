---
title: postman使用技巧
date: 2019-01-11 14:13:35
tags:
  - 工具技巧
categories: 
  - 工具使用
---

# Postman是什么

Postman是chrome的一款插件,用于做接口请求测试,无论是前端,后台还是测试人员,都可以用postman来测试接口,用起来非常方便。<!-- more -->

# Postman安装

## 官网下载(翻墙)

[https://www.getpostman.com/downloads/](https://www.getpostman.com/downloads/)

## 蓝奏云

[https://www.lanzous.com/i2en5xc](https://www.lanzous.com/i2en5xc)

# Postman常用功能

安装好之后，我们先打开Postman，可以看到界面分成左右两个部分，右边是我们后头要讲的collection，左边是现在要讲的request builder。在request builder中，我们可以通过Postman快速的随意组装出我们希望的request。一般来说，所有的HTTP Request都分成4个部分，URL, method, headers和body。而Postman针对这几部分都有针对性的工具。

![](https://i.imgur.com/JHqfDbU.png)

## URL

要组装一条Request, URL永远是你首先要填的内容，在Postman里面你曾输入过的URL是可以通过下拉自动补全的哦。如果你点击Params按钮，Postman会弹出一个键值编辑器，你可以在哪里输入URL的Parameter，Postman会帮你自动加入到URL当中，反之，如果你的URL当中已经有了参数，那Postman会在你打开键值编辑器的时候把参数自动载入

![](https://i.imgur.com/OyiHdls.png)

## Headers

点击’Headers’按钮，Postman同样会弹出一个键值编辑器。在这里，你可以随意添加你想要的Header attribute，同样Postman为我们通过了很贴心的auto-complete功能，敲入一个字母，你可以从下拉菜单里选择你想要的标准atrribute

![](https://i.imgur.com/N0KHlcZ.png)

## Method

要选择Request的Method是很简单的，Postman支持所有的Method，而一旦你选择了Method，Postman的request body编辑器会根据的你选择，自动的发生改变。

![](https://i.imgur.com/vJl711X.png)

## Request Body

如果我们要创建的request是类似于POST，那我们就需要编辑Request Body，Postman根据body type的不同，提供了4中编辑方式：

- form-data
- x-www-form-urlencoded
- raw
- binary

![](https://i.imgur.com/dgaY012.png)

（我们这里是可以传文件的哦）

# postman高级用法

## colletions(接口集合)

在开发过程中，可能会遇到多项目同时开发维护的情况，Postman友好的提供了colletions功能，类似与项目文件夹一样，可以把归属于同一类的接口分类到一起，便于管理维护。

1. 点击NEW -> 选择collection，创建一个项目空间。

![](https://i.imgur.com/RcYq3Ba.png)

2. 输入项目名称，点击create。

![](https://i.imgur.com/zGFYC1F.png)

## colletions folder(集合中的文件夹)

每个项目会有多个接口，有些是一类功能，例如，用户管理接口，文章列表接口，Postman提供folder目录来进行细致的分类。

1. 选择一个项目，点击Add Folder

![](https://i.imgur.com/v2qY1uW.png)

2. 输入目录名称，点击create

![](https://i.imgur.com/exymkyT.png)

每个接口都可以归类到某个项目，或某个项目的子目录中。

![](https://i.imgur.com/pRltLVX.png)

## Environment(环境变量)

Postman允许定义自己的环境变量（Environment），最常见的是将测试 URL 进行定义成变量的形式，这样随着你的域名怎么变，URL 就不用变更，非常方便。除此之外，也可以将一些敏感的测试值定义为环境变量，比如密码。接下来，来看下怎么新建一组环境变量，如下操作打开环境变量的管理入口：

![](https://i.imgur.com/G8b6kTl.png)

打开管理环境变量的窗口，输入名称，添加一组键值对，如下图所示：

![](https://i.imgur.com/5oJhjWe.png)

环境变量要以双大括号的方式来引用，可以在右上方下拉框处选择相应的环境变量，我们实测一下刚才添加的Url的变量：

![](https://i.imgur.com/FAc6qME.png)

## 通过脚本设置变量

Postman允许用户自定义脚本，并提供了两种类型的脚本：
- Pre-request Script：执行request请求前先运行，可以在里面预先设置些所需变量
- Tests：request返回后执行的,可以对返回信息进行提取过滤，或者执行一些验证操作

### 例子

获取如下返回信息中的user_id值:

```json
// 假设服务端返回的Body内容如下:
{
  "token": {
    "user_id": "2079876912",
    "access_token": "26A90E317DBC1AD363B2E2CE53F76F2DD85CB172DF7D813099477BAACB69DC49C794BAECEDC68331",
    "expires_at": "2016-06-22T12:46:51.637+0800",
    "refresh_token": "26A90E317DBC1AD3CD1556CF2B3923DD60AEBADDCBC1D9D899262A55D15273F735E407A6BEC56B84",
    "mac_key": "4FAhd4OpfC",
    "mac_algorithm": "hmac-sha-256",
    "server_time": "2016-06-15T12:46:51.649+0800"
  }
}
```

在Tests中对user_id值进行提取并赋值成全局变量:

```bash
// 判断是否存在'user_id'值
tests["Body contains user_id"] = responseBody.has("user_id");
if(tests["Body contains user_id"]){
    // 将返回信息解析成对象
    var responseData = JSON.parse(responseBody);
    tests["value_user_id"]=responseData.token.user_id
    // 设置全局变量
    postman.setGlobalVariable("user_id",tests["value_user_id"]);
}else{
    postman.setGlobalVariable("user_id","默认user_id");
}
```

# 实践案例

## 项目接口分类管理

![](https://i.imgur.com/LXxBYdq.png)

## 登录获取token并设置为全局变量

![](https://i.imgur.com/gIXLd5c.png)

## 接口使用登录后的token

![](https://i.imgur.com/1RCOliR.png)

