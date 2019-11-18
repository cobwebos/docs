---
title: 查询语音到文本容器终结点
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/15/2019
ms.author: dapine
ms.openlocfilehash: 2d96385f2d2d34d161739c55228220cf28871048
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132619"
---
### <a name="speech-to-text-or-custom-speech-to-text"></a>语音到文本或自定义语音到文本

容器提供基于 websocket 的查询终结点 Api，可通过[语音 SDK](../index.md)进行访问。 默认情况下，语音 SDK 使用联机语音服务。 若要使用该容器，需要更改初始化方法。

> [!TIP]
> 使用带有容器的语音 SDK 时，无需提供 Azure 语音资源[订阅密钥或身份验证持有者令牌](../rest-speech-to-text.md#authentication)。

请参阅下面的示例。

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

请从使用此 Azure 云初始化调用：

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

对于使用容器[主机](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet)的此调用：

```csharp
var config = SpeechConfig.FromHost(
    new Uri("ws://localhost:5000"));
```
# <a name="pythontabpython"></a>[Python](#tab/python)

请从使用此 Azure 云初始化调用：

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, region=service_region)
```

对于使用容器[主机](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)的此调用：

```python
speech_config = speechsdk.SpeechConfig(
    host="ws://localhost:5000")
```

***
