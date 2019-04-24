---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
origin.date: 09/24/2018
ms.date: 04/01/2019
ms.author: v-biyu
ms.openlocfilehash: ab4795325f733d15403b53f027daa4fb8c824cc6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60531028"
---
<!-- N.B. no header, language-agnostic -->

Microsoft 认知服务[语音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) 提供在应用程序中使用“语音翻译”的最简单方法。
该 SDK 提供该服务的完整功能。 执行语音翻译的基本过程包括以下步骤：

1. 创建语音翻译配置，并提供语音服务订阅密钥（或授权令牌）及[区域](~/articles/cognitive-services/speech-service/regions.md)作为参数。 根据需要更改配置。 例如，可以配置源和目标翻译语言，以及指定需要文本输出还是语音输出。

1. 从语音翻译配置创建翻译识别器。 若要从除默认麦克风之外的来源（例如，音频流或音频文件）识别语音，请提供音频配置。

1. 如果需要，可捆绑事件以进行异步操作。 然后，识别器在具有中间结果和最终结果以及可选音频输出的合成事件时调用事件处理程序。 否则，应用程序将只收到最终听录结果。

1. 开始识别。 对于单次翻译，请使用 `RecognizeOnceAsync()` 方法，该方法返回第一个已识别的话语。 对于长时间运行的翻译，请使用 `StartContinuousRecognitionAsync()` 方法，并捆绑事件以获得异步识别结果。

有关使用语音 SDK 的语音翻译方案，请参阅以下代码片段。

[!INCLUDE [Get a subscription key](cognitive-services-speech-service-get-subscription-key.md)]
