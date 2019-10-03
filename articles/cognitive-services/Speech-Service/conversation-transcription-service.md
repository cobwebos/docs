---
title: 对话脚本-语音服务
titleSuffix: Azure Cognitive Services
description: 对话脚本是语音服务的一项高级功能, 它结合了实时语音识别、发言人识别和 diarization。 对话脚本非常适合转录人员会议, 能够区分发言人, 使用户能够了解谁说什么和何时, 使参与者能够专注于会议并迅速跟进后续步骤。 此功能还改进了可访问性。 在脚本中, 你可以积极地吸引参与者的听力障碍。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: acbf82335523ee226f4b899180f0f22cb94cca5e
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562887"
---
# <a name="what-is-conversation-transcription"></a>什么是对话脚本？

对话脚本是语音服务的一项高级功能, 它结合了实时语音识别、发言人识别和 diarization。 对话脚本非常适合转录人员会议, 能够区分发言人, 使用户能够了解谁说什么和何时, 使参与者能够专注于会议并迅速跟进后续步骤。 此功能还改进了可访问性。 在脚本中, 你可以积极地吸引参与者的听力障碍。   

会话脚本通过可自定义的语音模型提供准确的识别功能, 你可以对其进行定制以了解行业和公司特定的词汇。 此外, 还可以将对话脚本与语音设备 SDK 配对, 以优化多麦克风设备的体验。

>[!NOTE]
> 目前, 我们建议将会话脚本用于小型会议。 如果你想要扩大大规模会议的对话方式, 请联系我们。

此图说明了与会话脚本结合使用的硬件、软件和服务。

![导入对话对话](media/scenarios/conversation-transcription-service.png)

>[!IMPORTANT]
> 需要具有特定几何配置的循环7麦克风阵列。 有关规范和设计的详细信息, 请参阅[Microsoft Speech DEVICE SDK 麦克风](https://aka.ms/cts/microphone)。 若要了解详细信息或购买开发工具包, 请参阅[获取 Microsoft Speech 设备 SDK](https://aka.ms/cts/getsdk)。

## <a name="get-started-with-conversation-transcription"></a>会话脚本入门

开始使用会话脚本需要执行三个步骤。

1. 收集用户的语音样本。
2. 使用用户语音示例生成用户配置文件
3. 使用 Speech SDK 确定用户 (发言人) 和转录 Speech

## <a name="collect-user-voice-samples"></a>收集用户语音示例

第一步是从每个用户那里收集音频记录。 用户语音应记录在无背景噪音的静默环境中。 每个音频采样的建议长度介于30秒到2分钟之间。 更长的音频示例会使识别扬声器时的准确性更高。 音频必须是带有 16 KHz 速率的单声道通道。

除前述指导外, 如何录制和存储音频取决于你--建议使用安全数据库。 在下一部分中, 我们将回顾如何使用此音频来生成用户配置文件, 这些配置文件与语音 SDK 一起用于识别扬声器。

## <a name="generate-user-profiles"></a>生成用户配置文件

接下来, 需要将收集的音频录制发送到签名生成服务, 以验证音频和生成用户配置文件。 [签名生成服务](https://aka.ms/cts/signaturegenservice)是一组 REST api, 可用于生成和检索用户配置文件。

若要创建用户配置文件, 你将需要使用`GenerateVoiceSignature` API。 提供了规范详细信息和示例代码:

> [!NOTE]
> 会话脚本目前在以下区域的 "en-us" 和 "zh-chs" 中提供: `centralus`和。 `eastasia`

* [REST 规范](https://aka.ms/cts/signaturegenservice)
* [如何使用会话脚本](https://aka.ms/cts/howto)

## <a name="transcribe-and-identify-speakers"></a>转录和识别扬声器

对话脚本需要多通道音频流和用户配置文件作为输入来生成转录并识别扬声器。 使用语音设备 SDK 将音频和用户配置文件数据发送到会话脚本服务。 如前文所述, 需要一个圆形七个麦克风阵列, 而语音设备 SDK 则需要使用对话脚本。

>[!NOTE]
> 有关规范和设计的详细信息, 请参阅[Microsoft Speech DEVICE SDK 麦克风](https://aka.ms/cts/microphone)。 若要了解详细信息或购买开发工具包, 请参阅[获取 Microsoft Speech 设备 SDK](https://aka.ms/cts/getsdk)。

若要了解如何将会话脚本用于语音设备 SDK, 请参阅[如何使用会话](https://aka.ms/cts/howto)脚本。


## <a name="quick-start-with-a-sample-app"></a>使用示例应用快速入门

Microsoft Speech Device SDK 提供了一个快速入门示例应用, 适用于所有与设备相关的示例。 会话脚本就是其中之一。 可以在[语音设备 SDK android 快速入门](https://aka.ms/sdsdk-quickstart)中找到它, 其中包含示例应用及其源代码供你参考。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解有关语音设备 SDK 的详细信息](speech-devices-sdk.md)
