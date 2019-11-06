---
title: 快速入门：识别语音、意向和实体，C++ - 语音服务
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
ms.openlocfilehash: b26b5edeaac1f6305ed2db920c711f906eb10384
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73506017"
---
## <a name="prerequisites"></a>先决条件

在开始之前，请务必：

> [!div class="checklist"]
> * [创建一个 Azure 搜索资源](../../../../get-started.md)
> * [创建一个 LUIS 应用程序并获取终结点密钥](../../../../quickstarts/create-luis.md)
> * [设置开发环境](../../../../quickstarts/setup-platform.md?tabs=windows)
> * [创建一个空示例项目](../../../../quickstarts/create-project.md?tabs=windows)

## <a name="open-your-project-in-visual-studio"></a>在 Visual Studio 中打开项目

第一步是确保已在 Visual Studio 中打开项目。

1. 启动 Visual Studio 2019。
2. 加载项目并打开 `helloworld.cpp`。

## <a name="start-with-some-boilerplate-code"></a>从一些样板代码开始

让我们添加一些代码作为项目的主干。 请注意，已创建一个名为 `recognizeIntent()` 的异步方法。
[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=6-16,73-81)]

## <a name="create-a-speech-configuration"></a>创建语音配置

在初始化 `IntentRecognizer` 对象之前，需要创建一个使用 LUIS 终结点密钥和区域的配置。 将此代码插入 `recognizeIntent()` 方法。

此示例使用 `FromSubscription()` 方法来生成 `SpeechConfig`。 有关可用方法的完整列表，请参阅 [SpeechConfig 类](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig)。

> [!NOTE]
> 请务必使用 LUIS 终结点密钥，而不是使用初学者密钥或创作密钥，因为仅终结点密钥对语音到意向的识别有效。 有关如何获取正确密钥的说明，请参阅[创建 LUIS 应用程序并获取终结点密钥](~/articles/cognitive-services/Speech-Service/quickstarts/create-luis.md)。

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=25)]

## <a name="initialize-a-intentrecognizer"></a>初始化 IntentRecognizer

现在，让我们创建 `IntentRecognizer`。 将此代码插入语音配置下的 `recognizeIntent()` 方法。
[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=28)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>添加 LanguageUnderstandingModel 和意向

现在需要将 `LanguageUnderstandingModel` 与意向识别器相关联，并添加要识别的意向。
[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=31-34)]

## <a name="recognize-an-intent"></a>识别意向

在 `IntentRecognizer` 对象中，我们将调用 `RecognizeOnceAsync()` 方法。 此方法是告知语音服务你要发送单个需识别的短语，在确定该短语后会停止识别语音。
（此为简写内容，今后将回头补充。）

在 using 语句中，添加以下代码：[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=44)]

## <a name="display-the-recognition-results-or-errors"></a>显示识别结果（或错误）

如果语音服务返回了识别结果，则需执行一些操作。 我们会简单地将结果输出到控制台。

在 using 语句中 `RecognizeOnceAsync()` 的下面，添加以下代码：[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=47-72)]

## <a name="check-your-code"></a>检查代码

此时，代码应如下所示：（我们已向此版本添加了一些注释）[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=6-81)]

## <a name="build-and-run-your-app"></a>生成并运行应用

现在，已准备好使用语音服务构建应用并测我们的语音识别。

1. **编译代码** - 在 Visual Studio 菜单栏中，选择“生成” > “生成解决方案”   。
2. **启动应用** - 在菜单栏中，选择“调试” > “开始调试”，或按 F5    。
3. **开始识别** - 它将提示你说英语短语。 你的语音将发送到语音服务，转录为文本，并在控制台中呈现。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [footer](./footer.md)]