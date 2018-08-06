---
title: 使用 REST API 识别语音
description: 了解如何使用语音服务中的语音转文本
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: v-jerkin
ms.openlocfilehash: 15db2d9b872c76d70fd531af07fb55c701e86494
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2018
ms.locfileid: "39331220"
---
# <a name="recognize-speech-by-using-the-rest-api"></a>使用 REST API 识别语音

[!include[Selector](../../../includes/cognitive-services-speech-service-how-to-recognize-speech-selector.md)]

可以使用 REST API 通过 HTTP POST 请求识别简短的陈述。

如果使用 SDK 不支持的语言，则 REST API 就是识别语音的最简单方法。
可以向服务终结点发布 HTTP POST 请求，同时在请求正文中传递完整陈述。
收到的响应将包含已识别的文本。

> [!NOTE]
> 使用 REST API 时，陈述需限制在 15 秒或更短时间内。
> 若要识别较长的陈述，请查看[语音 SDK](how-to-recognize-speech-csharp.md)。

有关语音转文本 REST API 的详细信息，请参阅 [REST API](rest-apis.md#speech-to-text)。 若要了解其实际操作，请从 GitHub 下载 [REST API 示例](https://github.com/Azure-Samples/SpeechToText-REST)。

## <a name="next-steps"></a>后续步骤

- [参阅 REST API 概述](rest-apis.md)
