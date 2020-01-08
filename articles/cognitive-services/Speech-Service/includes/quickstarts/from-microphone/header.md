---
title: 快速入门：从麦克风中识别语音 - 语音服务
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/20/2019
ms.author: erhopf
ms.openlocfilehash: ab51fe0323ea23c16cb52aa7a0570f8d51d40b2f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75468509"
---
本快速入门介绍如何使用[语音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) 以交互方式识别来自麦克风输入的语音，以及从捕获的音频中获取听录文本。 可轻松地将此功能集成到应用或设备中，用于执行对话听录等常见识别任务。 它还可用于更加复杂的集成，例如配合使用 Bot Framework 和语音 SDK 来生成语音助手。

满足几个先决条件后，通过麦克风识别语音只需四个步骤：

> [!div class="checklist"]
> * 通过订阅密钥和区域创建 `SpeechConfig` 对象。
> * 使用以上的 `SpeechConfig` 对象创建 `SpeechRecognizer` 对象。
> * 使用 `SpeechRecognizer` 对象，开始单个言语的识别过程。
> * 检查返回的 `SpeechRecognitionResult`。
