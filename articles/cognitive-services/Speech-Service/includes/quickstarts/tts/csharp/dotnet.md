---
title: 快速入门：合成语音，C# (Windows) - 语音服务
titleSuffix: Azure Cognitive Services
description: 按照本指南使用适用于 Windows 的 .NET framework 和语音 SDK 创建文本转语音控制台应用程序。 完成后，你可以从文本合成语音，并实时听到扬声器上的语音。
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/27/2019
ms.author: yinhew
ms.openlocfilehash: af101862e824d6527c08c232812913565536bb4a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73505153"
---
## <a name="prerequisites"></a>先决条件

在开始之前，请务必：

> [!div class="checklist"]
> * [创建 Azure 语音资源](../../../../get-started.md)
> * [设置开发环境](../../../../quickstarts/setup-platform.md?tabs=dotnet)
> * [创建空示例项目](../../../../quickstarts/create-project.md?tabs=dotnet)

## <a name="add-sample-code"></a>添加示例代码

1. 打开 **Program.cs** 并使用此示例替换自动生成的代码：

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/text-to-speech/helloworld/Program.cs#code)]

1. 找到字符串 `YourSubscriptionKey` 并将其替换为语音服务订阅密钥。

1. 查找字符串 `YourServiceRegion`，并将其替换为与订阅关联的[区域](~/articles/cognitive-services/Speech-Service/regions.md)。 例如，如果使用的是免费试用订阅，则区域是 `westus`。

1. 在菜单栏中，选择“文件”   > “全部保存”  。

## <a name="build-and-run-the-application"></a>构建并运行应用程序

1. 从菜单栏中，选择“构建”   > “构建解决方案”  以构建应用程序。 现在，编译代码时应不会提示错误。

1. 选择“调试”   > “开始调试”  （或选择 F5  ）以启动 helloworld  应用程序。

1. 输入一个英语短语或句子。 应用程序将你的文本传输到语音服务，该服务会将合成的语音发送到应用程序以在你的扬声器上播放。

   ![语音合成用户界面](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-dotnet-windows-console-output.png)

## <a name="next-steps"></a>后续步骤

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>另请参阅

- [创建自定义语音](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [录制自定义语音示例](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
