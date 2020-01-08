---
title: 快速入门：识别音频文件中的语音 - 语音服务
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: e333b156eaf9c4b6e09e631513ea099018f0691b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75468098"
---
在本快速入门中，我们将使用[语音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) 从音频文件识别语音。 满足几个先决条件后，从文件识别语音只需五个步骤：
> [!div class="checklist"]
> * 通过订阅密钥和区域创建 ````SpeechConfig```` 对象。
> * 创建指定 .WAV 文件名的 ````AudioConfig```` 对象。
> * 使用以上的 ````SpeechConfig```` 和 ````AudioConfig```` 对象创建 ````SpeechRecognizer```` 对象。
> * 使用 ````SpeechRecognizer```` 对象，开始单个言语的识别过程。
> * 检查返回的 ````SpeechRecognitionResult````。
