---
title: 文本到语音转换服务
titleSuffix: Azure Cognitive Services
description: 语音服务中的文本到语音功能是一项功能, 可让你的应用程序、工具或设备将文本转换为自然用户喜欢的合成语音。 选择 "标准" 和 "神经" 语音, 或创建自己的产品或品牌独有的自定义声音。 75多种语言和区域设置中提供了 75 45 多项标准语音, 5 种神经声音提供4种语言和区域设置。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: erhopf
ms.openlocfilehash: 3d2c3e2bf693f763db13d90961a31e871aa25235
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68558893"
---
# <a name="what-is-text-to-speech"></a>什么是文本到语音转换？

来自 Azure Speech Services 的文本到语音转换是一项服务, 可让你的应用程序、工具或设备将文本转换为类似于人的合成语音。 选择 "标准" 和 "神经" 语音, 或创建自己的产品或品牌独有的自定义声音。 75多种语言和区域设置中提供了 75 45 多项标准语音, 5 种神经声音提供4种语言和区域设置。 有关完整列表, 请参阅[支持的语言](language-support.md#text-to-speech)。

文本到语音技术允许内容创建者以不同的方式与其用户交互。 通过向用户提供与 content 呼叫时交互的选项, 可以将文本转换为语音。 无论用户是否具有视觉障碍、学习障碍, 还是需要浏览信息, 同时进行文本到语音转换, 都可以改善现有体验。 文本到语音功能对于语音 bot 和虚拟助手也是一个重要的附加功能。


通过利用语音合成标记语言 (SSML) (一种基于 XML 的标记语言), 使用文本到语音服务的开发人员可以指定输入文本如何转换为合成语音。 通过 SSML, 可以调整音调、发音、语速、音量等。 有关详细信息, 请参阅[SSML](#speech-synthesis-markup-language-ssml)。

### <a name="standard-voices"></a>标准语音

标准语音是使用统计参数合成和/或串联合成技术创建的。 这些声音非常可识别, 而且声音自然。 您可以轻松地让您的应用程序在45多种语言中进行口述, 其中包含各种声音选项。 这些声音提供了较高的发音准确度, 包括对缩写、首字母缩写扩展、日期/时间解释、polyphones 等的支持。 通过允许用户与你的内容呼叫时交互, 使用标准语音来提高应用程序和服务的可访问性。

### <a name="neural-voices"></a>神经语音

神经语音使用深层神经网络来克服传统的文本到语音系统的限制, 使其与口述语言的压力和 intonation 的模式相匹配, 并在综合中使用语音转换为计算机语音。 标准文本到语音将诗体论分解为单独的语言分析和由独立模型控制的声音预测步骤, 这可能会导致 muffled 语音合成。 我们的神经功能同时诗体论预测和语音合成功能, 这会导致声音更为流畅和自然。

可以使用神经语音使与聊天机器人和虚拟助手的交流更加自然和专注、可将数字文本（如电子书）转换为有声读物以及可增强车载导航系统。 随着人为自然的诗体论和清晰 articulation 的单词, 神经语音在与 AI 系统交互时大大减少了倾听疲劳。

神经声音支持不同的样式, 如中性和文字。 例如, Jessa (en-us) 语音可以说 cheerfully, 这是针对温、快乐交谈进行优化的。 您可以使用[语音合成标记语言](speech-synthesis-markup.md)来调整语音输出, 如音频、音调和速度。 有关可用声音的完整列表, 请参阅[支持的语言](language-support.md#text-to-speech)。

若要了解有关神经声音优点的详细信息, 请参阅[Microsoft 的新神经文本到语音服务帮助计算机与人对话](https://azure.microsoft.com/blog/microsoft-s-new-neural-text-to-speech-service-helps-machines-speak-like-people/)。

### <a name="custom-voices"></a>自定义语音

语音自定义允许您为品牌创建可识别的一种类型的声音。 若要创建自定义语音字体, 请创建一个工作室记录, 并上传关联的脚本作为训练数据。 然后，此服务会根据你的录音创建唯一的语音模型。 您可以使用此自定义语音字体合成语音。 有关详细信息, 请参阅[自定义语音](how-to-customize-voice-font.md)。

## <a name="speech-synthesis-markup-language-ssml"></a>语音合成标记语言 (SSML)

语音合成标记语言 (SSML) 是一种基于 XML 的标记语言, 它允许开发人员使用文本到语音服务来指定如何将输入文本转换为合成语音。 与纯文本相比, SSML 允许开发人员精细调整文本到语音输出的音调、发音、语速、音量和更多内容。 正常标点, 如在一个句点后暂停, 或使用正确的 intonation, 当以问号结束的句子将自动处理。

发送到文本到语音服务的所有文本输入都必须构造为 SSML。 有关详细信息, 请参阅[语音合成标记语言](speech-synthesis-markup.md)。

### <a name="pricing-note"></a>定价说明

使用文本到语音转换服务时, 将为转换为语音的每个字符 (包括标点) 支付费用。 尽管 SSML 文档本身并不计费, 但用于调整文本转换为语音的方式的可选元素 (例如音素和螺距) 被计为可计费字符。 下面列出了可计费的内容:

* 传递给请求的 SSML 正文中的文本到语音服务的文本
* 采用 SSML 格式的请求正文的文本字段内的所有标记 ( `<speak>`和`<voice>`标记除外)
* 字母、标点、空格、制表符、标记和所有空格字符
* Unicode 中定义的每个码位

有关详细信息, 请参阅[定价](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)。

> [!IMPORTANT]
> 每个中文、日语和朝鲜语字符都作为两个字符进行计费。

## <a name="core-features"></a>核心功能

下表列出了文本到语音转换的核心功能:

| 使用案例 | SDK | REST |
|----------|-----|------|
| 将文本转换为语音。 | 是 | 是 |
| 上载用于语音调整的数据集。 | 否 | 是\* |
| 创建和管理语音字体模型。 | 否 | 是\* |
| 创建和管理语音字体部署。 | 否 | 是\* |
| 创建和管理语音字体测试。 | 否 | 是\* |
| 管理订阅。 | 否 | 是\* |

\**使用 cris.ai 终结点可以使用这些服务。请参阅[Swagger 引用](https://westus.cris.ai/swagger/ui/index)。这些自定义语音训练和管理 Api 实施限制, 将请求限制为每5秒25次, 而语音合成 API 本身实现允许每秒200请求为最高的限制。发生限制时, 将通过消息标头通知您。*

## <a name="get-started-with-text-to-speech"></a>文本到语音入门

我们提供了快速入门, 旨在让你在不到10分钟的时间内运行代码。 此表包含按语言组织的文本到语音快速入门列表。

### <a name="sdk-quickstarts"></a>SDK 快速入门

| 快速入门 (SDK) | 平台 | API 参考 |
|------------|----------|---------------|
| [C#, .NET Core](quickstart-text-to-speech-dotnetcore.md) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#, .NET Framework](quickstart-text-to-speech-dotnet-windows.md) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#, UWP](quickstart-text-to-speech-csharp-uwp.md) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#、Unity](quickstart-text-to-speech-csharp-unity.md) | Windows、Android | [Browse](https://aka.ms/csspeech/csharpref) |
| [C++](quickstart-text-to-speech-cpp-windows.md) | Windows | [Browse](https://aka.ms/csspeech/cppref) |
| [C++](quickstart-text-to-speech-cpp-linux.md) | Linux | [Browse](https://aka.ms/csspeech/cppref) |

### <a name="rest-quickstarts"></a>REST 快速入门

| 快速入门 (REST) | 平台 | API 参考 |
|------------|----------|---------------|
| [C#, .NET Core](quickstart-dotnet-text-to-speech.md) | Windows、macOS、Linux | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Node.js](quickstart-nodejs-text-to-speech.md) | Windows、macOS、Linux | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Python](quickstart-python-text-to-speech.md) | Windows、macOS、Linux | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |

## <a name="sample-code"></a>示例代码

GitHub 上提供了文本到语音的示例代码。 这些示例涵盖了最常用编程语言的文本到语音转换。

* [文本到语音转换示例 (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [文本转语音示例 (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="reference-docs"></a>参考文档

* [语音 SDK](speech-sdk-reference.md)
* [语音设备 SDK](speech-devices-sdk.md)
* [REST API：语音转文本](rest-speech-to-text.md)
* [REST API：文本转语音](rest-text-to-speech.md)
* [REST API：批量听录和自定义](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>后续步骤

* [获取免费语音服务订阅](get-started.md)
* [创建自定义语音字体](how-to-customize-voice-font.md)
