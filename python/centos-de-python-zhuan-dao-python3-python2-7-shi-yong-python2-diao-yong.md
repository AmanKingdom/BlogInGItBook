### 说在前面的话： {#说在前面的话}

> 众所周知，CentOS 通常都安装了 python，但该 python 版本是 2.7，而 python2.7 到明年（2020）年就不再被维护了，所以我就给我的 CentOS 安装 python3，而且要用 python 命令直接调用，而不是 python3 这个命令。有人说修改原来 python 的软链接 python2.7 到 python3 不太好，因为涉及很多 CentOS 自身系统的软件调用问题，但我还是想这样用，所有遇到的 python2 调用问题，就留给时间去发现和解决吧，以后我也会收集遇到的问题整理出来给大家哈。
>
> 我用的是python3.7版本，如果是python3.6 以下的 3 版本的话，可以不用下面这个命令安装这么多依赖哈：

```
sudo yum install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gcc make libffi-devel
```

### 本次操作的原理

首先查看一下当前python2的存在路径：

```
which python
```

一般是：

```
/usr/bin/python
```

同时看一下目前`/usr/bin`目录下的`python`文件有哪些：

```
cd /usr/bin
ls python*
```

结果一般都是：

```
python  python2  python2.7
```

这几个文件有一些链接关系：

```
[aman@localhost bin]$ ls -al python*
lrwxrwxrwx. 1 root root    7 4月  23 2019 python -> python2
lrwxrwxrwx. 1 root root    9 4月  23 2019 python2 -> python2.7
-rwxr-xr-x. 1 root root 7136 8月   4 2017 python2.7
```

这里的意思就是，系统在找`python`时，`python`指向了`python2`，而`python2`由指向了`python2.7`，所以我们接下来就是让系统在输入`python2`时就是找`python2`，而输入`python`时去找`python3`，这无非就是Linux的软链接技巧而已，下面我们正式开始：

##### 1、备份原来的python，以防万一 {#1-备份原来的python以防万一}

```
sudo mv python python.bak
```

> 其实，如果不备份也是没关系的，只要python2还在就行了，因为本来那个python就是一个软链接，可以通过下面命令使得python还原：

```
sudo ln -s /usr/bin/python2 /usr/bin/python
```

##### 2、我们在centos上的一个目录下下载python3的安装包，我这里在`/usr/local`下下载 {#2-我们在centos上的一个目录下下载python3的安装包我这里在usrlocal下下载}

```
cd /usr/local
sudo wget https://www.python.org/ftp/python/3.7.3/Python-3.7.3.tgz
```

##### 3、解压缩到当前文件夹 {#3-解压缩到当前文件夹}

```
sudo tar -xvf Python-3.7.3.tgz
```

##### 4、创建python3目录 {#4-创建python3目录}

```
sudo mkdir /usr/local/python3
```

##### 5、进入解压缩后的文件夹并编译 {#5-进入解压缩后的文件夹并编译}

```
cd /usr/local/Python-3.7.3

sudo ./configure --prefix=/usr/local/python3

sudo make

sudo make install
```

##### 6、建立python3的软链接到python {#6-建立python3的软链接到python}

```
sudo ln -s /usr/local/python3/bin/python3 /usr/bin/python
```

##### 7、验证一下 {#7-验证一下}

```
python
python2
yum
```

这时，你会发现yum用不了了，那是因为本来yum是基于python2的，我们把python改为python3.7了，而yum还没反应过来，所以我们要帮他一把：

```
sudo vi /usr/bin/yum
```

把第一行的：

```
#!/usr/bin/python
```

改为：

```
#!/usr/bin/python2.7
```

保存退出，再输入`yum`试一下？

那么，我们做了这么多，究竟是做了些什么呢？输入下面代码可以查看：

```
[aman@localhost bin]$ ls -al python*
lrwxrwxrwx. 1 root root   30 4月  23 12:00 python -> /usr/local/python3/bin/python3
lrwxrwxrwx. 1 root root    9 4月  23 2019 python2 -> python2.7
-rwxr-xr-x. 1 root root 7136 8月   4 2017 python2.7
lrwxrwxrwx. 1 root root    7 4月  23 2019 python.bak -> python2
```

##### 8、还有pip {#8-还有pip}

```
sudo ln -s /usr/local/python3/bin/pip3 /usr/bin/pip
```

其实我们这样用python3代替python2是不太对的，因为centos上有很多东西是基于python2的，比如上面说到的yum。

当然，像上面这样把yum的配置文件修改一下当然是能正常使用的，但依然会有其他一些我们还没接触到的问题，比如防火墙的访问，如果输入平常使用的查看防火墙命令：

```
firewall-cmd --list-all
```

会报错，说什么'gi'模块找不到，我们总不能因为这一点小挫折就把python3改为python2吧？

所以我们还是要尽量找到根源的解决办法，毕竟linux不是python2做出来的，无非又是修改一下它们所需python的路径而已：

```
sudo vim /usr/bin/firewall-cmd
```

将首部的python路径改为python2.7，同理：

```
sudo vim /usr/sbin/firewalld
```

同样是将头部的python改为python2.7，修改好后再次执行：

```
firewall=cmd --list-all
```

如果还不行，那就继续找原因，目前我遇到的最多问题也就到这里解决了。

