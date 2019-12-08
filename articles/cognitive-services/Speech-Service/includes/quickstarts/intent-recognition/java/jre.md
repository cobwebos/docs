---
title: 快速入门：识别语音、意向和实体，Java - 语音服务
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 49aac37d298a1d2cd52e815ae6fa5e08e6cc80c2
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2019
ms.locfileid: "74816175"
---
## <a name="prerequisites"></a>先决条件

在开始之前，请务必：

> [!div class="checklist"]
>
> * [创建一个 Azure 搜索资源](../../../../get-started.md)
> * [创建语言理解 (LUIS) 应用程序并获取终结点密钥](../../../../quickstarts/create-luis.md)
> * [设置开发环境](../../../../quickstarts/setup-platform.md?tabs=jre)
> * [创建空示例项目](../../../../quickstarts/create-project.md?tabs=jre)

## <a name="open-your-project"></a>打开项目

加载项目并打开 `Main.java`。

## <a name="start-with-some-boilerplate-code"></a>从一些样本代码入手

添加一些代码作为项目的框架。
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=6-20,69-76)]

## <a name="create-a-speech-configuration"></a>创建语音配置

在初始化 `IntentRecognizer` 对象之前，需要创建一个使用 LUIS 终结点密钥和区域的配置。 将此代码插入到 main 中的 try/catch 块中

此示例使用 `FromSubscription()` 方法来生成 `SpeechConfig`。 有关可用方法的完整列表，请参阅 [SpeechConfig 类](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet)。
语音 SDK 将默认使用 en-us 作为语言进行识别。若要了解如何选择源语言，请参阅[指定语音转文本的源语言](../../../../how-to-specify-source-language.md)。

> [!NOTE]
> 请务必使用 LUIS 终结点密钥（而非初学者密钥或创作密钥），因为只有终结点密钥对于语音转意向的识别有效。 有关如何获取正确密钥的说明，请参阅[创建 LUIS 应用程序并获取终结点密钥](~/articles/cognitive-services/Speech-Service/quickstarts/create-luis.md)。

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=27)]

## <a name="initialize-an-intentrecognizer"></a>初始化 IntentRecognizer

现在，让我们创建 `IntentRecognizer`。 将此代码插入语音配置下。
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=30)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>添加 LanguageUnderstandingModel 和意向

现在需要将 `LanguageUnderstandingModel` 与意向识别器相关联，并添加要识别的意向。
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=33-36)]

## <a name="recognize-an-intent"></a>识别意向

在 `IntentRecognizer` 对象中，我们将调用 `recognizeOnceAsync()` 方法。 此方法是告知语音服务你要发送单个需识别的短语，在确定该短语后会停止识别语音。

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=41)]

## <a name="display-the-recognition-results-or-errors"></a>显示识别结果（或错误）

语音服务返回识别结果后，将需要对其进行处理。 我们将使其保持简单，并将结果打印到控制台。

在调用 `recognizeOnceAsync()` 下，添加以下代码：[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=44-65)]

## <a name="release-resources"></a>释放资源

使用完语音资源后，必须将其释放。 将此代码插入到 try/catch 块的末尾：[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=67-68)]

## <a name="check-your-code"></a>查看代码

此时，代码应如下所示：  
（我们已向此版本添加了一些注释）[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=6-76)]

## <a name="build-and-run-your-app"></a>生成并运行应用

按 F11，或选择“运行” > “调试”。  
接下来的 15 秒，通过麦克风提供的语音输入将被识别并记录到控制台窗口中。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [footer](./footer.md)]