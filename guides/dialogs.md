# 交流系统 #
- 概述
- 对话的执行者
 - 瀑布
 - 闭包
 - 对话对象
 - 简单会话

##　概述　##
Bot Builder 使用对话系统来完成人机之间的沟通。要理解“对话”，就把它们想象成等价于网络的路由一样简单。每个机器人都会有至少一个根对话，就如同每个网站往往都会有至少一个的根路由一样。当farmework从使用者中收到一条信息，它就会在这些路径中寻找如何进行下去。对于一些机器来说，一个简单的根对话就已经够了，但就像有些网站也拥有多条路径一样，这些机器也常需要更多的对话系统。
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
上面的例子展示了一个拥有两个对话系统的bot。第一条从用户接收到的信息被路由到根对话中的对话执行者中。这个功能通过一个能检查使用者发出信息的会话控制对象，给用户回馈一个信息，代表用户保存这一阶段，或者更改到另一个对话中。

当使用者和我们的bot开启一段对话时，我们会通过检查session.userData 的属性来查看我们是否知道用户的名字。这个数据可以被bot在与用户的互动中一直保持着，并且可以被储存作类似与用户的个人存储数据。如果我们不知道用户的名字，我们会更改bot的路径到“个人数据”对话中通过session.beginDialog()去询问用户的名字。

这个“个人数据”对话以瀑布的形式执行，当beginDialog()被发起时，瀑布的第一步会被立刻执行。这一步简单地调用Prompts.text()来询问用户的名字。这个内置的提示只是另一种用来执行的对话。Framework 持有很多针对各种对话的对话堆，所以如果我们要在这种情况下检查我们的bot的对话堆，他看起来就会像是 [‘/’, ‘/profile’, ‘BotBuilder:Prompts’]。这个针对各种对话的对话堆可以帮助framework了解该以什么方式应答用户。

当用户用他们的名字来应答，提示会用用户的回答来调用session.endDialogWithResult()。这个回答会被作为参数来传到根对话瀑布的第二步。在这一步我们要将用户的名字储存到session.userData.name属性并返回控制回根对话通过一个调用到endDialog()。在这里，根对话瀑布的下一步会被执行增加一个例行招呼发给用户。

内置提示使用户在说像'nevermind'或者'cancel'之类的就会取消执行是没有价值的。它取决于调用提示来决定取消的意思的对话来察觉提示被取消，你也可以检查 ResumeReason 代码返回到 result.resumed或者简单的检查result.response不是空文件。实际上有很多导致提示在返回时没有响应的原因，所以检查是否有空响应倾向于一个最好的办法。在我们的事例bot中，用户在被问到名字时如果回答'nevermind'，bot则会重复询问他们的名字。

## 对话的执行者 ##
一个智能对话可以被表达用于很多的表单
### 瀑布 ###
瀑布可能是你使用的最常见的对话的表单，所以了解它们如何工作是智能发展中的基本功。瀑布会让你履行一系列的步骤来从用户中获得输入。一个bot经常处于一个向用户提供信息或者问一个问题的状态然后等待输入。在Bot Builder的Node版本中，它的瀑布能驱动这个back-n-forth流量。

和内置的提示配合，你可以轻松地用一些问题来提示用户：
```javascript
bot.dialog('/', [
    function (session) {
        builder.Prompts.text(session, 'Hi! What is your name?');
    },
    function (session, results) {
        session.send('Hello %s!', results.response);
    }
]);
```
Bots 以Bot Builder为基础执行一些我们称作“引导会话”的东西，意思是bot通常会驱动（或引导）用户进行对话。通过瀑布你可以采取某种措施来驱动对话从而使瀑布移动到下一阶段。调用一个内置提示像Prompts.text()来独自推动对话因为用户对提示的反应被传到瀑布的下一阶段的输入。你也可以调用[session.beginDialog())]来开始你的对话之一来推动会话到下一步：
```javascript
bot.dialog('/', [
    function (session) {
        session.beginDialog('/askName');
    },
    function (session, results) {
        session.send('Hello %s!', results.response);
    }
]);
bot.dialog('/askName', [
    function (session) {
        builder.Prompts.text(session, 'Hi! What is your name?');
    },
    function (session, results) {
        session.endDialogWithResult(results);
    }
]);
```
这实现了像前面的相同的基础操作但调用了一个子对话来提示输入用户名字。这在这个例子中多少是有点无意义的，但如果你有多个想要填入的轮廓域，这可以是一个有效的方式来分割对话。

这个例子实际上可以被简化一部分。所有的瀑布都包含有一个能自动返回最后一步的结果的幻像，因此我们可以将其简化成：
```javascript
bot.dialog('/', [
    function (session) {
        session.beginDialog('/askName');
    },
    function (session, results) {
        session.send('Hello %s!', results.response);
    }
]);
bot.dialog('/askName', [
    function (session) {
        builder.Prompts.text(session, 'Hi! What is your name?');
    }
]);
```
瀑布的第一步可以收到传到对话的参数，并且每一步都会收到一个可以促进瀑布人工地向前的next()函数。在这个例子中，在我们已经配合这两个特征之下来创造一个'/ensureProfile'会话来核实用户预置文件已经被填入并为用户打出丢失的域。这个模式会让我们在之后添加域到配置文件。这样就可以自动地填入用户给bot发送的信息:
```javascript
bot.dialog('/', [
    function (session) {
        session.beginDialog('/ensureProfile', session.userData.profile);
    },
    function (session, results) {
        session.userData.profile = results.response;
        session.send('Hello %(name)s! I love %(company)s!', session.userData.profile);
    }
]);
bot.dialog('/ensureProfile', [
    function (session, args, next) {
        session.dialogData.profile = args || {};
        if (!session.dialogData.profile.name) {
            builder.Prompts.text(session, "What's your name?");
        } else {
            next();
        }
    },
    function (session, results, next) {
        if (results.response) {
            session.dialogData.profile.name = results.response;
        }
        if (!session.dialogData.profile.company) {
            builder.Prompts.text(session, "What company do you work for?");
        } else {
            next();
        }
    },
    function (session, results) {
        if (results.response) {
            session.dialogData.profile.company = results.response;
        }
        session.endDialogWithResult({ response: session.dialogData.profile });
    }
]);
```
在'/ensureProfile'会话中我们使用 session.dialogData来临时保存用户的配置文件。我们执行这个是因为当我们的bot被分布在许多计算节点时，每一步的瀑布都可能被不同的计算节点加工。你可以在这里储存任何你想储存的东西，但是你应该将自己局限于JS的基元来被适当地系列化。

当next()函数被传送一个 [IDialogResult](idialogresult)时没有任何价值，所以它可以模拟任何一个从内置命令或者有时简化你bot的逻辑的会话中返回的结果。
### 闭包 ###

你也可以发送一个单个的函数给你的简单地导致去创造一个一步瀑布的会话执行者：
bot.dialog('/', function (session) {
    session.send("Hello World");
});

### 对话对象 ###
针对更多的专业对话，你可以添加一个源于会话基本类的一个事例。这给你的bot起到内置提示作用提供最大灵活度，甚至瀑布也会被内部的执行成会话。
```javascript
bot.dialog('/', new builder.IntentDialog()
    .matches(/^hello/i, function (session) {
        session.send("Hi there!");
    })
    .onDefault(function (session) {
        session.send("I didn't understand. Say hello to me!");
    }));
```
### 简单会话 ###
从头执行一个新会话和有很多事情去考虑一样棘手。要尝试去覆盖没有被瀑布所覆盖的预案的体积，我们要文件包含一个简单会话的类别。闭包传到这个类别的工作和一个瀑布的一步和除了由调用内置命令或者其他会话所造成的传回一个闭包十分相似。不像瀑布，这里没有提前会话的幻像步。这很有效但也十分危险，因为你的bot会很容易陷入死循环。所以要注意使用：
```javascript
bot.dialog('/', new builder.SimpleDialog(function (session, results) {
    if (results && results.response) {
        session.send(results.response.toString('base64'));
    }
    builder.Prompts.text(session, "What would you like to base64 encode?");
}));
```
上面的例子是一个基64编码bot，它做的一切就是把用户的输入转成基64编码。它代理一个打印用户输入后翻译成的编码的紧凑循环。

