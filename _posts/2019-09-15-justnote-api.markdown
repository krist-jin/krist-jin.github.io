---
layout:     post
title:      "Justnote API dev log"
subtitle:   " \"Just build it\""
date:       2019-09-15 14:04:00
author:     "Jinbo"
header-img: "img/in-post/justnote/note-title-banner.jpg"
catalog: true
tags:
    - Dev
    - Justnote
---

果不其然，一拖两个月过去了😪。不过好在是API开发有了些进展，在此记录一下遇到的坑和收获。

首先，我的源码地址：https://github.com/krist-jin/Justnote/tree/master/api

如上篇所说，API的开发用了MERN的Stack。过程中主要参考了这篇文章[building REST API with Nodejs/MongoDB/Passport/JWT](https://medium.com/@kris101/building-rest-api-in-nodejs-mongodb-passport-jwt-6c557332d4ca)。作者Kris写的十分详细，而且每个阶段都有提供源码，推荐一看。

虽然如此，Kris的项目和我的需求还是有一些区别的，而且据他上次更新代码已经过去两年半，有些用法和package难免过时。对我来说有一点区别很重要，就是我个人倾向于用Typescript instead of JavaScript。类型的引入会极大的提高我的开发效率。还有就是Kris用到了很多第三方的packages，而我倾向于尽量将项目精简，所以我移除了很多非必需的packages。

