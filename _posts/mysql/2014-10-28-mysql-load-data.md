---
layout: post
title: 使用load导出mysql表数据
category: mysql数据库
description: 使用mysql的load data来快速的导入导出数据
keywords: mysql, dump, load, infile, outfile
---
　　当我们需要备份数据库时，需要导出数据，我们一般是使用mysqldump命令来进行数据的导出，下面我们先来试验一下

首先创建一张表

	CREATE TABLE `t1` (
		`id` int(11) DEFAULT NULL
	) ENGINE=MyISAM DEFAULT CHARSET=utf8

创建t1表，只有一个字段，往数据表中插入几行数据

	INSERT INTO `t1` VALUES (1),(2),(3);  

然后使用mysqldump开始备份

>mysqldump -uroot -proot test -F -l > /tmp/test.sql

这样我们就备份完毕了，我们可以查看一下test.sql的文件格式

	--
	-- Table structure for table `t1`
	--
	DROP TABLE IF EXISTS `t1`;
	/*!40101 SET @saved_cs_client     = @@character_set_client */;
	/*!40101 SET character_set_client = utf8 */;
	CREATE TABLE `t1` (
	  `id` int(11) DEFAULT NULL
	) ENGINE=MyISAM DEFAULT CHARSET=utf8;
	/*!40101 SET character_set_client = @saved_cs_client */;
	--
	-- Dumping data for table `t1`
	--
	LOCK TABLES `t1` WRITE;
	/*!40000 ALTER TABLE `t1` DISABLE KEYS */;
	INSERT INTO `t1` VALUES (1),(2),(3);
	/*!40000 ALTER TABLE `t1` ENABLE KEYS */;
	UNLOCK TABLES;
	/*!40103 SET TIME_ZONE=@OLD_TIME_ZONE */;

可以看到里面有一大堆表结构的语句和表创建的语句，当数据量非常大的时候，导出的速度就可想而知。

于是我们就需要在导出的时候避开这些语句，这里就要用到load的infile和outfile来导入导出数据。

导出outfile的使用
----------------

	SELECT * FROM `t1` INTO OUTFILE "/tmp/test.sql"

可以通过select查询将要导出的数据表和数据表的字段导出来，可以导出全部列和指定列，如：

	SELECT `id` FROM `t1` INTO OUTFILE "/tmp/test.sql"

这样就导出了t1表的id列。

看一下导出的文件格式

![sql文件格式][1]

可以看到只导出了数据，而不存在表的结构和其他不相干的语句，这样就大大减少了导出数据的量，当大数据量时，速度就会明显提升了

导入load data的使用
------------------

	LOAD DATA INFILE "/tmp/test.sql" INTO TABLE `t1`

这样是导入到t1表的全部列中，也可以导入到某一列或几列中

	LOAD DATA INFILE "/tmp/test.sql" INTO TABLE `t1`(`id`)

比较速度
-------

下面进行一下实际操作，在t1表中插入1000万行数据进行导入导出速度的比较。

	mysql> select count(*) from t1;        
	+----------+
	| count(*) |
	+----------+
	| 10485760 |
	+----------+

-------

###用outfile导出数据

	mysql> select * from t1 into outfile "/tmp/outfile.sql";
	Query OK, 10485760 rows affected (2.43 sec)

发现导出的速度非常快，1000万行数据只需要2秒多



###使用mysqldump导出数据

![导出时间][2]

发现导出需要12秒左右，是outfile的5倍左右

###比较两个文件的大小

![文件大小][3]

可以看到mysqldump导出的数据是outfile的2倍，这也就说明了outfile的优势所在。

-------

###使用load data infile导入

	mysql> load data infile "/tmp/outfile.sql" into table t1;    
	Query OK, 10485760 rows affected (12.46 sec)
	Records: 10485760  Deleted: 0  Skipped: 0  Warnings: 0


	mysql> select count(*) from t1;                      
	+----------+
	| count(*) |
	+----------+
	| 10485760 |
	+----------+

发现导入的速度也是不慢的


[1]: /images/20140904110643.jpg  "sql文件格式"
[2]: /images/20131130142325812.png "导出时间"
[3]: /images/20131130142531125.png "文件大小"