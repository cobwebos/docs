---
title: 安装语音容器
titleSuffix: Azure Cognitive Services
description: 详细介绍了语音伞形 Helm 图配置选项。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: ed64412ccf9d192506fafe546b1ccee7941aa43a
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73522387"
---
### <a name="speech-umbrella-chart"></a>语音（伞形图）

顶级“伞形”图中的值会替代相应的子图值。 因此，应在此处添加所有本地自定义值。

|参数|说明|默认|
| -- | -- | -- | -- |
| `speechToText.enabled` | **语音到文本**服务是否已启用。 | `true` |
| `speechToText.verification.enabled` | 是否启用了**语音到文本**服务的 `helm test` 功能。 | `true` |
| `speechToText.verification.image.registry` | `helm test` 用于测试**语音到文本**服务的 docker 映像存储库。 Helm 在群集内创建单独的 Pod 用于测试，并从该注册表拉取 *test-use* 映像。 | `docker.io` |
| `speechToText.verification.image.repository` | `helm test` 用于测试**语音到文本**服务的 docker 映像存储库。 Helm 测试 Pod 使用此存储库拉取 *test-use* 映像。 | `antsu/on-prem-client` |
| `speechToText.verification.image.tag` | 用于**语音到文本**服务 `helm test` 的 docker 映像标记。 Helm 测试 Pod 使用此标记拉取 *test-use* 映像。 | `latest` |
| `speechToText.verification.image.pullByHash` | 是否通过哈希方法拉取 *test-use* docker 映像。 如果为 `true`，则 `speechToText.verification.image.hash` 应随有效的映像哈希值一起添加。 | `false` |
| `speechToText.verification.image.arguments` | 用于执行*测试使用*的 docker 映像的参数。 Helm 测试箱在运行 `helm test`时，会将这些参数传递给容器。 | `"./speech-to-text-client"`<br/> `"./audio/whatstheweatherlike.wav"` <br/> `"--expect=What's the weather like"`<br/>`"--host=$(SPEECH_TO_TEXT_HOST)"`<br/>`"--port=$(SPEECH_TO_TEXT_PORT)"` |
| `textToSpeech.enabled` | 是否启用了**文本转语音**服务。 | `true` |
| `textToSpeech.verification.enabled` | 是否启用了**语音到文本**服务的 `helm test` 功能。 | `true` |
| `textToSpeech.verification.image.registry` | `helm test` 用于测试**语音到文本**服务的 docker 映像存储库。 Helm 在群集内创建单独的 Pod 用于测试，并从该注册表拉取 *test-use* 映像。 | `docker.io` |
| `textToSpeech.verification.image.repository` | `helm test` 用于测试**语音到文本**服务的 docker 映像存储库。 Helm 测试 Pod 使用此存储库拉取 *test-use* 映像。 | `antsu/on-prem-client` |
| `textToSpeech.verification.image.tag` | 用于**语音到文本**服务 `helm test` 的 docker 映像标记。 Helm 测试 Pod 使用此标记拉取 *test-use* 映像。 | `latest` |
| `textToSpeech.verification.image.pullByHash` | 是否通过哈希方法拉取 *test-use* docker 映像。 如果为 `true`，则 `textToSpeech.verification.image.hash` 应随有效的映像哈希值一起添加。 | `false` |
| `textToSpeech.verification.image.arguments` | 用于执行 *test-use* docker 映像的参数。 Helm 测试 Pod 在运行 `helm test` 时将这些参数传递给容器。 | `"./text-to-speech-client"`<br/> `"--input='What's the weather like'"` <br/> `"--host=$(TEXT_TO_SPEECH_HOST)"`<br/>`"--port=$(TEXT_TO_SPEECH_PORT)"` |