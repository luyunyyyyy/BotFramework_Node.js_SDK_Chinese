# Prompts
- Collecting Input
 - Prompts.text()
 - Prompts.confirm()
 - Prompts.number()
 - Prompts.time()
 - Prompts.choice()
 - Prompts.attachment()
- Dialog Actions

## Collecting Input
Bot的创建器中含有一些内置的Prompts，用于收集来自用户的输入。

Prompt类型           |功能描述                  
--------------------|-------------------------
Prompts.text()      | 请求用户输入一串文字       
Prompts.confirm()   | 请求用户确认一个操作       
Prompts.number()    | 请求用户输入一个数字       
Prompts.time()      | 请求用户输入一个时间或日期 
Prompts.choice()    | 请求用户从一个列表中做出一个选择   
Prompts.attachment()| 请求用户上传一份图片或视频  

这些内置的提示符以[Dialog](https://docs.botframework.com/en-us/node/builder/chat/dialogs/)形式执行，所以它们将会通过对session.endDialogWithresult().的调用来返回对用户的回应。任何一个DialogHandler都能够收到Dialog的结果，但是瀑布(waterfalls)往往是处理prompt结果的一种最简单的方式。

Prompts向它的调用者返回一个IPromptResult.用户的反馈将被包含在results.response领域中，此反馈可能为null。反馈为null的原因有很多种。内置的prompts允许用户通过说一些像“取消”或者“没关系”之类的话来取消某个操作，这些话将会导致反馈为null。有时用户可能并没有成功输入一个正确格式的回应，这也会导致反馈为null。通过检测在result.resumed中返回的ResumeReason，你可以确定导致反馈为null的其他原因。

###Prompts.text()
Prompts.text()会请求用户输入一串文字，用户的反馈将会被作为一个IPromptTextResult来返回。

`builder.Prompts.text(session, "What is your name?");`

###Prompts.confirm()
Prompts.confirm()会请求用户用“是”和“否”来确认一个操作，用户的反馈将会被作为一个IPromptConfirmResult来返回。

`builder.Prompts.confirm(session, "Are you sure you wish to cancel your order?");`

###Prompts.number()
Prompts.number()会请求用户反馈一个数字，用户的反馈将会被作为一个IPromptNumberResult来返回。

`builder.Prompts.number(session, "How many would you like to order?");`

###Prompts.time()
Prompts.time()会请求用户反馈一个时间，用户的反馈将会被作为一个IPromptTimeResult.来返回。bot framework使用一个名为Chrono的库来从语法上分析用户的反馈。这一框架既支持和当前时间有关的反馈，例如“五分钟以后。”,也支持和当前时间无关的反馈，例如“6月6日下午2点”。

用户反馈的results.response是一个实例，这个实例能被使用EntityRecognizer.resolveTime().的JavaScript日期对象分解。

```
bot.dialog('/createAlarm', [
    function (session) {
        session.dialogData.alarm = {};
        builder.Prompts.text(session, "What would you like to name this alarm?");
    },
    function (session, results, next) {
        if (results.response) {
            session.dialogData.name = results.response;
            builder.Prompts.time(session, "What time would you like to set an alarm for?");
        } else {
            next();
        }
    },
    function (session, results) {
        if (results.response) {
            session.dialogData.time = builder.EntityRecognizer.resolveTime([results.response]);
        }
        
        // Return alarm to caller  
        if (session.dialogData.name && session.dialogData.time) {
            session.endDialogWithResult({ 
                response: { name: session.dialogData.name, time: session.dialogData.time } 
            }); 
        } else {
            session.endDialogWithResult({
                resumed: builder.ResumeReason.notCompleted
            });
        }
    }
]);
```
###Prompts.choice()
Prompts.choice()会请求用户从一个列表中做出一个选择。用户的反馈会被作为一个IPromptChoiceResult来返回。含有选项的列表能通过IpromptOptions对象的property而以各种形式展现给用户。用户能通过输入选项的数字或者它的名称来表达他们的选择。支持选项内容的完全匹配和部分匹配。

有一系列的方式能将含有选项的列表传递给Prompts.choice()。

例如用竖线‘|’将字符串分隔开：

`builder.Prompts.choice(session, "Which color?", "red|green|blue");`

例如用字符串数组：

`builder.Prompts.choice(session, "Which color?", ["red","green","blue"]);`

例如使用Object map。当一个对象(object)被传递进来，对象键(objects keys)将会被用于确定用户的选择。

```
var salesData = {
    "west": {
        units: 200,
        total: "$6,000"
    },
    "central": {
        units: 100,
        total: "$3,000"
    },
    "east": {
        units: 300,
        total: "$9,000"
    }
};

bot.dialog('/', [
    function (session) {
        builder.Prompts.choice(session, "Which region would you like sales for?", salesData); 
    },
    function (session, results) {
        if (results.response) {
            var region = salesData[results.response.entity];
            session.send("We sold %(units)d units for a total of %(total)s.", region); 
        } else {
            session.send("ok");
        }
    }
]);
```

###Prompts.attachment()
Prompts.attachment()会请求用户上传一个文件附件例如一幅图片或一段视频。
用户的反馈将会被作为一个 IPromptAttachmentResult来返回。

`builder.Prompts.attachment(session, "Upload a picture for me to transform.");`

##Dialog Actions
Dialog actions提供一些常见操作的快捷方法。DialogAction类提供了一套固定的方法，
这些方法能够得到一个可以将dialog handler传递给任何接收者的闭包(closure)。
这些方法包括但不限于 UniversalBot.dialog(), Library.dialog(), IntentDialog.matches(),和IntentDialog.onDefault()。

Action类型           |功能描述                  
--------------------|-------------------------
DialogAction.send     | 发送一个固定的信息给用户       
DialogAction.beginDialog   | 将当前会话的控制移交到下一个对话       
DialogAction.endDialog   | 结束当前的对话       
DialogAction.validatedPrompt    | 通过用验证例程(validation routine)来包裹内置prompts，以创建一个定制的prompt
