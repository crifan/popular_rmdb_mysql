# MySQL命令行

## 用法举例

### database=数据库

#### 删除database数据库

```sql
drop database xxx;
```

### table=表

#### 查看当前已打开的表

```sql
show open tables;
```

#### 查看当前已打开的，正在使用的表

```sql
show open tables where in_use>0;
```

#### 查看表结构

```sql
describe table_name;
```

##### 举例

```sql
MySQL [xxx]> describe keyword;
+------------+-----------+------+-----+---------+----------------+
| Field      | Type      | Null | Key | Default | Extra          |
+------------+-----------+------+-----+---------+----------------+
| id         | int(11)   | NO   | PRI | NULL    | auto_increment |
| name       | char(100) | NO   | MUL | NULL    |                |
| type       | char(20)  | NO   |     | NULL    |                |
| createTime | datetime  | YES  |     | NULL    |                |
| modifyTime | datetime  | YES  |     | NULL    |                |
+------------+-----------+------+-----+---------+----------------+
5 rows in set (0.00 sec)
```

![mysql_describe_example](../assets/img/mysql_describe_example.png)

#### 查看已有表的创建create的命令

```sql
SHOW CREATE TABLE tbl_name
```

##### 举例

```sql
MySQL [xxx]> SHOW CREATE TABLE keyword;
+---------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Table   | Create Table                                                                                                                                                                                                                                                                                                              |
+---------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| keyword | CREATE TABLE `keyword` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` char(100) NOT NULL,
  `type` char(20) NOT NULL,
  `createTime` datetime DEFAULT NULL,
  `modifyTime` datetime DEFAULT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `Multi` (`name`,`type`)
) ENGINE=InnoDB AUTO_INCREMENT=7529 DEFAULT CHARSET=utf8 |
+---------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)
```

![mysql_show_create_example](../assets/img/mysql_show_create_example.png)

### record=记录=数据=值

#### 插入数据

##### 对于自增ID的数据插入可以省略ID

对于表结构是：

```sql
CREATE TABLE `keyword` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` char(100) NOT NULL,
  `type` char(20) NOT NULL,
  `createTime` datetime DEFAULT NULL,
  `modifyTime` datetime DEFAULT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `Multi` (`name`,`type`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

其中`id`是`AUTO_INCREMENT`

![mysql_create_ai_value](../assets/img/mysql_create_ai_value.png)

插入值时，可以省略ID：

```bash
INSERT INTO `keyword`(`name`,`type`,`createTime`,`modifyTime`) VALUES("topic2","topic","2018-07-13 10:09:27","2018-07-13 10:09:27");
```

#### 更新数据

```sql
UPDATE keyword SET name="animal" WHERE type="sectorTopic" AND id=5792;

UPDATE keyword SET name="ocean animal" WHERE type="topic" AND name="ocean animals";
```

#### 删除特定数据

```bash
SELECT * FROM keyword WHERE type="topic" AND name="basketball";
id=7426
DELETE FROM keyword_rel WHERE keyword2=7426;
```

#### 删除多个数据

比如id在`4`到`13`之间，包括`4`和`13`

```sql
DELETE FROM user WHERE id>=4 AND id<=13;
DELETE FROM user WHERE BETWEEN 4 AND 13;
```

### mysql全局

#### 查看当前进程

查看当前活跃的进程的列表：

```sql
show processlist;
```

##### 举例

```sql
mysql> show processlist;
+----+------+-----------------+-----------+---------+------+----------+------------------+
| Id | User | Host            | db        | Command | Time | State    | Info             |
+----+------+-----------------+-----------+---------+------+----------+------------------+
|  2 | root | localhost       | NULL      | Query   |    0 | starting | show processlist |
| 28 | root | localhost:53199 | nxxxg | Sleep   |  404 |          | NULL             |
+----+------+-----------------+-----------+---------+------+----------+------------------+
2 rows in set (0.01 sec)
```

#### 杀死进程

```sql
kill process_id;
```


#### 查看当前的mysql最大连接

```sql
show variables like 'max_connections';
```

#### 查看当前active活跃的连接数

```sql
show status where `variable_name` = 'Threads_connected';
```


#### 查看字符编码

```sql
SHOW VARIABLES LIKE "character_set_server";

SHOW VARIABLES LIKE "collation_server";
```
