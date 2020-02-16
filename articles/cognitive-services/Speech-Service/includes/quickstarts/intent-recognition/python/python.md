---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 01/27/2020
ms.topic: include
ms.author: dapine
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 8f3be4dfb0beeb97558dbda606af7c6bcc50bfa7
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77156480"
---
## <a name="prerequisites"></a>必备条件

准备工作：

* 如果这是第一个 Python 项目，请使用本指南<a href="~/articles/cognitive-services/Speech-Service/quickstarts/create-project.md" target="_blank">创建一个空示例项目<span class="docon docon-navigate-external x-hidden-focus"></span></a>。
* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md" target="_blank">安装用于开发环境的语音 SDK<span class="docon docon-navigate-external x-hidden-focus"></span></a>。

## <a name="create-a-luis-app-for-intent-recognition"></a>创建 LUIS 应用以进行意向识别

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project"></a>打开项目

1. 打开首选 IDE。
2. 创建新项目，并创建名为 `quickstart.py` 的文件，然后将其打开。

## <a name="start-with-some-boilerplate-code"></a>从一些样本代码入手

添加一些代码作为项目的框架。

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-7)]

## <a name="create-a-speech-configuration"></a>创建语音配置

需要创建一个使用 LUIS 预测资源的密钥和位置的配置，才能初始化 `IntentRecognizer` 对象。

将此代码插入 `quickstart.py`。 请确保更新以下值：

* 将 `"YourLanguageUnderstandingSubscriptionKey"` 替换为 LUIS 预测密钥。 
* 将 `"YourLanguageUnderstandingServiceRegion"` 替换为 LUIS 位置。 使用[区域](https://aka.ms/speech/sdkregion)中的“语音 SDK 参数”

>[!TIP]
> 如果需要帮助查找这些值，请参阅[创建 LUIS 应用以进行意向识别](#create-a-luis-app-for-intent-recognition)。

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=12)]

此示例使用 LUIS 密钥和区域构造 `SpeechConfig` 对象。 有关可用方法的完整列表，请参阅 [SpeechConfig 类](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig)。

语音 SDK 将默认使用 en-us 作为语言进行识别。若要了解如何选择源语言，请参阅[指定语音转文本的源语言](../../../../how-to-specify-source-language.md)。

## <a name="initialize-an-intentrecognizer"></a>初始化 IntentRecognizer

现在，让我们创建 `IntentRecognizer`。 将此代码插入语音配置下。

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=15)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>添加 LanguageUnderstandingModel 和意向

需要将 `LanguageUnderstandingModel` 与意向识别器相关联，并添加要识别的意向。 我们将使用预生成的域中的意向进行主自动化。

将此代码插入到你的 `IntentRecognizer` 下方。 请确保将 `"YourLanguageUnderstandingAppId"` 替换为 LUIS 应用 ID。 

>[!TIP]
> 如果需要查找此值的帮助，请参阅[创建 LUIS 应用以进行意向识别](#create-a-luis-app-for-intent-recognition)。

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=19-27)]

## <a name="recognize-an-intent"></a>识别意向

在 `IntentRecognizer` 对象中，我们将调用 `recognize_once()` 方法。 此方法是告知语音服务你要发送单个需识别的短语，在确定该短语后会停止识别语音。

将此代码插入到你的模型下方：

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=35)]

## <a name="display-the-recognition-results-or-errors"></a>显示识别结果（或错误）

语音服务返回识别结果后，将需要对其进行处理。 我们会简单地将结果输出到控制台。

在调用 `recognize_once()` 下，添加以下代码：[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=38-47)]

## <a name="check-your-code"></a>查看代码

此时，代码应如下所示：

> [!NOTE]
> 我们已向此版本添加了一些注释。

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-47)]

## <a name="build-and-run-your-app"></a>生成并运行应用

通过控制台或 IDE 运行示例：

```
python quickstart.py
```

接下来的 15 秒，通过麦克风提供的语音输入将被识别并记录到控制台窗口中。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [footer](./footer.md)]
