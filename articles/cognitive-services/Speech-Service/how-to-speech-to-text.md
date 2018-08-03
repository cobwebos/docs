---
title: 使用语音转文本 | Microsoft Docs
description: 了解如何使用语音服务中的语音转文本
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: v-jerkin
ms.openlocfilehash: 26cecedfc3ad2d472b9686e25054fe08253cee77
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2018
ms.locfileid: "39068516"
---
# <a name="use-speech-to-text-in-the-speech-service"></a>使用语音服务中的“语音转文本”

可通过两种不同方式在应用程序中使用“语音转文本”。

| 方法 | Description |
|-|-|
| [SDK](speech-sdk.md) | 适用于 C/C++、C# 和 Java 开发人员的最简单的方法 |
| [REST](rest-apis.md) | 使用 HTTP POST 请求识别简短语句 | 

## <a name="using-the-sdk"></a>使用 SDK

[语音 SDK](speech-sdk.md) 提供在功能完整的应用程序中使用“语音转文本”的最简单方法。

1. 创建语音工厂，同时提供语音服务订阅密钥和[区域](regions.md)或授权令牌。 还可在此时配置选项，例如识别语言或适用于自己的语音识别模型的自定义终结点。

2. 从工厂获取识别器。 提供三种不同类型的识别器。 每种识别器可以使用设备的默认麦克风、音频流或者文件中的音频。

    识别器 | 函数
    -|-
    语音识别器|提供语音的文本听录
    意向识别器|识别后通过 [LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/) 派生扬声器意向\*
    翻译识别器|将听录的文本翻译成另一种语言（请参阅[语音翻译](how-to-translate-speech.md)）

    \* 对于意向识别，为意向识别器创建语音工厂时，需要使用单独的 LUIS 订阅密钥。
    
4. 如果需要，可捆绑事件以进行异步操作。 然后，识别器将在其具有中间和最终结果时调用事件处理程序。 否则，应用程序将收到最终听录结果。

5. 开始识别。
   对于单次识别（例如命令或查询识别），请使用 `RecognizeAsync()`，它返回识别的第一条语句。
   对于长时间识别（例如听录），请使用 `StartContinuousRecognitionAsync()`，并将事件连接起来实现异步识别结果。

### <a name="sdk-samples"></a>SDK 示例

有关最新的示例集，请参阅[认知服务语音 SDK 示例 GitHub 存储库](https://aka.ms/csspeech/samples)。

## <a name="using-the-rest-api"></a>使用 REST API

如果使用 SDK 不支持的语言，则 REST API 就是识别语音的最简单方法。 可以向服务终结点发布 HTTP POST 请求，同时在请求正文中传递完整的语句。 收到的响应将包含已识别的文本。

> [!NOTE]
> 使用 REST API 时，语句需限制在 15 秒或更短时间内。

有关语音转文本 REST API 的详细信息，请参阅 [REST API](rest-apis.md#speech-to-text)。 要了解实际操作，请从 GitHub 下载 [REST API 示例](https://github.com/Azure-Samples/SpeechToText-REST)。

## <a name="next-steps"></a>后续步骤

- [获取语音试用订阅](https://azure.microsoft.com/try/cognitive-services/)
- [如何在 C++ 中识别语音](quickstart-cpp-windows.md)
- [如何在 C# 应用中识别语音](quickstart-csharp-dotnet-windows.md)
- [如何在 Java 中识别语音](quickstart-java-android.md)
