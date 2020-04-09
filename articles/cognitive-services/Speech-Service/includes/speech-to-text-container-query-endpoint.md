---
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: c029165a4994e5e3f73399fb00a1775b67128e44
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879000"
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
