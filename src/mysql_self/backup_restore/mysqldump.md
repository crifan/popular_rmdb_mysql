# mysqldump

`mysqldump`是MySQL的命令行工具，用于导出数据库，往往用于备份数据库的数据。

* 导出
  ```bash
  mysqldump -u user_name -p database_name > dump_out_sql_filename.sql
  ```

## 举例

导出数据库：

```bash
mysqldump -u root -p databaseName > databaseName_20190608.sql
```

带压缩 + 整个database数据库：

```bash
mysqldump --host=xx-xxxx.mysql.rds.aliyuncs.com --port=3306 --user=root —-password=your_password --default-character-set=utf8 xxx | gzip > aliyun_rds_xxx_mysql_dump.sql.gz
```

或-u的用户名和-p的密码的参数重去掉空格的写法：

```bash
mysqldump -h xxx.mysql.rds.aliyuncs.com -P 3306 -B xxx -uroot -pyour_password | gzip > aliyun_rds_xxx_mysql_dump.sql.gz
```

直接导出某个db的某个table表：

```bash
mysqldump -u user_name -p database_name table_name_in_db > dump_out_sql_filename.sql
```

多个database：

```bash
mysqldump -uroot -proot --databases db1 db2 >/tmp/user.sql
```

举例：

```bash
mysqldump -h HOST_OR_IP -P 3306 xxx keyword -uroot -pYOUR_PWD > aliyun_rds_xxx_mysql_keyword_180705.sql
```

批量导出某个数据库database中多个table表的数据，且带创建table的sql的做法是：

```bash
mysqldump --host your_mysql_host --port 3306 -u user_name -p db_name db_table_1 db_table_2 db_table_3 > single_db_mutile_table_data.sql
```

如果不想要创建table，则可以加上：`--no-create-info`

```bash
mysqldump --host your_mysql_host --port 3306 -u user_name -p db_name db_table_1 db_table_2 db_table_3 --no-create-info > single_db_mutile_table_data.sql
```
