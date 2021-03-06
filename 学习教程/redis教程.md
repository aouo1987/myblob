[TOC]

----

### _redis的安装_

####  _redis在linux下安装_

```shell
下载源码，解压缩后编译源码。
$ wget http://download.redis.io/releases/redis-2.8.3.tar.gz
$ tar xzf redis-2.8.3.tar.gz
$ cd redis-2.8.3
$ make
$ make install

当安装完redis之后，就需要进行测试，以下简单做一个测试来验证我们的redis是否安装成功。
b.首先我们启动redis服务，启动和关闭redis服务命令如下：
b.1  src/redis-server       启动redis，加上&表示使redis以后台程序方式运行
b.2 redis-server redis.conf  启动redis
b.3  src/redis-cli shutdown     关闭redis
```

#### _redis服务的启动和关闭_

```shell
按照service的形式来启动redis服务
执行命令 vim /etc/init.d/redis 创建脚本文件
# Date 2015-12-10
# chkconfig: 2345 10 90  
# description: Start and Stop redis   
REDISPORT=6379
EXEC=/root/redis/redis-stable/src/redis-server
REDIS_CLI=/root/redis/redis-stable/src/redis-cli
PIDFILE=/var/run/redis.pid
CONF="/root/redis/redis-stable/redis.conf"
case "$1" in   
        start)   
                if [ -f $PIDFILE ]   
                then   
                        echo "$PIDFILE exists, process is already running or crashed."  
                else  
                        echo "Starting Redis server..."  
                        $EXEC $CONF   
                fi   
                if [ "$?"="0" ]   
                then   
                        echo "Redis is running..."  
                fi   
                ;;   
        stop)   
                if [-f $PIDFILE ]   
                then   
                        echo "$PIDFILE exists, process is not running."  
                else  
                        PID=$(cat $PIDFILE)   
                        echo "Stopping..."  
                       $REDIS_CLI -p $REDISPORT  SHUTDOWN    
                        sleep 2  
                       while [ -x $PIDFILE ]   
                       do  
                                echo "Waiting for Redis to shutdown..."  
                               sleep 1  
                        done   
                        echo "Redis stopped"  
                fi   
                ;;   
        restart|force-reload)   
                ${0} stop   
                ${0} start   
                ;;   
        *)   
               echo "Usage: /etc/init.d/redis {start|stop|restart|force-reload}" >&2  
                exit 1  
esac

> 给文件添加权限,使得脚本可以执行 chmod 755 /etc/init.d/redis
> 以上工作顺利完成并且没有报错,则配置完成
  > service redis start 开启redis服务
  > service redis stop 关闭redis服务
[root@ssgao1987 ~]# service redis start
/etc/init.d/redis: line 1: te: command not found
Starting Redis server...
1601:C 09 Sep 14:14:41.944 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
1601:C 09 Sep 14:14:41.944 # Redis version=4.0.1, bits=64, commit=00000000, modified=0, pid=1601, just started
1601:C 09 Sep 14:14:41.944 # Configuration loaded
Redis is running...
[root@ssgao1987 ~]# service redis stop
/etc/init.d/redis: line 1: te: command not found
/etc/init.d/redis: line 26: [-f: command not found
cat: /var/run/redis.pid: No such file or directory
Stopping...
Redis stopped
[root@ssgao1987 ~]#   


'配置环境变量'
export REDIS_HOME=/root/redis/redis-stable
export PATH=$PATH:$JAVA_HOME/bin:$ZOOKEEPER/bin:$ACTIVEMQ_HOME/bin:$REDIS_HOME/src
[root@ssgao1987 src]# redis-cli 
127.0.0.1:6379> 
127.0.0.1:6379> keys
```



### _redis的数据类型_

##### _zset的介绍_

```shell

	Sorted set是set的一个升级版本,它在set的基础上增加了一个顺序属性,这个属性在添加修改元素时候可以指定,每次指定后,zset会自动重新按新的值进行调整顺序.
可以理解为有两列字段的数据表,一类存value, 一列存顺序编号.操作中key理解为zset的名字.
zadd 向名称为key的zset中添加元素member,score用于排序.如果该元素存在,则更新其顺序。(用法: zadd有序集合 顺序编号 元素值)


zadd 添加元素,格式是zadd zset的key score值项的值,Score和项可以使多对
		score可以是整数,也可以使浮点数,还可以是+inf表示无穷大 -inf 表示负无穷小
zrem 删除元素,格式是zrem zset的key项的值,项的值可以是多个
zcard 获取集合中元素的格式,格式是:zcard zset的key
zcount 获取分数区间内元素个数,格式是zcount zset的key起始score 终止score
zscore 获取元素的分数,格式是 zscore zset的key项的值,返回项在zset中的score
zrangebyscore  获取分数区间内的元素,格式是zrangebyscore zset的key 起始score 终止score(withscore),默认是包含端点值,如果加上"("表示不包含,后面还可以加上limit来限制.


127.0.0.1:6379> zadd ssgao_zset 1 ssgao
(integer) 1
127.0.0.1:6379> zadd ssgao_zset 2 ssgao_2
(integer) 1
127.0.0.1:6379> 
127.0.0.1:6379> zrange ssgao_zset 0 -1
1) "ssgao"
2) "ssgao_2"


127.0.0.1:6379> zcard ssgao_zset   (zcard 返回集合中元素个数)
(integer) 2
127.0.0.1:6379> zcount ssgao_zset 0 -1  (zcount: 返回集合中score在给定区间的数量)
(integer) 0
127.0.0.1:6379> zcount ssgao_zset 0 1
(integer) 1


zcard 	 查看zset集合中成员的个数
127.0.0.1:6379> zcard ssgao_zset
(integer) 5
zcount   获取zset集合中指定分支之间存在的成员个数
127.0.0.1:6379> zcount ssgao_zset
(error) ERR wrong number of arguments for 'zcount' command
127.0.0.1:6379> 
127.0.0.1:6379> zcount ssgao_zset 2 5  ---数值表示分数的意思
(integer) 0

```



##### _bitmap的介绍_

> _BitMap bitmap是一串连续的2进制数字 ( 0或1）,每一位所在的位置偏移为偏移offset_

```shell
Redis从2.0开始新增了setbit,gitbit,bitcount等bitmap的相关命令。虽然是新命令,但是并没有增加新的数据类型,因为setbit等命令只不过是在set上的扩展。在bitmap上可以执行and,or,xor以及其他位操作。


setbit命令介绍
	setbit key offset value   --命令的复杂度为o(1) offset是偏移量,从0开始,从左向右
	设置或清空key的value(字符串)在offset处的bit值(只能是0或者1)
getbit命令介绍
	getbit获取某一位上的值
	getbit key offset 
bitpos命令介绍
	bitpos key bit [start] [end]
	返回指定值0或1在指定区间上第一次出现的位置
	127.0.0.1:6379> SET mykey "\x00\xff\xf0"
		OK
	127.0.0.1:6379> BITPOS mykey 1 0
		(integer) 8
	127.0.0.1:6379> BITPOS mykey 1 2  ### 表示从第2个字节开始算
		(integer) 16

```

***空间占用以及第一次分配空间需要的时间***

```xml
offset为2^32-1分配(512MB) 需要使用300ms
offset为2^30-1分配(128MB) 需要大约80ms
offset为2^28-1分配(32)MB 需要大约30ms
offset为2^26-1分配(8)MB 需要大约8ms
offset和分配的空间计算公式: ($offset/8/1024/1024)MB
```



###### _bitmap使用案例_

```shell
redis使用get命令读取bitmap类型的数据
	在签到统计场景中,可以使用bitmap数据类型高效的存储签到数据,但getbit命令只能获取某一位值,就无法最优的满足部分业务场景了
	比如我们按年去存储一个用户的签到情况,365天,只需要 365/8 
```



















































































