# Prompts #

  - 输入采集
    - Prompts.choice()
    - Prompts.digits()
    - Prompts.confirm()
    - Prompts.record()
    - Prompts.action()

## 输入采集 ##
  Bot Builder 内置了很多 Prompts 用于采集用户的输入。
  <table>
    <thead>
      <tr>
        <th><strong>Prompt 类型</strong></th>
        <th><strong>描述</strong></th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td><a href="#promptschoice">Prompts.choice</a></td>
        <td>让用户从一个列表中选择。</td>
      </tr>
      <tr>
        <td><a href="#promptsdigits">Prompts.digits</a></td>
        <td>让用户输入一系列数字。</td>
      </tr>
      <tr>
        <td><a href="#promptsconfirm">Prompts.confirm</a></td>
        <td>让用户确认一个操作。</td>
      </tr>
      <tr>
        <td>让用户录制一段消息。</td>
      </tr>
      <tr>
        <td><a href="#promptsaction">Prompts.action</a></td>
        <td>发送一个原生 <a href="/en-us/node/builder/calling-reference/interfaces/_botbuilder_d_.iaction">action(操作)</a> 给 calling service 然后让机器人手动处理结果。</td>
      </tr>
    </tbody>
  </table>

  这些内置的 Prompts 被实现为对话框([Dialogs](https://docs.botframework.com/en-us/node/builder/chat/dialogs/)),这样他们就能通过调用[session.endDialogWithresult()](https://docs.botframework.com/en-us/node/builder/calling-reference/classes/_botbuilder_d_.callsession#enddialogwithresult)来返回用户的回复。任何 [DialogHandler](https://docs.botframework.com/en-us/node/builder/chat/dialogs/#dialog-handlers) 都能收到对话框的结果，但是瀑布流([waterfalls](https://docs.botframework.com/en-us/node/builder/chat/dialogs/#waterfall))更倾向于用最简单的方法处理 prompt 的结果。

  Prompts 返回给调用者一个 [IPromptResult](https://docs.botframework.com/en-us/node/builder/calling-reference/interfaces/_botbuilder_d_.ipromptresult.html) 对象。用户的回复将会包含于 [results.response](https://docs.botframework.com/en-us/node/builder/calling-reference/interfaces/_botbuilder_d_.ipromptresult.html#reponse) 字段，但如果用户没有输入正确地回复，这些字段可能为空。

## Prompts.choice() ##

  [Prompts.choice()](https://docs.botframework.com/en-us/node/builder/calling-reference/classes/_botbuilder_d_.prompts.html#choice) 方法让用户从一个列表中选择。用户的回复将会以 [IPromptChoiceResult](https://docs.botframework.com/en-us/node/builder/calling-reference/interfaces/_botbuilder_d_.ipromptchoiceresult.html) 返回。选项列表应以 [IRecognitionChoice](https://docs.botframework.com/en-us/node/builder/calling-reference/interfaces/_botbuilder_d_.irecognitionchoice) 对象的数组，传送给 prompt。

  你能配置 prompt 以使用语音识别来相应用户的选择，像这样：
  ```javascript
  calling.Prompts.choice(session, "Which department? support, billing, or claims?", [
      { name: 'support', speechVariation: ['support', 'customer service'] },
      { name: 'billing', speechVariation: ['billing'] },
      { name: 'claims', speechVariation: ['claims'] }
  ]);  
  ```
  或者通过 Skypes diling pad 用 DTMF 输入：
  ```javascript
  calling.Prompts.choice(session, "Which department? Press 1 for support, 2 for billing, or 3 for claims", [
      { name: 'support', dtmfVariation: '1' },
      { name: 'billing', dtmfVariation: '2' },
      { name: 'claims', dtmfVariation: '3' }
  ]);
  ```
  又或者同时使用语音识别和 DTMF：
  ```javascript
  bot.dialog('/departmentMenu', [
      function (session) {
          calling.Prompts.choice(session, "Which department? Press 1 for support, 2 for billing, 3 for claims, or star to return to previous menu.", [
              { name: 'support', dtmfVariation: '1', speechVariation: ['support', 'customer service'] },
              { name: 'billing', dtmfVariation: '2', speechVariation: ['billing'] },
              { name: 'claims', dtmfVariation: '3', speechVariation: ['claims'] },
              { name: '(back)', dtmfVariation: '*', speechVariation: ['back', 'previous'] }
          ]);
      },
      function (session, results) {
          if (results.response !== '(back)') {
              session.beginDialog('/' + results.response.entity + 'Menu');
          } else {
              session.endDialog();
          }
      },
      function (session) {
          // Loop menu
          session.replaceDialog('/departmentMenu');
      }
  ]);
  ```
  用户的选择将会以 [IFindMatchResult](https://docs.botframework.com/en-us/node/builder/calling-reference/interfaces/_botbuilder_d_.ifindmatchresult) 返回，这与 chat bots 类似。并且这些选项的名字将会分配给 [response.entity](https://docs.botframework.com/en-us/node/builder/calling-reference/interfaces/_botbuilder_d_.ifindmatchresult#entity) 属性。

## Prompts.digits() ##
  [Prompts.digits()](https://docs.botframework.com/en-us/node/builder/calling-reference/classes/_botbuilder_d_.prompts.html#digits) 方法让用户输入一串数字，可选地以某个字符结束。用户的回复将会以[IPromptDigitsResult](https://docs.botframework.com/en-us/node/builder/calling-reference/interfaces/_botbuilder_d_.ipromptdigitsresult.html)返回。
  ```javascript
  calling.Prompts.digits(session, "Please enter your account number followed by pound.", 10, { stopTones: ['#'] });
  ```

## Prompts.confirm() ##
  [Prompts.confirm()](https://docs.botframework.com/en-us/node/builder/calling-reference/classes/_botbuilder_d_.prompts.html#confirm)方法让用户确认某些操作。这个 prompt 基于调用只提供是和否选项的 choice prompt。用户能通过语音回复也能通过按“1”表示“是”，“2”表示“否”来回复。用户的回复将以[IPromptConfirmResult](https://docs.botframework.com/en-us/node/builder/calling-reference/interfaces/_botbuilder_d_.ipromptconfirmresult.html)返回。
  ```javascript
  calling.Prompts.confirm(session, "Would you like to end the call?");
  ```

## Prompts.record() ##
  [Prompts.record()](https://docs.botframework.com/en-us/node/builder/calling-reference/classes/_botbuilder_d_.prompts.html#record) 方法让用户录制一段消息。这个 prompt 基于调用只提供是和否选项的 choice prompt。录制的消息将以 [IPromptRecordResult](https://docs.botframework.com/en-us/node/builder/calling-reference/interfaces/_botbuilder_d_.ipromptrecordresult.html) 返回，录制的音频也将作为 {Buffer} 对象返回。
  ```javascript
  calling.Prompts.record(session, "Please leave a message after the beep.");
  ```

## Prompts.action() ##
  [Prompts.action()](https://docs.botframework.com/en-us/node/builder/calling-reference/classes/_botbuilder_d_.prompts.html#action) 方法让你能发送给 calling service 一个原生的 [action](https://docs.botframework.com/en-us/node/builder/calling-reference/interfaces/_botbuilder_d_.iaction) 对象。结果将会以 [IPromptActionResult](https://docs.botframework.com/en-us/node/builder/calling-reference/interfaces/_botbuilder_d_.ipromptactionresult.html) 返回，来让你的 bot 能手动处理。

  通常来说，你从不需要调用这个 prompt，但是一个你可能需要使用到这个的情况是你想要发送一个 playPrompt action 来给用户播放一个文件，然后你就能保持这个通话在线，以便于你能同时做另一个操作直到它完成为止。普通的 [session.send()](https://docs.botframework.com/en-us/node/builder/calling-reference/classes/_botbuilder_d_.callsession#send) 方法能让你播放一个文件；但如果那个 playPrompt action 不遵守 recognize action 或 record action，它却会自动中断通话。所以这给了你一个动态连续播放 prompt 的方法。如果你想要在你循环检查某个长期运行的操作是否完成的时候，同时播放等待音乐或空白间隔，你可能会用到这个 prompt。
