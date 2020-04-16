---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/22/2020
ms.author: trbye
ms.openlocfilehash: d97a1bf00d722414bedda4be79adc0c26d02c751
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422027"
---
## <a name="speech-modes"></a>语音模式

**互动**
- 用于命令和控制方案。
- 分段超时值为 X。
- 在一个可识别的话语结束时，服务将停止处理来自该请求 ID 的音频并结束转弯。 连接未关闭。
- 识别的最大限制为 20s。
- 调用的典型碳调用是`RecognizeOnceAsync`。

**谈话**
- 用于较长的运行识别。
- 分段超时值为 Y.（Y ！= X）
- 将处理多个完整的话语，而不会结束回合。
- 会因为太多的沉默而结束。
- 碳将继续与新的请求 ID 和根据需要重播音频。
- 在语音识别 10 分钟后，该服务将强制断开。
- 碳将重新连接并重播未确认的音频。
- 调用在碳与`StartContinuousRecognition`。

**听写**
- 允许用户通过说出标点符号来指定标点符号。
- 通过在对象上指定`EnableDictation`启动识别的`SpeechConfig`API 调用在碳中调用。
- <sup>第</sup>1 方群集`speech.fragment`返回消息以进行中间结果，<sup>第</sup>3`speech.hypothesis`方返回消息。