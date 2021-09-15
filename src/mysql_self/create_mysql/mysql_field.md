# 字段属性

在设计和查看数据库字段时

比如：

* 用`MySQL Workbench`设计表时
  * ![mysql_workbench_design_table](../assets/img/mysql_workbench_design_table.png)
* sequel中查看字段时
  * ![sequel_check_field](../assets/img/sequel_check_field.png)

会看到数据库字段的属性的缩写：

* PK
* NN
* UQ
* BIN
* UN
* ZF
* AI
* G

这部分都是通用的逻辑。

对应的含义是：

* `PK`=`Primary Key`=`主键`
* `Unsigned`=无符号整数
* `ZF`=`Zerofill`=用0填充
* `BIN`=`Binary`=二级制
* `Allow Null`=允许空值
* `Key`=键
* `Default`=默认值
* `UQ`=值唯一
* `AI`=`Auto Increment`=自增
