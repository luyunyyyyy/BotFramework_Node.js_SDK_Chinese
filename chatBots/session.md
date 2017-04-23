# session

# Overview

Session 对象每次你的 bot 接受到从用户发来的消息时候,都会发送到你的 对话处理器(dialog handlers). Session 对象是首要的机制,用来向用户发送消息和操作 bot 对话栈(dialog stack).

# Sending Messages 发送消息

`sessing.send()` 方法可以被用来向用户发送简单的消息, 附件和卡片. 你的 bot 可以随意调用 `send()` 多次, 以便于向用户发送消息. 当发送多个回复的时候, 每个回复会被自动的分组成一批, 作为集合传递给用户, 以保留消息的原始状态.

    自动的批处理

    当 bot 通过`session.send()`给用户发送多个回复的时候, 那些回复通过称为 "自动分组" 的功能, 来自动分批. 自动批处理默认等待 250ms 
