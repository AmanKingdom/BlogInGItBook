## 1、安装 MySQL {#1-安装-mysql}

### 1、下载 mysql yum 到本地： {#1-下载-mysql-yum-到本地}

```
sudo wget http://repo.mysql.com/mysql57-community-release-el7-10.noarch.rpm
```

### 2、安装 mysql yum： {#2-安装-mysql-yum}

```
sudo rpm -Uvh mysql57-community-release-el7-10.noarch.rpm
```

### 3、安装 mysql 服务端： {#3-安装-mysql-服务端}

```
yum install  -y  mysql-community-server
```

在这里，如果你更改 centos 的 python 为 python3 的话，那就会有个 yum 的报错：

```
File "/usr/libexec/urlgrabber-ext-down", line 28
```

其实只要改一下这个文件的 python 解析器路径就OK，先打开：

```
sudo vi /usr/libexec/urlgrabber-ext-down
```

然后把开头的那行 \# !/usr/bin/python 改为 \# !/usr/bin/python2.7 并再次执行安装即可

### 4、启动 mysql： {#4-启动-mysql}

```
service mysqld start
```

重启 mysql 是：

```
service mysqld restart
```

你也可以检查一下 mysql 的运行状态：

```
service mysqld status
```

### 5、登录并修改密码 {#5-登录并修改密码}

> 修改密码是一定要的

#### 1、先查看临时密码： {#1-先查看临时密码}

mysql5.7 安装并**启动**后是有个临时密码的，密码在 error log 中，查看请用：

```
grep 'temporary password' /var/log/mysqld.log
```

#### 2、登录： {#2-登录}

```
mysql -uroot -p
```

#### 3、修改密码： {#3-修改密码}

> 如果你不修改密码就执行数据库操作的话，它会报错:You must reset your password using ALTER USER statement before executing this statement. 它说你密码太简单了。。

```
ALTER USER 'root'@'localhost' IDENTIFIED BY '新的密码';
```

所以我们还要先修改一下某些全局参数使得它对密码不要这么敏感：

```
mysql> set global validate_password_policy=0;

mysql> set global validate_password_length=1;
```

然后再次执行修改数据库的密码：

```
ALTER USER 'root'@'localhost' IDENTIFIED BY '新的密码';
```

### 6、拓展补充 {#6-拓展补充}

#### 授权其他机器登陆 {#授权其他机器登陆}

```
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'yourpassword' WITH GRANT OPTION;

FLUSH  PRIVILEGES;
```

以上安装流程大部分来自：[https://www.cnblogs.com/silentdoer/articles/7258232.html\#tag0](https://www.cnblogs.com/silentdoer/articles/7258232.html#tag0)

## 2、安装 mysqlclient {#2-安装-mysqlclient}

本来，一句话就OK了的：

```
pip install mysqlclient
```

但是，往往会出毛病，会缺少一些东西，根据官方文档：[https://pypi.org/project/mysqlclient/](https://pypi.org/project/mysqlclient/)，我们需要安装一些依赖：

```
sudo yum install python-devel mysql-devel
```

然后再执行：

```
pip install mysqlclient
```

就OK了

> 注意，不要安装 mysqldb 之类的了，python3 已经不支持了

### 拓展补充 {#拓展补充}

mysql 数据库的一些基本操作：

查看有哪些数据库：

```
show databases;
```

进入数据库：

```
use 数据库名;
```

查看当前数据库中有哪些表:

```
show tables;
```

查看某个表的结构：

```
desc 表名
```

## 3、django 连接mysql {#3-django-连接mysql}

在 django 的[settings.py](http://settings.py)中进行设置：

```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': '数据库名字',
        'USER': '你的数据库用户',
        'PASSWORD'： '数据库密码',
        'HOST': 'localhost',
        'PORT': '3306',
    }
}
```

在执行数据库迁移前，要先在 mysql 数据库那边先把数据库创建出来了再进行连接：

```
mysql> create database 数据库名;
```

然后在 django 这边：

```
python manage.py migrate
```

至此，你已经成功啦！

