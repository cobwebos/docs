---
title: 演讲者识别概述-语音服务
titleSuffix: Azure Cognitive Services
description: 演讲者识别提供使用语音 biometry 根据其独特的语音特征来验证和识别扬声器的算法。 说话人识别用于回答“谁在说话？”的问题。 本文概述了演讲者识别服务的优势和功能。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: trbye
ms.custom: cog-serv-seo-aug-2020
keywords: 演讲者识别，语音 biometry
ms.openlocfilehash: 2c5b73b93c22ef27d7b68455f5e1e5108f25c984
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/02/2020
ms.locfileid: "89397253"
---
# <a name="what-is-speaker-recognition"></a>什么是说话人识别？

演讲者识别服务提供使用语音 biometry 根据其独特的语音特征来验证和识别扬声器的算法。 说话人识别用于回答“谁在说话？”的问题。 你为单个演讲者提供音频定型数据，这将基于演讲者语音的独特特征创建注册配置文件。 然后，您可以对此配置文件交叉检查音频语音样本，验证扬声器 (扬声器验证) ，还是对照一 *组* 已注册的扬声器配置文件交叉检查音频语音样本，以查看它是否与组 (发言人标识) 中的任何配置文件相匹配。 与此相反， [演讲者 Diarization](batch-transcription.md#speaker-separation-diarization) 在批处理操作中对音频段进行分组。

## <a name="speaker-verification"></a>说话人验证

说话人验证利用密码或自由格式的语音输入来精简验证已注册的发言人标识的过程。 它可用于验证个人是否有各种解决方案的安全、顺畅的客户参与，从呼叫中心的客户标识验证到无联系的设施访问。

### <a name="how-does-speaker-verification-work"></a>说话人验证是如何工作的？

:::image type="content" source="media/speaker-recognition/speaker-rec.png" alt-text="说话人验证流程图。":::

扬声器验证可以是文本相关的，也可以是与文本无关的。 **文本从属** 验证表示扬声器需要选择要在注册和验证阶段中使用的相同通行短语。 与**文本无关**的验证意味着演讲者可以在注册和验证短语中以日常语言说话。

对于 **文本从属** 验证，通过从一组预定义的短语中口述密码来注册演讲者的语音。 语音功能将从音频录音中提取，以形成唯一的语音签名，同时还会识别所选通行短语。 语音签名和通行短语一起用于验证扬声器。 

与**文本无关**的验证在注册期间或在要验证的音频示例中不会有什么限制，因为它仅将语音功能提取到分数相似性。 

这些 Api 并不用于确定音频是来自活动人员还是模拟/录制的发言人。 

## <a name="speaker-identification"></a>说话人识别

演讲者标识用于在一组已注册的发言人内确定未知扬声器的标识。 演讲者标识使你能够将语音特性应用到各个扬声器，并通过多个扬声器的方案解锁价值，如：

* 远程会议工作效率的支持解决方案 
* 构建多用户设备个性化

### <a name="how-does-speaker-identification-work"></a>演讲者识别如何工作？

发言人标识的注册与 **文本无关**，这意味着音频中的演讲者不会有任何限制。 与说话人验证类似，在注册阶段，录制扬声器的声音，并提取语音功能以形成唯一的语音签名。 在标识阶段，将输入语音样本与每个请求) 中指定的已注册语音样本 (最多50个指定列表进行比较。

## <a name="data-security-and-privacy"></a>数据安全和隐私

发言人注册数据存储在受保护的系统中，其中包括用于注册的语音音频和语音签名功能。 仅在升级算法时才使用用于注册的语音音频，需要重新提取功能。 该服务不会保留语音记录或在识别阶段发送到服务的已提取语音功能。 

您可以控制保留数据的时间长度。 可以通过 API 调用创建、更新和删除各个扬声器的注册数据。 删除订阅后，与该订阅关联的所有说话人注册数据也会一并删除。 

与所有认知服务资源一样，使用扬声器识别服务的开发人员必须了解 Microsoft 针对客户数据的策略。 你应确保已收到用户的相应权限，可以识别扬声器。 有关详细信息，请参阅 Microsoft 信任中心上的 [认知服务页](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/)   。 

## <a name="common-questions-and-solutions"></a>常见问题和解决方案

| 问题 | 解决方案 |
|---------|----------|
| 演讲者识别可以使用哪些方案？ | 呼叫中心客户验证，基于语音的患者签入，会议脚本，多用户设备个性化|
| 标识与验证之间有何区别？ | 标识是指检测一组扬声器中哪个成员正在说话的过程。 验证是确认扬声器与已知的或已 **注册** 的语音的操作。|
| 依赖文本和与文本无关的验证之间有何区别？ | 与文本相关的验证需要使用特定的传递短语进行注册和识别。 与文本无关的验证需要更长的语音示例进行注册，但可以说出任何内容，包括识别期间。|
| 支持哪些语言？ | 英语、法语、西班牙语、中文、德语、意大利语、日语和葡萄牙语 |
| 支持哪些 Azure 区域？ | 演讲者识别为预览版服务，目前仅在美国西部地区可用。|
| 支持哪些音频格式？ | Mono 16 位，16kHz PCM-编码的 WAV |
| **接受** 和 **拒绝** 响应不准确，如何调整阈值？ | 由于最佳阈值因情况而异，因此 API 决定是根据默认阈值 0.5 "接受" 还是 "拒绝"。 建议高级用户覆盖默认决策，并根据自己的方案对结果进行微调。 |
| 是否可以多次注册一个扬声器？ | 是的，对于文本相关验证，最多可注册50次扬声器。 对于与文本无关的验证或发言人标识，最多可注册300秒的音频。 |
| Azure 中存储了哪些数据？ | 注册音频存储在服务中，直到 [删除](speaker-recognition-basics.md#deleting-voice-profile-enrollments)语音配置文件。 不保留或存储识别音频示例。 |

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> * 完成演讲者识别 [基础知识文章](speaker-recognition-basics.md) ，了解可在应用程序中使用的常见设计模式。
> * 请参阅 [视频教程](https://azure.microsoft.com/resources/videos/speaker-recognition-text-independent-verification-developer-tutorial/) ，了解与文本无关的扬声器验证。
