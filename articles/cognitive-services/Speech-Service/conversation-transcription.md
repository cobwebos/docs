---
title: 对话转录（预览） - 语音服务
titleSuffix: Azure Cognitive Services
description: 对话转录是会议的解决方案，它结合了识别、演讲者 ID 和二分化，以提供任何会话的转录。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: trbye
ms.openlocfilehash: dcc7721aec067c4de309e3fdd926245a9d240f0d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402511"
---
# <a name="what-is-conversation-transcription-in-meetings-preview"></a>什么是会议中的对话转录（预览）？

对话转录是一种[语音转文本](speech-to-text.md)解决方案，它结合了语音识别、扬声器识别和句子归因到每个演讲者（也称为_二分化_），以提供任何会话的实时和/或异步转录。 对话转录在对话中区分演讲者，以确定谁说了什么和什么时候，并且开发人员可以轻松地将语音到文本添加到执行多扬声器二化的应用程序。

## <a name="key-features"></a>主要功能

- **时间戳**- 每个演讲者的话语都有一个时间戳，这样你可以很容易地找到什么时候说一个短语。
- **可读抄本**- 抄本会自动添加格式和标点符号，以确保文本与所说的内容紧密匹配。
- **用户配置文件**- 用户配置文件通过收集用户语音样本并将其发送到签名生成来生成。
- **扬声器标识**- 使用用户配置文件标识扬声器，并为每个扬声器分配_一个扬声器标识符_。
- **多扬声器二分化**- 通过将音频流与每个扬声器标识符合成来确定谁说了什么。
- **实时转录**– 提供谁在说话的内容和时间进行对话的实时记录。
- **异步转录**– 通过使用多声道音频流提供更高精度的转录。

> [!NOTE]
> 尽管对话转录不会限制室内的扬声器数量，但它针对每会话 2-10 个扬声器进行了优化。

## <a name="use-cases"></a>用例

### <a name="inclusive-meetings"></a>包容性会议

为了使会议包容每个人，如失聪和听力障碍的参与者，实时进行转录非常重要。 实时模式下的对话转录会获取会议音频并确定谁在说什么，允许所有会议参与者遵循成绩单并毫不拖延地参加会议。

### <a name="improved-efficiency"></a>提高效率

会议参与者可以集中讨论会议，并将笔记留给对话转录。 参与者可以积极参与会议，并快速跟进后续步骤，使用成绩单，而不是做笔记，并可能在会议期间遗漏某些内容。

## <a name="how-it-works"></a>工作原理

这是对话转录工作原理的高级概述。

![导入对话听录示意图](media/scenarios/conversation-transcription-service.png)

## <a name="expected-inputs"></a>预期输入

- **多声道音频流**– 有关规格和设计详细信息，请参阅[微软语音设备 SDK 麦克风](https://aka.ms/cts/microphone)。 若要详细了解或购买开发工具包，请参阅[获取 Microsoft 语音设备 SDK](https://aka.ms/cts/getsdk)。
- **用户语音示例**– 对话转录需要在对话之前提供用户配置文件。 您需要从每个用户收集音频录制，然后将录音发送到[签名生成服务](https://aka.ms/cts/signaturegenservice)以验证音频并生成用户配置文件。

## <a name="real-time-vs-asynchronous"></a>实时与异步

对话转录提供三种转录模式：

### <a name="real-time"></a>实时

音频数据实时处理，以返回扬声器标识符和脚本。 如果您的转录解决方案要求是向对话参与者提供其正在进行的对话的实时记录视图，请选择此模式。 例如，构建应用程序使会议更容易被聋哑和听力障碍的参与者更容易访问，是实时转录的理想用例。

### <a name="asynchronous"></a>异步

音频数据被批处理为返回扬声器标识符和脚本。 如果您的转录解决方案要求提供更高的精度，而无需实时记录视图，请选择此模式。 例如，如果要构建应用程序以允许会议参与者轻松赶上错过的会议，请使用异步转录模式获取高精度转录结果。

### <a name="real-time-plus-asynchronous"></a>实时加异步

音频数据进行实时处理，以返回扬声器标识符 + 脚本，此外，还会创建一个请求，以便通过异步处理获得高精度脚本。 如果应用程序需要实时转录，但还需要更高的精度转录，请在对话或会议发生后使用，请选择此模式。

## <a name="language-support"></a>语言支持

目前，对话转录支持以下区域的"美国"和"zh-CN"： *中亚*和 *东亚*。 如果您需要其他区域设置支持，请联系[对话转录功能组](mailto:CTSFeatureCrew@microsoft.com)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [实时转录对话](how-to-use-conversation-transcription-service.md)
