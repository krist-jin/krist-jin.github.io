---
layout:     post
title:      "Justnote API dev log - JWT enhancement"
subtitle:   " \"Just build it\""
date:       2019-09-22 10:22:00
author:     "Jinbo"
header-img: "img/in-post/justnote/road2.jpg"
catalog: true
tags:
    - Dev
    - Justnote
---

前几天开始了UI的开发，第一关便是用户注册登录。

之前在API端决定用JWT w/o session，主要因为JWT将用户的身份信息签名之后存在了token之中，这样API端无需维护session表，从而更加scalable。

在client端，JWT一般存在local storage或者cookie中，但是由于local storage可以被同域名下其他脚本读取，所以存在安全隐患。很多人推荐的best practice是将JWT存在cookie中，并设置`httpOnly: true, secure: true`

另外一方面，由于JWT包含用户身份信息，是作为和API端的验证凭据，所以一旦被窃取（虽然https加密可以某种程度上可以防止这一点），就可能被利用来窃取或者篡改数据。而且即便及时发现了JWT被窃取，API端想要吊销也不是很容易（有人推荐在API端维护一个blacklist每次请求都要检查，但是感觉overhead有点大）

所以，为了尽量减轻JWT被窃取所带来的损失，一方面应该对用户的敏感数据/操作（比如银行转账或者交易之类的）进行二级密码保护，另外一方面我们应该对JWT的有效期进行合理设置。`Auth0`[推荐](https://auth0.com/blog/refresh-tokens-what-are-they-and-when-to-use-them)应采用`access token`和`refresh token`结合的策略。acess token用来访问API资源，但是有效期设置的相对较短（如15分钟）。在access token过期后，为避免要求频繁重新登录，可以用refresh token来获得一个新的access token。refresh token的有效期可以设置较长（如7天）。这里我想补充一点，我觉得应该对refresh token的refresh权限也设置一个有效期（比如30分钟），这样即使refresh token被窃，也不至于在较长时间内可以被用来任意请求新的access token。

说回Justnote，我在之前的API开发中有两个坑没填：

1. 我将JWT token放在了request/response的header中，这样一来我需要在client端手动维护。不如应该将token直接放在cookie中交给browser来帮我管理。

2. 之前没有设置JWT token的有效期。作为一款笔记应用，用户的使用大概是频率较低但是比较集中，比如想起了一些什么东西然后花10分钟记录下来，然后可能几个小时内都不用，之后又想起了什么东西然后又花10分钟记录下来。每当用户想记录时，笔记应用应该是处于随时可用的状态，如果每次都要重新登录的花感觉会比较蛋疼。在我写到这里的时候，其实我采用的方案是把JWT有效期设置成15分钟，在15分钟内的任意请求都会刷新JWT，这样的话只有用户活动是连续的，那么JWT就不会过期。但是写到这里我发现这和笔记应用的使用习惯不太符合，很有可能的结果是，用户需要频繁重新登录。经过一番调研，我觉得比较合适的方案是在登录时提供`remember me on this device`的选项，如果未勾选，那么cookie是session cookie，当用户关闭浏览器时session关闭cookie和JWT token就会消失。在这种情况下，我给JWT设置一个7天的有效期，7天后即使session未结束，用户也需重新登录。如果勾选了`remember me on this device`的选项，那么我会给cookie和JWT token一个30天的有效期，之后需要重新登录。