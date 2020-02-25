---
title: 关于对话脚本（预览版）-语音服务
titleSuffix: Azure Cognitive Services
description: 对话脚本是一种语音到文本解决方案，它将语音识别、发言人标识和句子归属组合到每个扬声器（也称为 diarization），以提供任何会话的实时和/或异步方式。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: dapine
ms.openlocfilehash: 8e36a5c4d9ce2bc38565d6c9c256ed2cc7efe357
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/22/2020
ms.locfileid: "77561062"
---
# <a name="about-conversation-transcription-preview"></a>关于对话脚本（预览）

对话脚本是一种[语音到文本](speech-to-text.md)解决方案，它将语音识别、发言人标识和句子归属组合到每个扬声器（也称为_diarization_），以提供任何会话的实时和/或异步方式。 对话脚本将会话中的发言人区分开来，以确定谁说什么和何时，并使开发人员能够轻松地将语音到文本添加到执行多发言人 diarization 的应用程序。

## <a name="key-features"></a>主要功能

- **时间戳**-每个扬声器查询文本都有一个时间戳，以便您可以轻松地找到短语。
- **可读的脚本**-脚本自动添加格式设置和标点符号，以确保文本与所说的内容完全匹配。
- **用户配置文件**-通过收集用户语音示例并将其发送到签名生成来生成用户配置文件。
- **演讲者标识**-使用用户配置文件识别扬声器，并为每个扬声器指定_扬声器标识符_。
- **多扬声器 diarization** -通过使用每个扬声器标识符综合音频流来确定其含义。
- **实时**脚本–提供有关会话发生的时间和时间的实时脚本。
- **异步**脚本–通过使用多通道音频流提供更高准确性的脚本。

> [!NOTE]
> 虽然会话脚本未对房间中的扬声器数量施加限制，但对于每个会话，它已针对2-10 扬声器进行了优化。

## <a name="use-cases"></a>用例

### <a name="inclusive-meetings"></a>非独占会议

若要为每个人（例如失聪和听力障碍的参与者）提供会议，请务必实时进行脚本。 实时模式下的对话对话采用会议音频，并确定谁在说什么，这允许所有会议参与者在不延迟的情况下执行脚本并参与会议。

### <a name="improved-efficiency"></a>提高效率

会议参与者可以将精力集中在会议上，并对对话进行记录。 参与者随时可以参加会议，并通过使用脚本而不是记笔记并在会议期间可能会遗漏一些内容来快速跟进后续步骤。

## <a name="how-it-works"></a>工作原理

这是会话脚本工作原理的简要概述。

![导入对话对话](media/scenarios/conversation-transcription-service.png)

## <a name="expected-inputs"></a>预期输入

- **多通道音频流**–有关规范和设计的详细信息，请参阅[MICROSOFT Speech Device SDK 麦克风](https://aka.ms/cts/microphone)。 若要了解详细信息或购买开发工具包，请参阅[获取 Microsoft Speech 设备 SDK](https://aka.ms/cts/getsdk)。
- **用户语音示例**–对话要求在会话前需要用户配置文件。 需要从每个用户那里收集音频记录，然后将录制发送到[签名生成服务](https://aka.ms/cts/signaturegenservice)，以验证音频和生成用户配置文件。

## <a name="real-time-vs-asynchronous"></a>实时与异步

会话脚本提供了三种模式：

### <a name="real-time"></a>实时

实时处理音频数据以返回演讲者标识符 + 抄本。 如果脚本解决方案要求向对话参与者提供正在进行的会话的实时记录视图，请选择此模式。 例如，如果生成应用程序以使会议更易于访问，听力障碍参与者的理想使用方案是实时脚本。

### <a name="asynchronous"></a>异步

音频数据经过批处理处理，以返回演讲者标识符和脚本。 如果你的脚本解决方案要求提供更高的准确性，而无需实时记录视图，请选择此模式。 例如，如果你想要构建一个应用程序以允许会议参与者轻松地捕获错过的会议，则使用异步脚本模式来获取高准确性的脚本结果。

### <a name="real-time-plus-asynchronous"></a>实时加异步

实时处理音频数据以返回演讲者标识符 + 抄本，另外，还会创建一个请求，以通过异步处理获取高准确性脚本。 如果你的应用程序需要实时脚本，但还需要更高的准确性，以便在会话或会议发生后使用，请选择此模式。

## <a name="language-support"></a>语言支持

目前，对话脚本支持以下区域中的 "en-us" 和 "zh-chs"： *centralus*和 *eastasia*。 如果需要其他区域设置支持，请联系[对话脚本功能](mailto:CTSFeatureCrew@microsoft.com)人员。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [实时转录对话](how-to-use-conversation-transcription-service.md)
