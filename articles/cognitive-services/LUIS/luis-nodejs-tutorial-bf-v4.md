---
title: 机器人 - Node.js - v4
titleSuffix: Azure Cognitive Services
description: 使用 Node.js，生成集成了语言理解 (LUIS) 的聊天机器人。 此聊天机器人使用人力资源应用来快速实现一个机器人解决方案。 此机器人是使用 Bot Framework 4 和 Azure Web 应用机器人生成的。
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 01/30/2019
ms.author: diberry
ms.openlocfilehash: 54bae5548764ed1f89a2ffb7992eb222a058c706
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2019
ms.locfileid: "57403645"
---
# <a name="tutorial-luis-bot-in-nodejs-with-the-bot-framework-4x-and-the-azure-web-app-bot"></a>教程：使用 Bot Framework 4.x 和 Azure Web 应用机器人的 Node.js 中的 LUIS 机器人
使用 Node.js 可以生成与语言理解 (LUIS) 相集成的聊天机器人。 此机器人使用 HomeAutomation 应用来实现机器人解决方案。 此机器人是使用 Azure [Web 应用机器人](https://docs.microsoft.com/azure/bot-service/)和 [Bot Framework 版本](https://github.com/Microsoft/botbuilder-js) v4 构建的。

**本教程介绍如何执行下列操作：**

> [!div class="checklist"]
> * 创建 Web 应用机器人。 此过程将创建新的 LUIS 应用。
> * 将预生成的域添加到新的 LUIS 模型
> * 下载 Web 机器人服务创建的项目
> * 在计算机本地启动机器人和模拟器
> * 为新 LUIS 意向修改机器人代码
> * 在机器人中查看话语结果

## <a name="prerequisites"></a>先决条件

* [机器人模拟器](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)


## <a name="create-web-app-bot"></a>创建 Web 应用机器人

1. 在 [Azure 门户](https://portal.azure.com)中，选择“创建新资源”。

2. 在搜索框中，搜索并选择“Web 应用机器人”。 选择“创建”。

3. 在“机器人服务”中提供所需的信息：

    |设置|目的|建议的设置|
    |--|--|--|
    |机器人名称|资源名称|`luis-nodejs-bot-` + `<your-name>`，例如 `luis-nodejs-bot-johnsmith`|
    |订阅|要在其中创建机器人的订阅。|你的主要订阅。
    |资源组|Azure 资源的逻辑组|创建一个新组用于存储此机器人使用的所有资源，并将该组命名为 `luis-nodejs-bot-resource-group`。|
    |位置|Azure 区域 - 不一定要与 LUIS 创作或发布区域相同。|`westus`|
    |定价层|用于服务请求限制和计费。|`F0` 是免费层。
    |应用程序名称|将机器人部署到云（例如，humanresourcesbot.azurewebsites.net）时，该名称用作子域。|`luis-nodejs-bot-` + `<your-name>`，例如 `luis-nodejs-bot-johnsmith`|
    |机器人模板|机器人框架设置 - 参阅下表|
    |LUIS 应用位置|必须与 LUIS 资源区域相同|`westus`|

4. 在“机器人模板设置”中选择以下选项，然后选择这些设置下面的“选择”按钮：

    |设置|目的|选项|
    |--|--|--|
    |SDK 版本|机器人框架版本|**SDK v4**|
    |SDK 语言|机器人的编程语言|**Node.js**|
    |聊天/基础机器人|机器人类型|**基础机器人**|
    
5. 选择“创建”。 随即会创建机器人服务并将其部署到 Azure。 在此过程中，会创建名为 `luis-nodejs-bot-XXXX` 的 LUIS 应用。 此名称基于上一部分所述的机器人名称和应用名称。

    [![创建 Web 应用机器人](./media/bfv4-nodejs/create-web-app-service.png)](./media/bfv4-nodejs/create-web-app-service.png#lightbox)

6. 请将此浏览器标签页保持打开状态。 对于 LUIS 门户中的任何步骤，都需要打开新的浏览器标签页。部署新机器人服务后，转到下一部分。

## <a name="add-prebuilt-domain-to-model"></a>将预生成的域添加到模型
在部署机器人服务的过程中，将创建包含意向和示例话语的新 LUIS 应用。 机器人针对以下意向提供到新 LUIS 应用的意向映射： 

|基础机器人 LUIS 意向|示例话语|
|--|--|
|取消|`stop`|
|Greeting|`hello`|
|帮助|`help`|
|无|应用域以外的任何内容。|

将预生成的 HomeAutomation 应用添加到模型以处理类似于 `Turn off the living room lights` 的话语

1. 转到 [LUIS](https://www.luis.ai) 门户并登录。
2. 在“我的应用”页上，选择“创建日期”列以按应用创建日期排序。 Azure 机器人服务在上一部分已创建一个新应用。 该应用的名称为 `luis-nodejs-bot-` + `<your-name>` + 4 个随机字符。
3. 打开该应用，并在顶部导航栏中选择“生成”部分。
4. 在左侧导航栏中选择“预生成域”。
5. 通过选择相应卡片上的“添加域”来选择“HomeAutomation”域。
6. 在右上侧菜单中选择“训练”。
7. 在右上侧菜单中选择“发布”。 

    现在，Azure 机器人服务创建的应用包含新的意向：

    |基础机器人的新意向|示例话语|
    |--|--|
    |HomeAutomation.TurnOn|`turn the fan to high`
    |HomeAutomation.TurnOff|`turn off ac please`|

## <a name="download-the-web-app-bot"></a>下载 Web 应用机器人 
若要开发 Web 应用机器人代码，请下载该代码并在本地计算机上使用。 

1. 在 Azure 门户中，仍在 Web 应用机器人资源上选择“应用程序设置”，并复制“botFilePath”和“botFileSecret”的值。 稍后需将这些值添加到环境文件。 

2. 在 Azure 门户中，从“机器人管理”部分选择“生成”。 

3. 选择“下载机器人源代码”。 

    [![下载基础机器人的 Web 应用机器人源代码](../../../includes/media/cognitive-services-luis/bfv4/download-code.png)](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

4. 压缩源代码时，有一条消息会提供用于下载代码的链接。 选择该链接。 

5. 将 zip 文件保存到本地计算机，然后解压缩该文件。 打开项目。 

6. 打开 bot.js 文件并找到 `const results = await this.luisRecognizer.recognize(context);`。 输入到机器人的用户话语通过此处发送到 LUIS。

   ```javascript
    /**
     * Driver code that does one of the following:
     * 1. Display a welcome card upon startup
     * 2. Use LUIS to recognize intents
     * 3. Start a greeting dialog
     * 4. Optionally handle Cancel or Help interruptions
     *
     * @param {Context} context turn context from the adapter
     */
    async onTurn(context) {
        // Create a dialog context
        const dc = await this.dialogs.createContext(context);

        if(context.activity.type === ActivityTypes.Message) {
            // Perform a call to LUIS to retrieve results for the current activity message.
            const results = await this.luisRecognizer.recognize(context);
            
            const topIntent = LuisRecognizer.topIntent(results);

            // handle conversation interrupts first
            const interrupted = await this.isTurnInterrupted(dc, results);
            if(interrupted) {
                return;
            }

            // Continue the current dialog
            const dialogResult = await dc.continue();

            switch(dialogResult.status) {
                case DialogTurnStatus.empty:
                    switch (topIntent) {
                        case GREETING_INTENT:
                            await dc.begin(GREETING_DIALOG);
                            break;

                        case NONE_INTENT:
                        default:
                            // help or no intent identified, either way, let's provide some help
                            // to the user
                            await dc.context.sendActivity(`I didn't understand what you just said to me. topIntent ${topIntent}`);
                            break;
                    }

                case DialogTurnStatus.waiting:
                    // The active dialog is waiting for a response from the user, so do nothing
                break;

                case DialogTurnStatus.complete:
                    await dc.end();
                    break;

                default:
                    await dc.cancelAll();
                    break;

            }

        } else if (context.activity.type === 'conversationUpdate' && context.activity.membersAdded[0].name === 'Bot') {
            // When activity type is "conversationUpdate" and the member joining the conversation is the bot
            // we will send our Welcome Adaptive Card.  This will only be sent once, when the Bot joins conversation
            // To learn more about Adaptive Cards, see https://aka.ms/msbot-adaptivecards for more details.
            const welcomeCard = CardFactory.adaptiveCard(WelcomeCard);
            await context.sendActivity({ attachments: [welcomeCard] });
        }
    }
    ```

    机器人将用户话语发送到 LUIS 并获取结果。 首要意向确定聊天流。 


## <a name="start-the-bot"></a>启动机器人
在更改任何代码或设置之前，请验证机器人是否正常工作。 

1. 在 Visual Studio Code 中打开终端窗口。 

2. 安装此机器人的 npm 依赖项。 

    ```bash
    npm install
    ```
3. 创建一个文件用于保存机器人代码查找的环境变量。 将该文件命名为 `.env`。 添加以下环境变量：

    <!--there is no code language that represents an .env file correctly-->
    ```env
    botFilePath=
    botFileSecret=
    ```

    将环境变量的值设置为在执行**[下载 Web 应用机器人](#download-the-web-app-bot)** 部分步骤 1 时从 Azure 机器人服务的“应用程序设置”中复制的值。

4. 在监视模式下启动机器人。 启动后，对代码进行任何更改都会导致应用自动重启。

    ```bash
    npm run watch
    ```

5. 启动机器人后，终端窗口会显示运行机器人的本地端口：

    ```console
    > basic-bot@0.1.0 start C:\Users\pattiowens\repos\BFv4\luis-nodejs-bot-src
    > node ./index.js NODE_ENV=development

    restify listening to http://[::]:3978
    
    Get the Emulator: https://aka.ms/botframework-emulator
    
    To talk to your bot, open the luis-nodejs-bot-pattiowens.bot file in the Emulator
    ```

## <a name="start-the-emulator"></a>启动模拟器

1. 启动机器人模拟器。 

2. 在机器人模拟器中，选择项目根目录中的 *.bot 文件。 此 `.bot` 文件包含消息的机器人 URL 终结点：

    [![机器人模拟器 v4](../../../includes/media/cognitive-services-luis/bfv4/bot-emulator-v4.png)](../../../includes/media/cognitive-services-luis/bfv4/bot-emulator-v4.png#lightbox)

3. 输入在执行**[下载 Web 应用机器人](#download-the-web-app-bot)** 部分的步骤 1 时从 Azure 机器人服务的“应用程序设置”中复制的机器人机密。 这样，模拟器便可以访问 .bot 文件中所有已加密的字段。

    ![机器人模拟器机密 v4](../../../includes/media/cognitive-services-luis/bfv4/bot-secret.png)


4. 在机器人模拟器中输入 `Hello`，并获取基础机器人的正确响应。

    [![模拟器中的基础机器人响应](../../../includes/media/cognitive-services-luis/bfv4/emulator-test.png)](../../../includes/media/cognitive-services-luis/bfv4/emulator-test.png#lightbox)

## <a name="modify-bot-code"></a>修改机器人代码 

在 `bot.js` 文件中，添加用于处理新意向的代码。 

1. 在该文件的顶部找到 **Supported LUIS Intents** 节，并添加 HomeAutomation 意向的常量：

   ```javascript
    // Supported LUIS Intents
    const GREETING_INTENT = 'Greeting';
    const CANCEL_INTENT = 'Cancel';
    const HELP_INTENT = 'Help';
    const NONE_INTENT = 'None';
    const TURNON_INTENT = 'HomeAutomation_TurnOn'; // new intent
    const TURNOFF_INTENT = 'HomeAutomation_TurnOff'; // new intent
    ```

    请注意，LUIS 门户应用中的域与意向之间的句点 `.` 已替换为下划线 `_`。 

2. 找到用于接收话语的 LUIS 预测的 **isTurnInterrupted**，并添加用于在控制台上列显结果的代码行。

   ```javascript
    /**
     * Look at the LUIS results and determine if we need to handle
     * an interruptions due to a Help or Cancel intent
     *
     * @param {DialogContext} dc - dialog context
     * @param {LuisResults} luisResults - LUIS recognizer results
     */
    async isTurnInterrupted(dc, luisResults) {
        console.log(JSON.stringify(luisResults));
    ...
    ```

    机器人的响应并不与 LUIS REST API 请求完全相同，因此，必须查看 JSON 的响应来了解差异。 文本和意向属性相同，但实体属性值已修改。 

    ```json
    {
        "$instance": {
            "HomeAutomation_Device": [
                {
                    "startIndex": 23,
                    "endIndex": 29,
                    "score": 0.9776345,
                    "text": "lights",
                    "type": "HomeAutomation.Device"
                }
            ],
            "HomeAutomation_Room": [
                {
                    "startIndex": 12,
                    "endIndex": 22,
                    "score": 0.9079433,
                    "text": "livingroom",
                    "type": "HomeAutomation.Room"
                }
            ]
        },
        "HomeAutomation_Device": [
            "lights"
        ],
        "HomeAutomation_Room": [
            "livingroom"
        ]
    }
    ```

3. 对于 `DialogTurnStatus.empty`，请将意向添加到 onTurn 方法的 switch 语句：

   ```javascript
    switch (topIntent) {
        case GREETING_INTENT:
            await dc.begin(GREETING_DIALOG);
            break;

        // New HomeAutomation.TurnOn intent
        case TURNON_INTENT: 

            await dc.context.sendActivity(`TurnOn intent found, entities included: ${JSON.stringify(results.entities)}`);
            break;

        // New HomeAutomation.TurnOff intent
        case TURNOFF_INTENT: 

            await dc.context.sendActivity(`TurnOff intent found, entities included: ${JSON.stringify(results.entities)}`);
            break;

        case NONE_INTENT:
        default:
            // help or no intent identified, either way, let's provide some help
            // to the user
            await dc.context.sendActivity(`I didn't understand what you just said to me. topIntent ${topIntent}`);
            break;
    }
    ```

## <a name="view-results-in-bot"></a>在机器人中查看结果

1. 在机器人模拟器中输入话语：`Turn on the livingroom lights to 50%`

2. 机器人将做出以下响应：

    ```json
    TurnOn intent found, entities included: {"$instance":{“HomeAutomation_Device”:[{“startIndex”:23,“endIndex”:29,“score”:0.9776345,“text”:“lights”,“type”:“HomeAutomation.Device”}],“HomeAutomation_Room”:[{“startIndex”:12,“endIndex”:22,“score”:0.9079433,“text”:“livingroom”,“type”:“HomeAutomation.Room”}]},“HomeAutomation_Device”:[“lights”],“HomeAutomation_Room”:[“livingroom”]}
    ```

## <a name="learn-more-about-bot-framework"></a>深入了解 Bot Framework
Azure 机器人服务使用 Bot Framework SDK。 详细了解该 SDK 和机器人框架：

* [Azure 机器人服务](https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0) v4 文档
* [Bot Builder 示例](https://github.com/Microsoft/botbuilder-samples)
* [Bot Builder SDK](https://docs.microsoft.com/javascript/api/botbuilder-core/?view=botbuilder-ts-latest)
* [Bot Builder 工具](https://github.com/Microsoft/botbuilder-tools)：

## <a name="next-steps"></a>后续步骤

我们创建了一个 Azure 机器人服务，复制了机器人机密和 .bot 文件路径，并下载了代码的 zip 文件。 我们已将预生成的 HomeAutomation 域添加到在创建新 Azure 机器人服务过程中创建的 LUIS 应用，然后重新训练和并发布了该应用。 我们提取了代码项目，创建了环境文件 (`.env`)，并设置了机器人机密和 .bot 文件路径。 在 bot.js 文件中，我们添加了用于处理两个新意向的代码。 然后，我们在模拟器中测试了机器人，以查看针对某个新意向的话语做出的 LUIS 响应。 


> [!div class="nextstepaction"]
> [在 LUIS 中生成自定义域](luis-quickstart-intents-only.md)
