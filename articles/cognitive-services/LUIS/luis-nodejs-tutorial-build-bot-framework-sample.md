---
title: 在 Azure 中使用 Bot Builder SDK for Node.js 将 LUIS 与机器人进行集成 | Microsoft Docs
description: 使用 Bot Framework 生成与 LUIS 应用程序集成的机器人。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/06/2018
ms.author: diberry
ms.openlocfilehash: 6d6937105b11d94138b51660dc9f3c5e682e19bc
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2018
ms.locfileid: "39224069"
---
# <a name="integrate-luis-with-a-bot-using-the-bot-builder-sdk-for-nodejs"></a>使用 Bot Builder SDK for Node.js 将 LUIS 与机器人进行集成

本教程介绍如何使用 [Bot Framework][BotFramework]来生成与 LUIS 应用集成的机器人。

## <a name="prerequisite"></a>先决条件

在创建机器人前，请按照[创建应用](./luis-get-started-create-app.md)中的步骤生成该机器人使用的 LUIS 应用。

机器人会响应 LUIS 应用中来自 HomeAutomation 域的意向。 对于其中每个意向，LUIS 应用均会提供映射到它们的意向。 机器人会提供一个对话框，该对话框可处理 LUIS 检测到的意向。

| 意向 | 示例陈述 | 机器人功能 |
|:----:|:----------:|---|
| HomeAutomation.TurnOn | 开灯。 | 检测到 `HomeAutomation.TurnOn` 时，机器人会调用 `TurnOnDialog`。 可以在此对话框中调用 IoT 服务以打开设备，并告知用户设备已打开。 |
| HomeAutomation.TurnOff | 请关掉卧室灯。 | 检测到 `HomeAutomation.TurnOff` 时，机器人会调用 `TurnOffDialog`。 可以在此对话框中调用 IoT 服务以关闭设备，并告知用户设备已关闭。 |


## <a name="create-a-language-understanding-bot-with-bot-service"></a>使用机器人服务创建语言理解机器人

1. 在 [Azure 门户](https://portal.azure.com)的菜单边栏选项卡中，选择“创建新资源”，并选择“查看全部”。

    ![新建资源](./media/luis-tutorial-node-bot/bot-service-creation.png)

2. 在搜索框中，搜索“Web 应用机器人”。 

    ![新建资源](./media/luis-tutorial-node-bot/bot-service-selection.png)

3. 在“机器人服务边栏选项卡”中，提供所需信息，然后选择“创建”。 此操作可创建机器人服务和 LUIS 应用并将其部署到 Azure。 如果想要使用[语音启动](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming)，请在创建机器人前查看[区域要求](luis-resources-faq.md#what-luis-regions-support-bot-framework-speech-priming)。 
    * 将“应用名称”设置为机器人名称。 将机器人部署到云（例如，mynotesbot.azurewebsites.net）时，该名称用作子域。 <!-- This name is also used as the name of the LUIS app associated with your bot. Copy it to use later, to find the LUIS app associated with the bot. -->
    * 选择“订阅”、“[资源组](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)”、“应用服务计划”和“[位置](https://azure.microsoft.com/regions/)”。
    * 对于“机器人模板”字段，选择“语言理解 (Node.js)”模板。
    * 选择“LUIS 应用位置”。 这是创建应用的创作[区域][LUIS]。
    * 勾选法律声明的确认复选框。 法律声明条款在该复选框下方。

    ![“机器人服务”边栏选项卡](./media/luis-tutorial-node-bot/bot-service-setting-callout-template.png)


4. 确认已部署机器人服务。
    * 选择“通知”（钟形图标，位于 Azure 门户的上边缘）。 通知将从“部署已开始”更改为“部署已成功”。
    * 通知更改为“部署已成功”后，选择该通知上的“转到资源”。

## <a name="try-the-default-bot"></a>尝试使用默认机器人

查看“通知”，确认已部署机器人。 通知将从“正在进行部署...”更改为“部署已成功”。 选择“转到资源”按钮，打开机器人的资源边栏选项卡。

<!-- this step isn't supposed to be necessary -->
## <a name="install-npm-resources"></a>安装 NPM 资源
按以下步骤安装 NPM 包：

1. 从 Web 应用机器人的“机器人管理”部分选择“生成”。 

2. 随即打开一个新的浏览器窗口。 选择“打开联机代码编辑器”。

3. 在顶部导航栏中，选择 Web 应用机器人名称 `homeautomationluisbot`。 

4. 在下拉列表中，选择“打开 Kudu 控制台”。

5. 新的浏览器窗口将打开。 在控制台中输入以下命令：

    ```
    cd site\wwwroot && npm install
    ```

    等待安装过程结束。 返回到第一个浏览器窗口。 

## <a name="test-in-web-chat"></a>通过网上聊天执行测试
注册机器人后，选择“通过网上聊天执行测试”，打开“网上聊天”窗格。 在网上聊天中键入“你好”。

  ![通过网上聊天测试机器人](./media/luis-tutorial-node-bot/bot-service-web-chat.png)

机器人响应说：“你已经进行了问候。 你说了：你好。” 这可确认机器人已接收消息，并将其传递到其创建的默认 LUIS 应用。 此默认 LUIS 应用检测到了问候语意向。 在下一步中，需要将机器人连接到之前创建的 LUIS 应用，而不是默认 LUIS 应用。

## <a name="connect-your-luis-app-to-the-bot"></a>将 LUIS 应用连接到机器人

在第一个浏览器窗口中打开“应用程序设置”，然后编辑“LuisAppId”字段，以包含 LUIS 应用的应用程序 ID。

  ![更新 Azure 中的 LUIS 应用 ID](./media/luis-tutorial-node-bot/bot-service-app-id.png)

如果没有 LUIS 应用 ID，请使用登录 Azure 所用的同一帐户登录到 [LUIS](luis-reference-regions.md) 网站。 选择“我的应用”。 

1. 查找之前创建的 LUIS 应用，该应用包含 HomeAutomation 域中的意向和实体。

2. 在 LUIS 应用的“设置”页上，查找并复制应用 ID。

3. 如果尚未训练应用，请选择右上角的“训练”按钮来训练应用。

4. 如果尚未发布应用，请选择顶部导航栏中的“发布”，打开“发布”页。 选择“生产”槽和“发布”按钮。

## <a name="modify-the-bot-code"></a>修改机器人代码

转到第二个浏览器窗口（如果该窗口仍处于打开状态），或在第一个浏览器窗口中，选择“生成”，然后选择“打开联机代码编辑器”。

   ![打开联机代码编辑器](./media/luis-tutorial-node-bot/bot-service-build.png)

在代码编辑器中，打开 `app.js`。 它包含以下代码：

```javascript
/*-----------------------------------------------------------------------------
A simple Language Understanding (LUIS) bot for the Microsoft Bot Framework. 
-----------------------------------------------------------------------------*/

var restify = require('restify');
var builder = require('botbuilder');
var botbuilder_azure = require("botbuilder-azure");

// Setup Restify Server
var server = restify.createServer();
server.listen(process.env.port || process.env.PORT || 3978, function () {
   console.log('%s listening to %s', server.name, server.url); 
});
  
// Create chat connector for communicating with the Bot Framework Service
var connector = new builder.ChatConnector({
    appId: process.env.MicrosoftAppId,
    appPassword: process.env.MicrosoftAppPassword,
    openIdMetadata: process.env.BotOpenIdMetadata 
});

// Listen for messages from users 
server.post('/api/messages', connector.listen());

/*----------------------------------------------------------------------------------------
* Bot Storage: This is a great spot to register the private state storage for your bot. 
* We provide adapters for Azure Table, CosmosDb, SQL Azure, or you can implement your own!
* For samples and documentation, see: https://github.com/Microsoft/BotBuilder-Azure
* ---------------------------------------------------------------------------------------- */

var tableName = 'botdata';
var azureTableClient = new botbuilder_azure.AzureTableClient(tableName, process.env['AzureWebJobsStorage']);
var tableStorage = new botbuilder_azure.AzureBotStorage({ gzipData: false }, azureTableClient);

// Create your bot with a function to receive messages from the user
// This default message handler is invoked if the user's utterance doesn't
// match any intents handled by other dialogs.
var bot = new builder.UniversalBot(connector, function (session, args) {
    session.send('You reached the default message handler. You said \'%s\'.', session.message.text);
});

bot.set('storage', tableStorage);

// Make sure you add code to validate these fields
var luisAppId = process.env.LuisAppId;
var luisAPIKey = process.env.LuisAPIKey;
var luisAPIHostName = process.env.LuisAPIHostName || 'westus.api.cognitive.microsoft.com';

const LuisModelUrl = 'https://' + luisAPIHostName + '/luis/v2.0/apps/' + luisAppId + '?subscription-key=' + luisAPIKey;

// Create a recognizer that gets intents from LUIS, and add it to the bot
var recognizer = new builder.LuisRecognizer(LuisModelUrl);
bot.recognizer(recognizer);

// Add a dialog for each intent that the LUIS app recognizes.
// See https://docs.microsoft.com/bot-framework/nodejs/bot-builder-nodejs-recognize-intent-luis 
bot.dialog('GreetingDialog',
    (session) => {
        session.send('You reached the Greeting intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'Greeting'
})

bot.dialog('HelpDialog',
    (session) => {
        session.send('You reached the Help intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'Help'
})

bot.dialog('CancelDialog',
    (session) => {
        session.send('You reached the Cancel intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'Cancel'
})
```

已忽略 app.js 中的现有意向。 可以保留它们。 

## <a name="add-a-dialog-that-matches-homeautomationturnon"></a>添加与 HomeAutomation.TurnOn 匹配的对话框

复制以下代码并将其添加到 `app.js`。

```javascript
bot.dialog('TurnOn',
    (session) => {
        session.send('You reached the TurnOn intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'HomeAutomation.TurnOn'
})
```

附加到对话框的 [triggerAction][triggerAction] 上的“[匹配][matches]”选项会指定意向名称。 每当机器人接收到来自用户的陈述时，识别器就会运行。 如果检测到的得分最高的意向与绑定到对话框的 `triggerAction` 相匹配，机器人会调用该对话框。

## <a name="add-a-dialog-that-matches-homeautomationturnoff"></a>添加与 HomeAutomation.TurnOff 匹配的对话框

复制以下代码并将其添加到 `app.js`。

```javascript
bot.dialog('TurnOff',
    (session) => {
        session.send('You reached the TurnOff intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'HomeAutomation.TurnOff'
})
```
## <a name="test-the-bot"></a>测试机器人

在 Azure 门户中，选择“通过网上聊天执行测试”以测试机器人。 请尝试键入“开灯”和“关掉加热器”等消息，以调用添加到其中的意向。
   ![在网上聊天中测试 HomeAutomation 机器人](./media/luis-tutorial-node-bot/bot-service-chat-results.png)

> [!TIP]
> 如果发现机器人并未能始终识别正确意向或实体，请提供更多示例陈述对其进行训练，从而提高 LUIS 应用的性能。 无需对机器人代码进行任何修改即可重新训练 LUIS 应用。 请参阅[添加示例陈述](https://docs.microsoft.com/azure/cognitive-services/LUIS/add-example-utterances)和[训练和测试 LUIS 应用](https://docs.microsoft.com/azure/cognitive-services/LUIS/luis-interactive-test)。

## <a name="learn-more-about-bot-framework"></a>深入了解 Bot Framework
深入了解 [Bot Framework](https://dev.botframework.com/) 以及 [3.x](https://github.com/Microsoft/BotBuilder) 和 [4.x](https://github.com/Microsoft/botbuilder-js) SDK。

## <a name="next-steps"></a>后续步骤

<!-- From trying the bot, you can see that the recognizer can trigger interruption of the currently active dialog. Allowing and handling interruptions is a flexible design that accounts for what users really do. Learn more about the various actions you can associate with a recognized intent.--> 还可以尝试将其他意向（如帮助、取消和问候语）添加到 LUIS 应用。 然后添加新意向的对话框，并使用机器人对其进行测试。 

<!-- 
> [!NOTE] 
> The default LUIS app that the template created contains example utterances for Cancel, Greeting, and Help intents. In the list of apps, find the app that begins with the name specified in **App name** in the **Bot Service** blade when you created the Bot Service. -->

> [!div class="nextstepaction"]
> [添加意向](./luis-how-to-add-intents.md)
> [语音启动](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming)


[intentDialog]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.intentdialog.html

[intentDialog_matches]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.intentdialog.html#matches 

[NotesSample]: https://github.com/Microsoft/BotFramework-Samples/tree/master/docs-samples/Node/basics-naturalLanguage

[triggerAction]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.dialog.html#triggeraction

[confirmPrompt]: https://docs.botframework.com/node/builder/chat-reference/interfaces/_botbuilder_d_.itriggeractionoptions.html#confirmprompt

[waterfall]: bot-builder-nodejs-dialog-manage-conversation-flow.md#manage-conversation-flow-with-a-waterfall

[session_userData]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.session.html#userdata

[EntityRecognizer_findEntity]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.entityrecognizer.html#findentity

[matches]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.itriggeractionoptions.html#matches

[LUISAzureDocs]: https://docs.microsoft.com/azure/cognitive-services/LUIS/Home

[Dialog]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.dialog.html

[IntentRecognizerSetOptions]: https://docs.botframework.com/node/builder/chat-reference/interfaces/_botbuilder_d_.iintentrecognizersetoptions.html

[LuisRecognizer]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.luisrecognizer

[LUISConcepts]: https://docs.botframework.com/node/builder/guides/understanding-natural-language/

[DisambiguationSample]: https://github.com/Microsoft/BotBuilder/tree/master/Node/examples/feature-onDisambiguateRoute

[IDisambiguateRouteHandler]: https://docs.botframework.com/node/builder/chat-reference/interfaces/_botbuilder_d_.idisambiguateroutehandler.html

[RegExpRecognizer]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.regexprecognizer.html

[AlarmBot]: https://github.com/Microsoft/BotBuilder/blob/master/Node/examples/basics-naturalLanguage/app.js

[LUISBotSample]: https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/intelligence-LUIS

[UniversalBot]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.universalbot.html


<!-- Old Links -->
[Github-BotFramework-Emulator-Download]: https://aka.ms/bot-framework-emulator
[Github-LUIS-Samples]: https://github.com/Microsoft/LUIS-Samples
[Github-LUIS-Samples-node-hotel-bot]: https://github.com/Microsoft/LUIS-Samples/tree/master/bot-integration-samples/hotel-finder/nodejs
[NodeJs]: https://nodejs.org/
[BFPortal]: https://dev.botframework.com/
[RegisterInstructions]: https://docs.microsoft.com/bot-framework/portal-register-bot
[BotFramework]: https://docs.microsoft.com/bot-framework/
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions

