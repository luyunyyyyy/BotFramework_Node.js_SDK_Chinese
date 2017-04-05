# 自然语言理解 #
  - LUIS
  - 意图(Intents)、实体(Entites)和模型训练(Model Training)
  - 创建你的模型
  - 处理意图
  - 处理实体

## LUIS ##
  微软智能语言理解服务(Microsofts Language Understanding Intelligent Service)(LUIS)能为应用程序添加快速而高效的语言理解功能。有了 LUIS，你在任何适合你的想法的地方，运用由必应和小娜得到的，预存在的，世界级的，预编译的模型；并且，当你需要特殊的模型时，LUIS 能引导你快速地构建它们。

  LUIS的交互式机器学习和语言理解科技基于[微软研究院](http://research.microsoft.com/en-us/')和必应，包括了微软研究院的交互式概念学习平台(Microsoft Research’s Platform for Interactive Concept Learning)(PICL)。LUIS 也是微软的[牛津计划(Project Oxford)](https://www.projectoxford.ai/)的一部分。

  Bot Builder 让你能通过 [LuisDialog](https://docs.botframework.com/en-us/node/builder/chat/IntentDialog/)类，用 LUIS 添加自然语言理解功能。你能实现一个 LuisDialog 的实例来连接到你发布的语言模型的，然后能添加意图的处理器，来响应用户的对话。了解 LUIS 的实例请观看下面这十分钟的视频。
  - [Microsoft LUIS Tutorial ](https://vimeo.com/145499419)(视频)

## 意图、实体和模型训练
  人机交互的关键问题之一是电脑能否理解一个人的要求和能否找到对应满足意图的信息。举例来说，在一个新闻浏览app中，你会说“给我关于虚拟现实的公司的新闻”；这个例子中有“查找新闻”的意图和“虚拟现实的公司”这个主题。LUIS 就是设计来让你能非常快速地部署http后端，以此来：接受你发送的句子，并转释(原文为:interpret)它们表达的意图和出现的主要实体，如“虚拟现实的公司”。LUIS 让你能自定义设置符合你应用程序的意图和实体的集合，然后引导你构建一个语言理解系统。

  一旦你的程序部署好了、数据流也开始进入系统，LUIS 会主动地学习并完善自己。在主动学习过程中，LUIS 识别出相对不确定的交互并请求你根据意图和实体去标注它们。这样做能有诸多好处：首先，LUIS 知道哪些是不确定的，并在你所提供的帮助能最大限度地提高系统性能时，向你请求帮助。其次，当你专注于重要的事时，LUIS 能尽可能快地学习且尽可能少地占用你的时间。

## 创建你的模型
  为你的机器人添加自然语言支持的第一步便是创建你的 LUIS 模型。你要做的事是登录 LUIS 并为你的机器人创建一个新的 LUIS 应用。这个应用就是你用来添加意图和实体来让 LUIS 根据它们训练你的机器人的模型的。
  ![Alt text](https://docs.botframework.com/en-us/images/builder/builder-luis-create-app.png)
  为了创建新的应用，你需要选择是导入已有的模型(这是你想用 LUIS 做 Bot Builder 的示例应选的选项)，还是用预编译的 Cortana 应用。在本教程中，我们将创建一个基于预编译 Cortana 应用的机器人。当你选择了英语的预编译 Cortana 应用，你将会看到下面这样的对话框。

  你需要拷贝像这样列在对话框下的链接，这是用于绑定你的[LuisDialog](https://docs.botframework.com/en-us/node/builder/chat/IntentDialog/)类到这的链接。这个链接指向 LUIS 发布到你的机器人 LUIS 应用的模型，并会在你的应用的生命期内保持不变。所以一旦你已经训练好并发布你为了 LUIS 应用的模型，你就能甚至在不需要重新部署的情况下，升级和重新训练它。在你构建机器人早期，需要频繁重新训练模型的情况下，这会非常有用。

  ![Alt text](https://docs.botframework.com/en-us/images/builder/builder-luis-default-app.png)

## 处理意图 ##
  一旦你为你的 LUIS 应用部署了一个模型，我们便能创建一个使用那个模型的模块。为了简化操作，我们将创建一个能在[命令行窗口](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.consoleconnector)中交互的 [UniversalBot](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.universalbot) 通用机器人。

  为你的机器人创建一个文件夹，然后在里面运行:
  ```
  npm init
  ```

  用 npm 安装 Bot Builder 模块。
  ```
  npm install --save botbuilder
  ```

  创建一个包含以下代码的，名为 `app.js` 的文件。为了使用从 LUIS 获得的预编译Cortana应用的副本，你需要用从 LUIS 获得的链接，更改下面的示例代码中的相应部分。
  ```javascript
  var builder = require('botbuilder');

  // Create bot and bind to console
  var connector = new builder.ConsoleConnector().listen();
  var bot = new builder.UniversalBot(connector);

  // Create LUIS recognizer that points at our model and add it as the root '/' dialog for our Cortana Bot.
  var model = '<your models url>';
  var recognizer = new builder.LuisRecognizer(model);
  var dialog = new builder.IntentDialog({ recognizers: [recognizer] });
  bot.dialog('/', dialog);

  // Add intent handlers
  dialog.matches('builtin.intent.alarm.set_alarm', builder.DialogAction.send('Creating Alarm'));
  dialog.matches('builtin.intent.alarm.delete_alarm', builder.DialogAction.send('Deleting Alarm'));
  dialog.onDefault(builder.DialogAction.send("I'm sorry I didn't understand. I can only create & delete alarms."));
  ```
  这份示例引入了我们的 Cortana 模型并实现了两种意图处理器，一种是设定一个新闹钟，另一种是删除一个闹钟。这两种处理方式目前都只是使用 [DialogAction](https://docs.botframework.com/en-us/node/builder/chat/prompts/#dialog-actions) 来在触发时发送一段静态信息。Cortana 模型实际上能触发许多其他的意图，因此我们也同时添加了 [onDefault()](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.intentdialog#ondefault) 处理器来拦截任何我们目前不支持的意图。在命令行中运行这份示例会得到下面这样的结果：
  ```
  node app.js
  set an alarm in 5 minutes called wakeup
  Creating Alarm
  snooze the wakeup alarm
  I'm sorry I didn't understand. I can only create & delete alarms.
  delete the wakeup alarm
  Deleting Alarm
  ```

## 处理实体 ##
  既然我们已经能让我们的机器人理解用户意图的操作了，接下来就使它能真正地添加或删除闹钟。我们将扩展我们的示例来包含一些处理每个[意图](https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.iintent)的逻辑和一个只使用内存的简易闹钟安排器。
  ```javascript
  var builder = require('botbuilder');

  // Create bot and bind to console
  var connector = new builder.ConsoleConnector().listen();
  var bot = new builder.UniversalBot(connector);

  // Create LUIS recognizer that points at our model and add it as the root '/' dialog for our Cortana Bot.
  var model = '<your models url>';
  var recognizer = new builder.LuisRecognizer(model);
  var dialog = new builder.IntentDialog({ recognizers: [recognizer] });
  bot.dialog('/', dialog);

  // Add intent handlers
  dialog.matches('builtin.intent.alarm.set_alarm', [
      function (session, args, next) {
          // Resolve and store any entities passed from LUIS.
          var title = builder.EntityRecognizer.findEntity(args.entities, 'builtin.alarm.title');
          var time = builder.EntityRecognizer.resolveTime(args.entities);
          var alarm = session.dialogData.alarm = {
            title: title ? title.entity : null,
            timestamp: time ? time.getTime() : null  
          };

          // Prompt for title
          if (!alarm.title) {
              builder.Prompts.text(session, 'What would you like to call your alarm?');
          } else {
              next();
          }
      },
      function (session, results, next) {
          var alarm = session.dialogData.alarm;
          if (results.response) {
              alarm.title = results.response;
          }

          // Prompt for time (title will be blank if the user said cancel)
          if (alarm.title && !alarm.timestamp) {
              builder.Prompts.time(session, 'What time would you like to set the alarm for?');
          } else {
              next();
          }
      },
      function (session, results) {
          var alarm = session.dialogData.alarm;
          if (results.response) {
              var time = builder.EntityRecognizer.resolveTime([results.response]);
              alarm.timestamp = time ? time.getTime() : null;
          }

          // Set the alarm (if title or timestamp is blank the user said cancel)
          if (alarm.title && alarm.timestamp) {
              // Save address of who to notify and write to scheduler.
              alarm.address = session.message.address;
              alarms[alarm.title] = alarm;

              // Send confirmation to user
              var date = new Date(alarm.timestamp);
              var isAM = date.getHours() < 12;
              session.send('Creating alarm named "%s" for %d/%d/%d %d:%02d%s',
                  alarm.title,
                  date.getMonth() + 1, date.getDate(), date.getFullYear(),
                  isAM ? date.getHours() : date.getHours() - 12, date.getMinutes(), isAM ? 'am' : 'pm');
          } else {
              session.send('Ok... no problem.');
          }
      }
  ]);

  dialog.matches('builtin.intent.alarm.delete_alarm', [
      function (session, args, next) {
          // Resolve entities passed from LUIS.
          var title;
          var entity = builder.EntityRecognizer.findEntity(args.entities, 'builtin.alarm.title');
          if (entity) {
              // Verify its in our set of alarms.
              title = builder.EntityRecognizer.findBestMatch(alarms, entity.entity);
          }

          // Prompt for alarm name
          if (!title) {
              builder.Prompts.choice(session, 'Which alarm would you like to delete?', alarms);
          } else {
              next({ response: title });
          }
      },
      function (session, results) {
          // If response is null the user canceled the task
          if (results.response) {
              delete alarms[results.response.entity];
              session.send("Deleted the '%s' alarm.", results.response.entity);
          } else {
              session.send('Ok... no problem.');
          }
      }
  ]);

  dialog.onDefault(builder.DialogAction.send("I'm sorry I didn't understand. I can only create & delete alarms."));

  // Very simple alarm scheduler
  var alarms = {};
  setInterval(function () {
      var now = new Date().getTime();
      for (var key in alarms) {
          var alarm = alarms[key];
          if (now >= alarm.timestamp) {
              var msg = new builder.Message()
                  .address(alarm.address)
                  .text("Here's your '%s' alarm.", alarm.title);
              bot.send(msg);
              delete alarms[key];
          }
      }
  }, 15000);
  ```
  我们使用[瀑布流](https://docs.botframework.com/en-us/node/builder/chat/dialogs/#waterfall)来实现 set_alarm 和 delete_alarm [意图处理器](https://docs.botframework.com/en-us/node/builder/chat/IntentDialog/#intent-handling)。这是一个你可能会用在你的大部分意图处理方式上的常见模式。瀑布流这种方式在 Bot Builder 运作的第一步是某个对话（或像这个例子中的意图处理器）被激活并调用了瀑布流。接下来做些工作，之后通过调用其他对话（例如内置的提示）或者可选地调用传入的 next() 函数，继续执行瀑布流。当一个对话在某一步被调用了，任何从对话中返回的结果都会作为输入传入下一步的 results 参数。

  为了防止意图处理器将 LUIS 识别的任何实体从第一步，一路都在参数中传送；这些额外的信息往往还不是你在完全处理用户请求前想要的。LUIS 使用实体来一路传送额外的数据，但说真的，你不会想获得的是用户之前输入的每一点信息。所以在 set_alarm 这个例子中，我们想要支持的是“设定五分钟后的闹钟叫我起床”、“设个闹钟叫我起床”或就是“设个闹钟”。这意味着我们也许不总是从 LUIS 中获得所有我们期待的实体，甚至当我们得到他们的时候，我们希望有些实体被设定成无效的。所以在所有情况下，我们都要能准备好提示词，来提示用户哪些实体是无效的或是缺少的。Bot Builder 使我们相对容易地来在你的机器人中，用[瀑布流](https://docs.botframework.com/en-us/node/builder/chat/dialogs/#waterfall)和内置[提示语](https://docs.botframework.com/en-us/node/builder/chat/prompts/)来创建多变的提示。
