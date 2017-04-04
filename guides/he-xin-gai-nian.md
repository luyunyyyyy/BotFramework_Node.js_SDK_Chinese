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

<pre><code>
var builder = require('botbuilder');

var connector = new builder.ConsoleConnector().listen();
var bot = new builder.UniversalBot(connector);
</code></pre>
通用bot类实现了管理bot与用户的对话所需的全部逻辑。你可以使用连机器来把bot绑定到各种各样的频道。对于这个教程，我们将只需要通过命令行窗口来和bot交谈，所以，我们使用ConsoleConnector 类。将来，当你准备把你的bot部署到真实的频道，你将用ChatConnector来换掉ConsoleConnector，你将用你从Bot Framework管理界面获取到的你的bot的APP ID和Password来配置这个ChatConnector。

现在，我们建立了我们的bot和connector，我们需要给我们新建的bot对象添加一个对话。Botbuider将对话应用切分成叫做对话的组件。如果你想以构建一个web 应用的思路来构建一个会话应用，每个会话可以被认为是会话应用中的路由。当用户给你的bot发送一条消息的时候，框架将会追踪当前激活的是哪个会话，并自动地把收到的消息路由给激活的会话。对于我们的HelloBot，我们只需要添加单一的根(root)"/"会话来用"Hello World"来响应所有的消息。

```js
var builder = require('botbuilder');

var connector = new builder.ConsoleConnector().listen();
var bot = new builder.UniversalBot(connector);
bot.dialog('/', function (session) {
    session.send('Hello World');
});
```
我们可以运行我们的bot，并且用命令行与之交互。所以，运行bot并输入"hello":
<pre><code>node app.js
hello
Hello World
</code></pre>

## 收集输入 ##
你可能想让你的bot变得比当前这个He'llBot更加智能。所以，让我们赋予HelloBot新的能力，使它能询问用户的名字然后给用户个性化的问候。要实现这一点，我们将介绍一个新的概念——瀑布，它可以询问用户一些信息，并等待用户的回答。

<pre><code>
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
</code></pre>
通过传递一组函数给我们的会话处理程序，建立一个瀑布，第一个函数的结果被传递到第二个函数的输入。我们可以将的这些函数链接起来，创建任意长度的瀑布。

实际上，我们使用了一个SDK内置的（提示输入）prompts来等待用户的输入。我们使用的一个简单文本提示，它可以捕获用户当次输入的全部东西。此外，SDK还有各种各样的内置提示输入类型。如果我们运行我们更新过的HelloBot 我们可以看到我们的bot 问我们的名字，然后给我们一个个性化的问候。

## 增加会话和储存 ##
Bot Builder使你能把你的bot与用户之间的对话分成所谓的dialogs。你可以把dialogs串到一起来形成一个与用户之间的子对话来实现一些小任务。对于HelloBot 我们将增加一个新的 /profile dialog来引导用户填写他们的档案信息。这个信息需要被储存在某个地方，以便于，一方面我们可以使用`session.endDialog({ response: { name: ‘John' } })`将它作为我们的dialog的输出值返回给调用者。另一方面，也我们也可以使用SDK内置的储存系统来全局地储存它。在我们当前的情况下，对于一个用户，我们想要将这个信息全局地记住，我们就用`session.userData`来储存。这也给我们一个方便的方法来确定用户是否曾填写过档案，以决定我们是否需要让用户填写。

<pre><code>var builder = require('botbuilder');

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
</code></pre>

现在，当我们运行HelloBot，他将提示我们输入我们的名字一次。然后，在下次和bot聊天的时候，它将记住我们的名字。
<pre><code>
node app.js
hello
Hi! What is your name?
John
Hello John!
hi there
Hello John!
</code></pre>

SDK包含以下几种方式来持久化与用户或者对话相关的数据。

 - userData 在所有对话中为用户全局地储存信息
 - conversationData 对一个单一的对话全局地储存信息。那些对于在这个对话中的所有用户都可见的数据应当被储存在这里。它默认不可用，需要使用bot 的 [persistConversaytionData](URL'https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.iuniversalbotsettings.html#persistconversationdata')设置来启用。
 - privateConversationData （私有对话数据）为一个单一的对话全局地储存信息，但只对当前用户可见。这个数据跨越全部dialog，所以，拿来储存临时的状态。当对话结束的时候就会被清除。
 - dialogData 维持一个单一的dialog实例中的信息。它对于在一个瀑布中的步骤之间储存临时信息是至关重要的。

