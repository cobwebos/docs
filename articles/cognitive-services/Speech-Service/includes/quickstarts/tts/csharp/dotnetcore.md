---
title: 快速入门：合成语音，C# (.NET Core) - 语音服务
titleSuffix: Azure Cognitive Services
description: 了解如何在 Windows 上使用语音 SDK 通过 .NET Core 下的 C# 合成语音
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 6/24/2019
ms.author: yinhew
ms.openlocfilehash: 670d32a93df9e2a0363163079b50c7306f81975e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73504881"
---
> [!NOTE]
> .NET Core 是一个实现了 [.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard) 规范的开源跨平台 .NET 平台。

## <a name="prerequisites"></a>先决条件

在开始之前，请务必：

> [!div class="checklist"]
> * [创建 Azure 语音资源](../../../../get-started.md)
> * [设置开发环境](../../../../quickstarts/setup-platform.md?tabs=dotnetcore)
> * [创建空示例项目](../../../../quickstarts/create-project.md?tabs=dotnetcore)
## <a name="add-sample-code"></a>添加示例代码

1. 打开 `Program.cs` 并将其中的所有代码替换为以下内容。

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnetcore/text-to-speech/helloworld/Program.cs#code)]

1. 在同一文件中，将字符串 `YourSubscriptionKey` 替换为你的订阅密钥。

1. 另外，请将字符串 `YourServiceRegion` 替换为与你的订阅关联的[区域](~/articles/cognitive-services/Speech-Service/regions.md)（例如，免费试用版订阅的 `westus`）。

1. 保存对项目的更改。

## <a name="build-and-run-the-app"></a>生成并运行应用

1. 构建应用程序。 从菜单栏中，选择“构建” > “构建解决方案”   。 编译代码时应不会出错。

    ![Visual Studio 应用程序的屏幕截图，其中突出显示了“生成解决方案”选项](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-05-build.png "成功的生成")

1. 启动应用程序。 在菜单栏中，选择“调试” > “开始调试”，或按 F5    。

    ![Visual Studio 应用程序的屏幕截图，其中突出显示了“启动调试”选项](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "启动应用进行调试")

1. 此时会显示控制台窗口，提示你键入一些文本。 键入几个单词或一个句子。 键入的文本将传输到语音服务，并合成为语音，在扬声器上播放。

    ![成功合成后的控制台输出的屏幕截图](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-dotnet-windows-console-output.png "成功合成后的控制台输出")

## <a name="next-steps"></a>后续步骤

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>另请参阅

- [创建自定义语音](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [录制自定义语音示例](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
