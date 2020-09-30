---
layout:     post                    # 使用的布局（不需要改）
title:      Linux和maven面试题              # 标题 
subtitle:   doDispatcher,HandlerInterceptor #副标题
date:       2019-11-29              # 时间
author:     cl                      # 作者
header-img: img/post-bg-unix-linux.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - 技术
---

## Linux
1、Linux查看进程并杀死进程
	ps -aux|grep  进程关键字符串  ： 进程id+  进程的cpu 内存 占用 情况 
	ps -ef                                     :   进程和父进程id
	kill -9   进程id	

2、Linux连接超时处理方案
	网络不通：   ping  linux服务器的外网ip
	防火墙可能拦截端口号了
	有些软件启动时是使用保护模式启动

3、Linux中查看日志最后1000行
	tail   -n 1000  文件名

     Linux中查看日志最后200行并跟随查看

	tail -f -n 200  文件名  

4、Linux中端口号被占用如何解决
	netstat  -anp  |grep  8080
	kill -9  进程id

5、Linux中查看文本文件内容的方式
	cat   查看轻量级文件
	more   查看大文件
	less
	tail

	vim

6、Linux关闭防火墙并设置取消开机自启
6	service  firewalld   stop
	=== chkconfig  --level  3  firewalld  off 
7  	systemctl  stop firewalld
	systemctl  disable  firewalld

## redis
7、redis五大数据类型
	String   
	list   双向链表   有序 可以重复
	set	value值为空的  hash ， 无序不可重复
	zset	value值绑定分数的hash ， 按照分数有序 ，不可重复
	hash	类似于java的  Map<String,Object>

8、redis持久化方式+优缺点
	rdb:
		优点：  以压缩的形式  将快照集合保存到 rdb文件中，恢复时可以快速加载到内存中，性能高
			主从和集群  使用了 rdb的方式  将主机的数据同步给从机
		缺点：  按照一定的周期保存一次，最后一次保存后的数据可能丢失

	aof：
		优点：  以日志的形式 保存所有的写指令，可以防止误操作[可读]
		缺点：  占用磁盘多，每次还原数据时，redis会将文件的所有命令都再执行一遍  性能差
	同时启用rdb和aof时， redis优先选择使用aof中的数据

9、redis数据淘汰策略
	- redis在使用时，一般设置redis使用的内容为  服务器内存的 2/3

	在redis.conf的566行，maxmemory <bytes>  用来设置redis最大使用的内存容量
	- 如果以后redis中存储的数据内存容量到达设置的maxmemory 的值
	571 # volatile-lru -> Evict using approximated LRU among the keys with an expire set.
		将所有的有过期时间的数据  使用lru的算法进行移除[lru  最近最少使用策略   ]
 	572 # allkeys-lru -> Evict any key using approximated LRU.
		所有的数据  使用lru的算法进行移除
 	573 # volatile-lfu -> Evict using approximated LFU among the keys with an expire set.
 	574 # allkeys-lfu -> Evict any key using approximated LFU.
	 575 # volatile-random -> Remove a random key among the ones with an expire set.
		有过期时间的数据  随机移除一个
	 576 # allkeys-random -> Remove a random key, any key.
		所有数据  随机移除一个
 	577 # volatile-ttl -> Remove the key with the nearest expire time (minor TTL)
		有过期时间的数据  根据过期时间移除[ 将离过期时间最近的数据移除]
 	578 # noeviction -> Don't evict anything, just return an error on write operations.
		不移除数据，有写操作时返回错误[仍然可以读]     默认策略
		-  一般使用noeviction 策略
		-  可以对redis扩容
		- 如果需要修改策略：推荐使用volatile-ttl 或者volatile-lru
	 maxmemory-samples 5   ： lru或其他的算法  移除数据时  使用的样本数量 ， 最大值10，但是消耗cpu性能过大，最小值3，虽然性能高但是样本不够精确
				推荐使用redis默认的5


	LRU  和  FIFO： first in  first out

	redis的淘汰策略：mysql有200w条记录，如何保证redis中存储的20w条记录是热门数据
		使用allkeys-lru算法：   会将最近最少使用的数据移除掉，保留下的就是最近有人访问过的数据 
		 
10、redis和memcached的区别
	redis:  一般不支持win系统
		支持持久化
		单线程+IO多路复用

	memcached
		不支持持久化
		多线程+串行+锁

11、redis事务、mysql事务区别
	redis事务：  将多个命令组队  让redis执行，不能被其他命令打断
		多个命令之间没有影响， redis每个命令都是原子性的
	mysql事务：一组sql增删改操作，要么一起成功要么一起失败

12、如何使用redis做异步消息队列？
	List  
		lpush:从左边推入一个元素
		rpop:从右边弹出一个元素

13、redis使用场景有哪些
	热门数据存储  set/List
	排行榜  zset  	
	String  二进制安全 512m  [缩略图]、计数器(incr  decr)、验证码、需要设置过期时间的数据
	对象  hash    key-> Map<String,String>



14、如何理解redis 的 槽
	slots:
		redis集群会启动多台redis服务器，每台服务器的内存都是自己的独立内存，不能通过物理手段将其合并
		只能使用逻辑分区的方式  对多个服务器的内存进行分区处理
	
	将redis集群的每台服务器的内存 进行分区，每个存储数据的位置成为插槽
	以后存储数据时，会使用CRC16对key进行计算  并对16384取余 ， 得到一个0~16383之间的数字  ，就可以找到保存本数据的slots

## maven

15、maven的坐标
	g  :  一般是公司域名倒叙+  项目名
	a  ： 一般是项目的模块名
	v  ： 一般是模块的版本号

	maven根据坐标管理maven项目、maven的插件、第三方jar包
	-  在仓库中使用  gav拼接路径 和jar包名称去查找项目
16、多模块项目，如何管理项目的依赖？
	如果每个模块都需要使用的依赖，父工程中可以使用 依赖于 dependencies
	如果只有一部分模块需要使用、另外一部分模块不需要使用依赖，父工程可以使用依赖管理  dependenciesmanagement
			- 子工程不会自动依赖，还需要在子工程pom文件中使用dependencies 引入依赖
			- 父工程主要管理jar包版本


17、Maven依赖原则，如何解决jar包冲突
	先声明者优先
	路径短者优先
	
	- maven自动解决冲突时，如果依赖的jar包我们不想使用
		解决：
			方案1.  可以使用依赖排除   将指定的jar包排除掉
			方案2. 可以在当前项目中  手动引入自己希望使用的  jar包的版本 
	

18、maven生命周期
	clean生命周期：
		clean
	site生命周期[ 生成测试的网页  ]:

	defalut生命周期:  每个命令执行时  它前面的命令会自动执行一遍
		compile
		test-compile
		test
		package
		install

19、maven仓库
	本地库
	远程仓库：
		私服
		中央仓库镜像：阿里云仓库镜像
		中央仓库

	maven项目依赖jar包的顺序：
		- 根据gav 先从当前项目工作空间查找
		- 如果没有  去本地仓库查找
		- 如果没有 再去远程仓库查找

