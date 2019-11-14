---
title: 有关 Azure 中的文本转语音服务的常见问题
titleSuffix: Azure Cognitive Services
description: 获取有关文本转语音服务的常见问题的解答。
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: panosper
ms.openlocfilehash: 0ec9b9729ea93a0685179559bf58392944f37ba6
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075838"
---
# <a name="text-to-speech-frequently-asked-questions"></a>文本转语音常见问题

如果在本常见问题解答中找不到你的问题的解答，请检查[其他支持选项](support.md)。

## <a name="general"></a>一般信息

问：标准语音模型和自定义语音模型之间的区别是什么？

答：标准语音模型（也称为语音字体）使用 Microsoft 拥有的数据进行定型，已部署在云中。 可以使用自定义语音模型调整平均模型并转换说话者语音风格的音色和表达，或者基于用户准备的定型数据对完整的新模型进行定型。 如今，越来越多的客户希望为他们的机器人提供独一无二的品牌语音。 自定义语音生成平台是正确的选择。

问：如果想要使用标准语音模型，应从何处开始？

答：通过 HTTP 请求可以使用超过 45 种语言的 80 多种标准语音模型。 首先，获取[订阅密钥](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started)。 若要对预部署的语音模型进行 REST 调用，请参阅 [REST API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)。

问：如果想使用自定义的语音模型，API 是否与用于标准语音的 API 相同？

答：创建和部署自定义语音模型时，你会获得模型的唯一终结点。 若要使用语音在应用中说话，必须在 HTTP 请求中指定该终结点。 在 REST API 中为文本转语音服务提供的相同功能可用于自定义终结点。 了解如何[创建和使用自定义终结点](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-customize-voice-font#create-and-use-a-custom-voice-endpoint)。

问：要自行创建自定义语音模型，是否需要准备定型数据？

答：是的，必须自己为自定义语音模型准备定型数据。

创建自定义的语音模型需要一组语音数据。 其中包括一系列语音录制音频文件，以及每个音频文件的听录文本文件。 数字语音的结果主要依赖于定型数据的质量。 若要生成优质文本转语音的语音，必须确保录音是在安静的房间使用高品质立式麦克风完成的。 一致的音量、语速和语调，甚至语言表达方式的一致性对于生成优质数字语音来说至关重要。 强烈建议在录制室中录制语音。

当前我们不提供在线录音支持或任何录音室建议。 有关格式要求，请参阅[如何准备录音和脚本](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice-create-voice)。

问：应该使用哪些脚本来录制用于自定义语音定型的语音数据？

答：我们不限制用于语音录制的脚本。 你可使用自己的脚本来录制语音。 只需确保语音数据有足够的语音覆盖率。 若要对自定义语音进行定型，可从少量语音数据开始，可以是 50 个不同的句子（语音大约有 3-5 分钟）。 提供的数据越多，语音听起来就越自然。 提供 2000 多个句子（语音大约有 3-4 小时）的录音时，就可开始对完整语音字体进行定型。 若要获取高质量的完整语音，需要准备 6000 多个句子的录音（语音大约有 8-10 小时）。

我们提供可帮助你准备录音脚本的其他服务。 若要咨询，请联系[自定义语音客户支持人员](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation)。

问：如果所需并发高于默认值或门户中提供的值，应该怎么办？

答：以 20 个并发请求为增量纵向扩展模型。 有关更高缩放的咨询，请联系[自定义语音客户支持人员](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation)。

问：是否可以下载模型并在本地运行？

答：无法下载模型并在本地执行。

问：我的请求是否受到请求？

答：REST API 将请求限制为每 5 秒 25 个。 可以在我们的[文本转语音](text-to-speech.md)页面中找到详细信息。

## <a name="next-steps"></a>后续步骤

- [故障排除](troubleshooting.md)
- [发行说明](releasenotes.md)
