---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 01/27/2020
ms.topic: include
ms.author: dapine
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 1c5411778bf2e8ab25ce3e71dba92227b2653c3d
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2020
ms.locfileid: "77445267"
---
## <a name="prerequisites"></a>必备条件

准备工作：

* 如果这是第一个 C++ 项目，请使用本指南<a href="~/articles/cognitive-services/Speech-Service/quickstarts/create-project.md?tabs=windows" target="_blank">创建一个空示例项目<span class="docon docon-navigate-external x-hidden-focus"></span></a>。
* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?tabs=windows" target="_blank">安装用于开发环境的语音 SDK<span class="docon docon-navigate-external x-hidden-focus"></span></a>。

## <a name="create-a-luis-app-for-intent-recognition"></a>创建 LUIS 应用以进行意向识别

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project-in-visual-studio"></a>在 Visual Studio 中打开项目

接下来，在 Visual Studio 中打开项目。

1. 启动 Visual Studio 2019。
2. 加载项目并打开 `helloworld.cpp`。

## <a name="start-with-some-boilerplate-code"></a>从一些样本代码入手

添加一些代码作为项目的框架。 请注意，已创建名为 `recognizeIntent()` 的异步方法。

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=6-16,72-80)]

## <a name="create-a-speech-configuration"></a>创建语音配置

需要创建一个使用 LUIS 预测资源的密钥和位置的配置，才能初始化 `IntentRecognizer` 对象。

> [!IMPORTANT]
> 起始密钥和创作密钥将不起作用。 必须使用之前创建的预测密钥和位置。 有关详细信息，请参阅[创建 LUIS 应用以进行意向识别](#create-a-luis-app-for-intent-recognition)。

将此代码插入 `recognizeIntent()` 方法。 请确保更新以下值：

* 将 `"YourLanguageUnderstandingSubscriptionKey"` 替换为 LUIS 预测密钥。
* 将 `"YourLanguageUnderstandingServiceRegion"` 替换为 LUIS 位置。  使用[区域](https://aka.ms/speech/sdkregion)中的“区域标识符”。 

>[!TIP]
> 如果需要帮助查找这些值，请参阅[创建 LUIS 应用以进行意向识别](#create-a-luis-app-for-intent-recognition)。

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=25)]

此示例使用 `FromSubscription()` 方法来生成 `SpeechConfig`。 有关可用方法的完整列表，请参阅 [SpeechConfig 类](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig)。

语音 SDK 将默认使用 en-us 作为语言进行识别。若要了解如何选择源语言，请参阅[指定语音转文本的源语言](../../../../how-to-specify-source-language.md)。

## <a name="initialize-an-intentrecognizer"></a>初始化 IntentRecognizer

现在，让我们创建 `IntentRecognizer`。 将此代码插入语音配置下的 `recognizeIntent()` 方法。

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=28)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>添加 LanguageUnderstandingModel 和意向

需要将 `LanguageUnderstandingModel` 与意向识别器相关联，并添加要识别的意向。 我们将使用预生成的域中的意向进行主自动化。

将此代码插入到你的 `IntentRecognizer` 下方。 请确保将 `"YourLanguageUnderstandingAppId"` 替换为 LUIS 应用 ID。

>[!TIP]
> 如果需要查找此值的帮助，请参阅[创建 LUIS 应用以进行意向识别](#create-a-luis-app-for-intent-recognition)。

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=31-33)]

## <a name="recognize-an-intent"></a>识别意向

在 `IntentRecognizer` 对象中，我们将调用 `RecognizeOnceAsync()` 方法。 此方法是告知语音服务你要发送单个需识别的短语，在确定该短语后会停止识别语音。 此为简写内容，今后将回头补充。

将此代码插入到你的模型下方：

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=43)]

## <a name="display-the-recognition-results-or-errors"></a>显示识别结果（或错误）

语音服务返回识别结果后，将需要对其进行处理。 我们会简单地将结果输出到控制台。

将此代码插在 `auto result = recognizer->RecognizeOnceAsync().get();` 下：

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=46-71)]

## <a name="check-your-code"></a>查看代码

此时，代码应如下所示：

> [!NOTE]
> 我们已向此版本添加了一些注释。

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=6-79)]

## <a name="build-and-run-your-app"></a>生成并运行应用

现在，可以使用语音服务构建应用并测试语音识别。

1. “编译代码”- 在 Visual Studio 菜单栏中，选择“生成” > “生成解决方案”    。
2. **启动应用** - 在菜单栏中，选择“调试” > “开始调试”，或按 F5   <kbd></kbd>。
3. **开始识别** - 它将提示你说英语短语。 语音将发送到语音服务，转录为文本，并在控制台中呈现。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [footer](./footer.md)]
