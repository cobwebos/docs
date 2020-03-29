---
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/21/2020
ms.author: dapine
ms.openlocfilehash: 9b4317064196c4ea3d761fd1a0bd43a764054fe6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77563315"
---
### <a name="speech-to-text-or-custom-speech-to-text"></a>语音到文本或自定义语音到文本

该容器提供基于 Websocket 的查询终结点 API，这些 API 通过[语音 SDK](../index.yml)进行访问。 默认情况下，语音 SDK 使用联机语音服务。 若要使用该容器，需要更改初始化方法。

> [!TIP]
> 将语音 SDK 与容器一起使用时，不需要提供 Azure 语音资源[订阅密钥或身份验证承载令牌](../rest-speech-to-text.md#authentication)。

请参阅下面的示例。

# <a name="c"></a>[C#](#tab/csharp)

请从使用此 Azure 云初始化调用：

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

使用此容器[主机](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet)调用 ：

```csharp
var config = SpeechConfig.FromHost(
    new Uri("ws://localhost:5000"));
```
# <a name="python"></a>[Python](#tab/python)

请从使用此 Azure 云初始化调用：

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, region=service_region)
```

使用此容器[主机](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)调用 ：

```python
speech_config = speechsdk.SpeechConfig(
    host="ws://localhost:5000")
```

***
