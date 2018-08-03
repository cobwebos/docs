---
title: 有关 Azure 中语音转文本服务的常见问题 | Microsoft Docs
description: 以下是有关语音转文本的最常见问题的解答。
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 06/11/2018
ms.author: panosper
ms.openlocfilehash: 4a29435c0ace79fc3a5d3a5a42a0e91bdbc8da5e
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2018
ms.locfileid: "37082818"
---
# <a name="text-to-speech-frequently-asked-questions"></a>文本转语音常见问题

如果未在本常见问题解答中找到答案，请尝试在 [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) 和 [UserVoice](https://cognitive.uservoice.com/) 的自定义语音服务社区中提问

## <a name="general"></a>常规

问：标准语音模型和自定义语音模型之间的区别是什么？

答：标准语音模型（又名 语音字体）已使用 Microsoft 拥有的数据定型，并且已部署在云中。 自定义语音模型允许用户调整平均模型并根据说话者的语音风格传输音色和表达方式，或者基于用户准备的培训数据定型全新模型。 如今，越来越多的客户希望为他们的机器人提供独一无二的品牌语音。 自定义语音生成平台是正确的选择。

问：如果想要使用标准语音模型，应从何处开始？

答：通过 HTTP 请求可以使用超过 45 种语言的 80 多种标准语音模型。 首先，需要获取[订阅密钥](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/get-started)。 要对预先部署的语音模型进行 REST 调用，请参阅[此处的详细信息](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/rest-apis#text-to-speech)。

问：如果想使用自定义的语音模型，API 是否与标准语音相同？

答：创建和部署自定义语音模型后，你将获得模型的唯一终结点。 需要在 HTTP 请求中指定终结点，以使用语音在应用中说话。 通过 REST API 为文本转语音服务提供的相同功能也可用于自定义终结点。 了解如何[创建和使用自定义终结点](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/how-to-customize-voice-font#create-and-use-a-custom-endpoint)。

问：要自行创建自定义语音模型，是否需要准备培训数据？

答：需要自己准备培训数据。 创建自定义的语音模型需要一组语音数据。 其中包括一系列语音录制音频文件，以及每个音频文件的听录文本文件。 数字语音的结果主要依赖于培训数据的质量。 若要生成优质 TTS 语音，必须确保录音是在安静的房间使用高品质立式麦克风完成的。 一致的音量、语速、语调，甚至语言表达方式的一致性对于生成优质数字语音来说至关重要。 强烈建议在录音室录制语音。
目前我们不提供在线录音支持或任何录音室建议。 有关格式要求，请参阅[如何准备录音和脚本](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/how-to-customize-voice-font#prepare-recordings-and-transcripts)
 
问：应该使用哪些脚本来录制自定义语音培训的语音数据？ 

答：我们不限制用于语音录制的脚本。 你可使用自己的脚本来录制语音。 只需确保语音数据有足够的语音覆盖率。 若要培训自定义语音，可从少量语音数据开始，可以是 50 个不同的句子（语音大约有 3-5 分钟）。 提供的数据越多，语音听起来就越自然。 提供 2000 多个句子（语音大约有 3-4 小时）的录音时，就可开始培训完整的语音字体了。 若要获取高质量的完整语音，需要准备 6000 多个句子的录音（语音大约有 8-10 小时）。  
我们提供可帮助你准备录音脚本的其他服务。 若要咨询，请联系[自定义语音客户支持人员](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation)。

问：如果所需并发高于默认值或门户中提供的值，应该怎么办？

**解答**：以 20 个并发请求为增量纵向扩展模型。 有关更高缩放的咨询，请联系[自定义语音客户支持人员](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation)。

**问题**：是否可以下载模型并在本地运行？

**解答**：无法下载模型并在本地执行。

## <a name="next-steps"></a>后续步骤

* [故障排除](troubleshooting.md)
* [发行说明](releasenotes.md)
