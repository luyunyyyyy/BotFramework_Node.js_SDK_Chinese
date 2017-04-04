# 你的第一个bot——botframework Node.js SDK快速入门#
---


## Node.js 版的Bot builder 是什么，为什么要用它##

Nodejs版的BotBuilder 是一个强大的框架，用来构建可以处理自由的交互和更多的引导，并向用户返回可能的结果的机器人。它很容易使用。像Express和Restify这样的模型框架提供给开发者一个熟悉的方式来写聊天机器人。

高级特性：

- 强大的对话系统：拥有单一和组合的对话
- 内置的提示，可以提示用户回答是/否、字符串、数字、时间或者日期、从列表中选择、上传照片或者视频。
- 内置的对话（dialog），可以利用像Luis这样的强大的 AI框架。
- Bots 是无状态的，可以迅速扩展规模。
- Bots可以运行在绝大多数平台，例如Skype、Slack、telegram等等。

## 构建一个bot ##
为你的bot新建一个文件夹，进入文件夹，然后，运行 `npm init`

使用npm获取Bot Builder 和Restify模块：

新建一个app.js 文件，用下面的几行代码say hello
```javascript
var restify = require('restify');
var builder = require('botbuilder');

//=========================================================
// Bot Setup
//=========================================================

// Setup Restify Server
var server = restify.createServer();
server.listen(process.env.port || process.env.PORT || 3978, function () {
   console.log('%s listening to %s', server.name, server.url);
});

// Create chat bot
var connector = new builder.ChatConnector({
    appId: process.env.MICROSOFT_APP_ID,
    appPassword: process.env.MICROSOFT_APP_PASSWORD
});
var bot = new builder.UniversalBot(connector);
server.post('/api/messages', connector.listen());

//=========================================================
// Bots Dialogs
//=========================================================

bot.dialog('/', function (session) {
    session.send("Hello World");
});
```

## 测试你的bot ##
在本地主机使用 [bot framework 模拟器](URL'https://docs.botframework.com/en-us/tools/bot-framework-emulator/')来测试你的bot

从[此处](URL'https://aka.ms/bf-bc-emulator')安装模拟器。然后，在控制台窗口启动你的bot

```sh
node app.js
```

启动模拟器，对你的bot说声hello。

## 发布你的bot ##
把你的bot部署到云上，然后在 Microsoft Bot Framework [注册](URL'https://docs.botframework.com/en-us/csharp/builder/sdkreference/gettingstarted.html#registering')它。如果你要把你的bot部署到Microsoft Azure，可以看这个教程——[使用持续集成将一个Node.js 应用发布到Azure](URL'https://blogs.msdn.microsoft.com/sarahsays/2015/08/31/building-your-first-node-js-app-and-publishing-to-azure/').

注意：
当你在Bot Framework注册你的bot之后，bot管理界面会给你分配的应用id（appId）和应用密码（appSecret），你需要把你的bot以及模拟器中的这两个值更新。

## 深入了解 ##
学习如何构建一个强大的bot：

 - 核心概念指南
 - Chat SDK参考
 - Calling SDK 参考
 - GitHub上的Bot Builder
