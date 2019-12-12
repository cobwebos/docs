---
title: 快速入门：将语音翻译为文本 - 语音服务
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/09/2019
ms.author: yulili
ms.openlocfilehash: d4781808ce8e80f62e86ce1d0c6db9c38b2636d0
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74981109"
---
在本快速入门中，我们将使用[语音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) 以交互方式将一种语言的语音转换为另一种语言的文本。 在满足几项先决条件后，将语音转换为文本只需五个步骤：
> [!div class="checklist"]
> * 通过订阅密钥和区域创建 ````SpeechConfig```` 对象。
> * 更新 ````SpeechConfig```` 对象，指定源语言和目标语言。
> * 使用上面的 ````SpeechConfig```` 对象创建 ````TranslationRecognizer```` 对象。
> * 使用 ````TranslationRecognizer```` 对象，开始单个言语的识别过程。
> * 检查返回的 ````TranslationRecognitionResult````。
