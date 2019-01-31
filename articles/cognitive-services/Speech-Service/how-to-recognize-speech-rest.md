---
title: 使用 REST API 识别语音
titleSuffix: Azure Cognitive Services
description: 了解如何在认知服务语音服务中使用语音转文本 API。
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: erhopf
ms.openlocfilehash: 7c7bbaa986a6efdb82a50048c7c218f96cd4014a
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55220325"
---
# <a name="recognize-speech-by-using-the-rest-api"></a>使用 REST API 识别语音

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-how-to-recognize-speech-selector.md)]

可以使用 REST API 通过 HTTP POST 请求识别简短的话语。

如果使用 [SDK](speech-sdk.md) 不支持的语言，则 REST API 就是识别语音的最简单方法。 可以向服务终结点发出 HTTP POST 请求，并在请求正文中传递完整话语。 收到的响应将包含已识别的文本。

> [!NOTE]
> 使用 REST API 时，话语需限制在 15 秒或更短时间内。
> 若要识别较长的陈述，请查看[语音 SDK](how-to-recognize-speech-csharp.md)。

有关**语音转文本** REST API 的详细信息，请参阅 [REST API](rest-apis.md#speech-to-text-api) 一文。 若要查看操作中的 API，请从 GitHub 下载 [REST API 示例](https://github.com/Azure-Samples/SpeechToText-REST)。

## <a name="next-steps"></a>后续步骤

- 请参阅 [REST API 概述](rest-apis.md)。
