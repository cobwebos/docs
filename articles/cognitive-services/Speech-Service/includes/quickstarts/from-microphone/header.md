---
title: 快速入门：从麦克风中识别语音 - 语音服务
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 11/07/2019
ms.author: erhopf
ms.openlocfilehash: f3f28cdd8c3c77a5cde2ead86c49b41d54bab5d7
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961180"
---
在本快速入门中，我们将使用[语音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) 以交互方式识别从麦克风捕获的音频数据中的语音。 满足几个先决条件后，通过麦克风识别语音只需四个步骤：

> [!div class="checklist"]
> * 通过订阅密钥和区域创建 `SpeechConfig` 对象。
> * 使用以上的 `SpeechConfig` 对象创建 `SpeechRecognizer` 对象。
> * 使用 `SpeechRecognizer` 对象，开始单个言语的识别过程。
> * 检查返回的 `SpeechRecognitionResult`。
