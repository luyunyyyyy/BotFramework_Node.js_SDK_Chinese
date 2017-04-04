# 自然语言理解 #
  - LUIS
  - 意图(Intents)、实体(Entites)和模型训练(Model Training)
  - 创建你的模型
  - 处理意图
  - 处理实体

## LUIS ##
  微软智能语言理解服务(Microsofts Language Understanding Intelligent Service)(LUIS)能为应用程序添加快速而高效的语言理解功能。有了 LUIS，你在任何适合你的想法的地方，运用由必应和小娜得到的，预存在的，世界级的，预编译的模型；并且，当你需要特殊的模型时，LUIS 能引导你快速地构建它们。

  LUIS的交互式机器学习和语言理解科技基于[微软研究院](http://research.microsoft.com/en-us/')和必应，包括了微软研究院的交互式概念学习平台(Microsoft Research’s Platform for Interactive Concept Learning)(PICL)。LUIS 也是微软的[牛津计划(Project Oxford)](https://www.projectoxford.ai/)的一部分。

  Bot Builder 让你能通过 [LuisDialog](https://docs.botframework.com/en-us/node/builder/chat/IntentDialog/)类，用 LUIS 添加自然语言理解功能。你能实现一个 LuisDialog 的实例来连接到你发布的语言模型的，然后能添加处理意图的句柄，以此来响应用户的对话。了解 LUIS 的实例请观看下面这十分钟的视频。
  - [Microsoft LUIS Tutorial ](https://vimeo.com/145499419)(视频)

## 意图、实体和模型训练
  人机交互的关键问题之一是电脑能否理解一个人的要求和能否找到对应满足意图的信息。举例来说，在一个新闻浏览app中，你会说“给我关于虚拟现实的公司的新闻”；这个例子中有“查找新闻”的意图和“虚拟现实的公司”这个主题。LUIS 就是设计来让你能非常快速地部署http后端，以此来：接受你发送的句子，并转释(原文为:interpret)它们表达的意图和出现的主要实体，如“虚拟现实的公司”。LUIS 让你能自定义设置符合你应用程序的意图和实体的集合，然后引导你构建一个语言理解系统。

  一旦你的程序部署好了、数据流也开始进入系统，LUIS 会主动地学习并完善自己。在主动学习过程中，LUIS 识别出相对不确定的交互并请求你根据意图和实体去标注它们。这样做能有诸多好处：首先，LUIS 知道哪些是不确定的，并在你所提供的帮助能最大限度地提高系统性能时，向你请求帮助。其次，当你专注于重要的事时，LUIS 能尽可能快地学习且尽可能少地占用你的时间。

## 创建你的模型
  为你的机器人添加自然语言支持的第一步便是创建你的 LUIS 模型。你要做的事是登录 LUIS 并为你的机器人创建一个新的 LUIS 应用。这个应用就是你用来添加意图和实体来让 LUIS 根据它们训练你的机器人的模型的。
  ![Alt text](https://docs.botframework.com/en-us/images/builder/builder-luis-create-app.png)
  为了创建新的应用，你需要选择是导入已有的模型(这是你想用 LUIS 做 Bot Builder 的示例应选的选项)，还是用预编译的Cortana应用。在本教程中，我们将创建一个基于预编译Cortana应用的机器人。当你选择了英语的预编译Cortana应用，你将会看到下面这样的对话框。

  你需要拷贝像这样列在对话框下的链接，这是用于绑定你的[LuisDialog](https://docs.botframework.com/en-us/node/builder/chat/IntentDialog/)类到这的链接。这个链接指向 LUIS 发布到你的机器人 LUIS 应用的模型，并会在你的应用的生命期内保持不变。所以一旦你已经训练好并发布你为了 LUIS 应用的模型，你就能甚至在不需要重新部署的情况下，升级和重新训练它。在你构建机器人早期，需要频繁重新训练模型的情况下，这会非常有用。

  ![Alt text](https://docs.botframework.com/en-us/images/builder/builder-luis-default-app.png)

## 处理意图 ##

## 处理实体 ##
