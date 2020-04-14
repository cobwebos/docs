---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2020
ms.author: dapine
ms.openlocfilehash: 3775690802c89805ccf9df1ee6d6717a8818213f
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2020
ms.locfileid: "80659099"
---
本快速入门介绍如何使用[语音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) 以交互方式识别来自麦克风输入的语音，以及从捕获的音频中获取听录文本。 可轻松地将此功能集成到应用或设备中，用于执行对话听录等常见识别任务。 它还可用于更加复杂的集成，例如配合使用 Bot Framework 和语音 SDK 来生成语音助手。

满足几个先决条件后，通过麦克风识别语音只需四个步骤：

> [!div class="checklist"]
> * 通过订阅密钥和区域创建 `SpeechConfig` 对象。
> * 使用以上的 `SpeechConfig` 对象创建 `SpeechRecognizer` 对象。
> * 使用 `SpeechRecognizer` 对象，开始单个言语的识别过程。
> * 检查返回的 `SpeechRecognitionResult`。
