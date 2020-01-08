---
title: 快速入门：将语音合成音频文件 - 语音服务
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
ms.openlocfilehash: 9b13d8fc3b77426a59dea5399223b79c4bb4b1a1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75467367"
---
在本快速入门中，我们将使用[语音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) 将文本转换为音频文件中的合成语音。 在满足几项先决条件后，将语音合成到文件只需五个步骤：
> [!div class="checklist"]
> * 通过订阅密钥和区域创建 ````SpeechConfig```` 对象。
> * 创建指定 .WAV 文件名的“音频配置”对象。
> * 使用上面的配置对象创建 ````SpeechSynthesizer```` 对象。
> * 使用 ````SpeechSynthesizer```` 对象，将文本转换为合成语音，并将其保存到指定的音频文件中。
> * 检查返回的 ````SpeechSynthesizer```` 中是否有错误。
