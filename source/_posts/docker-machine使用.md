---
title: docker-machine使用
date: 2018-03-02 15:10:30
tags:
---

        docker-machine ls       #查看单前存在的机器
        
        docker-machine create --driver=virtualbox default    #创建一个机器
        
        docker-machine env default    #获取default虚拟机的环境变量
        
        docker-machine env default|Invoke-Expression  #将powershell与linux建立联接
        
        docker-machine start default      #启动default虚拟机
        
        docker-machine stop default       #停止default虚拟机
        
        ssh登录docker-machine的用户名 docker passwd tcuser
        
创建虚拟机并指定挂载到哪个盘符
        
        1,打开gitbash
        2,cd ~  切换到家目录
        3，vim .bash_profile  创建文件 并写入export MACHINE_STORAGE_PATH='D:\docker'
        4, 在上面的路径下建一个cache的文件夹，将toolbox安装文件下的boot2docker.iso拷贝到该文件夹：
        5,设置远程仓库 
        docker-machine -s "H:\docker" create --engine-registry-mirror=https://vf29u5xi.mirror.aliyuncs.com -d virtualbox default
        
