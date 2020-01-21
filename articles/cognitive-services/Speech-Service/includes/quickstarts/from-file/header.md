---
title: 快速入门：识别音频文件中的语音 - 语音服务
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/14/2020
ms.author: dapine
ms.openlocfilehash: 2b6270535c0cf69549a7412bd38d9207454e5500
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "76037910"
---
在本快速入门中，我们将使用[语音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) 从音频文件识别语音。 满足几个先决条件后，从文件识别语音只需几个步骤：
> [!div class="checklist"]
> * 通过订阅密钥和区域创建 `SpeechConfig` 对象。
> * 创建指定 .WAV 文件名的 `AudioConfig` 对象。
> * 使用以上的 `SpeechConfig` 和 `AudioConfig` 对象创建 `SpeechRecognizer` 对象。
> * 使用 `SpeechRecognizer` 对象，开始单个言语的识别过程。
> * 检查返回的 `SpeechRecognitionResult`。
