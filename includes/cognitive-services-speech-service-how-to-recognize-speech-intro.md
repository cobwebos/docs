---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/27/2018
ms.author: wolfma
ms.openlocfilehash: 1103e5a217ca4972cc1c983a7ad0d07b0797e9e9
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2018
ms.locfileid: "43129308"
---
<!-- N.B. no header, no intents here, language-agnostic -->

认知服务[语音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) 提供在具有功能完整的应用程序中使用“语音转文本”的最简单方法。

1. 创建语音工厂，并提供语音服务订阅密钥（或授权令牌）及[区域](~/articles/cognitive-services/speech-service/regions.md)作为参数。 还可以提供自定义终结点以指定非标准服务终结点。

1. 从语音工厂获取语音识别器。 可以配置输入语言和输出格式。 识别器可以使用设备的默认麦克风、音频流或者文件中的音频。

1. 如果需要，可捆绑事件以进行异步操作。 然后，识别器将在其具有中间和最终结果时调用事件处理程序。 否则，应用程序将只收到最终听录结果。

1. 开始识别。 对于单次识别（例如命令或查询识别），请使用 `RecognizeAsync()` 方法。 此方法返回第一个已识别的话语。 对于长时间运行的识别（例如听录），请使用 `StartContinuousRecognitionAsync()` 方法。 捆绑事件以获得异步识别结果。

有关使用语音 SDK 的语音识别方案，请参阅以下代码片段。

[!INCLUDE [Get a subscription key](cognitive-services-speech-service-get-subscription-key.md)]
