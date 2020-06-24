# redis-sentinel-docker-compose

### 问题分析
最近在使用docker构建redis集群时，遇到了一个问题：基于docker所构建的redis集群在docker容器内是能够访问的，但是在容器外或者两个无关联的容器间，是无法访问的。经过查阅官方文档，发现了原来是redis与的docker网络模式不兼容的原因。

我们知道docker在运行容器时，容器内部有一个独立的网络（默认情况下），通过docker设置的虚拟网桥与外部通信。并且我们可以使用-p参数来映射docker容器的端口。docker使用的是一种类似端口映射的技术，这导致容器内外的ip和端口可能是不一样。而redis主从节点间通信时，是基于INFO命令的信息来自动发现从节点的ip和端口信息。当集群运行的容器里时，redis获取的信息的容器内部的ip和端口。
运行info 命令，可以看到节点的ip和端口是容器内部ip。

### 解决方案
官方文档中提供了三种解决方案,我使用的是主动配置的方案:redis官方文档中提供了两个参数:可以强制slave向master声明任意IP和端口
```java
replica-announce-ip 5.5.5.5
replica-announce-port 1234
```

### 说明
docker-compose.yml中的192.168.1.184换成自己外网的对应ip即可,默认的redis_pwd密码换成自己的密码即可.注意全局替换.其他的不要改动.在服务器上执行
```java
docker-compose up -d
```

### 参考文档
[主从配置文档](https://redis.io/topics/replication)

[哨兵配置文档](https://redis.io/topics/sentinel)

[集群配置文档](https://redis.io/topics/cluster-tutorial)