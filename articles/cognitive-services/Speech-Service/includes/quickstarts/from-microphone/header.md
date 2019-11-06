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
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: 64f084f58ae4b12d92d6dee343a59dd56b1c351e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73505561"
---
在本快速入门中，我们将使用[语音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) 以交互方式识别从麦克风捕获的音频数据中的语音。 满足几个先决条件后，通过麦克风识别语音只需四个步骤：
> [!div class="checklist"]
> * 通过订阅密钥和区域创建 ````SpeechConfig```` 对象。
> * 使用以上的 ````SpeechConfig```` 对象创建 ````SpeechRecognizer```` 对象。
> * 使用 ````SpeechRecognizer```` 对象，开始单个言语的识别过程。
> * 检查返回的 ````SpeechRecognitionResult````。
