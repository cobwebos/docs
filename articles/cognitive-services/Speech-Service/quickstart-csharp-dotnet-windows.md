---
title: 快速入门：识别语音，.NET Framework (Windows) - 语音服务
titleSuffix: Azure Cognitive Services
description: 按照本指南使用适用于 Windows 的 .NET framework 和语音 SDK 创建语音转文本控制台应用程序。 完成后，可以使用计算机的麦克风实时将语音转录为文本。
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: wolfma
ms.openlocfilehash: cb140647394858fbc0a9a00ea125365d5b7a08d5
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327042"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-net-framework-windows"></a>快速入门：使用适用于 .NET Framework 的语音 SDK (Windows) 识别语音

还为[语音合成](quickstart-text-to-speech-dotnet-windows.md)和[语音翻译](quickstart-translate-speech-dotnetframework-windows.md)提供了快速入门。

如果愿意，请选择其他编程语言和/或环境：<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

按照本指南使用适用于 Windows 的 .NET framework 和语音 SDK 创建语音转文本控制台应用程序。 完成后，可以使用计算机的麦克风实时将语音转录为文本。

若要快速演示（不需要自己构建 Visual Studio 项目，如本文所述），请从 GitHub 获取最新的[认知服务语音 SDK 示例](https://github.com/Azure-Samples/cognitive-services-speech-sdk)。

## <a name="prerequisites"></a>先决条件

若要完成本项目，需要：

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)。
* 语音服务的订阅密钥。 [免费获得一个](get-started.md)。
* 能够访问计算机的麦克风。

## <a name="create-a-visual-studio-project"></a>创建 Visual Studio 项目

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>添加示例代码

1. 打开 **Program.cs** 并使用此示例替换自动生成的代码：

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. 找到字符串 `YourSubscriptionKey` 并将其替换为语音服务订阅密钥。

1. 查找字符串 `YourServiceRegion`，并将其替换为与订阅关联的[区域](regions.md)。 例如，如果使用的是免费试用订阅，则区域是 `westus`。

1. 在菜单栏中，选择“文件”   > “全部保存”  。

## <a name="build-and-run-the-app"></a>生成并运行应用

1. 从菜单栏中，选择“构建”   > “构建解决方案”  以构建应用程序。 现在，编译代码时应不会提示错误。

1. 选择“调试”   > “开始调试”  （或选择 F5  ）以启动 **helloworld** 应用程序。

1. 对着设备的麦克风讲出一个英文短语或句子。 应用程序会将语音传输到语音服务，后者将转录的文本发回到应用程序以供显示。

   ![语音识别用户界面](media/sdk/qs-csharp-dotnet-windows-10-console-output.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [浏览 GitHub 上的 C# 示例](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>另请参阅

- [训练自定义语音模型](how-to-custom-speech-train-model.md)
