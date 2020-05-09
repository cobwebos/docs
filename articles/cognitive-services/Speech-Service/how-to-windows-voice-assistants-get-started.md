---
title: Windows 上的语音助手-入门
titleSuffix: Azure Cognitive Services
description: 开始开发 windows 语音代理的步骤，包括对示例代码快速入门的参考。
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: 4ea2f88b02738645af3f8fc32d5fdb99168a1122
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2020
ms.locfileid: "82997377"
---
# <a name="getting-started-with-voice-assistants-on-windows"></a>Windows 上的语音助手入门

本指南将指导你完成在 Windows 上开发语音助手的步骤。

## <a name="set-up-your-development-environment"></a>设置开发环境

若要开始开发适用于 Windows 的语音助手，需要确保具有适当的开发环境。

- **Visual Studio：** 你将需要安装 [Microsoft Visual Studio 2017](https://visualstudio.microsoft.com/)、社区版或更高版本
- **Windows 版本**：具有 windows 有问必答 fast 循环构建的 Windows 和 windows 有问必答版 WINDOWS SDK 的 PC。此示例代码将在 Windows 预览体验版版本 vb_release_analog 19025 中验证为使用 Windows SDK 19018。指定版本以上的任何生成或 SDK 都应兼容。
- **UWP 开发工具**： Visual Studio 中的通用 Windows 平台开发工作负载。请参阅 "UWP[获取设置](https://docs.microsoft.com/windows/uwp/get-started/get-set-up)" 页，使您的计算机准备好开发 UWP 应用程序。
- **工作麦克风和音频输出**

## <a name="obtain-resources-from-microsoft"></a>从 Microsoft 获取资源

Windows 上完全自定义的语音代理所需的某些资源将需要 Microsoft 提供的资源。 [UWP 语音助手示例](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample)为初始开发和测试提供了这些资源的示例版本，因此不需要此部分进行初始开发。

- **关键字模型：** 语音激活需要 Microsoft 的关键字模型，格式为 bin 文件。 UWP 语音助手示例中提供的 bin 文件的关键字为 "Contoso"。
- **受限访问功能令牌：** 由于 ConversationalAgent Api 提供对麦克风音频的访问权限，因此它们受有限访问功能的限制。若要使用有限访问功能，你将需要从 Microsoft 获取连接到应用程序的包标识的有限访问权限功能标记。

## <a name="establish-a-dialog-service"></a>建立对话服务

若要获得完整的语音助手体验，应用程序需要一个对话框服务，

- 在给定音频文件中检测关键字
- 侦听用户输入并将其转换为文本
- 向机器人提供文本
- 将机器人的文本响应转换为音频输出

下面是使用直接行语音创建基本对话框服务的要求。

- **Speech Services 订阅：** 用于语音到文本和文本到语音转换的认知语音服务的订阅。 [在此处](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started)免费试用语音服务。
- **机器人框架机器人：** 使用 Bot Framework 4.2 或更高版本创建的机器人，该机器人已订阅[定向行语音](https://docs.microsoft.com/azure/cognitive-services/speech-service/direct-line-speech)以启用语音输入和输出。 [本指南](https://docs.microsoft.com/azure/cognitive-services/speech-service/tutorial-voice-enable-your-bot-speech-sdk)包含创建 "echo bot" 并将其订阅为直接行语音的分步说明。 你还可以转到[此处](https://blog.botframework.com/2018/05/07/build-a-microsoft-bot-framework-bot-with-the-bot-builder-sdk-v4/)，获取有关如何创建自定义机器人的步骤，然后按照[此处](https://docs.microsoft.com/azure/cognitive-services/speech-service/tutorial-voice-enable-your-bot-speech-sdk)的相同步骤，使用新的机器人（而不是 "echo 机器人"）来订阅。

## <a name="try-out-the-sample-app"></a>试用示例应用

通过语音服务订阅密钥和 echo bot 的机器人 ID，你可以试用[UWP 语音助手示例](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample)。 按照自述文件中的说明运行应用并输入你的凭据。

## <a name="create-your-own-voice-assistant-for-windows"></a>为 Windows 创建自己的语音助手

收到来自 Microsoft 的有限访问权限功能令牌和 bin 文件后，可以在 Windows 上自己的语音助手开始使用。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [阅读语音助手实现指南](windows-voice-assistants-implementation-guide.md)
