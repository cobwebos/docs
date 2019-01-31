---
title: 机器人 - C# - v4
titleSuffix: Language Understanding - Azure Cognitive Services
description: 使用 C# 构建集成了语言理解 (LUIS) 的聊天机器人。 此聊天机器人使用人力资源应用来快速实现一个机器人解决方案。 此机器人是使用 Bot Framework 4 和 Azure Web 应用机器人生成的。
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: cjgronlund
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 01/09/2019
ms.author: diberry
ms.openlocfilehash: 98c65b7adfe35c1ca80846ff7619dad69c5ba266
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55219220"
---
# <a name="tutorial-luis-bot-in-c-with-the-bot-framework-4x-and-the-azure-web-app-bot"></a>教程：使用 Bot Framework 4.x 和 Azure Web 应用机器人的 C# 中的 LUIS 机器人
使用 C#，可以构建集成了语言理解 (LUIS) 的聊天机器人。 此机器人使用 HomeAutomation 应用来实现机器人解决方案。 此机器人是使用 Azure [Web 应用机器人](https://docs.microsoft.com/azure/bot-service/)和 [Bot Framework 版本](https://github.com/Microsoft/botbuilder-js) v4 构建的。

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
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)


## <a name="create-web-app-bot"></a>创建 Web 应用机器人

1. 在 [Azure 门户](https://portal.azure.com)中，选择“创建新资源”。

2. 在搜索框中，搜索并选择“Web 应用机器人”。 选择“创建”。

3. 在“机器人服务”中提供所需的信息：

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

4. 在“机器人模板设置”中选择以下选项，然后选择这些设置下面的“选择”按钮：

    |设置|目的|选项|
    |--|--|--|
    |SDK 版本|机器人框架版本|**SDK v4**|
    |SDK 语言|机器人的编程语言|**C#**|
    |聊天/基础机器人|机器人类型|**基础机器人**|
    
5. 选择“创建”。 随即会创建机器人服务并将其部署到 Azure。 在此过程中，会创建名为 `luis-csharp-bot-XXXX` 的 LUIS 应用。 此名称基于上一部分所述的机器人名称和应用名称。

    [ ![创建 Web 应用机器人](./media/bfv4-csharp/create-web-app-service.png) ](./media/bfv4-csharp/create-web-app-service.png#lightbox)

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
2. 在“我的应用”页上，选择“创建日期”列以按应用创建日期排序。 Azure 机器人服务在上一部分已创建一个新应用。 该应用的名称为 `luis-csharp-bot-` + `<your-name>` + 4 个随机字符。
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

6. 打开 bot.cs 文件并找到 `_services.LuisServices`。 输入到机器人的用户话语通过此处发送到 LUIS。

    ```csharp
    /// <summary>
    /// Run every turn of the conversation. Handles orchestration of messages.
    /// </summary>
    /// <param name="turnContext">Bot Turn Context.</param>
    /// <param name="cancellationToken">Task CancellationToken.</param>
    /// <returns>A <see cref="Task"/> representing the asynchronous operation.</returns>
    public async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken)
    {
        var activity = turnContext.Activity;

        if (activity.Type == ActivityTypes.Message)
        {
            // Perform a call to LUIS to retrieve results for the current activity message.
            var luisResults = await _services.LuisServices[LuisConfiguration].RecognizeAsync(turnContext, cancellationToken).ConfigureAwait(false);

            // If any entities were updated, treat as interruption.
            // For example, "no my name is tony" will manifest as an update of the name to be "tony".
            var topScoringIntent = luisResults?.GetTopScoringIntent();

            var topIntent = topScoringIntent.Value.intent;
            switch (topIntent)
            {
                case GreetingIntent:
                    await turnContext.SendActivityAsync("Hello.");
                    break;
                case HelpIntent:
                    await turnContext.SendActivityAsync("Let me try to provide some help.");
                    await turnContext.SendActivityAsync("I understand greetings, being asked for help, or being asked to cancel what I am doing.");
                    break;
                case CancelIntent:
                    await turnContext.SendActivityAsync("I have nothing to cancel.");
                    break;
                case NoneIntent:
                default:
                    // Help or no intent identified, either way, let's provide some help.
                    // to the user
                    await turnContext.SendActivityAsync("I didn't understand what you just said to me.");
                    break;
            }
        }
        else if (activity.Type == ActivityTypes.ConversationUpdate)
        {
            if (activity.MembersAdded.Any())
            {
                // Iterate over all new members added to the conversation.
                foreach (var member in activity.MembersAdded)
                {
                    // Greet anyone that was not the target (recipient) of this message.
                    // To learn more about Adaptive Cards, see https://aka.ms/msbot-adaptivecards for more details.
                    if (member.Id != activity.Recipient.Id)
                    {
                        var welcomeCard = CreateAdaptiveCardAttachment();
                        var response = CreateResponse(activity, welcomeCard);
                        await turnContext.SendActivityAsync(response).ConfigureAwait(false);
                    }
                }
            }
        }

    }
    ```

    机器人将用户话语发送到 LUIS 并获取结果。 首要意向确定聊天流。 


## <a name="start-the-bot"></a>启动机器人
在更改任何代码或设置之前，请验证机器人是否正常工作。 

1. 在 Visual Studio 中打开解决方案文件。 

2. 创建 `appsettings.json` 文件，用于保存机器人代码查找的机器人变量：

    ```JSON
    {
    "botFileSecret": "",
    "botFilePath": ""

    }
    ```

    将变量的值设置为在执行**[下载 Web 应用机器人](#download-the-web-app-bot)** 部分步骤 1 时从 Azure 机器人服务的“应用程序设置”中复制的值。

3. 在 Visual Studio 中，启动机器人。 此时将打开一个浏览器窗口，其中会显示 Web 应用机器人的网站，网址为 `http://localhost:3978/`。

## <a name="start-the-emulator"></a>启动模拟器

1. 启动机器人模拟器。

2. 在机器人模拟器中，选择项目根目录中的 *.bot 文件。 此 `.bot` 文件包含消息的机器人 URL 终结点：

    [![机器人模拟器 v4](../../../includes/media/cognitive-services-luis/bfv4/bot-emulator-v4.png)](../../../includes/media/cognitive-services-luis/bfv4/bot-emulator-v4.png#lightbox)

3. 输入在执行**[下载 Web 应用机器人](#download-the-web-app-bot)** 部分的步骤 1 时从 Azure 机器人服务的“应用程序设置”中复制的机器人机密。 这样，模拟器便可以访问 `.bot` 文件中所有已加密的字段。

    ![机器人模拟器机密 v4](../../../includes/media/cognitive-services-luis/bfv4/bot-secret.png)

4. 在机器人模拟器中输入 `Hello`，并获取基础机器人的正确响应。

    [![模拟器中的基础机器人响应](../../../includes/media/cognitive-services-luis/bfv4/emulator-test.png)](../../../includes/media/cognitive-services-luis/bfv4/emulator-test.png#lightbox)

## <a name="modify-bot-code"></a>修改机器人代码 

在 `BasicBot.cs` 文件中，添加用于处理新意向的代码。 

1. 在该文件的顶部找到 **Supported LUIS Intents** 节，并添加 HomeAutomation 意向的常量：

    ```csharp
    // Supported LUIS Intents
    public const string GreetingIntent = "Greeting";
    public const string CancelIntent = "Cancel";
    public const string HelpIntent = "Help";
    public const string NoneIntent = "None";
    public const string TurnOnIntent = "HomeAutomation_TurnOn"; // new intent
    public const string TurnOffIntent = "HomeAutomation_TurnOff"; // new intent
    ```

    请注意，LUIS 门户应用中的域与意向之间的句点 `.` 已替换为下划线 `_`。 

2. 找到接收话语的 LUIS 预测的 **OnTurnAsync** 方法。 在 switch 语句中添加代码，用以返回两个 HomeAutomation 意向的 LUIS 响应。 

    ```csharp
    case TurnOnIntent:
        await turnContext.SendActivityAsync("TurnOn intent found, JSON response: " + luisResults?.Entities.ToString());
        break;
    case TurnOffIntent:
        await turnContext.SendActivityAsync("TurnOff intent found, JSON response: " + luisResults?.Entities.ToString());
        break;
    ```

    机器人的响应并不与 LUIS REST API 请求完全相同，因此，必须查看 JSON 的响应来了解差异。 文本和意向属性相同，但实体属性值已修改。 

    ```JSON
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



## <a name="view-results-in-bot"></a>在机器人中查看结果

1. 在机器人模拟器中输入话语：`Turn on the livingroom lights to 50%`

2. 机器人将做出以下响应：

    ```JSON
    TurnOn intent found, JSON response: {"$instance":{“HomeAutomation_Device”:[{“startIndex”:23,“endIndex”:29,“score”:0.9776345,“text”:“lights”,“type”:“HomeAutomation.Device”}],“HomeAutomation_Room”:[{“startIndex”:12,“endIndex”:22,“score”:0.9079433,“text”:“livingroom”,“type”:“HomeAutomation.Room”}]},“HomeAutomation_Device”:[“lights”],“HomeAutomation_Room”:[“livingroom”]}
    ```    

## <a name="learn-more-about-bot-framework"></a>深入了解 Bot Framework
Azure 机器人服务使用 Bot Framework SDK。 详细了解该 SDK 和机器人框架：

* [Azure 机器人服务](https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0) v4 文档
* [Bot Builder 示例](https://github.com/Microsoft/botbuilder-samples)
* [Bot Builder SDK](https://docs.microsoft.com/javascript/api/botbuilder-core/?view=botbuilder-ts-latest)
* [Bot Builder 工具](https://github.com/Microsoft/botbuilder-tools)：

## <a name="next-steps"></a>后续步骤

我们创建了一个 Azure 机器人服务，复制了机器人机密和 `.bot` 文件路径，并下载了代码的 zip 文件。 我们已将预生成的 HomeAutomation 域添加到在创建新 Azure 机器人服务过程中创建的 LUIS 应用，然后重新训练和并发布了该应用。 我们提取了代码项目，创建了环境文件 (`.env`)，并设置了机器人机密和 `.bot` 文件路径。 在 bot.js 文件中，我们添加了用于处理两个新意向的代码。 然后，我们在模拟器中测试了机器人，以查看针对某个新意向的话语做出的 LUIS 响应。 

查看更多使用聊天机器人的[示例](https://github.com/Microsoft/AI)。 

> [!div class="nextstepaction"]
> [在 LUIS 中生成自定义域](luis-quickstart-intents-only.md)
