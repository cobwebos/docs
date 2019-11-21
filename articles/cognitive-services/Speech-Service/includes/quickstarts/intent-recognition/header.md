---
title: 快速入门：识别语音、意向和实体 - 语音服务
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: df4604560e05899461b11ec0788f72b27241f1b9
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2019
ms.locfileid: "74125415"
---
在本快速入门中，我们将使用[语音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) 以交互方式识别从麦克风捕获的音频数据中的语音。 满足几个先决条件后，通过麦克风识别语音只需四个步骤：
> [!div class="checklist"]
>
> * 通过订阅密钥和区域创建 ````SpeechConfig```` 对象。
> * 使用以上的 ````SpeechConfig```` 对象创建 ````IntentRecognizer```` 对象。
> * 使用 ````IntentRecognizer```` 对象，开始单个言语的识别过程。
> * 检查返回的 ````IntentRecognitionResult````。
