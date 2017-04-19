# 使用VSCode进行本地调试 #
  - 概述
  - 启动 VSCode
  - 启动 Bot   - 配置 VSCode
  - 连接调试器
  - 调试 Bot
## 概述 ##
  如果你想要调试你的 Bot ，你能用棒棒的 [Bot Framework 模拟器](https://docs.botframework.com/en-us/tools/bot-framework-emulator/)。另一个选项是安装 [VSCode](https://code.visualstudio.com/) 并使用 Bot Builders 的 [TextBot](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.textbot.html) 类来调试你运行在命令行窗口中的 Bot 。这份向导将指引你做这件事。

## 启动 VSCode ##
  在本向导中，我们将使用 Bot Builders 的 [TodoBot](https://github.com/Microsoft/BotBuilder/tree/master/Node/examples/todoBot) 示例。当你把 VSCode 安装在你的电脑上后，你需要用“打开文件夹”选项打开你的 Bot 项目。
  ![Alt text](https://docs.botframework.com/en-us/images/builder/builder-debug-step1.png)

## 启动 Bot ##
  TodoBot 被设计来跨平台地运行 Bot ，这是能够在本地调试你的 Bot 的关键。为了在本地调试，你需要一个能用 [TextBot](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.textbot.html) 类在命令行窗口中运行的 Bot 的版本。在 TodoBot 这个例子中，我们能通过启动 textBot.js 类来本地运行。为了正确地用 VSCode 调试这个类我们希望能用 --debug-brk 参数启动 node，这能使它瞬间暂停。所以我们在一个控制台窗口中运行`node --debug-brk textBot.js`。
  ![Alt text](https://docs.botframework.com/en-us/images/builder/builder-debug-step2.png)

## 配置 VSCode ##
  在你能调试你当前暂停的 Bot 之前，你需要配置 VSCode 的 node 调试器。VSCode 知道你的工程使用 node 但是对于怎样启动 node 还需要很多可能的配置，所以需要你为每个工程（也就是文件夹）做一个一次性的配置。为了配置调试器，选择左边下面的调试选项卡然后按下绿色的运行按钮。VSCode 会让你选取你的调试环境，你可以直接选择 "node.js"。默认的设置就适合你的目的，所以你不需要调整其他的。你会看到有一个 .vscode 文件夹添加进了你的工程，如果你不想这个被收入 Git 仓库，你可以添加一条 `/**/.vscode` 到你的 .gitignore 文件中。
  ![Alt text](https://docs.botframework.com/en-us/images/builder/builder-debug-step3.png)

## 连接调试器 ##
  配置调试器会导致添加了两种调试模式，Launch(启动) 和 Attach(连接)。因为我们的 Bot 已经被暂停在命令行窗口了，我们会选择"Attach"(连接)模式然后再次按下绿色的运行按钮。
  ![Alt text](https://docs.botframework.com/en-us/images/builder/builder-debug-step4.png)

## 调试 Bot ##
  VSCode 会连接到你的暂停的 Bot ，从第一行代码开始运行；现在你已经准备好设定断点并调试你的 Bot 了！你的 Bot 能通过命令行交流，所以切换会你的 Bot 运行着的命令行窗口并说"hello"吧。
  ![Alt text](https://docs.botframework.com/en-us/images/builder/builder-debug-step5.png)
