---
layout:     post
title:      Android Http
date:       2015-09-14 10:36:09
author:     HFF  
summary:    Android Http
categories: jekyll
thumbnail:  heart
tags:
 - Android
---



#####1、常用的请求方法有GET、HEAD、POST

#####2、传输类型由Content－Type标记

#####3、无连接，处理请求即断开连接。无状态，对于事务处理没有记忆能力，如需要之前的信息，要重传


####Android Http请求API主要分两种：
1、Java的HttpURLConnection，默认带gzip压缩

2、Apache的HttpClient，默认不带gzip压缩

HttpURLConnection默认开启了gzip压缩，2.3 之后建议使用HttpURLConnection。

[Android’s HTTP Clients](http://android-developers.blogspot.sg/2011/09/androids-http-clients.html)这里讲了Apache HTTP Client和HttpURLConnection

#####Which client is best?

Apache HTTP client has fewer bugs on Eclair and Froyo. It is the best choice for these releases.

For Gingerbread and better, HttpURLConnection is the best choice. Its simple API and small size makes it great fit for Android. Transparent compression and response caching reduce network use, improve speed and save battery. New applications should use HttpURLConnection; it is where we will be spending our energy going forward.

还可以用OKHttp，Volly，Android-Async-Http的开源包



        