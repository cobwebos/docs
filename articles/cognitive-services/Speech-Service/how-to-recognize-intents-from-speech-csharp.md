---
title: 教程：使用适用于 C# 的语音 SDK 从语音中识别意向
titleSuffix: Azure Cognitive Services
description: 本教程介绍如何使用适用于 C# 的语音 SDK 从语音中识别意向。
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: wolfma
ms.openlocfilehash: 9a00dfd1186d19ce9432db8e636bffa40eb977af
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2019
ms.locfileid: "59280530"
---
# <a name="tutorial-recognize-intents-from-speech-using-the-speech-sdk-for-c"></a>教程：使用适用于 C# 的语音 SDK 从语音中识别意向

[!INCLUDE [Article selector](../../../includes/cognitive-services-speech-service-how-to-recognize-intents-from-speech-selector.md)]

认知服务[语音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) 与[语言理解服务 (LUIS)](https://www.luis.ai/home) 相集成，以提供**意向识别**。 意向是用户想要做的某件事：预订航班、查看天气预报或拨打电话。 用户可以使用任何普通字词。 LUIS 使用机器学习将用户请求映射到定义的意向。

> [!NOTE]
> LUIS 应用程序定义所要识别的意向和实体。 它与使用语音服务的 C# 应用程序不同。 在本文中，“应用”是指 LUIS 应用，“应用程序”是指 C# 代码。

在本教程中，我们将使用语音 SDK 开发一个 C# 控制台应用程序，用于通过设备的麦克风从用户话语中派生意图。 将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建引用语音 SDK NuGet 包的 Visual Studio 项目
> * 创建语音配置并获取意向识别器
> * 获取 LUIS 应用的模型并添加所需的意向
> * 指定用于语音识别的语言
> * 从文件中识别语音
> * 使用异步的事件驱动的连续识别

## <a name="prerequisites"></a>先决条件

在开始阅读本教程之前，请务必做好以下准备。

* 一个 LUIS 帐户。 可以通过 [LUIS 门户](https://www.luis.ai/home)免费创建一个帐户。
* Visual Studio 2017（任何版本）。

## <a name="luis-and-speech"></a>LUIS 和语音

LUIS 与语音服务集成，可从语音中识别意向。 不需要语音服务订阅，只需要 LUIS。

LUIS 使用两种密钥：

|密钥类型|目的|
|--------|-------|
|创作|用于以编程方式创建和修改 LUIS 应用|
|endpoint |授权访问特定的 LUIS 应用|

终结点密钥是本教程所需的 LUIS 密钥。 本教程使用一个示例家庭自动化 LUIS 应用，可以遵循[使用预生成的家庭自动化应用](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app)来创建该应用。 如果你已创建自己的 LUIS 应用，可以改用该应用。

当你创建 LUIS 应用时，系统会自动生成一个初学者密钥，让你使用文本查询测试该应用。 此密钥不会启用语音服务集成，因此不适用于本教程。 必须在 Azure 仪表板中创建 LUIS 资源并将其分配给 LUIS 应用。 在本教程中，可以使用免费订阅层。

在 Azure 仪表板中创建 LUIS 资源之后，请登录到 [LUIS 门户](https://www.luis.ai/home)，在“我的应用”页上选择自己的应用程序，然后切换到应用的“管理”页。 最后，在侧栏中单击“密钥和终结点”。

![LUIS 门户密钥和终结点设置](media/sdk/luis-keys-endpoints-page.png)

在“密钥和终结点”设置页上：

1. 向下滚动到“资源和密钥”部分，单击“分配资源”。
1. 在“将密钥分配到应用”对话框中进行以下选择：

    * 选择“Microsoft”作为租户。
    * 在“订阅名称”下，选择包含所要使用的 LUIS 资源的 Azure 订阅。
    * 在“密钥”下，选择要在应用中使用的 LUIS 资源。

片刻之后，新订阅将显示在页面底部的表格中。 单击密钥旁边的图标将其复制到剪贴板。 （可以使用其中的任一密钥。）

![LUIS 应用订阅密钥](media/sdk/luis-keys-assigned.png)

## <a name="create-a-speech-project-in-visual-studio"></a>在 Visual Studio 中创建语音项目

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-the-code"></a>添加代码

在 Visual Studio 项目中打开文件 `Program.cs`，并将文件开头位置的 `using` 语句块替换为以下声明。

[!code-csharp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#toplevel)]

在提供的 `Main()` 方法中添加以下代码。

```csharp
RecognizeIntentAsync().Wait();
Console.WriteLine("Please press Enter to continue.");
Console.ReadLine();
```

创建空的异步方法 `RecognizeIntentAsync()`，如下所示。

```csharp
static async Task RecognizeIntentAsync()
{
}
```

在此新方法的正文中添加以下代码。

[!code-csharp[Intent recognition by using a microphone](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentRecognitionWithMicrophone)]

将此方法中的占位符替换为你的 LUIS 订阅密钥、区域和应用 ID，如下所示。

|占位符|替换为|
|-----------|------------|
|`YourLanguageUnderstandingSubscriptionKey`|LUIS 终结点密钥。 如前所述，此密钥必须是从 Azure 仪表板获取的密钥，而不是“初学者密钥”。 可以在 [LUIS 门户](https://www.luis.ai/home)中应用的“密钥和终结点”页上（在“管理”下）找到此密钥。|
|`YourLanguageUnderstandingServiceRegion`|LUIS 订阅所在区域的短标识符，例如 `westus` 表示“美国西部”。 请参阅[区域](regions.md)。|
|`YourLanguageUnderstandingAppId`|LUIS 应用 ID。 可以在 [LUIS 门户](https://www.luis.ai/home)中应用的“设置”页上找到此 ID。|

完成这些更改后，可以生成 (Ctrl-Shift-B) 和运行 (F5) 教程应用程序。 出现提示时，请尝试对着电脑麦克风说出“关灯”。 结果将显示在控制台窗口中。

以下部分包含代码的讨论。


## <a name="create-an-intent-recognizer"></a>创建意向识别器

在语音中识别意向的第一步是从 LUIS 终结点密钥和区域创建语音配置。 语音配置可用于创建语音 SDK 的各种功能的识别器。 语音配置提供多种方式用于指定所要使用的订阅；此处我们使用了采用订阅密钥和区域的 `FromSubscription`。

> [!NOTE]
> 请使用 LUIS 订阅而不是语音服务订阅的密钥和区域。

接下来，使用 `new IntentRecognizer(config)` 创建意向识别器。 由于配置已知道要使用哪个订阅，因此，在创建识别器时无需再次指定订阅密钥和终结点。

## <a name="import-a-luis-model-and-add-intents"></a>导入 LUIS 模型并添加意向

现在，使用 `LanguageUnderstandingModel.FromAppId()` 从 LUIS 应用导入模型，并添加想要通过识别器的 `AddIntent()` 方法识别的 LUIS 意向。 这两个步骤会指出用户有可能在其请求中使用的单词，以此提高语音识别的准确性。 如果不需要在应用程序中识别应用的所有意向，则不需要添加这些意向。

添加意向需要三个参数：LUIS 模型（已创建并命名为 `model`）、意向名称和意向 ID。 ID 与名称之间的差别如下。

|`AddIntent()` 参数|目的|
|--------|-------|
|intentName |LUIS 应用中定义的意向的名称。 必须与 LUIS 意向名称完全匹配。|
|intentID    |语音 SDK 分配给已识别的意向的 ID。 可以是任何内容；不需要对应于 LUIS 应用中定义的意向名称。 例如，如果多个意向由相同的代码处理，则可以对这些意向使用相同的 ID。|

家庭自动化 LUIS 应用具有两个意向：一个意向是打开设备，另一个意向是关闭设备。 以下代码行将这些意向添加到识别器；请将 `RecognizeIntentAsync()` 方法中的三个 `AddIntent` 代码行替换为以下代码。

```csharp
recognizer.AddIntent(model, "HomeAutomation.TurnOff", "off");
recognizer.AddIntent(model, "HomeAutomation.TurnOn", "on");
```

也可以使用 `AddAllIntents` 方法将模型中的所有意向都添加到识别器中，而不是添加单个意向。

## <a name="start-recognition"></a>开始识别

创建识别器并添加意向后，可以开始识别。 语音 SDK 支持单次识别和连续识别。

|识别模式|要调用的方法|结果|
|----------------|-----------------|---------|
|单次|`RecognizeOnceAsync()`|返回在一个话语后面识别的意向（如果有）。|
|连续|`StartContinuousRecognitionAsync()`<br>`StopContinuousRecognitionAsync()`|识别多个话语。 有可用结果时发出事件（例如 `IntermediateResultReceived`）。|

教程应用程序使用单次模式，因此调用 `RecognizeOnceAsync()` 开始识别。 结果是包含有关已识别的意向的信息的 `IntentRecognitionResult` 对象。 以下表达式提取 LUIS JSON 响应：

```csharp
result.Properties.GetProperty(PropertyId.LanguageUnderstandingServiceResponse_JsonResult)
```

教程应用程序不会分析 JSON 结果，而只会在控制台窗口中显示结果。

![LUIS 识别结果](media/sdk/luis-results.png)

## <a name="specify-recognition-language"></a>指定识别语言

默认情况下，LUIS 可以识别美国英语中的意向 (`en-us`)。 将区域设置代码分配到语音配置的 `SpeechRecognitionLanguage` 属性可以识别其他语言的意向。 例如，创建识别器之前在教程应用程序中添加 `config.SpeechRecognitionLanguage = "de-de";` 可以识别德语中的意向。 请参阅[支持的语言](language-support.md#speech-to-text)。

## <a name="continuous-recognition-from-a-file"></a>从文件中连续识别

以下代码演示了使用语音 SDK 的其他两项意向识别功能。 第一项功能是连续识别：有可用的结果时，识别器会发出事件（参阅前文）。 然后，这些事件可由提供的事件处理程序处理。 使用连续识别时，需要调用识别器的 `StartContinuousRecognitionAsync()` 来开始识别，而不是调用 `RecognizeOnceAsync()`。

另一项功能是从 WAV 文件中读取包含要处理的语音的音频。 这涉及到创建音频配置，创建意向识别器时可以使用该配置。 该文件必须是采样率为 16 kHz 的单声道（单音）音频。

若要尝试这些功能，请将 `RecognizeIntentAsync()` 方法的正文替换为以下代码。

[!code-csharp[Intent recognition by using events from a file](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentContinuousRecognitionWithFile)]

如前所述修改代码以包含 LUIS 终结点密钥、区域和应用 ID，并添加家庭自动化意向。 将 `whatstheweatherlike.wav` 更改为音频文件的名称。 然后生成并运行。

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
在 samples/csharp/sharedcontent/console 文件夹中可以找到本文中使用的代码。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [如何识别语音](how-to-recognize-speech-csharp.md)
