# 目的会话

* [概述](https://docs.botframework.com/en-us/node/builder/chat/IntentDialog/#overview)
* [匹配常规语句](https://docs.botframework.com/en-us/node/builder/chat/IntentDialog/#matching-regular-expressions)
* [定向识别](https://docs.botframework.com/en-us/node/builder/chat/IntentDialog/#intent-recognizers)
* [实体识别](https://docs.botframework.com/en-us/node/builder/chat/IntentDialog/#entity-recognition)
  - [找到实体](https://docs.botframework.com/en-us/node/builder/chat/IntentDialog/#finding-entities)
  - [转换日期与时间](https://docs.botframework.com/en-us/node/builder/chat/IntentDialog/#resolving-dates--times)
  - [匹配列表元素](https://docs.botframework.com/en-us/node/builder/chat/IntentDialog/#matching-list-items)
* [onBegin & onDefault 处理机制](https://docs.botframework.com/en-us/node/builder/chat/IntentDialog/#onbegin--ondefault-handlers)
## 概述
[IntentDialog](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.intentdialog.html) 类（以下称目的会话）使你可以监听用户发送的关键词。我们称之为目的监听，因为你正企图确定用户打算做什么。目的会话对于开发终端更加开放并能理解自然语言表达方式的机器人是很有用的。关于深入了解使用目的会话向机器人添加自然语言支持的内容请移步[理解自然语言](https://docs.botframework.com/en-us/node/builder/guides/understanding-natural-language/)向导。

 >注意： 对于 Bot Builder v1.x 的用户，[CommandDialog](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.commanddialog) 和 [LuisDialog](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.luisdialog) 两个类是不建议使用的。这两个类仍然有效，但我们推荐开发者尽早将其更新为更加灵活的IntentDialog 类。
## 匹配常规语句
[IntentDialog.matches()](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.intentdialog.html#matches) 方法可以会触发一种机制，该机制基于可匹配常规语句的用户表达。

这是一个获取用户输入的瀑布流：
``` javascript
var intents = new builder.IntentDialog();
bot.dialog('/', intents);

intents.matches(/^echo/i, [
    function (session) {
        builder.Prompts.text(session, "What would you like me to say?");
    },
    function (session, results) {
        session.send("Ok... %s", results.response);
    }
]);
```

这是一个简单的闭包，可以和第一步的瀑布流起到相同的作用：
``` javascript
var intents = new builder.IntentDialog();
bot.dialog('/', intents);

intents.matches(/^version/i, function (session) {
    session.send('Bot version 1.2');
});
```

[DialogAction](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.dialogaction.html) 可以为实现更简单的闭包提供捷径。
``` javascript
var intents = new builder.IntentDialog();
bot.dialog('/', intents);

intents.matches(/^version/i, builder.DialogAction.send('Bot version 1.2'));
```

或者重定向的会话ID：
``` javascript
bot.dialog('/', new builder.IntentDialog()
    .matches(/^add/i, '/addTask')
    .matches(/^change/i, '/changeTask')
    .matches(/^delete/i, '/deleteTask')
    .onDefault(builder.DialogAction.send("I'm sorry. I didn't understand."))
);
```

## 目的识别
IntentDialog 类可以通过[识别器](https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.iintentrecognizer.html)插件的可扩展集合进行配置，从而能够使用基于诸如[LUIS](http://luis.ai/)一类定向识别服务的云。
推陈出新，Bot Builder 自带 [LuisRecognizer](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.luisrecognizer)，它可以触发你已经在某个网站训练过的机器学习模式。你可以创建一个指向自定义模式的LuisRecongnizer对象，并在IntentDialog 的构造时期使用 [options](https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.iintentdialogoptions) 结构将它传入。
``` javascript
var recognizer = new builder.LuisRecognizer('<your models url>');
var intents = new builder.IntentDialog({ recognizers: [recognizer] });
bot.dialog('/', intents);

intents.matches('Help', '/help');
```

当命名匹配时，目的识别器返回配对。为了与识别器提供的目的选项匹配，你需要把你想要达成的目的以string形式（而不是RegExp）传给 [IntentDialog.matches()](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.intentdialog#matches) 的句柄。这将你基于云的识别模型和规律语句的匹配融合起来。为了改进性能，在云识别器之前，规律语句总会得到评估。一个精确的规律语句匹配会避免同时调用所有的云识别器。

通过传递一个识别器序列，你可以聚合多个LUIS模式。你可以控制使用 [recognizeOrder](https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.iintentdialogoptions#recognizeorder) 评估识别器的次序。缺省情况下，识别器将被并行评估。返回的目的[分数](https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.iintent#score)最高的识别器将被匹配。你可以改变识别序列的次序，评估就将按照新序列进行。只要有一个识别器的返回目的得分达到1.0，其后的识别器将不会被评估。

>注意：你应该避免向LUIS的“None”目的添加matches()方法。用 [onDefault](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.intentdialog#ondefault) 代替。原因在于，如果LUIS模式不理解用户语句，它通常会为None目的返回一个非常高的分数。在这种方案下,当你已经用多个识别器配置IntentDialog，这会使None目的胜过不同模式下得分稍少一点的 non-None 目的。由于这种情况，LuisRecognizer禁用了所有None目的。如果你清楚地设定了一个None目的，它将从不被匹配。然而onDefault()处理机制可以实现同样的功能,因为只有当所有模式报告的最高得分都是None目的时才被触发。

## 实体识别
LUIS不仅能通过给定的语句辨识出用户的目的，也可以从中提取目的实体。任何从用户语句中提取出的目的实体将会通过[参数](https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.iintentrecognizerresult)列表传入目的管理器。Bot Builder 包含
[Entity Recognizer](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.entityrecognizer.html)，以使用这些实体来简化工作。

### 找到实体
你可以使用 [EntityRecognizer.findEntity()](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.entityrecognizer.html#findentity) 方法和 [EntityRecognizer.findAllEntities()](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.entityrecognizer.html#findallentities) 方法通过名字来搜寻一种特定的实体类型。
``` javascript
var recognizer = new builder.LuisRecognizer('<your models url>');
var intents = new builder.IntentDialog({ recognizers: [recognizer] });
bot.dialog('/', intents);

intents.matches('AddTask', [
    function (session, args, next) {
        var task = builder.EntityRecognizer.findEntity(args.entities, 'TaskTitle');
        if (!task) {
            builder.Prompts.text(session, "What would you like to call the task?");
        } else {
            next({ response: task.entity });
        }
    },
    function (session, results) {
        if (results.response) {
            // ... save task
            session.send("Ok... Added the '%s' task.", results.response);
        } else {
            session.send("Ok");
        }
    }
]);
```
### 转换日期与时间
LUIS 内置强大的时间实体识别器，它可以广泛识别自然语言表达的模糊时间与精确时间。问题在于，当LUIS返回它识别的日期与时间时，会将其分块返回。所以如果用户说"june 5th at 9 am"，它将返回用户语句中日期与时间的单独部分。这些部分需要使用 [EntityRecognizer.resolveTime()](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.entityrecognizer.html#resolvetime) 方法连接起来已得到实际处理完毕的时间。这个方法会试图将一个实体数组转换为一个JavaScript中Date类型的对象。如果该方法不能将其转换为有效的Data对象则返回null。
``` javascript
var recognizer = new builder.LuisRecognizer('<your models url>');
var intents = new builder.IntentDialog({ recognizers: [recognizer] });
bot.dialog('/', intents);

intents.matches('SetAlarm', [
    function (session, args, next) {
        var time = builder.EntityRecognizer.resolveTime(args.entities);
        if (!time) {
            builder.Prompts.time(session, 'What time would you like to set the alarm for?');
        } else {
            // Saving date as a timestamp between turns as session.dialogData could get serialized.
            session.dialogData.timestamp = time.getTime();
            next();
        }
    },
    function (session, results) {
        var time;
        if (results.response) {
            time = builder.EntityRecognizer.resolveTime([results.response]);
        } else if (session.dialogData.timestamp) {
            time = new Date(session.dialogData.timestamp);
        }
        
        // Set the alarm
        if (time) {

            // .... save alarm
            
            // Send confirmation to user
            var isAM = time.getHours() < 12;
            session.send('Setting alarm for %d/%d/%d %d:%02d%s',
                time.getMonth() + 1, time.getDate(), time.getFullYear(),
                isAM ? time.getHours() : time.getHours() - 12, time.getMinutes(), isAM ? 'am' : 'pm');
        } else {
            session.send('Ok... no problem.');
        }
    }
]);
```

### 匹配列表元素
Bot Builder 包含一个强大的[choice() 扩展](https://docs.botframework.com/en-us/node/builder/dialogs/Prompts/#promptschoice)，使你可以向用户呈现选择列表。LUIS使得了解用户对于一个命名实体的选择更加简单，但它不能确认用户输入的是一个有效的选择。你可以使用 [EntityRecognizer.findBestMatch()](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.entityrecognizer.html#findbestmatch) 方法和 [EntityRecognizer.findAllMatches()](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.entityrecognizer.html#findallmatches) 方法以证实用户输入的选择是有效的。这两个方法与在choice() 扩展中使用的方法是相同的，并且当用户语句与列表中的值匹配时具有更大的灵活性。

列表中的元素可以通过大小写不敏感的准确匹配规则进行匹配，所以给定列表["Red","Green","Blue"]，用户输入"red"就会匹配到"Red"。按照部分匹配规则，当用户输入"blu"时，就会匹配"Blue"。或者按照反转匹配模式,当用户输入"the green one"，就会匹配到"Green"。从内部来看，当评估部分匹配时,匹配函数会计算平均分数。例如"blu"与"Blue"之间的匹配平均分数应当是0.75；"the green one "与"Green"之间的平均匹配分数应当是0.88。能够触发匹配的最低分数是0.6，不过在每次匹配时该值可调整。

[IFindMatchResult](https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.ifindmatchresult.html) 对象会在每次匹配结束后返回，它包含对于当前监视列表的元素的[实体](https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.ifindmatchresult.html#entity)，[索引](https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.ifindmatchresult.html#index)与[评分](https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.ifindmatchresult.html#score)。
``` javascript
var recognizer = new builder.LuisRecognizer('<your models url>');
var intents = new builder.IntentDialog({ recognizers: [recognizer] });
bot.dialog('/', intents);

intents.matches('DeleteTask', [
    function (session, args, next) {
        // Process optional entities received from LUIS
        var match;
        var entity = builder.EntityRecognizer.findEntity(args.entities, 'TaskTitle');
        if (entity) {
            match = builder.EntityRecognizer.findBestMatch(tasks, entity.entity);
        }
        
        // Prompt for task name
        if (!match) {
            builder.Prompts.choice(session, "Which task would you like to delete?", tasks);
        } else {
            next({ response: match });
        }
    },
    function (session, results) {
        if (results.response) {
            delete tasks[results.response.entity];
            session.send("Deleted the '%s' task.", results.response.entity);
        } else {
            session.send('Ok... no problem.');
        }
    }
]);
```
## onBegin 与 onDefault 处理机制
IntentDialog 会使你创建一个 [onBegin](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.intentdialog.html#onbegin) 处理机制；该对话第一次被加载时通知onBegin。以及 [onDefault](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.intentdialog.html#ondefault) 处理机制；当用户命令没有匹配到任何现有的模式时通知onDefault。

onBegin处理机制会在当 [session.beginDialog()](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.session.html#begindialog) 被当前会话调用时激活，并给了该会话获取在调用beginDialog()时传递的任意声明的机会。该处理机制会被传入应去继续执行当前会话默认逻辑的next()函数。
``` javascript
intents.onBegin(function (session, args, next) {
    session.dialogData.name = args.name;
    session.send("Hi %s...", args.name);
    next();
})
```
onDefault处理机制会在用户命令没有匹配到任何现有模式时被激活。该处理机制可以是瀑布流，闭包，DialogAction，或者是重定向的对话ID。
``` javascript
intents.onDefault(builder.DialogAction.send("I'm sorry. I didn't understand."));
```
onDefault处理机制也可以被用于手动获取目的，当已经通过参数列表遍历所有新近完成的识别结果。
