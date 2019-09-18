---
title: 快速入门：合成语音，C++ (Windows) - 语音服务
titleSuffix: Azure Cognitive Services
description: 了解如何在 Windows 桌面上使用语音 SDK 通过 C++ 合成语音
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/24/2019
ms.author: yinhew
ms.openlocfilehash: 686b21d3e02266af77687778c32f0d1ca6d55154
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/05/2019
ms.locfileid: "70383078"
---
# <a name="quickstart-synthesize-speech-in-c-on-windows-by-using-the-speech-sdk"></a>快速入门：使用语音 SDK 在 Windows 上的 C++ 中合成语音

还为[语音识别](quickstart-cpp-windows.md)和[语音翻译](quickstart-translate-speech-cpp-windows.md)提供了快速入门。

在本文中，请创建适用于 Windows 的 C++ 控制台应用程序。 使用认知服务[语音 SDK](speech-sdk.md) 实时从文本合成语音，并在电脑的扬声器上播放语音。 该应用程序是使用[语音 SDK NuGet 包](https://aka.ms/csspeech/nuget)和 Microsoft Visual Studio 2019（任何版本）构建的。

有关可用于语音合成的语言/语音的完整列表，请参阅[语言支持](language-support.md#text-to-speech)。

## <a name="prerequisites"></a>先决条件

需要有语音服务订阅密钥才能完成此快速入门。 你可以免费获得一个。 有关详细信息，请参阅[免费试用语音服务](get-started.md)。

## <a name="create-a-visual-studio-project"></a>创建 Visual Studio 项目

[!INCLUDE [Quickstart C++ project](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

## <a name="add-sample-code"></a>添加示例代码

1. 打开源文件 **helloworld.cpp**。

1. 将所有代码替换为以下片段：

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/cpp-windows/helloworld/helloworld.cpp#code)]

1. 在同一文件中，将字符串 `YourSubscriptionKey` 替换为你的订阅密钥。

1. 将字符串 `YourServiceRegion` 替换为与订阅关联的[区域](regions.md)（例如，免费试用版订阅的 `westus`）。

1. 在菜单栏中，选择“文件”   > “全部保存”  。

## <a name="build-and-run-the-application"></a>构建并运行应用程序

1. 从菜单栏中，选择“构建”   > “构建解决方案”  以构建应用程序。 现在，编译代码时应不会提示错误。

1. 选择“调试”   > “开始调试”  （或按 F5  ）以启动 helloworld  应用程序。

1. 键入一个英语短语或句子。 应用程序将你的文本传输到语音服务，该服务会将合成的语音发送到应用程序以在你的扬声器上播放。

   ![成功语音合成后的控制台输出](media/sdk/qs-tts-cpp-windows-console-output.png)

## <a name="next-steps"></a>后续步骤

GitHub 上提供了其他示例，例如如何将语音保存到音频文件。

> [!div class="nextstepaction"]
> [浏览 GitHub 上的 C++ 示例](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>另请参阅

- [创建自定义语音](how-to-custom-voice-create-voice.md)
- [录制自定义语音示例](record-custom-voice-samples.md)
