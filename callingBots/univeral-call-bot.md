# 通用CallBot

+ Overview
+ 安装
+ Hello World
  + 设置 ngrok
  + 注册你的bot
  + 配置你的bot
  + 添加的你bot到通讯录
  + 测试你的 bot
+ Calling 的tips







### Overview

skype 提供了一个叫  [Calling Bots](https://docs.botframework.com/en-us/skype/calling/) 的功能，当开启这个功能时，用户可以发送一段语音给你的

bot并且可以使用交互式声音回复（Interactive Voice Response - IRV）和bot互动。Bot Builder 

for Node.js SDK包括一个特殊的[Calling SDK](https://docs.botframework.com/en-us/node/builder/calling-reference/modules/_botbuilder_d_) ，开发者可以使用该SDK来给他们的bot来添加

Calling特性。



从架构上来说CallingSDK和[Chat SDK](https://docs.botframework.com/en-us/node/builder/chat-reference/modules/_botbuilder_d_) 非常相似。他们有很多相似的类以及一些共有的构建模块

（比如说瀑布流[waterfalls](https://docs.botframework.com/en-us/node/builder/chat/dialogs/#waterfall)）你甚至可以通过Chat SDK 给正在和bot互动的用户发送一条信息。

即使两个SDK看起来很类似，但从设计意图上来说他们是互补的。这两个库有一些很明显的区别

你需要避免将他们弄混。





### 安装

可以使用`npm`来安装 Bot Builder 模块

```
npm install --save botbuilder-calling
```



或者使用`git`从github代码仓库克隆到本地。我们推荐你使用这种方法。

代码仓库中有很多提供的实例代码片段和一个可以运行的`demo-skype-calling`bot 

```
git clone https://github.com/Microsoft/BotBuilder.git
cd BotBuilder/Node
npm install
```



### Hello World

Calling Bot的Hello World 和 Chat Bot 的[HelloWorld](https://docs.botframework.com/en-us/node/builder/guides/core-concepts/#hello-world)很相似

```javascript
var restify = require('restify');
var calling = require('botbuilder-calling');

// 设置 Restify 服务器
var server = restify.createServer();
server.listen(process.env.port || process.env.PORT || 3978, function () {
   console.log('%s listening to %s', server.name, server.url); 
});

// 创建 calling bot
var connector = new calling.CallConnector({
    callbackUrl: 'https://<your host>/api/calls',
    appId: '<your bots app id>',
    appPassword: '<your bots app password>'
});
var bot = new calling.UniversalCallBot(connector);
server.post('/api/calls', connector.listen());

// 添加根对话
bot.dialog('/', function (session) {
    session.send('Watson... come here!');
});
```



模拟器目前还不支持测试Calling Bot，所以你应该自己通过发布流程发布你的bot来测试它。

在开发的时候你可以使用像 [ngrok](https://ngrok.com/) 的工具来在本地运行你的bot，但是你需要skype客户端来和

bot互动并测试。



##### 设置 ngrok

按照这个[教程](https://docs.botframework.com/en-us/node/builder/guides/core-concepts/#debugging-locally-using-ngrok)来在你的机器上安装ngrok并配置调试环境





##### 注册你的bot

这个[教程](https://docs.botframework.com/en-us/directory/publishing/)会告诉你一些关于注册你的bot 以及开放skype 频道的事情。

当你在 [developer portal](http://www.botframework.com/)  注册你的bot的时候，你需要一个消息的接收端。

====

we typically recommend that you pair your calling bot with a chat bot so the chat bot’s endpoint is what you would normally put in that field. If you’re only registering a calling bot you can simply paste your calling endpoint into that field.

====

为了开启Calling功能，你需要进入到你的bot的Skype 频道中，打开Calling 功能。之后你会获得

一个输入框来粘贴你的Calling 端。

保证你使用https ngrok 链接在你主机的部分

====

To enable the actual calling feature you’ll need to go into the skype channel for your bot and turn on the calling feature. You’ll then be provided with a field to copy your calling endpoint into. Make sure you use the https ngrok link for the host portion of your calling endpoint.

=====

##### 配置你的bot

在注册你的bot 时你会得到一个app ID 和密码，你需要将他们复制到你bot的connecter 设置中。你会得到一个完整的调用链接，把这个链接复制到callbackUrl设置项上面。





##### 添加 bot到通讯录

在developer portal网站你的bot注册页面上，你会在bots skype channel 边上看到一个

`add to skype `按钮 点击这个链接可以把你的bot添加到skype的通讯录列表中。你点过之

后你就可以和bot通话了（当然任何你发送了邀请链接并点击的人都可以通话）





##### 测试你的bot

你可以使用一个skype 客户端来测试你的bot。你需要注意，当你点击你bot的联系入口时（你可

能搜索bot才会看到），call的图标应该是亮起的。如果你之前已经添加call到一个已经存在的

bot，有时需要一会儿来等call的图标亮起。



当你点击call按钮，会自动拨号到你的bot 然后你会听到bot说

“Watson… come here!”（借用电话发明后的第一次通话内容） 

之后挂断







### Calling的tips

在你编写Calling bot的时候 [UniversalCallBot](https://docs.botframework.com/en-us/node/builder/calling-reference/classes/_botbuilder_d_.universalcallbot) 和 [CallConnector](https://docs.botframework.com/en-us/node/builder/calling-reference/classes/_botbuilder_d_.callconnector) 这两个类起到的作用与你在编

写Chat bot时大致相同。添加对话的方式完全相同。在你给你的Bot添加瀑布流（[waterfalls](https://docs.botframework.com/en-us/node/builder/chat/dialogs/#waterfall)）

的时候你获得session对象的步骤与Chat Bot中相同，但是你获得的session对象是一个 

[CallSession](https://docs.botframework.com/en-us/node/builder/calling-reference/classes/_botbuilder_d_.callsession) 类型的实例。这个类型添加了一些用来管理当前call的方法，包括 [answer()](https://docs.botframework.com/en-us/node/builder/calling-reference/classes/_botbuilder_d_.callsession#answer), 

[hangup()](https://docs.botframework.com/en-us/node/builder/calling-reference/classes/_botbuilder_d_.callsession#hangup), 和 [reject() ](https://docs.botframework.com/en-us/node/builder/calling-reference/classes/_botbuilder_d_.callsession#reject) 。大多数情况下你不需要考虑这些控制call的方法，因为CallSession有内

建的逻辑来帮助你管理当前call。当对方进行发送信息或者触发内建指令等动作时，session会自

动回答问题。在你调用[endConversation()](https://docs.botframework.com/en-us/node/builder/calling-reference/classes/_botbuilder_d_.callsession#endconversation)  或者session检测到你已经停止向call bot 提问时，

它会自动 挂起/中断 call 会话。



Calling Bot 与 Chat Bot 另外一个不同的地方是，Chat Bot 只会发消息，卡片，以及键盘信息给

用户。Calling bot 则处理 动作和结果（[Actions and Outcomes](https://docs.botframework.com/en-us/skype/calling/#actions-and-outcomes) ）。Skype的Calling bot 需要

创建由一个或多个动作（ [actions](https://docs.botframework.com/en-us/node/builder/calling-reference/interfaces/_botbuilder_d_.iaction).）构成的工作流（[workflows](https://docs.botframework.com/en-us/node/builder/calling-reference/interfaces/_botbuilder_d_.iworkflow)）。当然实际上 Bot Builder 

Calling SDK 已经帮你做好大部分事情了。 [CallSession.send()](https://docs.botframework.com/en-us/node/builder/calling-reference/classes/_botbuilder_d_.callsession#send) 方法可以把你发送的动作或者字

符串转化成 [PlayPromptActions](https://docs.botframework.com/en-us/node/builder/calling-reference/classes/_botbuilder_d_.playpromptaction) 。session 包含自动匹配逻辑，来将多个动作绑定到一个单独的

工作流上。这个单独的工作流会被发送到Calling服务，所以你可以安全的调用send()很多次。

你需要依赖SDK的内建指令来收集用户的输入。因为你需要这些输入来得到结果。

