1、进入/etc/sysconfig/networ k-scripts/目录：

```
cd /etc/sysconfig/network-scripts

```

2、查看网卡配置文件的文件名：

```
ls
```

一般有两个ifcfg文件，一个是ifcfg-lo，另一个是尾部带有一串数字的，不同系统不一样，但都是差不多的内容：

```
$ ls
ifcfg-ens33  ifdown-ppp       ifup-ib      ifup-Team
ifcfg-lo     ifdown-routes    ifup-ippp    ifup-TeamPort
ifdown       ifdown-sit       ifup-ipv6    ifup-tunnel
ifdown-bnep  ifdown-Team      ifup-isdn    ifup-wireless
ifdown-eth   ifdown-TeamPort  ifup-plip    init.ipv6-global
ifdown-ib    ifdown-tunnel    ifup-plusb   network-functions
ifdown-ippp  ifup             ifup-post    network-functions-ipv6
ifdown-ipv6  ifup-aliases     ifup-ppp
ifdown-isdn  ifup-bnep        ifup-routes
ifdown-post  ifup-eth         ifup-sit

```

本人系统的是`ifcfg-ens33`这个文件，我们需要编辑这个文件

3、编辑`ifcfg-ens33`，把最后一行的`ONBOOT=no`改为`ONBOOT=yes`即可：

```
vi ifcfg-ens33
```

或

```
sudo vi ifcfg-ens33
```

4、重启网卡服务

```
service network restart
```

就可以上网啦~

> 为初级菜鸟补充本文涉及的vi编辑器的使用： 用vi编辑器打开文件后，把光标移到要修改的位置，按`a`就是在光标后面添加文本的意思，输入`a`之后就可以编辑了，编辑完成按`ecs`离开`a`编辑状态，输入`:wq`就可以保存离开了，不想保存就按`:q`。



