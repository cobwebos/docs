---
title: 快速入门：合成语音 - 语音服务
titleSuffix: Azure Cognitive Services
description: 了解如何使用语音 SDK 来合成语音
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 09/20/2019
ms.author: yulili
ms.openlocfilehash: 263a8e98c4c029728272c020efe00e82be20f5e2
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73505073"
---
在本快速入门中，我们将使用[语音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) 将文本转换为合成语音。 满足几个先决条件后，将合成语音呈现到默认扬声器只需四个步骤：
> [!div class="checklist"]
> * 通过订阅密钥和区域创建 ````SpeechConfig```` 对象。
> * 使用上面的 ````SpeechConfig```` 对象创建 ````SpeechSynthesizer```` 对象。
> * 使用 ````SpeechSynthesizer```` 对象来朗读文本。
> * 检查返回的 ````SpeechSynthesisResult```` 中是否有错误。
