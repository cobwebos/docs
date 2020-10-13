---
title: 测试自定义命令应用
titleSuffix: Azure Cognitive Services
description: 本文介绍测试自定义命令应用程序的不同方法。
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.custom: devx-track-csharp
ms.openlocfilehash: ec8e40c0908855cd06d647bdd9121106e3553c11
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88918904"
---
# <a name="test-your-custom-commands-application"></a>测试自定义命令应用程序

本文介绍测试自定义命令应用程序的不同方法。

## <a name="test-in-the-portal"></a>在门户中测试

门户中的测试是检查自定义命令应用程序是否按预期运行的最简单且最快捷的方法。 成功训练应用后，单击 " `Test` 按钮" 以开始测试。

> [!div class="mx-imgBorder"]
> ![在门户中测试](media/custom-commands/create-basic-test-chat.png)

## <a name="test-with-windows-voice-assistant-client"></a>用 Windows 语音助手客户端进行测试

Windows 语音助手客户端是 c # 中的一种 Windows Presentation Foundation (WPF) 应用程序，使您可以轻松地在创建自定义客户端应用程序之前测试与机器人的交互。

该工具可以接受自定义命令应用程序 ID。 它允许您测试在自定义命令服务上托管的任务完成或命令和控制方案。

若要设置客户端，请签出 [Windows 语音助手客户端](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/clients/csharp-wpf)。

> [!div class="mx-imgBorder"]
> ![WVAC 创建配置文件](media/custom-commands/conversation.png)

## <a name="test-with-speech-sdk-enabled-client-applications"></a>测试已启用 Speech SDK 的客户端应用程序 
 (SDK) 的语音软件开发工具包公开了许多语音服务功能，可用于开发启用了语音功能的应用程序。 它还可在许多编程语言和所有平台上使用。

使用 Speech SDK 设置通用 Windows 平台 (UWP) 客户端应用程序，并将其与自定义命令应用程序集成：  
- [如何：使用语音 SDK 与客户端应用程序集成](./how-to-custom-commands-setup-speech-sdk.md)
- [如何：将活动发送到客户端应用程序](./how-to-custom-commands-send-activity-to-client.md)
- [如何：设置 web 终结点](./how-to-custom-commands-setup-web-endpoints.md)

对于其他编程语言和平台：
- [语音 SDK 编程语言、平台和方案容量](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk)
- [语音助手示例代码](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [请参阅 GitHub 上的示例](https://aka.ms/speech/cc-samples)

