---
title: 文本转语音 - 语音服务
titleSuffix: Azure Cognitive Services
description: 语音服务中的文本到语音功能使您的应用程序、工具或设备可以将文本转换为类似于用户的合成语音。 选择 "预设声音" 或创建自己的自定义语音。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: erhopf
ms.openlocfilehash: 0d233f63879326f05cafb873d2a0243543b00c6b
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075707"
---
# <a name="what-is-text-to-speech"></a>什么是文本转语音？

Azure 语音服务中的文本转语音服务可让应用程序、工具或设备将文本转换为类似于人类的自然合成语音。 从标准和神经语音中选择，或创建自己产品或品牌特有的自定义语音。 75 多种标准语音可在 45 种以上的语言和区域设置中使用，5 种神经语音可在 4 种语言和区域设置中使用。 有关完整列表，请参阅[支持的语言](language-support.md#text-to-speech)。

内容创作者可以借助文本转语音技术通过不同的方式来与其用户交互。 文本转语音通过为用户提供一个以听觉方式与内容交互的选项来改善辅助功能。 无论用户是否存在视觉缺陷、学习障碍，还是在开车时需要导航信息，文本转语音都可以改善现有的体验。 文本到语音功能也是语音 bot 和语音助手的重要附加项。

利用语音合成标记语言 (SSML)（基于 XML 的标记语言），使用文本转语音的开发人员可以指定如何将输入文本转换为合成语音。 通过 SSML 可以调整音节、发音、语速和音量等。 有关详细信息，请参阅 [SSML](#speech-synthesis-markup-language-ssml)。

### <a name="standard-voices"></a>标准语音

标准语音是使用统计参数合成和/或串联合成技术创建的。 这些语音的辨识度很高，且听起来非常自然。 你可以轻松地让应用程序使用多种语音选项以 45 种以上的语言讲述。 这些声音提供较高的发音准确度，支持缩写、缩略词扩展、日期/时间解释、多音字等。 使用标准语音可让用户以听觉方式来与内容交互，从而改善应用程序和服务的辅助功能。

### <a name="neural-voices"></a>神经语音

神经语音使用深度神经网络克服传统的文本转语音系统的限制，可以匹配口语中的重音和语调模式，并可将语音单位合成为计算机语音。 标准的文本转语音将韵律分解为由独立模型控制的分立语言分析和声学预测步骤，使语音合成变得含混不清。 我们的神经语音功能以同步方式执行韵律预测和语音合成，使语音更流畅且自然。

可以使用神经语音与聊天机器人和语音助手交互，使其更加自然和吸引人，将电子书等数字文本转换为 audiobooks 并增强汽车内导航系统。 神经语音可以生成类人的自然韵律和清晰的字词发音，当你在与 AI 系统交互时，它可以显著减轻听力疲劳。

神经语音支持不同的风格，如中性和快乐风格。 例如，Jessa (en-US) 语音风格比较快活，它已针对亲密快乐的交谈进行优化。 可以使用[语音合成标记语言](speech-synthesis-markup.md)调整语音输出，例如音调、音节和语速。 有关可用语音的完整列表，请参阅[支持的语言](language-support.md#text-to-speech)。

若要详细了解神经语音的优势，请参阅 [Microsoft 的新文本转语音神经服务可帮助机器像人类一样讲话](https://azure.microsoft.com/blog/microsoft-s-new-neural-text-to-speech-service-helps-machines-speak-like-people/)。

### <a name="custom-voices"></a>自定义语音

使用语音自定义可为品牌创建可辨识的独特语音。 若要创建自定义语音字体，请先进行录音，并将关联的脚本以训练数据的形式上传。 然后，此服务会根据你的录音创建唯一的语音模型。 可以使用此自定义语音字体来合成语音。 有关详细信息，请参阅[自定义语音](how-to-customize-voice-font.md)。

## <a name="speech-synthesis-markup-language-ssml"></a>语音合成标记语言 (SSML)

语音合成标记语言 (SSML) 是一种基于 XML 的标记语言，可让开发人员指定如何使用文本转语音服务将输入文本转换为合成语音。 与纯文本相比，SSML 可让开发人员微调音节、发音、语速、音量以及文本转语音输出的其他属性。 SSML 可自动处理正常的停顿（例如，在句号后面暂停片刻），或者在以问号结尾的句子中使用正确的音调。

发送到文本转语音服务的所有文本输入必须结构化为 SSML 。 有关详细信息，请参阅[语音合成标记语言](speech-synthesis-markup.md)。

### <a name="pricing-note"></a>定价说明

使用文本转语音服务时，需按照转换为语音的每个字符（包括标点）付费。 尽管 SSML 文档本身不计费，但用于调整文本转语音方式的可选元素（例如音素和音节）将算作计费字符。 下面列出了计费的内容：

- 在请求的 SSML 正文中传递给文本转语音服务的文本
- 请求正文的文本字段中所有 SSML 格式的标记，`<speak>` 和 `<voice>` 标记除外
- 字母、标点、空格、制表符、标记和所有空白字符
- Unicode 中定义的每个码位

有关详细信息，请参阅[定价](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)。

> [!IMPORTANT]
> 每个中文、日语和韩语字符算作两个计费字符。

## <a name="core-features"></a>核心功能

下表列出了文本转语音的核心功能：

| 使用案例                                  | SDK 中 IsInRole 中的声明 | REST  |
| ----------------------------------------- | --- | ----- |
| 将文本转换为语音。                   | 是 | 是   |
| 上传数据集以进行语音适应。     | 否  | 是\* |
| 创建和管理语音字体模型。      | 否  | 是\* |
| 创建和管理语音字体部署。 | 否  | 是\* |
| 创建和管理语音字体测试。       | 否  | 是\* |
| 管理订阅。                     | 否  | 是\* |

\*_使用 cris.ai 终结点提供这些服务。请参阅[Swagger 引用](https://westus.cris.ai/swagger/ui/index)。这些自定义语音训练和管理 Api 实施限制，将请求限制为每5秒25次，而语音合成 API 本身实现允许每秒200请求为最高的限制。发生限制时，将通过消息标头通知您。_

## <a name="get-started-with-text-to-speech"></a>文本转语音入门

我们专门提供了快速入门来帮助你在 10 分钟内运行代码。 下表按语言列出了文本转语音快速入门。

### <a name="sdk-quickstarts"></a>SDK 快速入门

| 快速入门 (SDK) | 平台 | API 参考 |
| ---------------- | -------- | ------------- |
| [C#、.NET Core](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-csharp&tabs=dotnetcore)  | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#、.NET Framework](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-csharp&tabs=dotnet) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#、UWP](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-csharp&tabs=uwp) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#、Unity](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-csharp&tabs=unity) | Windows、Android | [Browse](https://aka.ms/csspeech/csharpref) |
| [C++](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-cpp&tabs=windows) | Windows | [Browse](https://aka.ms/csspeech/cppref) |
| [C++](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-cpp&tabs=linux) | Linux | [Browse](https://aka.ms/csspeech/cppref) |
| [Java](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-java&tabs=jre) | Windows、Linux、macOS | [Browse](https://aka.ms/csspeech/javaref) |
| [Java](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-java&tabs=android) | Android | [Browse](https://aka.ms/csspeech/javaref) |
| [Objective-C](~/articles/cognitive-services/speech-service/quickstarts/text-to-speech-langs/objectivec-macos.md) | macOS | [Browse](https://aka.ms/csspeech/objectivecref) |
| [Objective-C](~/articles/cognitive-services/speech-service/quickstarts/text-to-speech-langs/objectivec-ios.md) | iOS | [Browse](https://aka.ms/csspeech/objectivecref) |
| 反应（~/articles/cognitive-services/speech-service/quickstarts/text-to-speech-langs/swift-macos.md | macOS | [Browse](https://aka.ms/csspeech/objectivecref) |
| [Swift](~/articles/cognitive-services/speech-service/quickstarts/text-to-speech-langs/swift-ios.md) | iOS | [Browse](https://aka.ms/csspeech/objectivecref) |
| [Python](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-python) | Window、Linux、macOS | [Browse](https://aka.ms/csspeech/pythonref) |

### <a name="rest-quickstarts"></a>REST 快速入门

| 快速入门 (REST) | 平台 | API 参考 |
| ----------------- | -------- | ------------- |
| [C#、.NET Core](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-csharp) | Windows、macOS、Linux | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Node.js](quickstart-nodejs-text-to-speech.md) | Windows、macOS、Linux | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Python](quickstart-python-text-to-speech.md) | Windows、macOS、Linux | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |

## <a name="sample-code"></a>代码示例

GitHub 上提供了文本转语音的示例代码。 这些示例涵盖了最流行编程语言的文本转语音转换。

- [文本转语音示例 (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [文本转语音示例 (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="reference-docs"></a>参考文档

- [语音 SDK](speech-sdk-reference.md)
- [语音设备 SDK](speech-devices-sdk.md)
- [REST API：语音到文本](rest-speech-to-text.md)
- [REST API：文本到语音转换](rest-text-to-speech.md)
- [REST API：批处理脚本和自定义](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>后续步骤

- [获取免费语音服务订阅](get-started.md)
- [创建自定义语音字体](how-to-customize-voice-font.md)
