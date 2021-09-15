# 备份和恢复

此处整理，MySQL数据库的备份和恢复。

## mysql 导入/恢复

### 直接命令行中导入

mysql 导入sql 恢复mysql：

```bash
mysql -u user_name -p database_name < previous_exported_sql_file.sql
```

然后输出密码，即可导入。


举例：

```bash
➜  mysql ll -h
-rw-r--r--@ 1 crifan  staff   5.1K 12 26 14:11 checkpoint_20181226.sql
➜  mysql mysql -u root -p xxx < checkpoint_20181226.sql
Enter password:
```

### 先进入mysql的命令行，再去导入

登录sql命令行，去source导入：

```bash
mysql -u username -p  database_name
```

进入后再`source`：

```bash
> source '/path/to/dumped/sql/file'
```

即可导入数据：

![imported_mysql_data](../assets/img/imported_mysql_data.png)

## mysqlimport

* `mysqlimport`
  * 不能用来导入`sql`，只能导入`txt`文本
  * 语法
    ```bash
    mysqlimport  [OPTIONS]  database  textfile
    ```
  * 举例
    ```bash
    mysqlimport -u root -p --local database_name dump.txt
    ```


## 注意事项

### 导入之前，先要创建一个空的数据库

* 方式1：登录sql命令行后去用命令新建
    ```bash
    CREATE DATABASE database_name DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
    ```
* 方式2：用工具去新建
  * `Sequel Pro`
    * ![sequel_pro_add_database](../assets/img/sequel_pro_add_database.png)
    * ![sequel_pro_new_db_info](../assets/img/sequel_pro_new_db_info.png)
* 才可以正常导入：
    ```bash
    mysql -u root -p qpy < qpy.sql
    Enter password:
    ```
* 否则会报错：
  ```bash
  mysql -u root -p qpy < qpy.sql
  Enter password:
  ERROR 1049 (42000): Unknown database 'qpy'
  ```
