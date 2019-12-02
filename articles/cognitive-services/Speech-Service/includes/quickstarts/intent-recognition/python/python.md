---
title: 快速入门：识别语音、意向和实体，Python - 语音服务
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
ms.openlocfilehash: f39ddc8a3460bc026bdac96c18b5bea4d6ecfc0f
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280396"
---
## <a name="prerequisites"></a>先决条件

在开始之前，请务必：

> [!div class="checklist"]
>
> * [创建一个 Azure 搜索资源](../../../../get-started.md)
> * [创建语言理解 (LUIS) 应用程序并获取终结点密钥](../../../../quickstarts/create-luis.md)
> * [设置开发环境](../../../../quickstarts/setup-platform.md)
> * [创建空示例项目](../../../../quickstarts/create-project.md)

## <a name="open-your-project"></a>打开项目

在 Python 编辑器中打开 Quickstart.py。

## <a name="start-with-some-boilerplate-code"></a>从一些样板代码开始

添加一些代码作为项目的框架。
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-7)]

## <a name="create-a-speech-configuration"></a>创建语音配置

在初始化 `IntentRecognizer` 对象之前，需要创建一个使用 LUIS 终结点密钥和区域的配置。 接下来，插入此代码。

此示例使用 LUIS 密钥和区域构造 `SpeechConfig` 对象。 有关可用方法的完整列表，请参阅 [SpeechConfig 类](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig)。

> [!NOTE]
> 请务必使用 LUIS 终结点密钥（而非初学者密钥或创作密钥），因为只有终结点密钥对于语音转意向的识别有效。 有关如何获取正确密钥的说明，请参阅[创建 LUIS 应用程序并获取终结点密钥](~/articles/cognitive-services/Speech-Service/quickstarts/create-luis.md)。

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=12)]

## <a name="initialize-an-intentrecognizer"></a>初始化 IntentRecognizer

现在，让我们创建 `IntentRecognizer`。 将此代码插入语音配置下。
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=15)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>添加 LanguageUnderstandingModel 和意向

现在需要将 `LanguageUnderstandingModel` 与意向识别器相关联，并添加要识别的意向。
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=19-27)]

## <a name="recognize-an-intent"></a>识别意向

在 `IntentRecognizer` 对象中，我们将调用 `recognize_once()` 方法。 此方法是告知语音服务你要发送单个需识别的短语，在确定该短语后会停止识别语音。
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=35)]

## <a name="display-the-recognition-results-or-errors"></a>显示识别结果（或错误）

语音服务返回识别结果后，将需要对其进行处理。 我们会简单地将结果输出到控制台。

在 using 语句中对 `recognize_once()` 的调用下面，添加以下代码：[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=38-47)]

## <a name="check-your-code"></a>检查代码

此时，代码应如下所示：  
（我们已向此版本添加了一些注释）[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-47)]

## <a name="build-and-run-your-app"></a>生成并运行应用

通过控制台或 IDE 运行示例：

    ````
    python quickstart.py
    ````

接下来的 15 秒，通过麦克风提供的语音输入将被识别并记录到控制台窗口中。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [footer](./footer.md)]
