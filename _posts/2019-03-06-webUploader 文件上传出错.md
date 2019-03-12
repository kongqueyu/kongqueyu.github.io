---
layout: post
title: "webUploader 文件上传出错"
date: 2019-03-06
description: "webUploader 文件上传失败"
tag:
---

##事情起因:
        后台管理系统使用webUploader(0.1.5)进行大文件上传,上传成功率在百分之八十左右。一周左右需要被处理一次文件上传失败。
##事件背景:
    页面发送请求到nginx,然后转发至微服务。
##源码优化历史:
        2018/7/10 增加指数退避算法进行失败重传,
        配置参数 chunkRetry:7 分片上传 于网络问题出错，允许自动重传多少次
            (```)
            webuploader.js 增加代码
            var times = (Math.pow(2, block.retried) * 1000) + Math.floor(Math.random() * 1000);
            setTimeout(function(){
                tr.send();
            },times);
            (```)
        2019/02/01 增加异常捕获
            (```)
             var typeArr = type.split( '|' ), status, statusText;
             type = typeArr[0];
             status = parseFloat( typeArr[1] ),
             statusText = typeArr[2];
             owner.trigger( 'uploadError', file, type, status, statusText );

             var separator = '|', // 分隔符// 拼接的状态，在 widgets/upload.js 会有代码用到这个分隔符
             status = separator + xhr.status +
             separator + xhr.statusText;
             return me.trigger( 'error', 'server' + status );
             return me.trigger( 'error', me._status ? 'http' + status : 'abort' );
            (```)
##问题类型:
        1、Failed to load resource:net::ERR_SSL_VERSION_INTERFERENCE
        2、Failed to load resource:net::ERR_EMPTY_RESPONSE
        3、Failed to load resource:net::ERR_TIMED_OUT
        4、Failed to load resource:net::the server responded with a status of 502(Bad Gateway)
        5、net::ERR_INTERNET_DISCONNECTED
        6、插件(uploadError)捕获 timeout
        7、插件(uploadError)捕获 abort
        8、插件(uploadError)捕获 server
        9、Failed to load resource:the server responded with a status 500()
##问题分析:
      1、在文件上传过程中,停止微服务(当微服务发生异常时)
      出现 问题类型 4、5、8、9
      解决方式：指数退避算法已解决，请求会重试，待服务重启时可以正常上传。

      2、在文件上传过程中，停止nginx （nginx发生异常时同）
      出现 问题类型 2、7  上传出错停止止上传。
      解决方式 查看nginx

      3、网络发生错误：
      会出现3、6
      检查网络 或者上传国内服务器的需要检查host文件是否正常设置代理 参见子节目上传页面。

      4、问题类型 1 比较常见又很难复现
            在chrome 浏览器 地址栏中输入 chrome://flags/，页面搜索 TSL1.3 找到后修改为Disabled;原因是服务器不支持TSL导致.(由于本地不容易复现此问题，所以该答案没有经过真是验证)
      问题参考：
        https://www.thewindowsclub.com/err_ssl_version_interference
        https://www.zlmyweb.com/a/notes/jsl/2018/0608/23.html
##带着未解决的问题，了解下SSL TLS是什么？
##网上的概念如下：
SSL:(Secure Socket Layer, 安全套接字层)，位于可靠的面向连接的网络协议和应用层协议之间的一种协议层。SSL通过互相认证，使用数字签名确保完整性、使用加密确保私密性，以实现客户端和服务器之间的安全通讯。 该协议由两层组成：SSL记录协议和SSL握手协议。
TLS:(Transport Layer Security,安全传输层)，TLS 是建立在传输层TCP协议之上的协议，服务于应用层，它的前身是SSL,它实现了将应用层的报文进行加密后再交由TCP进行传输功能。
由概念来看，应该是服务器不支持该版本的安全协议。





