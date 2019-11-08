---
title: 快速入门：将语音翻译为语音 - 语音服务
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 09/20/2019
ms.author: yulili
ms.openlocfilehash: 77b060a5caf9865a1296cd6644cf0c51e3253a0e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73504841"
---
在本快速入门中，我们将使用[语音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) 以交互方式将一种语言的语音翻译为另一种语言的语音。 在满足几项先决条件后，将语音翻译为语音只需六个步骤：
> [!div class="checklist"]
> * 通过订阅密钥和区域创建 ````SpeechTranslationConfig```` 对象。
> * 更新 ````SpeechTranslationConfig```` 对象，指定源语言和目标语言。
> * 更新 ````SpeechTranslationConfig```` 对象，指定语音输出语音名称。
> * 使用以上的 ````SpeechTranslationConfig```` 对象创建 ````TranslationRecognizer```` 对象。
> * 使用 ````TranslationRecognizer```` 对象，开始单个言语的识别过程。
> * 检查返回的 ````TranslationRecognitionResult````。
