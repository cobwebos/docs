---
title: 微调文本到语音输出-语音服务
titleSuffix: Azure Cognitive Services
description: 在语音 SDK 中启用日志记录。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 4cf2338d76ce31f44eaf3fb235e5f8796602d819
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562748"
---
# <a name="fine-tune-text-to-speech-output"></a>微调文本转语音输出

使用 Azure 语音服务, 可以使用[语音合成标记语言 (SSML)](speech-synthesis-markup.md)调整文本到语音输出的速度、发音、音量、音调和轮廓。 SSML 是一种基于 XML 的标记语言, 它使用标记来通知服务需要优化哪些功能。 然后, 将 SSML 消息发送到文本到语音服务的每个请求的正文中。 为了简化自定义过程, 语音服务现在提供了[语音调整](https://aka.ms/voicetuning)工具, 使您能够以可视方式实时检查和微调文本到语音输出。

语音调整工具支持 Microsoft 的[标准](language-support.md#standard-voices)、[神经](language-support.md#text-to-speech)和[自定义语音](how-to-customize-voice-font.md)。

## <a name="get-started-with-the-voice-tuning-tool"></a>语音调整工具入门

你需要先完成以下步骤, 然后才能开始通过语音调整工具来微调文本到语音输出:

1. 如果还没有[可用的 Microsoft 帐户](https://account.microsoft.com/account), 请创建一个。
2. 如果还没有[Azure 帐户](https://azure.microsoft.com/free/), 请创建一个。 单击 "**免费开始**", 并使用 Microsoft 帐户创建新的 Azure 帐户。

3. 在 Azure 门户中创建语音服务订阅。 提供[如何创建语音资源](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure)的分步说明。
   >[!NOTE]
   >在 Azure 门户中创建语音资源时, Azure 位置信息需要与 TTS 语音区域匹配。 神经 TTS 语音支持一组 Azure 位置。 有关支持的完整列表, 请参阅[区域](regions.md#text-to-speech)。

   >[!NOTE]
   >你需要在 Azure 门户中创建 F0 或 S0 键, 然后才能使用服务。 语音优化**不**支持[30 天免费试用版](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started?branch=release-build-cogserv-speech-services#free-trial)。

4. 登录到[语音优化](https://aka.ms/voicetuning)门户, 并连接语音服务订阅。 选择单个语音服务订阅, 并创建一个项目。
5. 选择 "**新建优化**"。 然后执行以下步骤:

   * 找到并选择 "**所有订阅**"。  
   * 选择“连接现有订阅”。  
     ![连接现有订阅](./media/custom-voice/custom-voice-connect-subscription.png)。
   * 输入 Azure Speech Services 订阅密钥, 并选择 "**添加**"。 订阅密钥在 "[订阅" 页](https://go.microsoft.com/fwlink/?linkid=2090458)的 "语音自定义门户" 中提供。 还可以从[Azure 门户](https://portal.azure.com/)的 "资源管理" 窗格中获取密钥。
   * 如果你有多个计划使用的语音服务订阅, 请对每个订阅重复这些步骤。

## <a name="customize-the-text-to-speech-output"></a>自定义文本到语音输出

现在, 你已创建帐户并链接了你的订阅, 接下来可以开始优化文本到语音转换的输出。

1. 选择一种语音。
2. 输入要编辑的文本。
3. 开始进行编辑之前, 请播放音频以获得输出。
4. 选择要优化的单词/句子, 并开始试验不同的基于 SSML 的函数。

>[!TIP]
> 有关调整 SSML 和优化语音输出的详细信息, 请参阅[语音合成标记语言 (SSML)](speech-synthesis-markup.md)。

## <a name="limitations"></a>限制

对于标准和自定义语音, 神经语音优化与优化略有不同。

* 神经语音不支持 Intonation。
* 螺距和成交量功能仅用于完成句子。 这些功能在 word 级别不可用。
* 对于 "速率", 某些神经声音可以根据字词进行优化, 而其他一些则需要你选择整句。

> [!TIP]
> 语音调整工具提供有关功能和优化的上下文信息。

## <a name="next-steps"></a>后续步骤
* [在 Azure 中创建语音资源](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure)
* [开始语音调整](https://speech.microsoft.com/app.html#/VoiceTuning)
* [语音合成标记语言 (SSML)](speech-synthesis-markup.md)
