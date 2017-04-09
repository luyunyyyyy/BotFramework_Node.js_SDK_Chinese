# 翻译工作指导

本文将告诉大家如何在使用gitbook markdown来进行文档的协同翻译

### 先决条件
1. 拥有github账号
2. 掌握基础的git使用
3. 掌握基本的markdown语法


## 2. 基础的git的使用

没了解过git？  可以参考[怎样使用 GitHub？ -知乎](https://www.zhihu.com/question/20070065)

[git使用规范-阮一峰](http://www.ruanyifeng.com/blog/2015/08/git-use-process.html)
一个简明的使用git进行协作的教程

[git 远程操作详解-阮一峰](http://www.ruanyifeng.com/blog/2014/06/git_remote.html)
这个更加详细。

## 3. 基础markdown语法

[认识与入门Markdown-少数派](https://sspai.com/post/25137)
这个可以使你对markdown有初步的了解

**编辑器**：
可以使用vscode或者atom。并安装相关插件。

[用VS Code打造最佳Markdown编辑器](http://www.jianshu.com/p/18876655b452)——
至于这里面说的配置文件和字体之类的不用管。

[Atom与markdown](http://www.jianshu.com/p/ad3e737e5dc2) 一个有关在atom里编辑markdown的教程

如果下载atom比较慢的话，可以尝试在neu.sqwe.tk 上寻找安装包（校园内网访问不走流量）

**注意：**在vscode或者atom都会集成git的功能，可以不用输入命令来实现commit push pull等操作


以下将展示几个在我们的文档里面常用的语法：
### 1. 多级标题

`#` 一级
`##`二级
`###`三级

注意，#号后面要有一个空格
效果:
# 一级
## 二级
### 三级

### 2. 嵌入代码
在三个点后面可以加上语言，可以支持代码高亮

<pre><code>
``` javascript
 var builder = require('botbuilder');
```
</code></pre>
效果：

```javascript
 var builder = require('botbuilder');
```

### 3. 超链接

<code>
[超链接的名字](baidu.com)
</code>

圆括号里面是链接地址

效果：

[百度](baidu.com)

4. 图片
<pre>
  ![Alt text](https://docs.botframework.com/en-us/images/builder/builder-luis-default-app.png)
</pre>

括号里面是图片的链接

效果：

  ![Alt text](https://docs.botframework.com/en-us/images/builder/builder-luis-default-app.png)
