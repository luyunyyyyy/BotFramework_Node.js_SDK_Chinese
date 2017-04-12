# 教程 #
 - 概述
 - 安装
 - hello world
 - 收集输入
 - 添加对话和储存
 - 确定意图
 - 将bot发布到 Bot Framework Service
 - 使用ngrok进行本地调试

## 概述 ##
Bot Builder是一个用node.js来构建会话程序（Bots）的框架。它提供了所有管理一个bot的会话方面所需的全部特性，从最简单的基于命令的bot到拥有丰富的自然语言的bot。您可以轻松地将使用框架构建的机器人连接到用户，无论他们在什么地方交谈，从短信到Skype到Slack等等...

## 安装 ##
想要上手，你可以选择通过 NPM来安装Bot Builder 模块

也可以使用git从我们的GitHub 代码仓库clone。这种方法可能会比用 NPM好一点，因为它可以给你提供丰富的实例代码段和bot。


## Hello World ##
一但Bot Builder模块安装完成，我们就可以开始构建我们的第一个叫做HelloBot的"Hello World" bot。我们首先要决定的是我们想要构建哪一类bot。Bot Builder使你能为各种平台构建bot。但是对于我们将要构建的HelloBot，我们只需要通过命令行来与之交互，所以我们将建立一个绑定了一个控制台连接器（ConsoleConnector）实例的通用bot（UniversalBot）。

```javascript
var builder = require('botbuilder');

var connector = new builder.ConsoleConnector().listen();
var bot = new builder.UniversalBot(connector);
```
通用bot类实现了管理bot与用户的对话所需的全部逻辑。你可以使用连机器来把bot绑定到各种各样的频道。对于这个教程，我们将只需要通过命令行窗口来和bot交谈，所以，我们使用ConsoleConnector 类。将来，当你准备把你的bot部署到真实的频道，你将用ChatConnector来换掉ConsoleConnector，你将用你从Bot Framework管理界面获取到的你的bot的APP ID和Password来配置这个ChatConnector。

现在，我们建立了我们的bot和connector，我们需要给我们新建的bot对象添加一个对话。Botbuider将对话应用切分成叫做对话的组件。如果你想以构建一个web 应用的思路来构建一个会话应用，每个会话可以被认为是会话应用中的路由。当用户给你的bot发送一条消息的时候，框架将会追踪当前激活的是哪个会话，并自动地把收到的消息路由给激活的会话。对于我们的HelloBot，我们只需要添加单一的根(root)"/"会话来用"Hello World"来响应所有的消息。

```javascript
var builder = require('botbuilder');

var connector = new builder.ConsoleConnector().listen();
var bot = new builder.UniversalBot(connector);
bot.dialog('/', function (session) {
    session.send('Hello World');
});
```

我们可以运行我们的bot，并且用命令行与之交互。所以，运行bot并输入"hello":
```
node app.js
hello
Hello World
```

## 收集输入 ##
你可能想让你的bot变得比当前这个HelloBot更加智能。所以，让我们赋予HelloBot新的能力，使它能询问用户的名字然后给用户个性化的问候。要实现这一点，我们将介绍一个新的概念——瀑布，它可以询问用户一些信息，并等待用户的回答。

```javascript
var builder = require('botbuilder');

var connector = new builder.ConsoleConnector().listen();
var bot = new builder.UniversalBot(connector);
bot.dialog('/', [
    function (session) {
        builder.Prompts.text(session, 'Hi! What is your name?');
    },
    function (session, results) {
        session.send('Hello %s!', results.response);
    }
]);
```
通过传递一组函数给我们的会话处理程序，建立一个瀑布，第一个函数的结果被传递到第二个函数的输入。我们可以将的这些函数链接起来，创建任意长度的瀑布。

实际上，我们使用了一个SDK内置的（提示输入）prompts来等待用户的输入。我们使用的一个简单文本提示，它可以捕获用户当次输入的全部东西。此外，SDK还有各种各样的内置提示输入类型。如果我们运行我们更新过的HelloBot 我们可以看到我们的bot 问我们的名字，然后给我们一个个性化的问候。

## 增加会话和储存 ##
Bot Builder使你能把你的bot与用户之间的对话分成所谓的dialogs。你可以把dialogs串到一起来形成一个与用户之间的子对话来实现一些小任务。对于HelloBot 我们将增加一个新的 /profile dialog来引导用户填写他们的档案信息。这个信息需要被储存在某个地方，以便于，一方面我们可以使用`session.endDialog({ response: { name: ‘John' } })`将它作为我们的dialog的输出值返回给调用者。另一方面，也我们也可以使用SDK内置的储存系统来全局地储存它。在我们当前的情况下，对于一个用户，我们想要将这个信息全局地记住，我们就用`session.userData`来储存。这也给我们一个方便的方法来确定用户是否曾填写过档案，以决定我们是否需要让用户填写。

```javascript
var builder = require('botbuilder');

var connector = new builder.ConsoleConnector().listen();
var bot = new builder.UniversalBot(connector);
bot.dialog('/', [
    function (session, args, next) {
        if (!session.userData.name) {
            session.beginDialog('/profile');
        } else {
            next();
        }
    },
    function (session, results) {
        session.send('Hello %s!', session.userData.name);
    }
]);

bot.dialog('/profile', [
    function (session) {
        builder.Prompts.text(session, 'Hi! What is your name?');
    },
    function (session, results) {
        session.userData.name = results.response;
        session.endDialog();
    }
]);
```

现在，当我们运行HelloBot，他将提示我们输入我们的名字一次。然后，在下次和bot聊天的时候，它将记住我们的名字。
```
node app.js
hello
Hi! What is your name?
John
Hello John!
hi there
Hello John!
```

SDK包含以下几种方式来持久化与用户或者对话相关的数据。

 - userData 在所有对话中为用户全局地储存信息
 - conversationData 对一个单一的对话全局地储存信息。那些对于在这个对话中的所有用户都可见的数据应当被储存在这里。它默认不可用，需要使用bot 的 [persistConversaytionData](URL'https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.iuniversalbotsettings.html#persistconversationdata')设置来启用。
 - privateConversationData （私有对话数据）为一个单一的对话全局地储存信息，但只对当前用户可见。这个数据跨越全部dialog，所以，拿来储存临时的状态。当对话结束的时候就会被清除。
 - dialogData 维持一个单一的dialog实例中的信息。它对于在一个瀑布中的步骤之间储存临时信息是至关重要的。
 
 使用 Bot Builder 来构建 Bot 是无状态的，以至于可以轻松地将其拓展到多个计算节点运行。因此，通常，你应该避免尝试使用全局变量或者函数闭包来保存状态。否则，当你想拓展你的bot的时候将会出现问题。我们应该利用上面的数据容器来保存临时和永久的数据。

 现在，HelloBot 可以记住事情了。让我们来尝试使他更好地理解用户。

 ## 确定意图

 构建一个优秀的 Bot 的关键之一，就是能在用户让你的 Bot 做一些事情的时候，能有效地确定用户的意图。Bot Builder包含一个强大的IntentDialog 类，这个类被设计来辅助完成确定用户意图的任务。IntentDialog类使你能用两种技术的组合来确定用户的意图。你可以传一个正则表达式到[IntentDialog.matchs()](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.intentdialog.html#matches)这个函数，用户的消息将会与这个正则表达式进行比较。如果匹配的话，和这个正则表达式关联的处理程序将会被触发。

 正则表达式很好，但是对于更强大的意图识别，你可以通过[LIUS](https://www.luis.ai/)来使用机学习。只需要把[LuisRecognizer](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.luisrecognizer.html)加入到你的IntentDialog中（你可以在示例中到是怎么使用的）。IntentDialog也支持正则表达式和识别器插件的结合，它将使用评分法来识别哪个处理程序最有可能被触发。如果没有识别出意图或者得分低于一个[阈值](https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.iintentdialogoptions.html#intentthreshold),onDefault() dialog将会被触发。

 ```javascript
 var builder = require('botbuilder');

var connector = new builder.ConsoleConnector().listen();
var bot = new builder.UniversalBot(connector);
var intents = new builder.IntentDialog();
bot.dialog('/', intents);

intents.matches(/^change name/i, [
    function (session) {
        session.beginDialog('/profile');
    },
    function (session, results) {
        session.send('Ok... Changed your name to %s', session.userData.name);
    }
]);

intents.onDefault([
    function (session, args, next) {
        if (!session.userData.name) {
            session.beginDialog('/profile');
        } else {
            next();
        }
    },
    function (session, results) {
        session.send('Hello %s!', session.userData.name);
    }
]);

bot.dialog('/profile', [
    function (session) {
        builder.Prompts.text(session, 'Hi! What is your name?');
    },
    function (session, results) {
        session.userData.name = results.response;
        session.endDialog();
    }
]);
 ```

 我们的 Bot 已经更新为使用IntentDialog，它将寻找用户说的话里面有没有 “change name”。如你所见，这个意图的处理程序是另一个瀑布。这意味着你可以在给被触发的意图的回复里面以任何顺序执行的提示或者操作。我们把之前的dialog 瀑布移动到了意图dialog onDefault()的处理程序里面，否则仍然没有变化。现在，我们运行 HelloBot ，我们将获得如下的对话流。

 ```
 node app.js
hello
Hi! What is your name?
John
Hello John!
change name
Hi! What is your name?
Steve
Ok... Changed your name to Steve
Hi
Hello Steve!
 ```

 ## 发布到 Bot Framework 服务
 现在，我们拥有了一个功能完整的 HelloBot（它可以很好地问候用户）。我们应该把它发布到Bot Framework服务，以便于我们可以通过多种交流渠道来和它交谈。我们需要更新我们的 Bot ，使他使用一个正确配置的ChatConnector（连接器）：

 ```javascript
var builder = require('botbuilder');
var restify = require('restify');

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

var intents = new builder.IntentDialog();
bot.dialog('/', intents);

intents.matches(/^change name/i, [
    function (session) {
        session.beginDialog('/profile');
    },
    function (session, results) {
        session.send('Ok... Changed your name to %s', session.userData.name);
    }
]);

intents.onDefault([
    function (session, args, next) {
        if (!session.userData.name) {
            session.beginDialog('/profile');
        } else {
            next();
        }
    },
    function (session, results) {
        session.send('Hello %s!', session.userData.name);
    }
]);

bot.dialog('/profile', [
    function (session) {
        builder.Prompts.text(session, 'Hi! What is your name?');
    },
    function (session, results) {
        session.userData.name = results.response;
        session.endDialog();
    }
]);
 ```
 {
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Launch",
            "type": "node",
            "request": "launch",
            "program": "${workspaceRoot}/app.js",
            "stopOnEntry": false,
            "args": [],
            "cwd": "${workspaceRoot}",
            "preLaunchTask": null,
            "runtimeExecutable": null,
            "runtimeArgs": [
                "--nolazy"
            ],
            "env": {
                "NODE_ENV": "development",
                "MICROSOFT_APP_ID": "Your bots App ID",
                "MICROSOFT_APP_PASSWORD": "Your Bots Password"
            },
            "externalConsole": false,
            "sourceMaps": false,
            "outDir": null
        },  
我们现在更新过的 Bot 需要使用[Restify](http://restify.com/),所以我们需要安装一下。在我们的bot所在目录里面打开命令行，输入：

```
npm install --save restify
```

我们的 bot 有了一些新的配置代码，但所有dialog仍旧不变。想要完成转换，你需要在Bot Framework的开发者面板去注册你的bot，使用你的bot的应用 ID 和密码来配置ChatConnector。当你运行在本地时，你通过环境变量来将这些参数传递给你的bot。当你部署到云端之后，你可以通过托管站点的web配置来配置。

在你部署到云端之前，你可以使用[Bot Framework 模拟器](https://docs.botframework.com/en-us/tools/bot-framework-emulator/)在本地测试你的bot，以确保正确配置。保证你在模拟器里面设置的应用 ID 和密码都与你的bot所配置的相匹配。

## 使用ngrok进行本地调试

如果你在 MAC 电脑上运行，不能使用模拟器，或者是你想要调试一个部署时你发现的问题。你可以轻松地配置bot，使其通过[ngrok](https://ngrok.com/)在本地运行。

首先需要安装ngrok，然后在命令行窗口里面输入：
```
ngrok http 3978
```

这样将配置一个ngrok转发链接，来把请求转发到你运行在3978端口上的bot那里去。你还需要在你的Bot Framework开发者面板中将转发链接设置为注册端点。配置之后，端点应该像这样——`https://0d6c4024.ngrok.io/api/messages`。别忘了链接末尾包含的`/api/messages`。

你现在可以在调试模式运行你的bot了。如果你使用的是[VScode](https://code.visualstudio.com/),你需要将应用 ID 和密码配置到启动 `env` 里面。

```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Launch",
            "type": "node",
            "request": "launch",
            "program": "${workspaceRoot}/app.js",
            "stopOnEntry": false,
            "args": [],
            "cwd": "${workspaceRoot}",
            "preLaunchTask": null,
            "runtimeExecutable": null,
            "runtimeArgs": [
                "--nolazy"
            ],
            "env": {
                "NODE_ENV": "development",
                "MICROSOFT_APP_ID": "Your bots App ID",
                "MICROSOFT_APP_PASSWORD": "Your Bots Password"
            },
            "externalConsole": false,
            "sourceMaps": false,
            "outDir": null
        },  
```

配置 `.vscode/launch.json` 文件，按下启动，你就可以开始调试了。