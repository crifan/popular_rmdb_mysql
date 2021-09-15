# Python中使用mysql

此处介绍如何在Python中使用和操作MySQL数据库。

* 先选择合适的mysql连接器/适配器
  * `PyMySQL`
    * https://github.com/PyMySQL/PyMySQL
      * star: `3000+`
  * `mysqlclient`
    * https://github.com/PyMySQL/mysqlclient-python
      * star: `1000+`
  * Oracle的官方的`MySQL Connector`
    * https://github.com/mysql/mysql-connector-python
      * star: `300+`
      * 基本用法
        * 安装
          ```bash
          pipenv install mysql-connector-python
          ```
        * 导入
          ```bash
          import mysql
          ```
  * 结论
    * 选用的star最多的且支持Python3的：`PyMySQL`

## 心得

### mysql中操作表的字段名时是否一定要用反引号括起来

MySQL中的一些对象，比如：

* database
* table
* index
* column
* alias
* view
* stored procedure
* partition
* tablespace
* resource group
* other object

这些对象的名字，被叫做 标识符 identifier

而标识符的所允许的字符是有一定的限制的

有些标识符是在某些情况下是区分大小写的

对于一个标识符来说，可能被引起来quoted，也可能是没有被引起来unqoted

* 如果包含特殊字符，后者是系统保留字，则必须要被quote
   * 例外是：当这个标识符后面有个点，表示引用字段，则可以不用quote
   * 而系统保留字的话，比如，`select`，`interval`，`from`等等
     * 详见：[MySQL :: MySQL 8.0 Reference Manual :: 9.3 Keywords and Reserved Words](https://dev.mysql.com/doc/refman/8.0/en/keywords.html)
* 对于普通的标识符
  * 如果不quote的话，所允许的字符有：
    * `0-9`数字，`a-Z`的字母，美元符号`$`，下划线`_`
    * 扩展的Unicode字符：`U+0080` .. `U+FFFF`
  * 对于quote的标识符的话，所允许的字符有：
    * 基本的ASCII字符: `U+0001` .. `U+007F`
    * 扩展的Unicode字符: `U+0080` .. `U+FFFF`
* 不论是quote还是unquote，都不允许：
  * ASCII `NUL` (`U+0000`)
  * supplementary characters (`U+10000` and higher)
* 标识符中可以以数字开头，但是要quote，且不能只是数字
* `Database`, `table`, `column`的名字最后不能以空格结尾

对于quote的字符：

* 标准的来说是：`反引号` == &#96; == `backticks`

举例：

```sql
mysql> SELECT * FROM `select` WHERE `select`.id > 100;
```

* 如果启用了`ANSI_QUOTES`的SQL模式的话，则也可以用 `双引号` == `""` 去quote

举例：

```sql
mysql> CREATE TABLE "test" (col INT);
ERROR 1064: You have an error in your SQL syntax...
mysql> SET sql_mode='ANSI_QUOTES';
mysql> CREATE TABLE "test" (col INT);
Query OK, 0 rows affected (0.00 sec)
```

`ANSI_QUOTES`模式的话，会使得系统把双引号内的字符，作为标识符

同时也意味着，对于普通的字符串来说（本来是用双引号括起来的），现在只能用`单引号` == `'` 去括起来了。

关于`ANSI_QUOTES`，详见：

[MySQL :: MySQL 8.0 Reference Manual :: 5.1.10 Server SQL Modes](https://dev.mysql.com/doc/refman/8.0/en/sql-mode.html#sqlmode_ansi_quotes)

下面再来说一些，更加特殊的情况：

quote字符，比如`反引号`，`双引号`，也是可以被括起来，放在标识符里的，不过是：

如果被quote起来的字符，包含了quote字符本身，就需要写两次

举例：

```sql
mysql> CREATE TABLE `a``b` (`c"d` INT);
```

解释：

此处的table的标识符名字是：

```bash
a`b
```

由于外部的quote引起来的字符用的是`反引号` &#96;

所以a和b中间的反引号，要写成2遍，变成：


```bash
a``b
```

被quote引起来就是：

```bash
`a``b`
```

而对于另外的一个（只有启用了ANSI_QUOTES）quote字符：双引号 `"` 来说，由于外部的quote字符不是双引号，所以不用写2遍，所以可以写成：

```bash
`c"d`
```

对比标识符的命名来说：

不建议用数字开头，比如：`Me` `MeN`，其中`M`和`N`是数字

总之：

为了避免其他副作用，尽量只用普通的字符：`0-9`，`a-Z`，`下划线`

去命名，作为标识符

* 一，省去了quote的考虑
  * 避免其他不必要的麻烦
* 二，也是好的编程和设计的原则和规范

而对于此处的：

问：写mysql的代码中，是否一定要用别人那种escape的写法？

答：

* 如果自己的标识符变量命名，都是很规则，比如我的`cityDealerPrice`，`modelStatus`等等，没有包含特殊的容易导出解析错误的单词或字符，那就不需要用 &#96; 去quote
* 如果不能确保标识符是普通的字符，比如标识符中可能出现空格或其他特殊字符，或者是系统保留字，则最好是用上 &#96; 去quote起来，以防万一。
