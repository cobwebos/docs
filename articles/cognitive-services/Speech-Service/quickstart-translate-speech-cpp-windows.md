---
title: 快速入门：翻译语音，C++ (Windows) - 语音服务
titleSuffix: Azure Cognitive Services
description: 在本快速入门中，你将创建一个 C++ 应用程序来捕获用户语音，将其翻译为另一种语言，并将文本输出到命令行。 本指南适用于 Windows 用户。
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/24/2019
ms.author: erhopf
ms.openlocfilehash: 6ba4e44efc7ff24aa48f9f16840b2248423f7241
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382684"
---
# <a name="quickstart-translate-speech-in-c-on-windows-by-using-the-speech-sdk"></a>快速入门：使用语音 SDK 在 Windows 上的 C++ 中翻译语音

还为[语音识别](quickstart-cpp-windows.md)和[语音合成](quickstart-text-to-speech-cpp-windows.md)提供了快速入门。

在本快速入门中，你将创建一个 C++ 应用程序，该应用程序从计算机的麦克风中捕获用户语音，翻译语音，并将翻译后的文本实时转录到命令行。 该应用程序是使用[语音 SDK NuGet 包](https://aka.ms/csspeech/nuget)和 Microsoft Visual Studio 2019（任何版本）生成的。

有关可用于语音翻译的语言的完整列表，请参阅[语言支持](language-support.md)。

## <a name="prerequisites"></a>先决条件

需要有语音服务订阅密钥才能完成此快速入门。 你可以免费获得一个。 有关详细信息，请参阅[免费试用语音服务](get-started.md)。

## <a name="create-a-visual-studio-project"></a>创建 Visual Studio 项目

[!INCLUDE [Quickstart C++ project](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

## <a name="add-sample-code"></a>添加示例代码

1. 打开源文件 **helloworld.cpp**。

1. 将所有代码替换为以下片段：

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/cpp-windows/helloworld/helloworld.cpp?range=2-#code)]

1. 在同一文件中，将字符串 `YourSubscriptionKey` 替换为你的订阅密钥。

1. 将字符串 `YourServiceRegion` 替换为与订阅关联的[区域](regions.md)（例如，免费试用版订阅的 `westus`）。

1. 在菜单栏中，选择“文件”   > “全部保存”  。

## <a name="build-and-run-the-application"></a>构建并运行应用程序

1. 从菜单栏中，选择“构建”   > “构建解决方案”  以构建应用程序。 现在，编译代码时应不会提示错误。

1. 选择“调试”   > “开始调试”  （或按 F5  ）以启动 helloworld  应用程序。

1. 说一个英语短语或句子。 应用程序将语音传输到语音服务，该服务会翻译并转录为文本（在本例中为法语和德语）。 然后，语音服务会将该文本发送回应用程序以供显示。

   ![成功翻译语音后的控制台输出](media/sdk/qs-translate-cpp-windows-output.png)

## <a name="next-steps"></a>后续步骤

GitHub 上提供了其他示例，例如如何从音频文件中读取语音或将翻译后的文本转为合成语音。

> [!div class="nextstepaction"]
> [浏览 GitHub 上的 C++ 示例](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>另请参阅

- [训练自定义语音模型](how-to-custom-speech-train-model.md)
