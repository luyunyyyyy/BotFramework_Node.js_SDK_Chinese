#UniversalBot
* 回顾
* Connectors
   * chatconnector
   * consoleconnector
* proactive messaging
   * 存储用户地址
   * 发送消息
   * 开始对话
   * Proactive Messaging 和本地化
    
# Overview
  UniversalBot类是机器人的大脑。它负责管理机器人与用户的所有对话。在初始化机器人的时候，连接器会将你的机器人与Bot Framework或者与控制台连接。接下来，你就可以为你的机器人配置上拥有实现真实的对话的逻辑啦~~\(≧▽≦)/~

> 小贴士 (⊙v⊙)：
> 对于那些Bot Builder v1.x的用户， the BotConnectorBot, TextBot, and SkypeBot类已经过时啦~
> 虽然他们在多数情况下还是能运行，但是鼓励开发者们在方便的时候去使用新的UniversalBot
> 类哦！古老的SlackBot已经被从SDK中移出，并且很不幸的是，在这种时候唯一的选择是为开发者们把他们的 Slack bots移到Bot Framework。

# Connectors
 UniversalBot 支持一种可拓展的connector系统，可以让你的机器人实现接收信息&事件以及各种消息来源。创造性地，Bot Builder 包括了一个能连接到Bot Framework的ChatConnector以及一个用于与console window的bot交互的ConsoleConnector.
## ChatConnector
 ChatConnector 使得UniversalBot能与模拟器或者任意一个由BotFramework支持的channels交流。以下是一个配置了ChatConnector的“hello world”机器人：
```
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

   appld&appPassword设置通常是必须的，且会在developer portal注册机器人时形成。此规则 的唯一的例外是在本地运行模拟器。当你初次设计你的机器人时，你可以在模拟器中将“Microsoft App Id” & “Microsoft App Password”设置成空白，安全性在不能再机器人和模拟器上有效执行。当配置时，这些值都要求有正确的操作。

   此处的例子表示从环境变量中检索其 appId & appPassword设置。这使得机器人能在配置到像Microsoft Azure这种虚拟主机时，能将这些值存储在一个配置文件中。当你在本地测试机器人的安全设置时，你需要在用来运行机器人的控制台窗口中手动地设施环境变量，或者如果你使用的是VSCode，你可以把他们加进你的 launch.json 文件的"env"部分。

  # ConsoleConnector
   控制台连接器使得机器人能通过控制台窗口与人交互。这种连接器在你需要快速测试或者在Mac上测试时（即当你不能方便的运行模拟器的情况下）十分有用。以下是一个"hello world"机器人配置使用该连接器的例子：
  ```
   var builder = require('botbuilder');

var connector = new builder.ConsoleConnector().listen();
var bot = new builder.UniversalBot(connector);
bot.dialog('/', function (session) {
   session.send('Hello World'); 
}); 
```
 
   如果你是使用VSCode进行debug，那么你要对你的机器人以类似    
   >  --debug-brk app.js
   节点的命令开始，然后你再使用附加模式开启调试器。

  # Provactive Messging
   SDK支持两种信息传递模式。
   * Reactive Messages
   是指机器人用以回复从用户那收到的信息的一类信息。 这是一类最常见的信息传递模式，可以使用session.send()方法实现。
   * Proactive Messages
   是一类机器人用以回应某些外部事件的信息，就像timer firing或一个被触发的通知。这类信息的应用有：比如告知你项目已经装运，或者提醒你有一个关于团队成员状态的日常调查。

  ## 存储用户地址
   UniversalBot类提供 bot.send() 和 bot.beginDialog() 两种方法来与用户积极的交流。在你使用任意一种方法之前，你需要将用户的地址存下来。你可以通过序列session.message.address属性到你将会用到的字符串上：
  ```
 bot.dialog('/createSubscription', function (session, args) {
    // Serialize users address to a string.
    var address = JSON.stringify(session.message.address);

    // Save subscription with address to storage.
    session.sendTyping();
    createSubscription(args.userId, address, function (err) {
        // Notify the user of success or failure and end the dialog.
        var reply = err ? 'unable to create subscription.' : 'subscription created';
        session.endDialog(reply);
    }); 
  }); 
  ```
   你不应该假设给用户的address对象总会是有效的。特别是由ChatConnector返回的Address包含一个servicelUrl属性,理论上会改变和防止机器人接触用户。因此，你应该考虑定期更新为用户存储的地址对象。

  ## 发送消息
    要主动的发消息给用户，你需要添加web hook或者其他的逻辑可以触发主动通知。在下面的例子中，我们将会给机器人添加一个web hook，使得机器人能够发送一个通知消息给用户：
``` 
server.post('/api/notify', function (req, res) {
    // Process posted notification
    var address = JSON.parse(req.body.address);
    var notification = req.body.notification;

    // Send notification as a proactive message
    var msg = new builder.Message()
        .address(address)
        .text(notification);
    bot.send(msg, function (err) {
        // Return success/failure
        res.status(err ? 500 : 200);
        res.end();
    });});
``` 
   在web hook中，可以将之前存下来的用户的address反序列化。然后呢，程序将会合成信息，并使用bot.send()发送出去。我们可以选择提供一个callback去判断信息是否成功发送。

  ## 开始对话
   除了主动发送信息，我们可以使用bot.beginbialog()去开始一个新对话。bot.sen()和bot.beginDialog()是十分微妙的。使用bot.send()不会影响机器人和用户之间存在的任何一个对话，所以使用起来比较安全。而使用bot.beginDialog()会结束即存对话，并且在这个指定的对话框中开始一段新的对话。一般来说，如果对话不需要用户的回复，我们应该使用bot.send()，否则就使用bot.beginDialog()。
   开始主动对话和发送主动对话很相似。以下例子我们将使用web hook引起一段面向多个团队成员的standup对话。在web hook中我们仅遍历完所有的成员，并调用能带有每个成员地址的bot.beginDialog()。机器人将询问成员的状态，并将他们的状态放进a dailt status report：
   ```
   server.post('/api/standup', function (req, res) {
    // Get list of team members to run a standup with.
    var members = req.body.members;
    var reportId = req.body.reportId;
    for (var i = 0; i < members.length; i++) {
        // Start standup for the specified team member
        var user = members[i];
        var address = JSON.parse(user.address);
        bot.beginDialog(address, '/standup', { userId: user.id, reportId: reportId });
    }
    res.status(200);
    res.end();
});

bot.dialog('/standup', [
    function (session, args) {
        // Remember the ID of the user and status report
        session.dialogData.userId = args.userId;
        session.dialogData.reportId = args.reportId;

        // Ask user their status
        builder.Prompts.text(session, "What is your status for today?");
    },
    function (session, results) {
        var status = results.response;
        var userId = session.dialogData.userId;
        var reportId = session.dialogData.reportId;

        // Save their repsonse to the daily status report.
        session.sendTyping();
        saveTeamMemberStatus(userId, reportId, status, function (err) {
            if (!err) {
                session.endDialog('Got it... Thanks!');
            } else {
                session.error(err);
            }
        });
    }
]);
   ```

## Proactive Messaging 和本地化
  对于那些支持多种语言的机器人，你需要使用bot.beginDialog()与用户对话。只是因为用户的首选区域设置将会作为对话对象的一部分，仅在当前对话框可用。我们最开始的通知例子可以使用bot.beginDialog()而非bot.send()来轻松地更新：
     ```
     server.post('/api/notify', function (req, res) {
    // Process posted notification
    var address = JSON.parse(req.body.address);
    var notification = req.body.notification;
    var params = req.body.params;

    // Send notification as a proactive message
    bot.beginDialog(address, '/notify', { msgId: notification, params: params });
    res.status(200);
    res.end();
});

bot.dialog('/notify', function (session, args) {
    // Deliver notification to the user.
    session.endDialog(args.msgId, args.params);
});  
```
现在，可以通过the SDK’s built-in localization support来发送用户首选的语言类型的信息。使用bot.beginDiaolog()相比之下的一个缺点是：在当前对话前任意一个人机对话都将在发送用户消息前结束。对于只支持一种语言机器人，bot.send()还是首选。
