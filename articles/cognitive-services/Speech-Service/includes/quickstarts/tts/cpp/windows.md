---
title: 快速入门：合成语音，C++ (Windows) - 语音服务
titleSuffix: Azure Cognitive Services
description: 了解如何在 Windows 桌面上使用语音 SDK 通过 C++ 合成语音
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/24/2019
ms.author: yinhew
ms.openlocfilehash: c0666dccbb7f110e122c4632995299a414bf690a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73505409"
---
## <a name="prerequisites"></a>先决条件

入门之前，准备工作有：

> [!div class="checklist"]
> * [创建 Azure 搜索资源](../../../../get-started.md)
> * [设置开发环境](../../../../quickstarts/setup-platform.md?tabs=windows)
> * [创建空示例项目](../../../../quickstarts/create-project.md?tabs=windows)

## <a name="add-sample-code"></a>添加示例代码

1. 打开源文件 **helloworld.cpp**。

1. 将所有代码替换为以下片段：

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/text-to-speech/helloworld/helloworld.cpp#code)]

1. 在同一文件中，将字符串 `YourSubscriptionKey` 替换为你的订阅密钥。

1. 将字符串 `YourServiceRegion` 替换为与订阅关联的[区域](~/articles/cognitive-services/Speech-Service/regions.md)（例如，免费试用版订阅的 `westus`）。

1. 在菜单栏中，选择“文件”   > “全部保存”  。

## <a name="build-and-run-the-application"></a>构建并运行应用程序

1. 从菜单栏中，选择“构建”   > “构建解决方案”  以构建应用程序。 现在，编译代码时应不会提示错误。

1. 选择“调试”   > “开始调试”  （或按 F5  ）以启动 helloworld  应用程序。

1. 键入一个英语短语或句子。 应用程序将你的文本传输到语音服务，该服务会将合成的语音发送到应用程序以在你的扬声器上播放。

   ![成功语音合成后的控制台输出](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-cpp-windows-console-output.png)

## <a name="next-steps"></a>后续步骤

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>另请参阅

- [创建自定义语音](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [录制自定义语音示例](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
