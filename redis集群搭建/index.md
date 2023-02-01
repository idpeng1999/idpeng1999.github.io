# Redis集群搭建


## windows下redis的集群搭建（三主三从）

**总体概述**

1. Redis （安装Redis并运行3个实例,Redis集群需要至少3个以上节点，低于3个无法创建)
2. Ruby语言运行环境
3. Redis的Ruby驱动redis-xxxx.gem
4. 创建Redis集群的工具redis-trib.rb
5. 所需要的安装包(`/zip/Redis集群搭建/redis.zip`)

### 1.下载redis对应版本（版本必需要3.0以上）

* 下载地址: [https://github.com/MSOpenTech/redis/releases](https://github.com/MSOpenTech/redis/releases)
* 下载`Redis-x64-3.0.504.zip`

### 2.下载并安装Ruby语言环境

* 下载地址: [https://rubyinstaller.org/downloads/](https://rubyinstaller.org/downloads/)
* 安装`rubyinstaller-devkit-3.0.4-1-x64.exe`(未上传)

![下载](/img/Redis集群搭建/5.png)


### 3.下载ruby环境下Redis的驱动

* 下载地址: [https://rubygems.org/gems/redis/versions/3.2.2](https://rubygems.org/gems/redis/versions/3.2.2)
* 下载`redis-3.2.2.gem`
* 将下载的Redis驱动文件`redis-3.3.2.gem`复制到Ruby安装目录下
* 打开CMD，运行如下命令: `gem install --local D:\java\Ruby30-x64\redis-3.2.2.gem`
* 成功截图，表示Redis驱动已安装成功

![成功截图](/img/Redis集群搭建/1.png)

### 4. 在redis下创建模拟的主从服务器

* 在redis目录下建立8003-8008六个文件夹
* 将redis下面的文件复制到8003-8008节点文件夹下面
* 修改6个文件夹下`redis.windows.conf`文件配置

![配置1](/img/Redis集群搭建/3.png)
![配置2](/img/Redis集群搭建/4.png)


### 5.下载redis-trib.rb(redis集群管理工具)  

* 下载地址：[https://github.com/beebol/redis-trib.rb](https://github.com/beebol/redis-trib.rb)
* 下载`redis-trib.rb`
* 放在该位置

![位置](/img/Redis集群搭建/2.png)

### 6.运行redis集群

* 依次进入8003-8008六个文件夹
* 运行命令`redis-server.exe redis.windows.conf`
* 启动完成后，到`redis-trib.rb`所在目录
* 运行命令`redis-trib.rb create --replicas 1 127.0.0.1:8003 127.0.0.1:8004 127.0.0.1:8005 127.0.0.1:8006 127.0.0.1:8007 127.0.0.1:8008`
* 三主三从集群搭建成功！
---
* `dump.rdb`是由Redis服务器自动生成的 
* 默认情况下,每隔一段时间redis服务器程序会自动对数据库做一次遍历,把内存快照写在一个叫做`dump.rdb`的文件里,这个持久化机制叫做SNAPSHOT。
* 有了SNAPSHOT后,如果服务器宕机,重新启动redis服务器程序时redis会自动加载dump.rdb,将数据库状态恢复到上一次做SNAPSHOT时的状态。

### windows下redis集群搭建出现的问题

1. 异常:`Node 127.0.0.1:8003 is not empty. Either the node already knows other nodes (check with CLUSTER NODES) or contains some key in database 0.`

* 问题描述：导致异常的主要原因是该节点中默认生成的配置或历史存储数据不一致导致的。 
* 可能出现的错误原因：这里由于在构建集群时，先启动了8003-8008节点的，之后启动了redis服务，启动先后顺序发生了问题，导致Node xxx is not empty 
* 解决方法: `清除对应节点的dump.rdb、nodes.conf文件，重启之后即可`
* 必要的情况下进入redis执行`flushdb`

2. 创建redis集群报错：`ERR Slot 0 is already busy (Redis::CommandError)`

* 问题描述: slot插槽被占用了、这是因为 搭建集群前时，以前redis的旧数据和配置信息没有清理干净。
* 解决方法: 用redis-cli 登录到每个节点执行  `flushall`  和 `cluster reset`  

```shell

./redis-cli -h IP -p 端口

flushall

cluster reset

```








