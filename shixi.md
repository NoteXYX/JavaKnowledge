# 一、字符串相关技巧
1、StringUtils
 - 判断字符串是否为空？
   &emsp;其中**空**是指字符串为null、空字符串或都是空白（空格等）
    &emsp;maven需要引入依赖：commons-lang3
```xml
		<dependency>
            <groupId>org.apache.commons</groupId>
            <artifactId>commons-lang3</artifactId>
            <version>3.7</version>
        </dependency>
```
```java
package com.hfut;
import org.apache.commons.lang3.StringUtils;
public class test {
    public static void main(String[] args) {
        String nullStr = null;
        String emptyStr = "";
        String spacesStr = "  ";
        String tabStr = "\t";
        String str = "123";
        System.out.println(StringUtils.isBlank(nullStr));	//true
        System.out.println(StringUtils.isBlank(emptyStr));	//true
        System.out.println(StringUtils.isBlank(spacesStr));	//true
        System.out.println(StringUtils.isBlank(tabStr));	//true
        System.out.println(StringUtils.isBlank(str));		//false
    }
}
```
 - 连接String、Integer、Long等Object

```java
import com.google.common.base.Joiner;
StringUtils.joinWith(",", myStr, myInteger, myLong)
List<Long> tag = Arrays.asList(1L,2L,3L);
String str = Joiner.on(",").join(tag);
```

2、Google Guava库
```xml
 	<dependency>
        <groupId>com.google.guava</groupId>
        <artifactId>guava</artifactId>
        <version>21.0</version>
    </dependency>
```
1）参数校验

```java
import com.google.common.base.Preconditions;
Preconditions.checkArgument(!CollectionUtils.isEmpty(positionList),"资源位置信息参数不能为空");
Preconditions.checkNotNull(userId, "获取用户开户信息请求参数用户Id不能为空");
```

# 二、数组、集合相关技巧
1、数组和List转换
要想把基本数据类型的数组转化为其包装类型的list，可以使用guava类库的工具方法，示例如下：
```java
int[] intArray = {1, 2, 3, 4};
List<Integer> list = Ints.asList(intArray);
```
转换后的list为定长list，不能添加和删除会报错。
2、判断集合是否为空
其中**空**指null或empty

```java
import org.springframework.util.CollectionUtils;
if (CollectionUtils.isEmpty(myList)) {
	return Collections.emptyList();
}
```
3、判断两个集合是否有交集

```java
CollectionUtils.containsAny(Collection<?> source, Collection<?> candidates)
```
4、Collectors.groupingBy
5、集合排序，先按...排后按...排

```java
myList.sort(Comparator.comparing(...)
			.thenComparing()
			.thenComparing())
```

# 三、Java 8新特性
1、stream使用
[Java 8 Stream](https://www.runoob.com/java/java8-streams.html)
[Java流（Stream）操作实例-筛选、映射、查找匹配](https://blog.csdn.net/zyhlwzy/article/details/78625136)
# 四、数据库相关
1、分页接口 pagehelper
`import com.github.pagehelper`
2、主从复制、读写分离
读写分离，基本的原理是让主数据库处理事务性增、改、删操作（INSERT、UPDATE、DELETE），而从数据库处理SELECT查询操作。数据库复制被用来把事务性操作导致的变更同步到集群中的从数据库。
[【mysql 读写分离】10分钟了解读写分离的作用](https://blog.csdn.net/u013421629/article/details/78793966)
3、Mybatis常用语法
1）去除多余的逗号：

```xml
<trim prefix="(" suffix=")" suffixOverrides=",">
	<if test="position != null">
        #{position,jdbcType=INTEGER},
    </if>
    <if test="title != null">
        #{title,jdbcType=VARCHAR},
    </if>
</trim>
```
此外`<where> </where>`和`<set> </set>`标签也可以自动去除多余的逗号、AND 、OR
2）循环语法

```xml
and `position` in
      <foreach item="item" collection="posIdList" separator="," open="(" close=")" index="">
        #{item}
      </foreach>
```
3) 尽量用>或>=，不用转义。

| 原有符号  |  mybatis中 |
|:-----:|:-----:|
| < | \&lt; |
| >| \&gt;|
|&|\&amp;|
|’|\&apos;|
|"|\&quot;|
|!=|\<![CDATA[<> ]]>|

4、数据库建表时推荐字符集：
DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci
# 五、GSON使用
1、Json -> Object

```java
new Gson().fromJson(value, type);
```

[Gson的fromJson()方法
](https://www.jianshu.com/p/bca8117ad49e)
2、GsonUtil.toJson(userTags)

```java
public static String toJson(Object object) {
        return (new GsonBuilder()).serializeNulls().disableHtmlEscaping().create().toJson(object);
    }
```
3、获取List<MarketingActivityBO>的类型
`Type type = new TypeToken<List<MarketingActivityBO>>() {}.getType();`
4、创建GsonBuilder

```java
Gson gson = new GsonBuilder()
                .setDateFormat("yyyy-MM-dd HH:mm:ss")
                .create();
```

# 六、SpringBoot相关
1、yml配置多个环境
[SpringBoot多环境yml文件配置](https://blog.csdn.net/weixin_34351321/article/details/86265157?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.nonecase&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.nonecase)
2、实现自定义注解
[Spring 自定义注解](https://my.oschina.net/magicalSam/blog/1359369)
# 七、时间转换
1、long时间戳与Date转换

```java
import java.util.Date;

public class DateUtil extends cn.hutool.core.date.DateUtil {
    /**
     * 时间戳转date
     * @param date
     * @return
     */
    public static Date parseFortuneTime(long date){
        return DateUtil.date(date);
    }
    /**
     * 转时间戳（毫秒）
     * @param date
     * @return
     */
    public static Long formatFortuneTime(Date date){
        if (date != null){
            return date.getTime();
        }
        return null;
    }
}
```

```java
import cn.hutool.core.date.DateTime;
import java.util.Calendar;

DateTime now = DateUtil.date();	//获取当前时间
Calendar day = Calendar.getInstance();
day.setTime(date);
```
引入Hutool:

```xml
<dependency>
    <groupId>cn.hutool</groupId>
    <artifactId>hutool-all</artifactId>
    <version>4.5.15</version>
</dependency>
```
毫秒时间戳（13位）转秒时间戳（10位）
```java
long timeStampSec = System.currentTimeMillis()/1000;
String timestamp = String.format("%010d", timeStampSec);
如 2017-02-27 15:48:27 的13位时间戳为：1488181707000

System.out.println(String.format("%010d",1488181707000L/1000));
输出：1488181707
```
时间格式：

```java
SimpleDateFormat df = new SimpleDateFormat(“yyyy-MM-dd HH:mm:ss”);	//设置日期格式
System.out.println(df.format(new Date()));	// new Date()为获取当前系统时间
```

[Hutool 参考文档](https://www.bookstack.cn/read/hutool/a6819f05207359bb.md)
[时间戳与时间转换网站](https://tool.lu/timestamp/)

# 八、Linux相关
1、查看日志的Linux指令
tail request.log -n 300 -f            查看request.log的后300行并实时更新

cat error.log -n | grep "timed out"   查看error.log中包含"timed out"的行号

sed -n '95,98p' error.log          查看error.log中的95-98行

grep -n -C10 'Exception' error.log | tail -n 20    查看error.log中包含“Exception”的那一行的上下10行（C），并取结果的后20行。
[linux查看日志方法](https://blog.csdn.net/harry5508/article/details/90041986)
# 九、Redis相关
1、Redis持久化策略：RDB（数据快照）、AOF（文件重写）。两者都开启的话会优先使用AOF，因为AOF的完整性更高。执行持久化时是通过主进程fork子进程的方式实现，然后把持久化的工作交给子进程，自己不会有相关的 I/O 操作。Linux fork 子进程采用的是 copy-on-write 的方式
2、常用数据结构：String、List、Hash、Set、SortedSet、HyperLogLog
3、大key拆分：
- 可以尝试将对象分拆成几个key-value， 使用multiGet获取值，这样分拆的意义在于分拆单次操作的压力，将操作压力平摊到多个redis实例中，降低对单个redis的IO影响；或者转成hash存储。
- hash， set，zset，list 中存储过多的元素。
     以hash为例，原先的正常存取流程是  hget(hashKey, field) ; hset(hashKey, field, value)
     现在，固定一个桶的数量，比如 10000， 每次存取的时候，先在本地计算field的hash值，模除 10000， 确定了该field落在哪个key上。

     newHashKey  =  hashKey + ( hash(field) % 10000）;   hset (newHashKey, field, value) ;  hget(newHashKey, field)
# 十、消息队列相关
1、使用场景：异步、削峰、解耦。
2、MQ可能产生的问题：重复消费、消息丢失、顺序消费
3、主流的消息队列中间件主要有：**Kafka**、**RocketMQ**、ActiveMQ、RabbitMQ
参考：[敖丙：消息队列](https://github.com/AobingJava/JavaFamily/blob/master/docs/mq/%E6%B6%88%E6%81%AF%E9%98%9F%E5%88%97%E5%9F%BA%E7%A1%80.md)
4、zookeeper启动命令：
- 修改zookeeper配置文件，设置dataDir、dataLogDir：vim apache-zookeeper-3.6.1-bin/conf/zoo.cfg
- 开启服务端：zkServer.sh start
- 开启客户端：zkCli.sh -server 127.0.0.1:2181
- 关闭服务端：zkServer.sh stop

5、kafka启动命令：
- 先开启zookeeper服务端：zkServer.sh start
- 初次启动需修改配置文件：vim kafka_2.12-2.5.0/config/server.properties

```java
//有几个broker就设置几个broker.id
broker.id=0
listeners = PLAINTEXT://localhost:9092
log.dirs=/Users/mac/Documents/kafka/log
zookeeper.connect=localhost:2181
```

- kafka-server-start.sh config/server.properties
- 使用JPS查看kafka是否启动成功：
```java
jps -l
20438 org.apache.zookeeper.server.quorum.QuorumPeerMain
21725 sun.tools.jps.Jps
20446 kafka.Kafka
```

6、创建主题：
- 创建一个副本因子为1，分区为4的主题，名为topic-demo：
`kafka-topics.sh --zookeeper localhost:2181 --create --topic topic-demo --replication-factor 1 --partitions 4`
- 查看分区详情：kafka-topics.sh --zookeeper localhost:2181 --describe --topic topic-demo

```java
Topic: topic-demo	PartitionCount: 4	ReplicationFactor: 1	Configs: 
	Topic: topic-demo	Partition: 0	Leader: 0	Replicas: 0	Isr: 0
	Topic: topic-demo	Partition: 1	Leader: 0	Replicas: 0	Isr: 0
	Topic: topic-demo	Partition: 2	Leader: 0	Replicas: 0	Isr: 0
	Topic: topic-demo	Partition: 3	Leader: 0	Replicas: 0	Isr: 0
```

# 十一、Thrift
1、基本类型 （括号内为对应的Java类型）

 - bool（boolean）: 布尔类型(TRUE or FALSE) 
 - byte（byte）: 8位带符号整数 
 - i16（short）:16位带符号整数 
 - i32（int）: 32位带符号整数 
 - i64（long）: 64位带符号整数 
 - double（double）:64位浮点数 
 - string（String）: 采用UTF-8编码的字符串
 
 2、判断thrift里的变量是否被设置了
```java
public boolean isSetId() {
    return __isset_bit_vector.get(__ID_ISSET_ID);
  }
```

# 十二、Vim常用操作
- :wq	保存并退出
- $	移动当前行行尾
- ^或者0	移动到当前行行首
- gg	整个文档的首部
- w	下一个单词的首字母位置
- b	上一个单词的首字母位置
- G	文档最后一行
# 十三、好用的插件
1、idea 插件 free mybatis plugin 
2、Lombok
3、Thrift support
# 十四、Git相关
- git status 查看当前工作区情况
- git add -A  提交所有变化
- git add -u  提交被修改(modified)和被删除(deleted)文件，不包括新文件(new)
- git add .  提交新文件(new)和被修改(modified)文件，不包括被删除(deleted)文件
- git commit -am "coment" 提交代码
- git reset HEAD XXX/XXX/XXX.java 就是对某个文件取消暂存
- git checkout -- xxx.java表示撤销对某个文件的修改（**慎用！**）
