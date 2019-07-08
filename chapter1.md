# 

# 怎么玩

有多种方式，这里介绍两种最简单的方式：

### 方式一：直接用 [GitBook 在线编辑](https://app.gitbook.com)

![](/assets/import.png)

这种方式太简单了，自行登录了解便可操作。但总体使用思路可以归结为如下：

1、创建账户登录进入

2、创建一个工作空间 workspace，也就是 gitbook 所在位置

3、编辑内容，编辑所见的内容就是发布后所得的内容

4、使用 GitBook 提供的网址访问该 gitbook

### 方式二：用 [GitBook 本地编辑器](https://legacy.gitbook.com/editor) （windows 版）

> 既然第一种方式那么简单了，那为什么还要第二种方式呢？其实是第一种方式加载速度太慢了，需要科学\#上网才好编辑，其实现在这种方式依然需要用到 [GitBook 在线编辑](https://app.gitbook.com) ，等下会说到。

**总体操作思路：**

1、下载 [GitBook 本地编辑器](https://legacy.gitbook.com/editor)

2、打开 GitBook 本地编辑器 创建一个 gitbook

3、编辑该 gitbook 的内容，保存后存储到 GitHub 仓库上

4、在 [GitBook 在线编辑](https://app.gitbook.com) 上新建一个 workspace，并设置从 GitHub 到 GitBook 的同步方式

5、通过 [GitBook 在线编辑](https://app.gitbook.com) 提供的网址访问该 gitbook

**具体操作：**

**1、先下载 **[**GitBook Editor**](https://legacy.gitbook.com/editor)

![](/assets/import2.png)

**2、电脑上打开 GitBook Editor，登录选项是可选的，我们先跳过，直入主题：创建一个 GitBook**

![](/assets/import3.png)

**默认创建的 GitBook 是保存在 **`C:\Users\用户名\GitBook\Library\Import`**目录下，如果你想改变 GitBook 存放的位置，可以选择下图所示位置的 **`change library path`** 把 GitBook 存放到你指定的位置：**

![](/assets/import4.png)

**3、编辑网站的内容：**

![](/assets/import5.png)

> 看不清楚吗？可以放大浏览器查看呢

**4、发布这个网站**

我们已经有页面内容了，想要在浏览器访问就需要开启 gitbook 服务器，这种方法可以参看我的下一篇教程，现在只说简单的，我们现在不想在本地或云服务器搭建 gitbook 服务器，那就直接在 [GitBook 在线编辑](https://app.gitbook.com) 中新建一个 gitbook 并与我们的 GitHub 仓库关联起来，操作入口在哪？请看下面：

4.1、找到 [GitBook 在线编辑 ](https://app.gitbook.com)的这个位置：

![](/assets/import6.png)

4.2、选择了 GitHub 之后，就可以选择关联 GitHub 仓库，记住，遇到下面图片所示的选项时，应选这个：从 GitHub 到 GitBook

![](/assets/import7.png)

4.3、关联好 GitHub 的 gitbook 仓库 和 GitBook 在线编辑 上的 gitbook 后，便可以在本地编辑 -&gt; 同步到 GitHub -&gt; 从 GitBook 在线编辑器设置的网站链接访问该 gitbook：

![](/assets/import8.png)

这里的链接后缀是可以改的，在这里改：

![](/assets/import9.png)

### **拓展**

细心的你可能会发现我这个笔记本的域名和你那里显示的不一样，这是因为我绑定了我的个人域名的子域名，如果你有域名的话，也可以在域名管理平台上像我这样设置：

> 本人域名：bee123.xyz
>
> 绑定子域名：aman.bee123.xyz

绑定方式：

![](/assets/import10.png)

然后在 [GitBook 在线编辑](https://app.gitbook.com) 上，修改这里：

![](/assets/import11.png)

这样，便可以通过该子域名访问这个 gitbook 了。

> 更多详细用法，请参照 [GitBook官方文档](https://docs.gitbook.com/)



