[参考文章](http://www.jb51.net/softs/541181.html)

>  redis windows是一个key-value存储系统，本站为大家提供的redis windows下载地址包含32位与64位，可根据自己的实际情况选择，都是windows环境的，Redis是一个key-value存储系统。Redis的出现，很大程度补偿了memcached这类key/value存储的不足，在部分场合可以对关系数据库起到很好的补充作用，有需要此款工具的朋友们欢迎前来下载使用。

#### 基本简介

Redis是一个key-value存储系统。和Memcached类似，它支持存储的value类型相对更多，包括string(字符串)、list(链表)、set(集合)、zset(sorted set --有序集合)和hash（哈希类型）。这些数据类型都支持push/pop、add/remove及取交集并集和差集及更丰富的操作，而且这些操作都是原子性的。在此基础上，redis支持各种不同方式的排序。与memcached一样，为了保证效率，数据都是缓存在内存中。区别的是redis会周期性的把更新的数据写入磁盘或者把修改操作写入追加的记录文件，并且在此基础上实现了master-slave。

#### 使用方法

下载完成后 可以右键解压到 某个硬盘下 比如D:\Redis\redis-2.6。
在D:\Redis\redis-2.6\bin\release下 有两个zip包 一个32位一个64位。
根据自己windows的位数 解压到D:\Redis\redis-2.6 根目录下

![](http://files.jb51.net/file_images/article/201703/2017031517001091.jpg)

进入redis目录后 开启服务  （注意加上redis.conf）

redis windows(含32/64位)

这个窗口要保持开启  关闭时redis服务会自动关闭
redis会自动保存数据到硬盘 所以图中是我第二次开启时 多了一个 DB loaded from disk

![](http://files.jb51.net/file_images/article/201703/2017031517001093.jpg)

截图
![](http://files.jb51.net/file_images/article/201703/2017031517001090.jpg)


win下bat启动

```
=============================

rem Start Redis

@echo off
echo -------------- Start Redis --------------
::redis的安装顶层目录

D:

::设置显示文字颜色

color 0a

::修改当前目录到redis下

cd D:/redis

::启动server

redis-server.exe redis.conf

=============================
```
