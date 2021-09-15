# 安装MySQL

## Mac安装MySQL

### 下载和安装mysql

先去mysql官网下载dmg安装文件，然后安装：

[MySQL :: Download MySQL Community Server](https://dev.mysql.com/downloads/mysql/)

->

[MySQL :: Download MySQL Community Server](https://dev.mysql.com/downloads/mysql/5.7.html#downloads)

找到此处需要的：

* `mysql-5.7.22-macos10.13-x86_64.dmg`
  * 版本：macOS 10.13 (x86, 64-bit),
  * 格式：DMG Archive
  * 大小：341.1MB

下载后，双击dmg即可安装。

注意期间会有生成一个临时密码。

### 把mysql加到PATH环境变量

```bash
vi ~/.bashrc
```

把 `/usr/local/mysql/bin` 加进去：

```bash
export PATH=/usr/local/mysql/bin:$PATH
```

然后source使得立刻生效

```bash
source ~/.bashrc
```

通过which，确保能找到

```bash
which mysql
```

### 设置mysql密码

Mac中dmg安装的mysql默认不需要密码就可以登录的

所以此处最好去设置对应的密码

且如果用默认的临时密码，可能会导致其他工具无法正常连接

单独开个终端shell去：

```bash
sudo mysqld_safe --skip-grant-tables
```

然后另外再开个终端去：

```bash
use mysql;
update user set authentication_string=password('new_password') where user='root’;
flush privileges;
exit
```

注意：`password('new_password`中，是`英文的单引号`=`'`，不是`中文的单引号`=`‘`

-》我就是从别人网页中拷贝过来，结果发现是单引号，导致无法继续正常执行的。

然后就可以正常使用：

```bash
mysql -u root
```

去用新密码登录进去了。

### 设置密码不过期

为了防止其他mysql工具连接出错，还需要设置密码不过期，且重新再去设置一下新密码

```bash
mysql -u root -p
SET PASSWORD = PASSWORD('your_new_password’);
use mysql;
update user set password_expired='N' where user='root’;
flush privileges;
exit
```

### 确保mysql的服务已运行

后续记得用mysql之前，确保mysql（的server）是正常运行了的：

对于mysql的服务的管理是：

```bash
sudo /usr/local/mysql/support-files/mysql.server status
sudo /usr/local/mysql/support-files/mysql.server start
sudo /usr/local/mysql/support-files/mysql.server stop
sudo /usr/local/mysql/support-files/mysql.server restart
```
