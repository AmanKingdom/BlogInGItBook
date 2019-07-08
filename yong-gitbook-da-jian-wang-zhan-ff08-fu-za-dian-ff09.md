> 在上一篇提到的都是寄托在 GitBook 上的 gitbook，如果我们想把 gitbook 源代码放到自己的服务器上应该怎么弄呢？

# 一、下载安装 [node.js](https://nodejs.org/en/)

![](/assets/import31.png)

# 二、安装 gitbook 命令行工具 gitbook-cli

```
npm install gitbook-cli -g
```

> 现在下载的 node.js 自带了 npm 工具包了，所以不需要另外下载 npm

# 三、创建 gitbook

在 cmd 下，我们创建一个目录来存放 gitbook 即可：

![](/assets/import32.png)

# 四、开启 gitbook 服务器，通过 URL 访问这个 gitbook

![](/assets/import33.png)

![](/assets/import34.png)

# 拓展

gitbook 还可以将该网站生成 html 文本，方便本地阅读，方法如下：

在 gitbook 目录下：

```
gitbook build
```

![](/assets/import35.png)

这时，gitbook 目录下多了一个 \_book 的文件夹，里面的文件就是静态的HTML页面了：

![](/assets/import37.png)

> 更多详细用法，请参照 [GitBook官方文档](https://docs.gitbook.com/)



