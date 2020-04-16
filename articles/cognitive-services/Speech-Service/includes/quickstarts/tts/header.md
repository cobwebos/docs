---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: trbye
ms.openlocfilehash: 005cf83508d25e8f44190e07336fbb4e444f8e6b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400381"
---
在本快速入门中，你将使用[语音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) 将文本转换为合成语音。 在[文本转语音语言支持](../../../language-support.md#text-to-speech)下，文本转语音服务为合成语音提供了多种选项。 满足几个先决条件后，将合成语音呈现到默认扬声器只需四个步骤：
> [!div class="checklist"]
> * 通过订阅密钥和区域创建 `SpeechConfig` 对象。
> * 使用以上的 `SpeechConfig` 对象创建 `SpeechSynthesizer` 对象。
> * 使用 `SpeechSynthesizer` 对象来朗读文本。
> * 检查返回的 `SpeechSynthesisResult` 中是否有错误。
