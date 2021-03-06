### _Hbase简介_

```xml
Hbase是Apache Hadoop的数据库,能够对大型数据提供随机,实时读写访问,是Google的BitTable的开源实现。
Hbase的目标是存储并处理大型的数据,更具体的说仅用普通的硬件配置,能够处理成千上万的行和列所组成的大型数据库。
Hbase是一个开源的,分布式的,多版本的,面向列的存储模型。可以直接使用本地文件系统可以使用Hadoop的HDFS文件存储系统。为了提高数据的可靠性和系统的健壮性,并且发挥Hbase处理大型数据的能力,还是使用HDFS作为文件存储系统更佳。
另外,HBase存储的是松散型数据,具体来说,Hbase存储的数据介于映射(key/value)和关系数据之间.

Hbase是Apache的hadoop项目的子项目.HBase不同于一般的关系数据库,它是一个适合于非结构化数据存储的数据库。
Hbase基于列而不是基于行的模式

```









### _Hbase的概念术语_





***行键Row Key***

```xml
用来检索记录的主键,访问HbaseTable中的行
Row Key的概念和mysql中的主键是完全一样的,Hbase使用RowKey来唯一区分某一行的数据
由于Hbase支持3种查询方式
1) 基于RowKey的单行查询
2) 基于RowKey的范围扫描
3) 全表扫描
因此RowKey对Hbase的性能影响非常大,RowKey的设计就显得尤为重要。设计的时候要兼顾基于RowKey的单行查询也要键入RowKey的范围扫描。
具体RowKey要如何设计后续会整理相关的文章做进一步的描述。这里大家只要有一个概念就是RowKey的设计极为重要

rowkey行键可以是任意字符串(最大长度为64KB,实际中一般为10~100Bytes)最好是16。在Hbase内部,rowKey保存为字节数组。
Hbase会对表中的数据按照rowKey排序(字典顺序)
```

***列族 Column Family***

```xml
	Table在水平访问有一个或多个ColumnFamily组成,一个Column Family中可以由任意多个Column组成,即ColumnFamily支持动态扩展,无需预先定义Column的数量以及类型,所有Column均以二进制格式存储,用户需要自行进行类型转换。

列族 Hbase引入的概念
Hbase通过列族划分数据的存储,列族下面可以包含任意多的列,实现灵活的数据存储。就像是家族的概念,一个家族是由很多个家庭组成。列族也类似,列族由一个一个的列组成(任意多)。
Hbase表创建的时候,就必须制定列族。就像关系型数据库创建的时候必须指定具体的列一样
Hbase的列族不是越多越好,官方推荐的列族最好小于或者等于3,一般场景都是一个列族
```

***列column***

```xml
由Hbase中的列族ColumnFamily + 列的名称(cell)组成列
列,可理解为MySQL列
```

***单元格cell***

```xml
Hbase中通过row和columns确定的为一个存储单元称为cell
由rowkey,column Family:column version 他们三个参数确定唯一一个cell

Cell中的数据是没有类型,全部是字节码形式存储。
```

***TimeStamp的概念***

```xml
TimeStamp对Hbase来说至关总要,因为它是实现Hbase多版本的关键.在Hbase中使用不同的timestamp来标识相同的rowKey行对应的不同版本的数据
Hbase中通过rowKey和columns确定为一个存储单元成为cell.每个cell都保存同一个数据的多个版本。版本通过时间戳来索引。时间戳的类型是64位整型。
时间戳可以由Hbase(在数据写入时自动)赋值,此时时间戳是精确到毫秒的当前系统时间。
时间戳也可以有客户显式赋值。如果应用程序要避免数据版本冲突,就必须自己生成具有唯一性的时间戳。
每个cell中,不同版本的数据按照时间倒序排序,即最新的数据排在最前面

为了避免数据存在过多的版本造成管理(包括存储和索引)负担,hbase提供了两种数据版本回收方式
	> 保存数据的最后n个版本
	> 保存最近一段时间内的版本(设置数据的声明周期TTL)
用户可以针对每个列族进行设置
```



***版本version***

```xml
每个cell都保存着同一份数据的多个版本.版本通过时间戳来索引
```



***传统数据库的一个表结构***

| 姓名       | 年龄 | 性别       | 成绩 |
| ---------- | ---- | ---------- | ---- |
| liujinghui | 18   | strong man | 100  |
| zhangsan   | 88   | Niangs     | -1   |

***转换为Hbase的数据库表的结构***

| -      | Info | soure |      |      |      |
| :----- | ---- | ----- | ---- | ---- | ---- |
| Row_Ke |      |       |      |      |      |
|        |      |       |      |      |      |





















































### _总结_