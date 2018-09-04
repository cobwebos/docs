---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/27/2018
ms.author: wolfma
ms.openlocfilehash: a4accb0846ec9cf5efc5ca5aa00c731edd41d4d4
ms.sourcegitcommit: a1140e6b839ad79e454186ee95b01376233a1d1f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2018
ms.locfileid: "43143004"
---
<!-- N.B. no header, language-agnostic -->

Microsoft 认知服务[语音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) 提供了一种识别**语音中的意向**的方法，并且由认知服务[语言理解服务 (LUIS)](https://www.luis.ai/home) 提供支持。

1. 使用 LUIS 订阅密钥和[区域](~/articles/cognitive-services/speech-service/regions.md#regions-for-intent-recognition)作为参数创建语音工厂。 LUIS 订阅密钥在服务文档中称为**终结点密钥**。 不能使用 LUIS 创作密钥。 （请参见本部分中后面的注释。）

1. 从语音工厂获取意向识别器。 识别器可以使用设备的默认麦克风、音频流或者文件中的音频。

1. 获取基于 **AppId** 的语言理解模型。 添加所需的意向。 

1. 如果需要，可捆绑事件以进行异步操作。 然后，识别器将在其具有中间和最终结果（包括意向）时调用事件处理程序。 如果没有捆绑事件，应用程序只会收到最终听录结果。

1. 启动意向识别。 对于单次识别（例如命令或查询识别），请使用 `RecognizeAsync()` 方法。 此方法返回第一个已识别的话语。 对于长时间运行的识别，请使用 `StartContinuousRecognitionAsync()` 方法。 捆绑事件以获得异步识别结果。

有关使用语音 SDK 的意向识别方案，请参阅以下代码片段。 使用自己的 LUIS 订阅密钥（终结点密钥）、订阅的[区域](~/articles/cognitive-services/speech-service/regions.md#regions-for-intent-recognition)以及意向模型的 **AppId** 替换示例中的值。

> [!NOTE]
> 与语音 SDK 支持的其他服务不同，意向识别需要特定的订阅密钥（LUIS 终结点密钥）。 有关意向识别技术的信息，请参阅 [LUIS 网站](https://www.luis.ai)。 有关如何获取**终结点密钥**的信息，请参阅[创建 LUIS 终结点密钥](https://docs.microsoft.com/azure/cognitive-services/LUIS/luis-how-to-azure-subscription#create-luis-endpoint-key)。
