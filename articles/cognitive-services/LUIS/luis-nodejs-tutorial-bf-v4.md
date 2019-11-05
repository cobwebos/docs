---
title: 教程：语言理解机器人 Node.js v4
titleSuffix: Azure Cognitive Services
description: 使用 Node.js，生成集成了语言理解 (LUIS) 的聊天机器人。 此聊天机器人使用人力资源应用来快速实现一个机器人解决方案。 此机器人是使用 Bot Framework 4 和 Azure Web 应用机器人生成的。
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 9a38f43b24e5db6a60ff38cd0f1d9b59b9875bba
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492671"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-nodejs"></a>教程：通过 Node.js 使用启用了语言理解的 Web 应用机器人 

使用 Node.js 生成集成了语言理解的聊天机器人 (LUIS)。 该机器人是使用 Azure [Web 应用机器人](https://docs.microsoft.com/azure/bot-service/)资源和 [Bot Framework 版本](https://github.com/Microsoft/botbuilder-dotnet) V4 构建的。

[!INCLUDE [Waiting for Bot refresh](./includes/wait-bot-upgrade.md)]

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
    |Subscription|要在其中创建机器人的订阅。|你的主要订阅。
    |Resource group|Azure 资源的逻辑组|创建一个新组用于存储此机器人使用的所有资源，并将该组命名为 `luis-nodejs-bot-resource-group`。|
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
|GetWeather|`what's the weather like?`|
|无|应用域以外的任何内容。|

## <a name="test-the-bot-in-web-chat"></a>通过网上聊天测试机器人

1. 在 Azure 门户中，为新机器人选择“通过网上聊天执行测试”  。 
1. 在“键入消息”文本框中，输入文本 `Book a flight from Seattle to Berlin tomorrow`  。 机器人将使用要预定机票的验证答复你。 

    ![Azure 门户的屏幕截图，输入文本“你好”。](./media/bfv4-nodejs/ask-bot-question-in-portal-test-in-web-chat.png)

    可以使用测试功能对机器人进行快速测试。 要进行更完整的测试（包括调试），请下载机器人代码并使用 Visual Studio。 

## <a name="download-the-web-app-bot-source-code"></a>下载 Web 应用机器人源代码
若要开发 Web 应用机器人代码，请下载该代码并在本地计算机上使用。 

1. 在 Azure 门户中，从“机器人管理”部分选择“生成”   。 

1. 选择“下载机器人源代码”。  

    [![下载基础机器人的 Web 应用机器人源代码](../../../includes/media/cognitive-services-luis/bfv4/download-code.png)](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

1. 弹出询问“是否在下载的 zip 文件中包含应用设置?”的对话框时，选择“是”   。 这会提供 LUIS 设置。 

1. 压缩源代码时，有一条消息会提供用于下载代码的链接。 选择该链接。 

1. 将 zip 文件保存到本地计算机，然后解压缩该文件。 使用 Visual Studio 打开项目。 

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>查看将言语发送到 LUIS 并获得答复的代码

1. 若要将用户话语发送到 LUIS 预测终结点，请打开“dialogs -> flightBookingRecognizer.js”文件  。 输入到机器人的用户话语通过此处发送到 LUIS。 来自 LUIS 的响应从 executeLuisQuery 方法返回  。  

    ````javascript
    class FlightBookingRecognizer {

        ...

        /**
         * Returns an object with preformatted LUIS results for the bot's dialogs to consume.
         * @param {TurnContext} context
         */
        async executeLuisQuery(context) {
            return await this.recognizer.recognize(context);
        }

        ...

    }
    ````

1. “dialogs -> mainDialog”将捕获话语并将其发送到 actStep 方法中的 executeLuisQuery  。


    ````javascript
    class MainDialog extends ComponentDialog {

        constructor(luisRecognizer, bookingDialog) {
            ...
            this.luisRecognizer = luisRecognizer;
            ...
        }


        ...

        /**
         * Second step in the waterfall.  This will use LUIS to attempt to extract the origin, destination and travel dates.
         * Then, it hands off to the bookingDialog child dialog to collect any remaining details.
         */
        async actStep(stepContext) {

            ...

            const luisResult = await this.luisRecognizer.executeLuisQuery(stepContext.context);

            switch (LuisRecognizer.topIntent(luisResult)) {
                    case 'BookFlight':
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
            
                    case 'GetWeather':
                        // We haven't implemented the GetWeatherDialog so we just display a TODO message.
                        const getWeatherMessageText = 'TODO: get weather flow here';
                        await stepContext.context.sendActivity(getWeatherMessageText, getWeatherMessageText, InputHints.IgnoringInput);
                        break;
            
                    default:
                        // Catch all for unhandled intents
                        const didntUnderstandMessageText = `Sorry, I didn't get that. Please try asking in a different way (intent was ${ LuisRecognizer.topIntent(luisResult) })`;
                        await stepContext.context.sendActivity(didntUnderstandMessageText, didntUnderstandMessageText, InputHints.IgnoringInput);
                    }
            
                    return await stepContext.next();

        }

        ...

    }
    ````
<a name="ask-bot-a-question-for-the-book-flight-intent"></a>

## <a name="use-the-bot-emulator-to-test-the-bot"></a>使用机器人模拟器测试机器人

向机器人询问关于预订机票意向的问题。

1. 启动机器人模拟器并选择“打开机器人”  。
1. 在“打开机器人”弹出对话框中，输入机器人 URL（如 `http://localhost:3978/api/messages`）  。 `/api/messages` 路由是机器人的 Web 地址。
1. 输入在下载的机器人代码根目录下 .env 文件中找到的“Microsoft 应用 ID”和“Microsoft 应用密码”    。

1. 在机器人模拟器中输入 `Book a flight from Seattle to Berlin tomorrow`应可获得基本机器人的答复，与在“通过网上聊天执行测试”中收到的答复相同  。

    [![模拟器中的基础机器人响应](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png#lightbox)

1. 请选择“是”。  机器人将以其操作摘要答复你。 
1. 从机器人模拟器的日志中，选择包含 `Luis Trace` 的行。 这将显示 LUIS 对言语的意图和本质的 JSON 答复。

    [![模拟器中的基础机器人响应](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)


[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>后续步骤

查看更多使用聊天机器人的[示例](https://github.com/microsoft/botframework-solutions)。 

> [!div class="nextstepaction"]
> [构建包含自定义使用者域的语言理解应用](luis-quickstart-intents-only.md)