---
title: remote debug principle analysis
date: 2018-06-01 16:32:56
tags:
---
    Java远程调试的原理是两个VM之间通过debug协议进行通信，然后以达到远程调试的目的。两者之间可以通过socket进行通信。
    
    首先被debug程序的虚拟机在启动时要开启debug模式，启动debug监听程序。
    
    jdwp是Java Debug Wire Protocol的缩写。
    
    java -Xdebug -Xrunjdwp:server=y,transport=dt_socket,address=8000,suspend=n zhc_application
    
    这是jdk1.7版本之前的方法，1.7之后可以这样用：
    
    java -agentlib:jdwp=transport=dt_socket,address=8000,server=y,suspend=n my.jar
    
    my.jar 是main程序，server=y表示是监听其他debugclient端的请求。address=8000表示监听端口是8000
    
    suspend表示是否在调试客户端建立连接之后启动 VM。如果为y，那么当前的VM就是suspend直到有debug client连接进来才开始执行程序。
    
    如果你的程序不是服务器监听模式并且很快就执行完毕的，那么可以选择在y来阻塞它的启动。