---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2020
ms.author: trbye
ms.openlocfilehash: fdcb5bb57afe145b57cb1223242d4d5625a2dfb7
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81400786"
---
需要语音资源订阅密钥和区域才能创建语音配置对象。 实例化语音识别器对象需要配置对象。

识别器实例公开了多种用于识别语音的方法。 在此示例中识别了一次语音。 此功能是告知语音服务，你要发送单个需识别的短语，并且在该短语被识别后会停止识别语音。 生成结果后，代码会将识别原因写入控制台。

> [!TIP]
> 语音 SDK 将默认使用 `en-us` 作为语言进行识别。若要了解如何选择源语言，请参阅[指定语音转文本的源语言](../../../how-to-specify-source-language.md)。