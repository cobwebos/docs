---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/22/2020
ms.author: trbye
ms.openlocfilehash: d97a1bf00d722414bedda4be79adc0c26d02c751
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "81422027"
---
## <a name="speech-modes"></a>语音模式

**交互式**
- 适用于命令和控制方案。
- 的分段超时值为 X。
- 在一个可识别的查询文本结束时，该服务将停止处理来自该请求 ID 的音频，并结束该请求。 连接未关闭。
- 识别的最大限制为 .20s。
- 调用的典型碳调用为 `RecognizeOnceAsync` 。

**引**
- 适用于运行识别的时间。
- 的分段超时值为 (Y！ = X) 
- 将处理多个完整的最谈话，而无需终止操作。
- 将结束翻转的声音太多。
- "碳" 将继续使用新的请求 ID 并根据需要重播音频。
- 该服务将在经过10分钟的语音识别后强行断开连接。
- 碳会重新连接并重播未确认的音频。
- 在抄送中通过调用 `StartContinuousRecognition` 。

**听写**
- 允许用户通过说话来指定标点符号。
- 通过在 `EnableDictation` 对象上指定， `SpeechConfig` 而不考虑启动识别的 API 调用，来调用。
- 1个<sup>st</sup> 方群集返回 `speech.fragment` 中间结果的消息，3个<sup>rd</sup> 方返回 `speech.hypothesis` 消息。