# session

# Overview

Session 对象每次你的 bot 接受到从用户发来的消息时候,都会发送到你的 对话处理器(dialog handlers). Session 对象是首要的机制,用来向用户发送消息和操作 bot 对话栈(dialog stack).

# Sending Messages 发送消息

`sessing.send()` 方法可以被用来向用户发送简单的消息, 附件和卡片. 你的 bot 可以随意调用 `send()` 多次, 以便于向用户发送消息. 当发送多个回复的时候, 每个回复会被自动的分组成一批, 作为集合传递给用户, 以保留消息的原始状态.

    自动的批处理

    当 bot 通过`session.send()`给用户发送多个回复的时候, 那些回复通过称为 "自动分组" 的功能, 来自动分批. 自动批处理在每次调用 `send()` 后默认等待下一次调用 250ms.

    为了最后一次调用时避免这个 250ms 的暂停, 你可以手动调用 `session.sendBatch()` 手动触发批处理. 实际上, 事实上你很少需要调用 `sendBatch()` 作为内建的提示, `session.endConversation()` 为你自动调用 `sendBatch()`.

    批处理的目的是尝试避免机器人发送的多条信息在展示的时候乱序. 不幸的是, 并不是所有的聊天客户端都支持这一功能. 通常的, 如果你发送包含图片的消息在一条不包含图片的消息之后, 客户端倾向于在展示消息前下载图片, 你会看到消息在用户界面翻转. 为了尽量减少这种情况，您可以尝试确保您的图像来自CDN, 并尽量避免使用过大的图像. 在极端情况下, 甚至可能需要在消息与图像之间插入1-2秒延迟. 您可以通过在开始延迟之前调用`session.sendTyping()` 使该延迟更加自然.

    SDK的自动批处理延迟是可配置的, 因此如果您要一起禁用SDK的自动批处理逻辑, 您可以将默认延迟设置为大数，然后手动调用`sendBatch()`, 并返回批处理后调用的回调 已被送达。


# 文本消息

为了给用户发送简单的文本消息, 你可以简单的调用 `session.send("hello")`. 消息能支持模版参数,通过 `session.send("hello there %s", name)` 进行扩展. 当前的 SDK 使用一个名为 "node-springt" 的模块去实现模版的功能, 可以在 sprintf 的文档查看所有可能支持的列表.

    注意: 一个关于使用命名参数的警告. 一个很常见的错误是忘记使用尾随的格式说明符, 例如 's'. 当你使用命名参数 `session.send("Hello there %(name)s", user)`. 如果这样, 你会得到一个不合法模版的运行时错误. 使用它作为您应该验证模板正确的指标。


# 附件

很多聊天服务支持发送图片, 视频和文件作为附件发送给用户. 你可以使用 `session.send()`, 但你需要将其与 SDK 的消息构造器类一起使用. 你可以使用 `attachments()` 或者 `addAttachment()` 方法来创建包含图像的消息.

```
    bot.dialog('/picture', [
        function (session) {
            session.send("You can easily send pictures to a user...");
            var msg = new builder.Message(session)
                .attachments([{
                    contentType: "image/jpeg",
                    contentUrl: "http://www.theoldrobots.com/images62/Bender-18.JPG"
                }]);
            session.endDialog(msg);
        }
    ]);
```   

# 卡片

一些聊天服务正在开始支持发送一些包含文字, 图像, 甚至按钮的卡片. 不是所有聊天服务都支持卡片或者或者有相同丰富程度, 因此你需要查阅各个服务文档以确定当前支持的内容.

这个机器人构建的 SDK 包含一个帮助类的集合, 这些类可以备用来以跨平台的方式向用户呈现卡片. HeroCard 和 ThumbnailCard 类可以被用来向用户呈现一些包含文字, 图片, 和可选的按钮. 在大多数的平台上, 你可能注意到两个卡片没有区别, 但是在 skype 上, 你可以用他们来控制用户的图片的表现形式.

```
bot.dialog('/cards', [
    function (session) {
        var msg = new builder.Message(session)
            .textFormat(builder.TextFormat.xml)
            .attachments([
                new builder.HeroCard(session)
                    .title("Hero Card")
                    .subtitle("Space Needle")
                    .text("The <b>Space Needle</b> is an observation tower in Seattle, Washington, a landmark of the Pacific Northwest, and an icon of Seattle.")
                    .images([
                        builder.CardImage.create(session, "https://upload.wikimedia.org/wikipedia/commons/thumb/7/7c/Seattlenighttimequeenanne.jpg/320px-Seattlenighttimequeenanne.jpg")
                    ])
                    .tap(builder.CardAction.openUrl(session, "https://en.wikipedia.org/wiki/Space_Needle"))
            ]);
        session.endDialog(msg);
    }
]);
```

卡片通常可以具有轻触动作, 让你指定当用户点击卡片时应该发生的情况(打开链接, 发送消息等)你可以使用 CardAction 类来自定义此行为. 您可以指定几种不同类型的操作, 但大多数平台只支持少量操作. 你通常会坚持使用`openUrl(), postBack(), dialogAction()`操作来实现最大的兼容性.

    注意: `postBack()` 和 `imBack()` 的区别很微妙.
