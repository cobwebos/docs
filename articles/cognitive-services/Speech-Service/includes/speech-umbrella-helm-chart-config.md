---
title: 安装语音容器
titleSuffix: Azure Cognitive Services
description: 详细介绍了语音伞 helm 图配置选项。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: ed64412ccf9d192506fafe546b1ccee7941aa43a
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "67717208"
---
### <a name="speech-umbrella-chart"></a>语音 (伞图)

顶级 "伞" 图表中的值会替代相应的子图表值。 因此, 应在此处添加所有本地自定义值。

|参数|描述|默认|
| -- | -- | -- | -- |
| `speechToText.enabled` | **语音到文本**服务是否已启用。 | `true` |
| `speechToText.verification.enabled` | 是否启用`helm test` **语音到文本**服务的功能。 | `true` |
| `speechToText.verification.image.registry` | 用于测试`helm test` **语音到文本**服务的 docker 映像存储库。 Helm 在群集内创建用于测试的单独 pod, 并从此注册表拉取*测试使用*映像。 | `docker.io` |
| `speechToText.verification.image.repository` | 用于测试`helm test` **语音到文本**服务的 docker 映像存储库。 Helm 测试盒使用此存储库来拉取*测试使用*映像。 | `antsu/on-prem-client` |
| `speechToText.verification.image.tag` | 用于`helm test` **语音到文本**服务的 docker 映像标记。 Helm 测试盒使用此标记拉取*测试使用*映像。 | `latest` |
| `speechToText.verification.image.pullByHash` | 是否通过哈希请求*测试使用*docker 映像。 如果`true`为`speechToText.verification.image.hash` , 则应添加具有有效图像哈希值的。 | `false` |
| `speechToText.verification.image.arguments` | 用于执行*测试使用*的 docker 映像的参数。 Helm 测试箱在运行`helm test`时将这些参数传递给容器。 | `"./speech-to-text-client"`<br/> `"./audio/whatstheweatherlike.wav"` <br/> `"--expect=What's the weather like"`<br/>`"--host=$(SPEECH_TO_TEXT_HOST)"`<br/>`"--port=$(SPEECH_TO_TEXT_PORT)"` |
| `textToSpeech.enabled` | **文本到语音**服务是否已启用。 | `true` |
| `textToSpeech.verification.enabled` | 是否启用`helm test` **语音到文本**服务的功能。 | `true` |
| `textToSpeech.verification.image.registry` | 用于测试`helm test` **语音到文本**服务的 docker 映像存储库。 Helm 在群集内创建用于测试的单独 pod, 并从此注册表拉取*测试使用*映像。 | `docker.io` |
| `textToSpeech.verification.image.repository` | 用于测试`helm test` **语音到文本**服务的 docker 映像存储库。 Helm 测试盒使用此存储库来拉取*测试使用*映像。 | `antsu/on-prem-client` |
| `textToSpeech.verification.image.tag` | 用于`helm test` **语音到文本**服务的 docker 映像标记。 Helm 测试盒使用此标记拉取*测试使用*映像。 | `latest` |
| `textToSpeech.verification.image.pullByHash` | 是否通过哈希请求*测试使用*docker 映像。 如果`true`为`textToSpeech.verification.image.hash` , 则应添加具有有效图像哈希值的。 | `false` |
| `textToSpeech.verification.image.arguments` | 要使用*测试-使用*docker 映像执行的参数。 Helm 测试箱在运行`helm test`时将这些参数传递给容器。 | `"./text-to-speech-client"`<br/> `"--input='What's the weather like'"` <br/> `"--host=$(TEXT_TO_SPEECH_HOST)"`<br/>`"--port=$(TEXT_TO_SPEECH_PORT)"` |