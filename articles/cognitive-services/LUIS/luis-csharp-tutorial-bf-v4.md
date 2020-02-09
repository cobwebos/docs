---
title: 教程：语言理解机器人 C# v4
description: 使用 C# 构建集成了语言理解 (LUIS) 的聊天机器人。 此机器人是使用 Bot Framework 4 和 Azure Web 应用机器人服务生成的。
ms.topic: tutorial
ms.date: 02/03/2020
ms.openlocfilehash: 2381f4cba39f81ab593f3149a2708f442156f30d
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2020
ms.locfileid: "76987984"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-c"></a>教程：使用在 C# 中启用语言理解的 Web 应用机器人

使用 C# 构建集成了语言理解 (LUIS) 的聊天机器人。 该机器人是使用 Azure [Web 应用机器人](https://docs.microsoft.com/azure/bot-service/)资源和 [Bot Framework 版本](https://github.com/Microsoft/botbuilder-dotnet) V4 构建的。

**本教程介绍如何执行下列操作：**

> [!div class="checklist"]
> * 创建 Web 应用机器人。 此过程将创建新的 LUIS 应用。
> * 下载 Web 机器人服务创建的机器人项目
> * 在计算机本地启动机器人和模拟器
> * 在机器人中查看话语结果

## <a name="prerequisites"></a>必备条件

* [机器人模拟器](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)


## <a name="create-a-web-app-bot-resource"></a>创建 Web 应用机器人资源

1. 在 [Azure 门户](https://portal.azure.com)中，选择“创建新资源”  。

1. 在搜索框中，搜索并选择“Web 应用机器人”  。 选择“创建”  。

1. 在“机器人服务”  中提供所需的信息：

    |设置|目的|建议的设置|
    |--|--|--|
    |机器人名称|资源名称|`luis-csharp-bot-` + `<your-name>`，例如 `luis-csharp-bot-johnsmith`|
    |订阅|要在其中创建机器人的订阅。|你的主要订阅。
    |资源组|Azure 资源的逻辑组|创建一个新组用于存储此机器人使用的所有资源，并将该组命名为 `luis-csharp-bot-resource-group`。|
    |位置|Azure 区域 - 不一定要与 LUIS 创作或发布区域相同。|`westus`|
    |定价层|用于服务请求限制和计费。|`F0` 是免费层。
    |应用程序名称|将机器人部署到云（例如，humanresourcesbot.azurewebsites.net）时，该名称用作子域。|`luis-csharp-bot-` + `<your-name>`，例如 `luis-csharp-bot-johnsmith`|
    |机器人模板|机器人框架设置 - 参阅下表|
    |LUIS 应用位置|必须与 LUIS 资源区域相同|`westus`|
    |应用服务计划/位置|请勿更改提供的默认值。|
    |Application Insights|请勿更改提供的默认值。|
    |Microsoft 应用 ID 和密码|请勿更改提供的默认值。|

1. 在“机器人模板”中选择以下选项，然后选择这些设置下面的“选择”按钮   ：

    |设置|目的|选项|
    |--|--|--|
    |SDK 版本|机器人框架版本|**SDK v4**|
    |SDK 语言|机器人的编程语言|**C#**|
    |机器人|机器人类型|**基础机器人**|

1. 选择“创建”  。 随即会创建机器人服务并将其部署到 Azure。 在此过程中，会创建名为 `luis-csharp-bot-XXXX` 的 LUIS 应用。 此名称基于 Azure 机器人服务应用名称。

    > [!div class="mx-imgBorder"]
    > [![创建 Web 应用机器人](./media/bfv4-csharp/create-web-app-service.png)](./media/bfv4-csharp/create-web-app-service.png#lightbox)

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

1. 弹出询问“是否在下载的 zip 文件中包含应用设置?”的对话框时，选择“是”   。

1. 压缩源代码时，有一条消息会提供用于下载代码的链接。 选择该链接。

1. 将 zip 文件保存到本地计算机，然后解压缩该文件。 使用 Visual Studio 打开项目。

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>查看将言语发送到 LUIS 并获得答复的代码

1. 若要将用户话语发送到 LUIS 预测终结点，请打开 FlightBookingRecognizer.cs 文件  。 输入到机器人的用户话语通过此处发送到 LUIS。 来自 LUIS 的响应从 RecognizeAsync 方法返回  。

    ```csharp
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.AI.Luis;
    using Microsoft.Extensions.Configuration;

    namespace Microsoft.BotBuilderSamples
    {
        public class FlightBookingRecognizer : IRecognizer
        {
            private readonly LuisRecognizer _recognizer;

            public FlightBookingRecognizer(IConfiguration configuration)
            {
                var luisIsConfigured = !string.IsNullOrEmpty(configuration["LuisAppId"]) && !string.IsNullOrEmpty(configuration["LuisAPIKey"]) && !string.IsNullOrEmpty(configuration["LuisAPIHostName"]);
                if (luisIsConfigured)
                {
                    var luisApplication = new LuisApplication(
                        configuration["LuisAppId"],
                        configuration["LuisAPIKey"],
                        "https://" + configuration["LuisAPIHostName"]);

                    _recognizer = new LuisRecognizer(luisApplication);
                }
            }

            // Returns true if luis is configured in the appsettings.json and initialized.
            public virtual bool IsConfigured => _recognizer != null;

            public virtual async Task<RecognizerResult> RecognizeAsync(ITurnContext turnContext, CancellationToken cancellationToken)
                => await _recognizer.RecognizeAsync(turnContext, cancellationToken);

            public virtual async Task<T> RecognizeAsync<T>(ITurnContext turnContext, CancellationToken cancellationToken)
                where T : IRecognizerConvert, new()
                => await _recognizer.RecognizeAsync<T>(turnContext, cancellationToken);
        }
    }
    ````

1. 打开“对话框”->“MainDialog.cs”将捕获话语并将其发送到 actStep 方法中的 executeLuisQuery  。

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.Dialogs;
    using Microsoft.Bot.Schema;
    using Microsoft.Extensions.Logging;
    using Microsoft.Recognizers.Text.DataTypes.TimexExpression;

    namespace Microsoft.BotBuilderSamples.Dialogs
    {
        public class MainDialog : ComponentDialog
        {
            private readonly FlightBookingRecognizer _luisRecognizer;
            protected readonly ILogger Logger;

            // Dependency injection uses this constructor to instantiate MainDialog
            public MainDialog(FlightBookingRecognizer luisRecognizer, BookingDialog bookingDialog, ILogger<MainDialog> logger)
                : base(nameof(MainDialog))
            {
                _luisRecognizer = luisRecognizer;
                Logger = logger;

                AddDialog(new TextPrompt(nameof(TextPrompt)));
                AddDialog(bookingDialog);
                AddDialog(new WaterfallDialog(nameof(WaterfallDialog), new WaterfallStep[]
                {
                    IntroStepAsync,
                    ActStepAsync,
                    FinalStepAsync,
                }));

                // The initial child Dialog to run.
                InitialDialogId = nameof(WaterfallDialog);
            }

            private async Task<DialogTurnResult> IntroStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                if (!_luisRecognizer.IsConfigured)
                {
                    await stepContext.Context.SendActivityAsync(
                        MessageFactory.Text("NOTE: LUIS is not configured. To enable all capabilities, add 'LuisAppId', 'LuisAPIKey' and 'LuisAPIHostName' to the appsettings.json file.", inputHint: InputHints.IgnoringInput), cancellationToken);

                    return await stepContext.NextAsync(null, cancellationToken);
                }

                // Use the text provided in FinalStepAsync or the default if it is the first time.
                var messageText = stepContext.Options?.ToString() ?? "What can I help you with today?\nSay something like \"Book a flight from Paris to Berlin on March 22, 2020\"";
                var promptMessage = MessageFactory.Text(messageText, messageText, InputHints.ExpectingInput);
                return await stepContext.PromptAsync(nameof(TextPrompt), new PromptOptions { Prompt = promptMessage }, cancellationToken);
            }

            private async Task<DialogTurnResult> ActStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                if (!_luisRecognizer.IsConfigured)
                {
                    // LUIS is not configured, we just run the BookingDialog path with an empty BookingDetailsInstance.
                    return await stepContext.BeginDialogAsync(nameof(BookingDialog), new BookingDetails(), cancellationToken);
                }

                // Call LUIS and gather any potential booking details. (Note the TurnContext has the response to the prompt.)
                var luisResult = await _luisRecognizer.RecognizeAsync<FlightBooking>(stepContext.Context, cancellationToken);
                switch (luisResult.TopIntent().intent)
                {
                    case FlightBooking.Intent.BookFlight:
                        await ShowWarningForUnsupportedCities(stepContext.Context, luisResult, cancellationToken);

                        // Initialize BookingDetails with any entities we may have found in the response.
                        var bookingDetails = new BookingDetails()
                        {
                            // Get destination and origin from the composite entities arrays.
                            Destination = luisResult.ToEntities.Airport,
                            Origin = luisResult.FromEntities.Airport,
                            TravelDate = luisResult.TravelDate,
                        };

                        // Run the BookingDialog giving it whatever details we have from the LUIS call, it will fill out the remainder.
                        return await stepContext.BeginDialogAsync(nameof(BookingDialog), bookingDetails, cancellationToken);

                    case FlightBooking.Intent.GetWeather:
                        // We haven't implemented the GetWeatherDialog so we just display a TODO message.
                        var getWeatherMessageText = "TODO: get weather flow here";
                        var getWeatherMessage = MessageFactory.Text(getWeatherMessageText, getWeatherMessageText, InputHints.IgnoringInput);
                        await stepContext.Context.SendActivityAsync(getWeatherMessage, cancellationToken);
                        break;

                    default:
                        // Catch all for unhandled intents
                        var didntUnderstandMessageText = $"Sorry, I didn't get that. Please try asking in a different way (intent was {luisResult.TopIntent().intent})";
                        var didntUnderstandMessage = MessageFactory.Text(didntUnderstandMessageText, didntUnderstandMessageText, InputHints.IgnoringInput);
                        await stepContext.Context.SendActivityAsync(didntUnderstandMessage, cancellationToken);
                        break;
                }

                return await stepContext.NextAsync(null, cancellationToken);
            }

            // Shows a warning if the requested From or To cities are recognized as entities but they are not in the Airport entity list.
            // In some cases LUIS will recognize the From and To composite entities as a valid cities but the From and To Airport values
            // will be empty if those entity values can't be mapped to a canonical item in the Airport.
            private static async Task ShowWarningForUnsupportedCities(ITurnContext context, FlightBooking luisResult, CancellationToken cancellationToken)
            {
                var unsupportedCities = new List<string>();

                var fromEntities = luisResult.FromEntities;
                if (!string.IsNullOrEmpty(fromEntities.From) && string.IsNullOrEmpty(fromEntities.Airport))
                {
                    unsupportedCities.Add(fromEntities.From);
                }

                var toEntities = luisResult.ToEntities;
                if (!string.IsNullOrEmpty(toEntities.To) && string.IsNullOrEmpty(toEntities.Airport))
                {
                    unsupportedCities.Add(toEntities.To);
                }

                if (unsupportedCities.Any())
                {
                    var messageText = $"Sorry but the following airports are not supported: {string.Join(',', unsupportedCities)}";
                    var message = MessageFactory.Text(messageText, messageText, InputHints.IgnoringInput);
                    await context.SendActivityAsync(message, cancellationToken);
                }
            }

            private async Task<DialogTurnResult> FinalStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                // If the child dialog ("BookingDialog") was cancelled, the user failed to confirm or if the intent wasn't BookFlight
                // the Result here will be null.
                if (stepContext.Result is BookingDetails result)
                {
                    // Now we have all the booking details call the booking service.

                    // If the call to the booking service was successful tell the user.

                    var timeProperty = new TimexProperty(result.TravelDate);
                    var travelDateMsg = timeProperty.ToNaturalLanguage(DateTime.Now);
                    var messageText = $"I have you booked to {result.Destination} from {result.Origin} on {travelDateMsg}";
                    var message = MessageFactory.Text(messageText, messageText, InputHints.IgnoringInput);
                    await stepContext.Context.SendActivityAsync(message, cancellationToken);
                }

                // Restart the main dialog with a different message the second time around
                var promptMessage = "What else can I do for you?";
                return await stepContext.ReplaceDialogAsync(InitialDialogId, promptMessage, cancellationToken);
            }
        }
    }
    ```

## <a name="start-the-bot-code-in-visual-studio"></a>在 Visual Studio 中，启动机器人代码

在 Visual Studio 2019 中，启动机器人。 此时将打开一个浏览器窗口，其中会显示 Web 应用机器人的网站，网址为 `http://localhost:3978/`。 主页上显示关于你的机器人的信息。

![主页上显示关于你的机器人的信息。](./media/bfv4-csharp/running-bot-web-home-page-success.png)

## <a name="use-the-bot-emulator-to-test-the-bot"></a>使用机器人模拟器测试机器人

1. 启动机器人模拟器并选择“打开机器人”  。
1. 在“打开机器人”弹出对话框中，输入机器人 URL（如 `http://localhost:3978/api/messages`）  。 `/api/messages` 路由是机器人的 Web 地址。
1. 输入下载的机器人代码根目录中的“appsettings.json”文件中的“Microsoft 应用 ID”和“Microsoft 应用密码”    。


1. 在机器人模拟器中输入 `Book a flight from Seattle to Berlin tomorrow` 应可获得基本机器人的响应，与上一部分中在“通过网上聊天执行测试”中收到的响应相同  。

    [![模拟器中的基础机器人响应](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png#lightbox)

1. 请选择“是”。  机器人将以其操作摘要答复你。
1. 从机器人模拟器的日志中，选择包含 `Luis Trace` 的行。 这将显示 LUIS 对言语的意图和本质的 JSON 答复。

    [![模拟器中的基础机器人响应](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)


[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]


## <a name="next-steps"></a>后续步骤

查看更多使用聊天机器人的[示例](https://github.com/microsoft/botframework-solutions)。

> [!div class="nextstepaction"]
> [构建包含自定义使用者域的语言理解应用](luis-quickstart-intents-only.md)
