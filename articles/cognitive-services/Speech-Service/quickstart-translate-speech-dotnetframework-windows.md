---
title: 快速入门：翻译语音，C# (.NET Framework Windows) - 语音服务
titleSuffix: Azure Cognitive Services
description: 在本快速入门中，你将创建一个 .NET Framework 应用程序来捕获用户语音，将其翻译为另一种语言，并将文本输出到命令行。 本指南适用于 Windows 用户。
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: erhopf
ms.openlocfilehash: bb710a3e3adb13aa3999c13043c8bb93f6b885f1
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327344"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-net-framework-windows"></a>快速入门：使用适用于 .NET Framework (Windows) 的语音 SDK 翻译语音

还为[语音识别](quickstart-csharp-dotnet-windows.md)和[语音合成](quickstart-text-to-speech-dotnet-windows.md)提供了快速入门。

在本快速入门中，你将创建一个 .NET Framework 应用程序，该应用程序从计算机的麦克风中捕获用户语音，翻译语音，并将翻译后的文本实时转录到命令行。 此应用程序可以在 32 位或 64 位 Windows 上运行，并且它是使用[语音 SDK NuGet 包](https://aka.ms/csspeech/nuget)和 Microsoft Visual Studio 2019 生成的。

有关可用于语音翻译的语言的完整列表，请参阅[语言支持](language-support.md)。

## <a name="prerequisites"></a>先决条件

本快速入门需要：

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)。
* 语音服务的 Azure 订阅密钥。 [免费获得一个](get-started.md)。

## <a name="create-a-visual-studio-project"></a>创建 Visual Studio 项目

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>添加示例代码

1. 打开 **Program.cs** 并将其中的所有代码替换为以下内容。

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. 查找字符串 `YourSubscriptionKey`，并将其替换为订阅密钥。

1. 查找字符串 `YourServiceRegion`，并将其替换为与订阅关联的[区域](regions.md)。 例如，如果使用的是免费试用订阅，区域是 `westus`。

1. 在菜单栏中，选择“文件”   > “全部保存”  。

## <a name="build-and-run-the-application"></a>构建并运行应用程序

1. 从菜单栏中，选择“构建”   > “构建解决方案”  以构建应用程序。 现在，编译代码时应不会提示错误。

1. 选择“调试”   > “开始调试”  （或选择 F5  ）以启动 helloworld  应用程序。

1. 对着设备的麦克风讲出一个英文短语或句子。 应用程序将语音传输到语音服务，该服务会将语音翻译为其他语言（在本例中为德语）的文本。 语音服务将翻译后的文本发送回应用程序，该应用程序在窗口中显示翻译。

   ![语音翻译用户界面](media/sdk/qs-translate-csharp-dotnetcore-windows-output.png)

## <a name="next-steps"></a>后续步骤

GitHub 上提供了其他示例，例如如何从音频文件中读取语音，以及将翻译后的文本作为合成语音输出。

> [!div class="nextstepaction"]
> [浏览 GitHub 上的 C# 示例](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>另请参阅

- [训练自定义语音模型](how-to-custom-speech-train-model.md)
