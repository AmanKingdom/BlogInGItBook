Linux系统之间远程连接的最基础的方式之一就是使用终端通过ssh协议连接，下面是个人使用本地Ubuntu系统远程连接云服务器centOS系统的过程：

### 如何连接： {#如何连接}

其实简单得很，就一句话：

```
ssh -p 22 root@服务器公网ip地址
```

但是如果报错说没有这个命令的话，请看下面的：

### 拓展补充1 {#拓展补充1}

首先，在本地Ubuntu安装ssh：

```
sudo apt-get install ssh
```

（如果本地的是centOS，则用yum install ssh就行了，或者不用安装也能直接用）

然后查看ssh服务有没有开启：

```
sudo ps -e | grep ssh
```

如果看到有sshd，则是已经开启服务了。

如果没有开启，则可以输入下面命令来开启：

```
service sshd start
```

接下来就可以登录远程服务器了：

```
ssh -p 22 root@服务器公网ip地址
```

这里的22是云服务器的端口号，一般都是22，root是云服务器的登录账号，@后面的是云服务器的公网地址，接下来输入云服务器系统账号的密码就可以登录到远程端了

### 拓展补充2 {#拓展补充2}

当然，也可以用更简单的命令来登录（但有些机器不一定能直接这样用，我还说不清）：

```
ssh 服务器公网ip地址
```

如果连接时出现警告：

```
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@    
@           WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!                 @   
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@   
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!                               
Someone could be eavesdropping on you right now (man-in-the-middle attack)!        
It is also possible that a host key has just been changed.                                         
The fingerprint for the ECDSA key sent by the remote host is                                    
SHA256:se31Y+/jTxiN1lnYJsZebYIssm+VqGMNpMBjiBRjQJs.                                    
Please contact your system administrator.                                                              
Add correct host key in /home/aman/.ssh/known_hosts to get rid of this message.    
Offending ECDSA key in /home/aman/.ssh/known_hosts:1                                       
  remove with:                                                                                                        
  ssh-keygen -f "/home/aman/.ssh/known_hosts" -R "47.107.115.111"                   
ECDSA host key for 47.107.115.111 has changed and you have requested strict 
checking.                                                                       
Host key verification failed.
```

或许这是因为第一次连接的原因，在本地生成了一个认证，存储在本地机器的known\_hosts中，查看用：

```
ssh-keygen -l -f ~/.ssh/known_hosts
```

输入下方命令即可解决：

```
ssh-keygen -R 服务器公网ip地址
```

（注意，这个命令是重新生成认证密钥的，以后再次连接则不需要加上 -R 这个选项）

连接：

```
ssh -p 22 root@服务器公网ip地址
```

或：

```
ssh 服务器公网ip地址
```



