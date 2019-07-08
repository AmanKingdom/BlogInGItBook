> 说在前面的话： 为什么我要加上阿里云在这篇文章里呢？因为阿里云服务器提供外界的访问端口是有安全组的，而我们这里的部署过程中，nginx 默认使用80端口，如果阿里云服务器控制台上没有设置好这个安全组规则，那下面做的许多工作就很难验证从而不知道有没有做对了。。

# 总体实现思路

1、安装依赖（django + uwsgi + virtualenv + nginx）

2、进入 virtualenv 虚拟环境安装依赖（django + uwsgi）

3、通过创建一个简单的 django 网站实例测试效果

4、配置 uwsgi 和 nginx

5、启动 uwsgi 和 nginx

6、浏览器通过公网 IP 访问

但本教程的 CentOS 是已经将其自带的 python 由原来的 python2.7 转为 python3，要想使用 python2.7，则是输入 python2，所以在使用本教程前，建议先看一看这篇： CentOS 的 python 转到 python3，python2.7 使用 python2 调用

# 具体实现过程

### 安装目前所有需要的依赖包包 {#安装目前所有需要的依赖包包}

#### 1、下载安装 Django2.1 {#1-下载安装-django21}

> 比较建议的是下载指定版本的Django

```
sudo pip install django==2.1
# 建立软链接
sudo ln -s /usr/local/python3/bin/django-admin /usr/bin/django-admin
sudo ln -s /usr/local/python3/bin/django-admin.py /usr/bin/django-admin.py
```

检查一下：

```
cd /usr/bin
[bin]$ ls -al django*
lrwxrwxrwx. 1 root root 35 4月  24 23:07 django-admin -> /usr/local/python3/bin/django-admin
lrwxrwxrwx. 1 root root 38 4月  24 23:08 django-admin.py -> /usr/local/python3/bin/django-admin.py
```

#### 2、安装uwsgi {#2-安装uwsgi}

```
sudo pip install uwsgi
# 建立软链接
sudo ln -s /usr/local/python3/bin/uwsgi /usr/bin/uwsgi
```

#### 3、安装虚拟环境virtualenv {#3-安装虚拟环境virtualenv}

```
sudo pip install virtualenv
# 建立软链接
sudo ln -s /usr/local/python3/bin/virtualenv /usr/bin/virtualenv
```

#### 4、安装nginx {#4-安装nginx}

切换到一个用来存放临时文件的目录下载安装包，我就随便选个`/home`吧，反正都是要删掉的：

```
cd/home/
```

下载：

```
wget http://nginx.org/download/nginx-1.13.7.tar.gz
```

解压：

```
tar -zxvf nginx-1.13.7.tar.gz
```

安装：

```
cd nginx-1.13.7
./configure
make
make install
```

**到目前为止，我们项目暂时所需的各种安装包就装好了，接下来是创建一个基础的**`django`**项目来运行一下看看效果：**

### 测试效果 {#测试效果}

#### 1、创建Django项目 {#1-创建django项目}

> 记住，在centos上创建的Django项目不要带中文路径，另外，本例的项目放在`/home/project`中

创建`myblog`项目：

```
cd /home/projects

[projects]$ django-admin startproject myblog
[projects]$cd myblog
[myblog]$ python manage.py startapp app
```

在项目文件夹内部创建专属的虚拟环境：

```
[myblog]$ virtualenv --python=/usr/bin/python venv
```

> 在我的centos中，为方便统一管理，我的Django项目都是包含项目自身所需的虚拟环境，而不是把该虚拟环境放在项目外

#### 2、进入虚拟环境状态，在虚拟环境状态下安装各种包 {#2-进入虚拟环境状态在虚拟环境状态下安装各种包}

```
[myblog]$ source venv/bin/activate
(venv) [myblog]$ pip install django==2.1    #安装虚拟环境的django
(venv) [myblog]$ pip install uwsgi          #安装uwsgi
```

#### 3、对项目中的settings.py的修改 {#3-对项目中的settingspy的修改}

1、在`INSTALLED_APPS`中添加网站应用的名称，我这里是`app`

2、修改`ALLOWED_HOSTS=['*']`，让任何IP都可以访问

3、在项目根目录`/myblog/`中新建`templates`和`static`,然后在`settings.py`中的`TEMPLATES`的`DIRS`里添加模板路径：

```
'DIRS': [os.path.join(BASE_DIR, 'templates')],
```

4、在`settinsg.py`的文件尾部添加：

```
STATICFILES_DIRS = (
    os.path.join(BASE_DIR,'static'),
)
```

#### 4、用django自带的服务器启动一下项目 {#4-用django自带的服务器启动一下项目}

```
python manage.py runserver
```

**如果能运行就对了，按**`ctrl+C`**退出，接着用**`uwsgi`**和**`nginx`**来搭建真正能用的服务器**

#### 5、配置uwsgi {#5-配置uwsgi}

> linux新建一个文件的命令：touch myblog.xml

在项目根文件夹`myblog`下新建一个`myblog.xml`，输入以下内容：

```
<uwsgi>    
       <socket>127.0.0.1:8997</socket> <!-- 内部端口，自定义 --> 
       <chdir>/home/projects/myblog/</chdir> <!-- 项目路径 -->            
       <module>myblog.wsgi</module>  <!-- myblog为wsgi.py所在目录名--> 
       <processes>4</processes> <!-- 进程数 -->     
       <daemonize>uwsgi.log</daemonize> <!-- 日志文件 -->
</uwsgi>
```

#### 6、配置nginx.conf {#6-配置nginxconf}

进入目录`/usr/local/nginx/conf`：

```
cd /usr/local/nginx/conf
```

先备份一下这里的`nginx.conf`：

```
cp nginx.conf nginx.conf.bak
```

然后打开`nginx.conf`，将原来的所有内容删掉：

```
vim nginx.conf
```

> linux删掉一个文件的所有内容：按`esc`然后输入`:%d`

回车确认，直接复制下面的内容粘贴并修改好对应自己项目的那部分：

```
events {
    worker_connections  1024;
    }
    http {
        include       mime.types;
        default_type  application/octet-stream;
        sendfile        on;
        server {
            listen 80;
            server_name  127.0.0.1:80; #也可以直接放自己的域名
            charset utf-8;
            location / {
               include uwsgi_params;
               uwsgi_pass 127.0.0.1:8997;  #端口要和uwsgi里配置的一样
               uwsgi_param UWSGI_SCRIPT myblog.wsgi;  #wsgi.py所在的目录名+.wsgi
               uwsgi_param UWSGI_CHDIR /home/projects/myblog/; #项目路径

            }
            location /static/ {
            alias /home/projects/myblog/static/; #静态资源路径
            }
        }
    }
```

#### 7、启动nginx {#7-启动nginx}

进入`/usr/local/nginx/sbin/`:

```
cd /usr/local/nginx/sbin
```

检测配置文件的完整性：

```
 ./nginx -t
```

如果不成功，则检查上面那个`conf`文件哪里写错了

如果成功则：

```
./nginx
```

没有任何提示则表示启动成功

> linux 的哲学就是：没有消息就是好消息

#### 8、启动uwsgi {#8-启动uwsgi}

进入项目根目录：

```
cd /home/projects/myblog
```

输入命令：

```
uwsgi -x myblog.xml
```

#### 9、重启`nginx`： {#9-重启nginx}

```
cd /usr/local/nginx/sbin
./nginx -s reload
```

**到目前为止，你已经成功搭建好环境啦！请在浏览器用你的域名或云服务器的公网ip地址访问即可~~**

## 拓展补充1 {#拓展补充1}

查看nginx日志：

```
tail -f /usr/local/nginx/logs/access.log
```

查看uwsgi日志：

```
tail -f uwsgi.log
```

查看nginx的进程：

```
ps -ef|grep nginx
```

杀死进程：

```
 kill   进程ID
```

## 拓展补充2 {#拓展补充2}

这时候你打开 localhost/admin 试试？如果你的界面很丑，网页没有加载 css 样式的话，你应该先浏览器按 F12 查看下错误是什么，如果是 Permission denied 那么就是权限问题，这时候去到 /usr/local/nginx/conf/nginx.conf 修改一下，给开头添加一句：

```
user root;
```

就行了。

但是如果是没有找到 css 文件路径之类的报错呢，那就要检查下 nginx.conf 和[settings.py](http://settings.py)中的 STATIC\_ROOT 是否配置对了路径:

1、在[settings.py](http://settings.py)的最后添加：

```
STATIC_ROOT = os.path.join(BASE_DIR, 'static')
```

或者：

```
STATIC_ROOT = '项目的绝对路径/static'
```

2、在虚拟环境下输入：

```
python manage.py collectstatic
```

然后就OK了

