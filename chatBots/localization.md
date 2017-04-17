# 本地化

 - 概述
 - 确定地区
 - 本地化 Prompts
 - 命名空间Prompt

## 概述
Bot Builder 包含丰富的本地化系统，以便于所构建的bot能使用多种语言与用户进行交流。所有prompts 可以使用一个JSON文件来进行本地化。这个文件储存在你的bot 的文件目录里面。如果你使用像LUIS这样的系统来处理自然语言。你可以为你的bot所支持每个语言分别配置一个模型，用这些模型来配置你的[LuisRecognizer](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.luisrecognizer)。SDK将根据用户首选的地区来自动选择模型。

## 确定地区

针对用户进行本地化的第一步就是要能够识别首选语言的能力。 SDK 提供了一个[seesion.preferredLocale()](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.session#preferredlocale)方法来保存和检索每个用户的首选项。下面是一个简单的示例dialog，用于提示用户输入他们首选的语言并持久化他们的选择。

```javascript
bot.dialog('/localePicker', [
    function (session) {
        // Prompt the user to select their preferred locale
        builder.Prompts.choice(session, "What's your preferred language?", 'English|Español|Italiano');
    },
    function (session, results) {
        // Update preferred locale
        var locale;
        switch (results.response.entity) {
            case 'English':
                locale = 'en';
            case 'Español':
                locale = 'es';
            case 'Italiano':
                locale = 'it';
                break;
        }
        session.preferredLocale(locale, function (err) {
            if (!err) {
                // Locale files loaded
                session.endDialog("Your preferred language is now %s.", results.response.entity);
            } else {
                // Problem loading the selected locale
                session.error(err);
            }
        });
    }
]);

```
另一个选项是安装一层中间件。使用类似于微软的文本分析 API的服务去根据用户发送的信息来检测用户的语言。

```javascript

var request = require('request');

bot.use({
    receive: function (event, next) {
        if (event.text && !event.textLocale) {
            var options = {
                method: 'POST',
                url: 'https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages?numberOfLanguagesToDetect=1',
                body: { documents: [{ id: 'message', text: event.text }]},
                json: true,
                headers: {
                    'Ocp-Apim-Subscription-Key': '<YOUR API KEY>'
                }
            };
            request(options, function (error, response, body) {
                if (!error && body) {
                    if (body.documents && body.documents.length > 0) {
                        var languages = body.documents[0].detectedLanguages;
                        if (languages && languages.length > 0) {
                            event.textLocale = languages[0].iso6391Name;
                        }
                    }
                }
                next();
            });
        } else {
            next();
        }
    }
});
```
当用户没有指定地区的时候，调用`session.preferredLocale()`将自动返回所检测到的语言。preferredLocale()的具体搜索顺序是：
 - 通过调用`session.preferredLocale()`来保存地区。这个值将保存在`session.userData['BotBuilder.Data.PreferredLocale']`里面。
 - 检测到`session.message.textLocale`所指定的地区。
 - Bot 配置[默认地区](https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.iuniversalbotsettings#localizersettings)
 - English('en')。

你可以在构建的时候配置bot的默认地区：

```javascript
var bot = new builder.UniversalBot(connector, {
    localizerSettings: { 
        defaultLocale: "es" 
    }
});
```

# 本地化Prompts

Bot Builder的默认的本地化系统是基于文件的。使用一个储存在硬盘里的JSON文件来使bot支持多语言。默认情况下，本地化系统将在`./locale/<IETF TAG>/index.json`中搜寻bot的prompts。`<IETF TAG>`是一个有效的[IETF 语言标签](https://en.wikipedia.org/wiki/IETF_language_tag),代表prompts的首选地区。下面是一个bot的目录结构的截图。这个bot支持英语、意大利与和法语三种语言。

![pic](https://docs.botframework.com/en-us/images/builder/locale-dir.png)

文件的结构简单明了。这是简单的从消息ID到本地化文本字符串JSON映射。如果值是一个数组，而不是一个字符串，任何时候使用[session.localizer.gettext()](https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.ilocalizer#gettext)来检索，都会随机返回一个值。通过在调用[session.send()](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.session#send)时，只需要简单地传递一个消息ID，而不是语言的具体文本，通常将自动地返回一个消息的本地化版本。

```javascript
bot.dialog("/", [
    function (session) {
        session.send("greeting");
        session.send("instructions");
        session.beginDialog('/localePicker');
    },
    function (session) {
        builder.Prompts.text(session, "text_prompt");
    },
```

内部地，SDK将调用`session.preferredLocale()`来获得用户首选的地区，并将在调用`session.localizaer.gettext()`时根据地区将消息ID映射到本地化文本字符串。有时你可能需要手动调用`localizer`。例如，传递给[Prompts.choice()]()的枚举值不会自动本地化，所以，你可能需要在调用prompt之前手动检索本地化列表：

```javascript
var options = session.localizer.gettext(session.preferredLocale(), "choice_options");
    builder.Prompts.choice(session, "choice_prompt", options);
```

默认的localizer将会跨越多个文件来搜索一个消息ID。如果它不能找到一个ID（或如果没有被提供本地化文件），它将仅仅返回文本的ID，这使得本地化文件是透明的和可选的。文件按照以下顺序搜索：
 - 首先，搜索在`session.preferedLocale()`所返回的区域设置(locale)对应目录下的index.json
 - 接下来，如果区域设置(locale)包含一个可选的子标签，例如`en-US`,那么 `en`的根标签将会被搜索。
 - 最后，bot默认配置的区域设置将会被搜索。

 # 命名空间 Prompts

 默认的本地化版本支持Prompts的命名空间，以避免消息ID之间的冲突。

