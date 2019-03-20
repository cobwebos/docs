---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 09/24/2018
ms.author: wolfma
ms.openlocfilehash: 80bf9247bbb07fa61b7153e321b1991b82a9d616
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/14/2019
ms.locfileid: "58114927"
---
<!-- N.B. no header, language-agnostic -->

Microsoft 认知服务[语音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) 提供了一种识别**语音中的意向**的方法，并且由认知服务[语言理解服务 (LUIS)](https://www.luis.ai/home) 提供支持。

1. 使用 LUIS 订阅密钥和[区域](~/articles/cognitive-services/speech-service/regions.md#intent-recognition)作为参数创建语音配置。 LUIS 订阅密钥在服务文档中称为**终结点密钥**。 不能使用 LUIS 创作密钥。 （请参见本部分中后面的注释。）

1. 根据语音配置创建意向识别器。 若要从除默认麦克风之外的来源（例如，音频流或音频文件）识别语音，请提供音频配置。

1. 获取基于 **AppId** 的语言理解模型。 添加所需的意向。

1. 如果需要，可捆绑事件以进行异步操作。 然后，识别器将在其具有中间和最终结果（包括意向）时调用事件处理程序。 如果没有捆绑事件，应用程序只会收到最终听录结果。

1. 启动意向识别。 对于单次识别（例如命令或查询识别），请使用 `RecognizeOnceAsync()` 方法。 此方法返回第一个已识别的话语。 对于长时间运行的识别，请使用 `StartContinuousRecognitionAsync()` 方法。 捆绑事件以获得异步识别结果。

有关使用语音 SDK 的意向识别方案，请参阅以下代码片段。 使用自己的 LUIS 订阅密钥（终结点密钥）、订阅的[区域](~/articles/cognitive-services/speech-service/regions.md#intent-recognition)以及意向模型的 **AppId** 替换示例中的值。

> [!NOTE]
> 与语音 SDK 支持的其他服务不同，意向识别需要特定的订阅密钥（LUIS 终结点密钥）。 有关意向识别技术的信息，请参阅 [LUIS 网站](https://www.luis.ai)。 有关如何获取**终结点密钥**的信息，请参阅[创建 LUIS 终结点密钥](https://docs.microsoft.com/azure/cognitive-services/LUIS/luis-how-to-azure-subscription)。
