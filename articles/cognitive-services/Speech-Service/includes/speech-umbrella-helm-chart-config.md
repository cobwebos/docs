---
title: 安装语音容器
titleSuffix: Azure Cognitive Services
description: 详细介绍了语音涵盖性 helm 图表配置选项。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: ed64412ccf9d192506fafe546b1ccee7941aa43a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711809"
---
### <a name="speech-umbrella-chart"></a>语音 （涵盖性图表）

顶级"涵盖性"图表中的值重写相应的子图表值。 因此，所有本地自定义的值应在此处添加。

|参数|描述|默认|
| -- | -- | -- | -- |
| `speechToText.enabled` | 是否**语音到文本**服务已启用。 | `true` |
| `speechToText.verification.enabled` | 是否`helm test`的功能**语音到文本**服务已启用。 | `true` |
| `speechToText.verification.image.registry` | Docker 映像存储库的`helm test`使用来测试**语音到文本**服务。 Helm 创建用于测试群集内的单独 pod 和拉取*测试使用*此注册表中的映像。 | `docker.io` |
| `speechToText.verification.image.repository` | Docker 映像存储库的`helm test`使用来测试**语音到文本**服务。 Helm 测试 pod 使用此存储库拉取*测试使用*映像。 | `antsu/on-prem-client` |
| `speechToText.verification.image.tag` | 与一起使用的 docker 映像标记`helm test`有关**语音到文本**服务。 Helm 测试 pod 使用此标记来拉取*测试使用*映像。 | `latest` |
| `speechToText.verification.image.pullByHash` | 是否*测试使用*哈希的拉取 docker 映像。 如果`true`，`speechToText.verification.image.hash`应添加，使用有效的图像的哈希值。 | `false` |
| `speechToText.verification.image.arguments` | 用于执行的自变量*测试使用*docker 映像。 Helm 测试 pod 在运行时到容器传递这些参数`helm test`。 | `"./speech-to-text-client"`<br/> `"./audio/whatstheweatherlike.wav"` <br/> `"--expect=What's the weather like"`<br/>`"--host=$(SPEECH_TO_TEXT_HOST)"`<br/>`"--port=$(SPEECH_TO_TEXT_PORT)"` |
| `textToSpeech.enabled` | 是否**文本到语音转换**服务已启用。 | `true` |
| `textToSpeech.verification.enabled` | 是否`helm test`的功能**语音到文本**服务已启用。 | `true` |
| `textToSpeech.verification.image.registry` | Docker 映像存储库的`helm test`使用来测试**语音到文本**服务。 Helm 创建用于测试群集内的单独 pod 和拉取*测试使用*此注册表中的映像。 | `docker.io` |
| `textToSpeech.verification.image.repository` | Docker 映像存储库的`helm test`使用来测试**语音到文本**服务。 Helm 测试 pod 使用此存储库拉取*测试使用*映像。 | `antsu/on-prem-client` |
| `textToSpeech.verification.image.tag` | 与一起使用的 docker 映像标记`helm test`有关**语音到文本**服务。 Helm 测试 pod 使用此标记来拉取*测试使用*映像。 | `latest` |
| `textToSpeech.verification.image.pullByHash` | 是否*测试使用*哈希的拉取 docker 映像。 如果`true`，`textToSpeech.verification.image.hash`应添加，使用有效的图像的哈希值。 | `false` |
| `textToSpeech.verification.image.arguments` | 要执行的参数*测试使用*docker 映像。 Helm 测试 pod 在运行时向容器传递这些参数`helm test`。 | `"./text-to-speech-client"`<br/> `"--input='What's the weather like'"` <br/> `"--host=$(TEXT_TO_SPEECH_HOST)"`<br/>`"--port=$(TEXT_TO_SPEECH_PORT)"` |