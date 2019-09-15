---
layout:     post
title:      "Justnote API dev log"
subtitle:   " \"Just build it\""
date:       2019-09-15 14:04:00
author:     "Jinbo"
header-img: "img/in-post/justnote/road1.jpg"
catalog: true
tags:
    - Dev
    - Justnote
---

果不其然，一拖两个月过去了😪。不过好在是API开发算是有了些进展，在此记录一下遇到的坑和收获。

首先，我的源码地址：[https://github.com/krist-jin/Justnote/tree/master/api](https://github.com/krist-jin/Justnote/tree/master/api)

如上篇所说，API的开发用了MERN的Stack。过程中主要参考了这篇文章[building REST API with Nodejs/MongoDB/Passport/JWT](https://medium.com/@kris101/building-rest-api-in-nodejs-mongodb-passport-jwt-6c557332d4ca)。作者Kris写的十分详细，而且每个阶段都有提供源码，推荐一看。

虽然如此，Kris的项目和我的需求还是有一些区别的，而且据他上次更新代码已经过去两年半，有些用法和package难免过时。对我来说有一点区别很重要，就是我个人倾向于用Typescript instead of JavaScript。类型的引入会极大的提高我的开发效率。还有就是Kris用到了很多第三方的packages，而我倾向于尽量精简项目，所以我移除了很多非必需的packages，能放在devDependencies的就不放在dependencies里面。

## typescript和webpack的配置
请参考`package.json`的`scripts`部分，和`tsconfig.json`，以及`webpack.config.js`

## Debugging in VSCode
大部分微软的软件产品的槽点多到我真的不想用（对我说的就是你们Lync/Skype/OneDrive/Outlook等等）。但是，不得不说VSCode是真的好用，简直好用到不像是微软出品🤣

如果想在VS Code里面对nodejs project进行debugging(设置断点什么的)，这里记录一种比较简单的方法：

在terminal运行node project，之后在VS Code的Debug Tab里面添加一个Configuration，选择Attach by Process ID，之后运行debugging然后选择正在运行的node project的process。对于Typescript的project注意要正确配置source map

## types-installer
为了让Typescript识别第三方Package的Class type，需要将每个type的dependency加入package.json，这个过程如果手动来加的有点麻烦，所以可以用这个`types-installer`来帮忙自动添加type的dependency。

用法：`types-installer install --toDev`

它会自动扫描package.json中的dev dependencies然后添加对应type

## dotenv
如果项目有一些环境变量需要配置，更重要的是，如果有一些credentials需要保存（例如数据库的用户名密码），其中一种办法就是将这些变量存在.env文件中然后让project在运行之前加载读取。但是值得注意的是，一定不能存到git中。

这里记录一个叫`dotenv`的第三方package用法，它可以帮助读取并加载.env文件中的变量。

首先在项目主目录创建.env文件，然后将.env加入.gitignore。之后将所需的变量定义在.env文件中（比如PORT=3000）。接下来，我们可以利用node的`-r`选项来预加载`dotenv`，这样一来，`dotenv`就不需要被加入`dependencies`了。做法是在package.json文件中，在对应的script用`node -r dotenv/config your_script.js`

在代码中需要访问变量时，可以通过`process.env.PORT`的方式获取。

## mlab.com
用mlab.com(MongoLab)可以免费host 512MB的Mongo数据库。

不过它最近被MongoDB收购并关闭了新用户注册的渠道，它推荐新用户可以用MongoDB Atlas服务。我看了一下，好像和mlab差不多，同样，512MB也是免费的。

## npx
可以用来直接运行node package

## Use Typescript with Mongoose
在用Mongoose的时候，取出的对象的type是Document。为了让它识别我们对model定义的fields，可以创建一个interface然后extends Document，将fields和methods的signature加入这个interface的定义。这样在拿到数据库对象的时候，我们可以给它对应的type。

> 暂时想到这么多，有机会再补充