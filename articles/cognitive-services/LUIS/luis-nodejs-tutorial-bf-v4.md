---
title: 语言理解机器人 Node.js v4
titleSuffix: Azure Cognitive Services
description: 使用 Node.js，生成集成了语言理解 (LUIS) 的聊天机器人。 此聊天机器人使用人力资源应用来快速实现一个机器人解决方案。 此机器人是使用 Bot Framework 4 和 Azure Web 应用机器人生成的。
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 06/24/2019
ms.author: diberry
ms.openlocfilehash: a06bd5a1a061de82230e93b867ea88e333b3cc93
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442557"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-nodejs"></a>教程：通过 Node.js 使用启用了语言理解的 Web 应用机器人 

使用 Node.js 生成集成了语言理解的聊天机器人 (LUIS)。 该机器人是使用 Azure [Web 应用机器人](https://docs.microsoft.com/azure/bot-service/)资源和 [Bot Framework 版本](https://github.com/Microsoft/botbuilder-dotnet) V4 构建的。

**本教程介绍如何执行下列操作：**

> [!div class="checklist"]
> * 创建 Web 应用机器人。 此过程将创建新的 LUIS 应用。
> * 下载 Web 机器人服务创建的机器人项目
> * 在计算机本地启动机器人和模拟器
> * 在机器人中查看话语结果

## <a name="prerequisites"></a>先决条件

* [机器人模拟器](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)


## <a name="create-a-web-app-bot-resource"></a>创建 Web 应用机器人资源

1. 在 [Azure 门户](https://portal.azure.com)中，选择“创建新资源”  。

1. 在搜索框中，搜索并选择“Web 应用机器人”  。 选择“创建”  。

1. 在“机器人服务”  中提供所需的信息：

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
    |应用服务计划/位置|请勿更改提供的默认值。|
    |Application Insights|请勿更改提供的默认值。|
    |Microsoft 应用 ID 和密码|请勿更改提供的默认值。|

1. 在“机器人模板”中选择以下选项，然后选择这些设置下面的“选择”按钮   ：

    |设置|目的|选项|
    |--|--|--|
    |SDK 版本|机器人框架版本|**SDK v4**|
    |SDK 语言|机器人的编程语言|**Node.js**|
    |机器人|机器人类型|**基础机器人**|
    
1. 选择“创建”  。 随即会创建机器人服务并将其部署到 Azure。 在此过程中，会创建名为 `luis-nodejs-bot-XXXX` 的 LUIS 应用。 此名称基于 Azure 机器人服务应用名称。

    [![创建 Web 应用机器人](./media/bfv4-nodejs/create-web-app-service.png)](./media/bfv4-nodejs/create-web-app-service.png#lightbox)

    请等到创建机器人服务后再继续操作。

## <a name="the-bot-has-a-language-understanding-model"></a>该机器人包含语言理解模型

在机器人服务创建过程中，还会创建带有意图和示例言语的新 LUIS 应用。 机器人针对以下意向提供到新 LUIS 应用的意向映射： 

|基础机器人 LUIS 意向|示例话语|
|--|--|
|预定机票|`Travel to Paris`|
|取消|`bye`|
|无|应用域以外的任何内容。|

## <a name="test-the-bot-in-web-chat"></a>通过网上聊天测试机器人

1. 在 Azure 门户中，为新机器人选择“通过网上聊天执行测试”  。 
1. 在“键入消息”文本框中，输入文本 `hello`  。 机器人将答复有关机器人框架的信息，还会答复该特定 LUIS 模型的示例查询，例如预订飞往巴黎的机票。 

    ![Azure 门户的屏幕截图，输入文本“你好”。](./media/bfv4-nodejs/ask-bot-question-in-portal-test-in-web-chat.png)

    可以使用测试功能对机器人进行快速测试。 要进行更完整的测试（包括调试），请下载机器人代码并使用 Visual Studio。 

## <a name="download-the-web-app-bot-source-code"></a>下载 Web 应用机器人源代码
若要开发 Web 应用机器人代码，请下载该代码并在本地计算机上使用。 

1. 在 Azure 门户中，从“机器人管理”部分选择“生成”   。 

1. 选择“下载机器人源代码”。  

    [![下载基础机器人的 Web 应用机器人源代码](../../../includes/media/cognitive-services-luis/bfv4/download-code.png)](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

1. 弹出询问“是否在下载的 zip 文件中包含应用设置?”的对话框时，选择“是”   。

1. 压缩源代码时，有一条消息会提供用于下载代码的链接。 选择该链接。 

1. 将 zip 文件保存到本地计算机，然后解压缩该文件。 使用 Visual Studio 打开项目。 

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>查看将言语发送到 LUIS 并获得答复的代码

1. 打开“dialogs”>“luisHelper.js”文件  。 输入到机器人的用户话语通过此处发送到 LUIS。 该方法以 bookDetails JSON 对象的形式返回 LUIS 的答复  。 创建自己的机器人时，还应创建自己的对象以从 LUIS 返回详细信息。 

    ```nodejs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    const { LuisRecognizer } = require('botbuilder-ai');
    
    class LuisHelper {
        /**
         * Returns an object with preformatted LUIS results for the bot's dialogs to consume.
         * @param {*} logger
         * @param {TurnContext} context
         */
        static async executeLuisQuery(logger, context) {
            const bookingDetails = {};
    
            try {
                const recognizer = new LuisRecognizer({
                    applicationId: process.env.LuisAppId,
                    endpointKey: process.env.LuisAPIKey,
                    endpoint: `https://${ process.env.LuisAPIHostName }`
                }, {}, true);
    
                const recognizerResult = await recognizer.recognize(context);
    
                const intent = LuisRecognizer.topIntent(recognizerResult);
    
                bookingDetails.intent = intent;
    
                if (intent === 'Book_flight') {
                    // We need to get the result from the LUIS JSON which at every level returns an array
    
                    bookingDetails.destination = LuisHelper.parseCompositeEntity(recognizerResult, 'To', 'Airport');
                    bookingDetails.origin = LuisHelper.parseCompositeEntity(recognizerResult, 'From', 'Airport');
    
                    // This value will be a TIMEX. And we are only interested in a Date so grab the first result and drop the Time part.
                    // TIMEX is a format that represents DateTime expressions that include some ambiguity. e.g. missing a Year.
                    bookingDetails.travelDate = LuisHelper.parseDatetimeEntity(recognizerResult);
                }
            } catch (err) {
                logger.warn(`LUIS Exception: ${ err } Check your LUIS configuration`);
            }
            return bookingDetails;
        }
    
        static parseCompositeEntity(result, compositeName, entityName) {
            const compositeEntity = result.entities[compositeName];
            if (!compositeEntity || !compositeEntity[0]) return undefined;
    
            const entity = compositeEntity[0][entityName];
            if (!entity || !entity[0]) return undefined;
    
            const entityValue = entity[0][0];
            return entityValue;
        }
    
        static parseDatetimeEntity(result) {
            const datetimeEntity = result.entities['datetime'];
            if (!datetimeEntity || !datetimeEntity[0]) return undefined;
    
            const timex = datetimeEntity[0]['timex'];
            if (!timex || !timex[0]) return undefined;
    
            const datetime = timex[0].split('T')[0];
            return datetime;
        }
    }
    
    module.exports.LuisHelper = LuisHelper;
    ```

1. 打开“对话”>“bookingDialog.js”以了解如何使用 BookingDetails 对象来管理会话流  。 逐步询问旅行详情，然后确认整个订单并最终向用户复述。 

    ```nodejs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    const { TimexProperty } = require('@microsoft/recognizers-text-data-types-timex-expression');
    const { ConfirmPrompt, TextPrompt, WaterfallDialog } = require('botbuilder-dialogs');
    const { CancelAndHelpDialog } = require('./cancelAndHelpDialog');
    const { DateResolverDialog } = require('./dateResolverDialog');
    
    const CONFIRM_PROMPT = 'confirmPrompt';
    const DATE_RESOLVER_DIALOG = 'dateResolverDialog';
    const TEXT_PROMPT = 'textPrompt';
    const WATERFALL_DIALOG = 'waterfallDialog';
    
    class BookingDialog extends CancelAndHelpDialog {
        constructor(id) {
            super(id || 'bookingDialog');
    
            this.addDialog(new TextPrompt(TEXT_PROMPT))
                .addDialog(new ConfirmPrompt(CONFIRM_PROMPT))
                .addDialog(new DateResolverDialog(DATE_RESOLVER_DIALOG))
                .addDialog(new WaterfallDialog(WATERFALL_DIALOG, [
                    this.destinationStep.bind(this),
                    this.originStep.bind(this),
                    this.travelDateStep.bind(this),
                    this.confirmStep.bind(this),
                    this.finalStep.bind(this)
                ]));
    
            this.initialDialogId = WATERFALL_DIALOG;
        }
    
        /**
         * If a destination city has not been provided, prompt for one.
         */
        async destinationStep(stepContext) {
            const bookingDetails = stepContext.options;
    
            if (!bookingDetails.destination) {
                return await stepContext.prompt(TEXT_PROMPT, { prompt: 'To what city would you like to travel?' });
            } else {
                return await stepContext.next(bookingDetails.destination);
            }
        }
    
        /**
         * If an origin city has not been provided, prompt for one.
         */
        async originStep(stepContext) {
            const bookingDetails = stepContext.options;
    
            // Capture the response to the previous step's prompt
            bookingDetails.destination = stepContext.result;
            if (!bookingDetails.origin) {
                return await stepContext.prompt(TEXT_PROMPT, { prompt: 'From what city will you be travelling?' });
            } else {
                return await stepContext.next(bookingDetails.origin);
            }
        }
    
        /**
         * If a travel date has not been provided, prompt for one.
         * This will use the DATE_RESOLVER_DIALOG.
         */
        async travelDateStep(stepContext) {
            const bookingDetails = stepContext.options;
    
            // Capture the results of the previous step
            bookingDetails.origin = stepContext.result;
            if (!bookingDetails.travelDate || this.isAmbiguous(bookingDetails.travelDate)) {
                return await stepContext.beginDialog(DATE_RESOLVER_DIALOG, { date: bookingDetails.travelDate });
            } else {
                return await stepContext.next(bookingDetails.travelDate);
            }
        }
    
        /**
         * Confirm the information the user has provided.
         */
        async confirmStep(stepContext) {
            const bookingDetails = stepContext.options;
    
            // Capture the results of the previous step
            bookingDetails.travelDate = stepContext.result;
            const msg = `Please confirm, I have you traveling to: ${ bookingDetails.destination } from: ${ bookingDetails.origin } on: ${ bookingDetails.travelDate }.`;
    
            // Offer a YES/NO prompt.
            return await stepContext.prompt(CONFIRM_PROMPT, { prompt: msg });
        }
    
        /**
         * Complete the interaction and end the dialog.
         */
        async finalStep(stepContext) {
            if (stepContext.result === true) {
                const bookingDetails = stepContext.options;
    
                return await stepContext.endDialog(bookingDetails);
            } else {
                return await stepContext.endDialog();
            }
        }
    
        isAmbiguous(timex) {
            const timexPropery = new TimexProperty(timex);
            return !timexPropery.types.has('definite');
        }
    }
    
    module.exports.BookingDialog = BookingDialog;
    ```


## <a name="install-dependencies-and-start-the-bot-code-in-visual-studio"></a>在 Visual Studio 中安装依赖项并启动机器人代码

1. 在 VSCode 集成终端中，使用命令 `npm install` 安装依赖项。
1. 同样从集成终端，使用命令 `npm start` 启动机器人。 


## <a name="use-the-bot-emulator-to-test-the-bot"></a>使用机器人模拟器测试机器人

1. 启动机器人模拟器并选择“打开机器人”  。
1. 在“打开机器人”弹出对话框中，输入机器人 URL（如 `http://localhost:3978/api/messages`）  。 `/api/messages` 路由是机器人的 Web 地址。
1. 输入在下载的机器人代码根目录下 .env 文件中找到的“Microsoft 应用 ID”和“Microsoft 应用密码”    。

    或者，可以创建新的机器人配置，并从机器人的 Visual Studio 项目中的 .env 文件中复制 `MicrosoftAppId` 和 `MicrosoftAppPassword`  。 机器人配置文件的名称应与机器人名称相同。 

    ```json
    {
        "name": "<bot name>",
        "description": "<bot description>",
        "services": [
            {
                "type": "endpoint",
                "appId": "<appId from .env>",
                "appPassword": "<appPassword from .env>",
                "endpoint": "http://localhost:3978/api/messages",
                "id": "<don't change this value>",
                "name": "http://localhost:3978/api/messages"
            }
        ],
        "padlock": "",
        "version": "2.0",
        "overrides": null,
        "path": "<local path to .bot file>"
    }
    ```

1. 在机器人模拟器中输入 `Hello`应可获得基本机器人的答复，与在“通过网上聊天执行测试”中收到的答复相同  。

    [![模拟器中的基础机器人响应](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png#lightbox)


## <a name="ask-bot-a-question-for-the-book-flight-intent"></a>向机器人询问关于预定机票的问题

1. 在机器人模拟器中，输入以下言语来预订机票： 

    ```bot
    Book a flight from Paris to Berlin on March 22, 2020
    ```

    机器人模拟器将要求你进行确认。 

1. 请选择“是”。  机器人将以其操作摘要答复你。 
1. 从机器人模拟器的日志中，选择包含 `Luis Trace` 的行。 这将显示 LUIS 对言语的意图和本质的 JSON 答复。

    [![模拟器中的基础机器人响应](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)


[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>后续步骤

查看更多使用聊天机器人的[示例](https://github.com/microsoft/botframework-solutions)。 

> [!div class="nextstepaction"]
> [构建包含自定义使用者域的语言理解应用](luis-quickstart-intents-only.md)