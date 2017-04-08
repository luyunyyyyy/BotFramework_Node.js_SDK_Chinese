# 示例 #
  - 概述
  - Hello World
  - 基础技巧
  - 演示机器人

## 概述 ##
  Node.js 版本的 Bot Builder 示例都是按组组织的，并且都被设计来展示构建好机器人所需要的技巧。要使用这些示例，用 Git 从 GitHub 克隆仓库。
  ```
  git clone https://github.com/Microsoft/BotBuilder.git
  cd BotBuilder/Node
  npm install
  ```
  以下这些 node 的示例可以在 "Node/examples" 下找到。

## Hello World ##
  这些示例展示了框架支持的每种类型的机器人的简单 "Hello World" 示例。
  <table>
    <thead>
      <tr>
        <th><strong>示例</strong></th>
        <th><strong>描述</strong></th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td><a href="https://github.com/Microsoft/BotBuilder/tree/master/Node/examples/hello-ConsoleConnector">hello-ConsoleConnector</a></td>
        <td>ConsoleConnector 类的 "Hello World"</td>
      </tr>
      <tr>
        <td><a href="https://github.com/Microsoft/BotBuilder/tree/master/Node/examples/hello-ChatConnector">hello-ChatConnector</a></td>
        <td>ChatConnector 类的 "Hello World"</td>
      </tr>
    </tbody>
  </table>

## 基础技巧 ##
  这些例子展示了构建一个好机器人需要的基础技巧。所有的例子使用能在命令行窗口执行的 TextBot 类。
  <table>
    <thead>
      <tr>
        <th><strong>示例</strong></th>
        <th><strong>描述</strong></th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td><a href="https://github.com/Microsoft/BotBuilder/tree/master/Node/examples/basics-waterfall">basics-waterfall</a></td>
        <td>展示怎样使用瀑布流来提示用户一系列问题。</td>
      </tr>
      <tr>
        <td><a href="https://github.com/Microsoft/BotBuilder/tree/master/Node/examples/basics-loops">basics-loops</a></td>
        <td>展示怎样使用 session.replaceDialog() 来创建循环。</td>
      </tr>
      <tr>
        <td><a href="https://github.com/Microsoft/BotBuilder/tree/master/Node/examples/basics-menus">basics-menus</a></td>
        <td>展示怎样为机器人创建简单的菜单系统。</td>
      </tr>
      <tr>
        <td><a href="https://github.com/Microsoft/BotBuilder/tree/master/Node/examples/basics-naturalLanguage">basics-naturalLanguage</a></td>
        <td>展示怎样使用 LuisDialog 来为机器人添加自然语言支持。</td>
      </tr>
      <tr>
        <td><a href="https://github.com/Microsoft/BotBuilder/tree/master/Node/examples/basics-multiTurn">basics-multiTurn</a></td>
        <td>展示怎样用瀑布流支持简单的多轮对话。</td>
      </tr>
      <tr>
        <td><a href="https://github.com/Microsoft/BotBuilder/tree/master/Node/examples/basics-firstRun">basics-firstRun</a></td>
        <td>展示怎样用一些中间件创建一个第一次运行的体验。</td>
      </tr>
      <tr>
        <td><a href="https://github.com/Microsoft/BotBuilder/tree/master/Node/examples/basics-logging">basics-logging</a></td>
        <td>展示怎样用一些中间件记录、过滤收到的信息。</td>
      </tr>
      <tr>
        <td><a href="https://github.com/Microsoft/BotBuilder/tree/master/Node/examples/basics-localization">basics-localization</a></td>
        <td>展示怎样为机器人添加多语言支持。</td>
      </tr>
      <tr>
        <td><a href="https://github.com/Microsoft/BotBuilder/tree/master/Node/examples/basics-customPrompt">basics-customPrompt</a></td>
        <td>展示怎样创建多变复杂的自定义提示。</td>
      </tr>
      <tr>
        <td><a href="https://github.com/Microsoft/BotBuilder/tree/master/Node/examples/basics-libraries">basics-libraries</a></td>
        <td>展示怎样打包一系列对话成库，以此来在多个机器人中共享。</td>
      </tr>
    </tbody>
  </table>

## 示例机器人 ##
  这里有一些机器人被设计来展示有哪些可能的对话途径。它们是为你的机器人的对话途径扩展一些闪光点特性的很好的代码片段。
  <table>
    <thead>
      <tr>
        <th><strong>示例</strong></th>
        <th><strong>描述</strong></th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td><a href="https://github.com/Microsoft/BotBuilder/tree/master/Node/examples/demo-skype">demo-skype</a></td>
        <td>一个用来展示在 Skype 上的能力的机器人。</td>
      </tr>
      <tr>
        <td><a href="https://github.com/Microsoft/BotBuilder/tree/master/Node/examples/demo-skype-calling">demo-skype-calling</a></td>
        <td>用来展示怎样构建一个 Skype 上的通话机器人。</td>
      </tr>
      <tr>
        <td><a href="https://github.com/Microsoft/BotBuilder/tree/master/Node/examples/demo-facebook">demo-facebook</a></td>
        <td>一个用来展示在 Facebook 上的能力的机器人。</td>
      </tr>
    </tbody>
  </table>

你能在 [Bot Builder SDK 示例库](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp) 上找到更多示例。
