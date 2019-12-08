---
title: 快速入门：将语音合成音频文件 - 语音服务
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
ms.openlocfilehash: 7d9a03f5a57473ce651560b261a4cf84b4ca4824
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2019
ms.locfileid: "74817892"
---
在本快速入门中，我们将使用[语音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) 将文本转换为音频文件中的合成语音。 在满足几项先决条件后，将语音合成到文件只需五个步骤：
> [!div class="checklist"]
> * 通过订阅密钥和区域创建 ````SpeechConfig```` 对象。
> * 创建指定 .WAV 文件名的“音频配置”对象。
> * 使用上面的配置对象创建 ````SpeechSynthesizer```` 对象。
> * 使用 ````SpeechSynthesizer```` 对象，将文本转换为合成语音，并将其保存到指定的音频文件中。
> * 检查返回的 ````SpeechSynthesizer```` 中是否有错误。
