---
title: 教程：语言理解机器人 Node.js v4
description: 在本教程中，将使用 Node.js 生成集成了语言理解 (LUIS) 的聊天机器人。 此聊天机器人使用人力资源应用来快速实现一个机器人解决方案。 此机器人是使用 Bot Framework 4 和 Azure Web 应用机器人生成的。
ms.topic: tutorial
ms.date: 06/23/2020
ms.custom: devx-track-javascript
ms.openlocfilehash: 522502a043778d6f482c1756506063dfa6515ca3
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2020
ms.locfileid: "87405018"
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

1. 在 [Azure 门户](https://portal.azure.com)中，选择“创建新资源”。

1. 在搜索框中，搜索并选择“Web 应用机器人”。 选择“创建”。

1. 在“机器人服务”中提供所需的信息：

    |设置|目的|建议的设置|
    |--|--|--|
    |机器人句柄|资源名称|`luis-nodejs-bot-` + `<your-name>`，例如 `luis-nodejs-bot-johnsmith`|
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

1. 在“机器人模板”中选择以下选项，然后选择这些设置下面的“选择”按钮 ：

    |设置|目的|选项|
    |--|--|--|
    |SDK 语言|机器人的编程语言|**Node.js**|
    |机器人|机器人类型|**基础机器人**|

1. 选择“创建”。 随即会创建机器人服务并将其部署到 Azure。 在此过程中，会创建名为 `luis-nodejs-bot-XXXX` 的 LUIS 应用。 此名称基于 Azure 机器人服务应用名称。

    > [!div class="mx-imgBorder"]
    > [![创建 Web 应用机器人](./media/bfv4-nodejs/create-web-app-service.png)](./media/bfv4-nodejs/create-web-app-service.png#lightbox)

    请等到创建机器人服务后再继续操作。

1. 选择通知中的`Go to resource`以转到 Web 页面机器人页面。

## <a name="the-bot-has-a-language-understanding-model"></a>该机器人包含语言理解模型

在机器人服务创建过程中，还会创建带有意图和示例言语的新 LUIS 应用。 机器人针对以下意向提供到新 LUIS 应用的意向映射：

|基础机器人 LUIS 意向|示例话语|
|--|--|
|预定机票|`Travel to Paris`|
|取消|`bye`|
|GetWeather|`what's the weather like?`|
|无|应用域以外的任何内容。|

## <a name="test-the-bot-in-web-chat"></a>通过网上聊天测试机器人

1. 在 Azure 门户中，为新机器人选择“通过网上聊天执行测试”。
1. 在“键入消息”文本框中，输入文本 `Book a flight from Seattle to Berlin tomorrow`。 机器人将使用要预定机票的验证答复你。

    ![Azure 门户的屏幕截图，输入文本“你好”。](./media/bfv4-nodejs/ask-bot-question-in-portal-test-in-web-chat.png)

    可使用测试功能来快速测试机器人。 要进行更完整的测试（包括调试），请下载机器人代码并使用 Visual Studio Code。

## <a name="download-the-web-app-bot-source-code"></a>下载 Web 应用机器人源代码
若要开发 Web 应用机器人代码，请下载该代码并在本地计算机上使用。

1. 在 Azure 门户中，从“机器人管理”部分选择“生成” 。

1. 选择“下载机器人源代码”。

    [![下载基础机器人的 Web 应用机器人源代码](../../../includes/media/cognitive-services-luis/bfv4/download-code.png)](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

1. 弹出询问“是否在下载的 zip 文件中包含应用设置?”的对话框时，选择“是” 。 这会提供 LUIS 设置。

1. 压缩源代码时，有一条消息会提供用于下载代码的链接。 选择该链接。

1. 将 zip 文件保存到本地计算机，然后解压缩该文件。 使用 Visual Studio Code 打开项目文件夹。

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>查看将言语发送到 LUIS 并获得答复的代码

1. 若要将用户话语发送到 LUIS 预测终结点，请打开“dialogs -> flightBookingRecognizer.js”文件。 输入到机器人的用户话语通过此处发送到 LUIS。 来自 LUIS 的响应从 executeLuisQuery 方法返回。

    ```javascript
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.

    const { LuisRecognizer } = require('botbuilder-ai');

    class FlightBookingRecognizer {
        constructor(config) {
            const luisIsConfigured = config && config.applicationId && config.endpointKey && config.endpoint;
            if (luisIsConfigured) {
                // Set the recognizer options depending on which endpoint version you want to use e.g v2 or v3.
                // More details can be found in https://docs.microsoft.com/en-gb/azure/cognitive-services/luis/luis-migration-api-v3
                const recognizerOptions = {
                    apiVersion: 'v3'
                };

                this.recognizer = new LuisRecognizer(config, recognizerOptions);
            }
        }

        get isConfigured() {
            return (this.recognizer !== undefined);
        }

        /**
         * Returns an object with preformatted LUIS results for the bot's dialogs to consume.
         * @param {TurnContext} context
         */
        async executeLuisQuery(context) {
            return await this.recognizer.recognize(context);
        }

        getFromEntities(result) {
            let fromValue, fromAirportValue;
            if (result.entities.$instance.From) {
                fromValue = result.entities.$instance.From[0].text;
            }
            if (fromValue && result.entities.From[0].Airport) {
                fromAirportValue = result.entities.From[0].Airport[0][0];
            }

            return { from: fromValue, airport: fromAirportValue };
        }

        getToEntities(result) {
            let toValue, toAirportValue;
            if (result.entities.$instance.To) {
                toValue = result.entities.$instance.To[0].text;
            }
            if (toValue && result.entities.To[0].Airport) {
                toAirportValue = result.entities.To[0].Airport[0][0];
            }

            return { to: toValue, airport: toAirportValue };
        }

        /**
         * This value will be a TIMEX. And we are only interested in a Date so grab the first result and drop the Time part.
         * TIMEX is a format that represents DateTime expressions that include some ambiguity. e.g. missing a Year.
         */
        getTravelDate(result) {
            const datetimeEntity = result.entities.datetime;
            if (!datetimeEntity || !datetimeEntity[0]) return undefined;

            const timex = datetimeEntity[0].timex;
            if (!timex || !timex[0]) return undefined;

            const datetime = timex[0].split('T')[0];
            return datetime;
        }
    }

    module.exports.FlightBookingRecognizer = FlightBookingRecognizer;
    ```

1. “dialogs -> mainDialog”将捕获话语并将其发送到 actStep 方法中的 executeLuisQuery。

    ```javascript
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.

    const { TimexProperty } = require('@microsoft/recognizers-text-data-types-timex-expression');
    const { MessageFactory, InputHints } = require('botbuilder');
    const { LuisRecognizer } = require('botbuilder-ai');
    const { ComponentDialog, DialogSet, DialogTurnStatus, TextPrompt, WaterfallDialog } = require('botbuilder-dialogs');

    const MAIN_WATERFALL_DIALOG = 'mainWaterfallDialog';

    class MainDialog extends ComponentDialog {
        constructor(luisRecognizer, bookingDialog) {
            super('MainDialog');

            if (!luisRecognizer) throw new Error('[MainDialog]: Missing parameter \'luisRecognizer\' is required');
            this.luisRecognizer = luisRecognizer;

            if (!bookingDialog) throw new Error('[MainDialog]: Missing parameter \'bookingDialog\' is required');

            // Define the main dialog and its related components.
            // This is a sample "book a flight" dialog.
            this.addDialog(new TextPrompt('TextPrompt'))
                .addDialog(bookingDialog)
                .addDialog(new WaterfallDialog(MAIN_WATERFALL_DIALOG, [
                    this.introStep.bind(this),
                    this.actStep.bind(this),
                    this.finalStep.bind(this)
                ]));

            this.initialDialogId = MAIN_WATERFALL_DIALOG;
        }

        /**
         * The run method handles the incoming activity (in the form of a TurnContext) and passes it through the dialog system.
         * If no dialog is active, it will start the default dialog.
         * @param {*} turnContext
         * @param {*} accessor
         */
        async run(turnContext, accessor) {
            const dialogSet = new DialogSet(accessor);
            dialogSet.add(this);

            const dialogContext = await dialogSet.createContext(turnContext);
            const results = await dialogContext.continueDialog();
            if (results.status === DialogTurnStatus.empty) {
                await dialogContext.beginDialog(this.id);
            }
        }

        /**
         * First step in the waterfall dialog. Prompts the user for a command.
         * Currently, this expects a booking request, like "book me a flight from Paris to Berlin on march 22"
         * Note that the sample LUIS model will only recognize Paris, Berlin, New York and London as airport cities.
         */
        async introStep(stepContext) {
            if (!this.luisRecognizer.isConfigured) {
                const messageText = 'NOTE: LUIS is not configured. To enable all capabilities, add `LuisAppId`, `LuisAPIKey` and `LuisAPIHostName` to the .env file.';
                await stepContext.context.sendActivity(messageText, null, InputHints.IgnoringInput);
                return await stepContext.next();
            }

            const messageText = stepContext.options.restartMsg ? stepContext.options.restartMsg : 'What can I help you with today?\nSay something like "Book a flight from Paris to Berlin on March 22, 2020"';
            const promptMessage = MessageFactory.text(messageText, messageText, InputHints.ExpectingInput);
            return await stepContext.prompt('TextPrompt', { prompt: promptMessage });
        }

        /**
         * Second step in the waterfall.  This will use LUIS to attempt to extract the origin, destination and travel dates.
         * Then, it hands off to the bookingDialog child dialog to collect any remaining details.
         */
        async actStep(stepContext) {
            const bookingDetails = {};

            if (!this.luisRecognizer.isConfigured) {
                // LUIS is not configured, we just run the BookingDialog path.
                return await stepContext.beginDialog('bookingDialog', bookingDetails);
            }

            // Call LUIS and gather any potential booking details. (Note the TurnContext has the response to the prompt)
            const luisResult = await this.luisRecognizer.executeLuisQuery(stepContext.context);
            switch (LuisRecognizer.topIntent(luisResult)) {
            case 'BookFlight': {
                // Extract the values for the composite entities from the LUIS result.
                const fromEntities = this.luisRecognizer.getFromEntities(luisResult);
                const toEntities = this.luisRecognizer.getToEntities(luisResult);

                // Show a warning for Origin and Destination if we can't resolve them.
                await this.showWarningForUnsupportedCities(stepContext.context, fromEntities, toEntities);

                // Initialize BookingDetails with any entities we may have found in the response.
                bookingDetails.destination = toEntities.airport;
                bookingDetails.origin = fromEntities.airport;
                bookingDetails.travelDate = this.luisRecognizer.getTravelDate(luisResult);
                console.log('LUIS extracted these booking details:', JSON.stringify(bookingDetails));

                // Run the BookingDialog passing in whatever details we have from the LUIS call, it will fill out the remainder.
                return await stepContext.beginDialog('bookingDialog', bookingDetails);
            }

            case 'GetWeather': {
                // We haven't implemented the GetWeatherDialog so we just display a TODO message.
                const getWeatherMessageText = 'TODO: get weather flow here';
                await stepContext.context.sendActivity(getWeatherMessageText, getWeatherMessageText, InputHints.IgnoringInput);
                break;
            }

            default: {
                // Catch all for unhandled intents
                const didntUnderstandMessageText = `Sorry, I didn't get that. Please try asking in a different way (intent was ${ LuisRecognizer.topIntent(luisResult) })`;
                await stepContext.context.sendActivity(didntUnderstandMessageText, didntUnderstandMessageText, InputHints.IgnoringInput);
            }
            }

            return await stepContext.next();
        }

        /**
         * Shows a warning if the requested From or To cities are recognized as entities but they are not in the Airport entity list.
         * In some cases LUIS will recognize the From and To composite entities as a valid cities but the From and To Airport values
         * will be empty if those entity values can't be mapped to a canonical item in the Airport.
         */
        async showWarningForUnsupportedCities(context, fromEntities, toEntities) {
            const unsupportedCities = [];
            if (fromEntities.from && !fromEntities.airport) {
                unsupportedCities.push(fromEntities.from);
            }

            if (toEntities.to && !toEntities.airport) {
                unsupportedCities.push(toEntities.to);
            }

            if (unsupportedCities.length) {
                const messageText = `Sorry but the following airports are not supported: ${ unsupportedCities.join(', ') }`;
                await context.sendActivity(messageText, messageText, InputHints.IgnoringInput);
            }
        }

        /**
         * This is the final step in the main waterfall dialog.
         * It wraps up the sample "book a flight" interaction with a simple confirmation.
         */
        async finalStep(stepContext) {
            // If the child dialog ("bookingDialog") was cancelled or the user failed to confirm, the Result here will be null.
            if (stepContext.result) {
                const result = stepContext.result;
                // Now we have all the booking details.

                // This is where calls to the booking AOU service or database would go.

                // If the call to the booking service was successful tell the user.
                const timeProperty = new TimexProperty(result.travelDate);
                const travelDateMsg = timeProperty.toNaturalLanguage(new Date(Date.now()));
                const msg = `I have you booked to ${ result.destination } from ${ result.origin } on ${ travelDateMsg }.`;
                await stepContext.context.sendActivity(msg, msg, InputHints.IgnoringInput);
            }

            // Restart the main dialog with a different message the second time around
            return await stepContext.replaceDialog(this.initialDialogId, { restartMsg: 'What else can I do for you?' });
        }
    }

    module.exports.MainDialog = MainDialog;
    ```

<a name="ask-bot-a-question-for-the-book-flight-intent"></a>

## <a name="start-the-bot-code"></a>启动机器人代码

1. 打开 Windows 控制台或 Linux/macOS 终端。

1. 更改为具有机器人代码的目录，并输入以下命令来安装 dotenv 依赖项：

    ```console
    npm install dotenv --save
    ```

1. 输入以下命令来启动机器人：

    ```console
    node index.js
    ```

此时将打开一个浏览器窗口，其中会显示 Web 应用机器人的网站，网址为 `http://localhost:3978/`。 主页上显示关于你的机器人的信息。

![主页上显示关于你的机器人的信息。](./media/bfv4-csharp/running-bot-web-home-page-success.png)

## <a name="use-the-bot-emulator-to-test-the-bot"></a>使用机器人模拟器测试机器人

向机器人询问关于预订机票意向的问题。

1. 启动机器人模拟器并选择“打开机器人”。
1. 在“打开机器人”弹出对话框中，输入机器人 URL（如 `http://localhost:3978/api/messages`）。 `/api/messages` 路由是机器人的 Web 地址。
1. 输入在下载的机器人代码根目录下 .env 文件中找到的“Microsoft 应用 ID”和“Microsoft 应用密码”  。

1. 在机器人模拟器中输入 `Book a flight from Seattle to Berlin tomorrow`应可获得基本机器人的答复，与在“通过网上聊天执行测试”中收到的答复相同。

    [![模拟器中的基础机器人响应](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png#lightbox)

1. 请选择“是”。 机器人将以其操作摘要答复你。
1. 从机器人模拟器的日志中，选择包含 `<- trace LuisV3 Trace` 的行。 这将显示 LUIS 对言语的意图和本质的 JSON 答复。

    [![模拟器中的基础机器人响应](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)

[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>后续步骤

查看更多使用聊天机器人的[示例](https://github.com/microsoft/botframework-solutions)。

> [!div class="nextstepaction"]
> [构建包含自定义使用者域的语言理解应用](luis-quickstart-intents-only.md)
