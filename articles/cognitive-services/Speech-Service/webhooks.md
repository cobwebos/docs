---
title: Webhook-语音服务
titleSuffix: Azure Cognitive Services
description: Webhook 是 HTTP 调用, 适用于在处理长时间运行的进程 (如导入、适应、准确性测试或长时间运行的文件的转录) 时优化解决方案。
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: panosper
ms.openlocfilehash: 3d07e540bf88c956f61b5d3b2a98702cad616985
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68558807"
---
# <a name="webhooks-for-speech-services"></a>Webhook for Speech Services

Webhook 类似于 HTTP 回调, 使你的应用程序在可用时可以接受来自语音服务的数据。 使用 webhook, 可以通过无需持续轮询响应来优化 REST Api 的使用。 在接下来的几个部分中, 你将了解如何将 webhook 与语音服务配合使用。

## <a name="supported-operations"></a>支持的操作

语音服务支持所有长时间运行的操作的 webhook。 下面列出的每项操作都可以在完成后触发 HTTP 回调。

* DataImportCompletion
* ModelAdaptationCompletion
* AccuracyTestCompletion
* TranscriptionCompletion
* EndpointDeploymentCompletion
* EndpointDataCollectionCompletion

接下来, 让我们创建一个 webhook。

## <a name="create-a-webhook"></a>创建 Webhook

让我们创建一个用于脱机脚本的 webhook。 方案: 用户具有长时间运行的音频文件, 这些文件希望使用批处理脚本 API 异步转录。

可以通过向 https://\<\>cris.ai/api/speechtotext/v2.1/transcriptions/hooks 发出 POST 请求来创建 webhook。

请求的配置参数作为 JSON 提供:

```json
{
  "configuration": {
    "url": "https://your.callback.url/goes/here",
    "secret": "<my_secret>"
  },
  "events": [
    "TranscriptionCompletion"
  ],
  "active": true,
  "name": "TranscriptionCompletionWebHook",
  "description": "This is a Webhook created to trigger an HTTP POST request when my audio file transcription is completed.",
  "properties": {
      "Active" : "True"
  }

}
```
对批处理脚本 API 发出的所有 POST 请求都`name`需要。 `description` 和`properties`参数是可选的。

使用`Active`属性, 无需删除并重新创建 webhook 注册, 就可以打开和关闭 URL。 如果在完成该过程后, 只需调用一次, 请删除 webhook 并将该`Active`属性切换为 false。

事件数组中`TranscriptionCompletion`提供了事件类型。 当脚本进入终端状态 (`Succeeded`或`Failed`) 时, 它将回叫终结点。 当回叫注册的 URL 时, 请求将包含`X-MicrosoftSpeechServices-Event`一个标头, 其中包含一个已注册的事件类型。 每个已注册的事件类型都有一个请求。

有一种无法订阅的事件类型。 它是`Ping`事件类型。 使用 ping URL 完成创建 webhook 时, 会将具有此类型的请求发送到 URL (请参阅下文)。  

在配置中, 属性`url`是必需的。 POST 请求发送到此 URL。 `secret`用于创建负载的 SHA256 哈希, 并将机密作为 HMAC 密钥。 当回叫注册的 URL `X-MicrosoftSpeechServices-Signature`时, 将哈希设置为标头。 此标头是 Base64 编码的。

此示例演示如何使用C#验证负载:

```csharp

private const string EventTypeHeaderName = "X-MicrosoftSpeechServices-Event";
private const string SignatureHeaderName = "X-MicrosoftSpeechServices-Signature";

[HttpPost]
public async Task<IActionResult> PostAsync([FromHeader(Name = EventTypeHeaderName)]WebHookEventType eventTypeHeader, [FromHeader(Name = SignatureHeaderName)]string signature)
{
    string body = string.Empty;
    using (var streamReader = new StreamReader(this.Request.Body))
    {
        body = await streamReader.ReadToEndAsync().ConfigureAwait(false);
        var secretBytes = Encoding.UTF8.GetBytes("my_secret");
        using (var hmacsha256 = new HMACSHA256(secretBytes))
        {
            var contentBytes = Encoding.UTF8.GetBytes(body);
            var contentHash = hmacsha256.ComputeHash(contentBytes);
            var storedHash = Convert.FromBase64String(signature);
            var validated = contentHash.SequenceEqual(storedHash);
        }
    }

    switch (eventTypeHeader)
    {
        case WebHookEventType.Ping:
            // Do your ping event related stuff here (or ignore this event)
            break;
        case WebHookEventType.TranscriptionCompletion:
            // Do your subscription related stuff here.
            break;
        default:
            break;
    }

    return this.Ok();
}

```
在此代码段中, `secret`解码并验证。 你还会注意到, webhook 事件类型已切换。 目前每个已完成的脚本有一个事件。 在放弃之前, 代码将为每个事件重试5次 (每个事件一次, 延迟一次)。

### <a name="other-webhook-operations"></a>其他 webhook 操作

获取所有已注册的 webhook:GET https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks

获取一个特定 webhook:GET https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id

删除一个特定 webhook:DELETE https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id

> [!Note]
> 在上面的示例中, 区域为 "westus"。 这应该替换为在 Azure 门户中创建语音服务资源的区域。

POST https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id/ping 正文: 空

向注册的 URL 发送 POST 请求。 请求包含`X-MicrosoftSpeechServices-Event`一个带有值 ping 的标头。 如果已使用机密注册 webhook, 则会包含一个`X-MicrosoftSpeechServices-Signature`标头, 其中包含负载为的 SHA256 哈希, 密钥为 HMAC 密钥。 哈希是 Base64 编码的。

POST https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id/test 正文: 空

如果系统中存在订阅事件类型 (脚本) 的实体并且处于适当的状态, 则将 POST 请求发送到已注册的 URL。 将从已调用 web 挂钩的最后一个实体生成有效负载。 如果不存在实体, 则 POST 将以204响应。 如果可以发出测试请求, 则将使用200响应。 请求正文与 web 挂钩已订阅的特定实体的 GET 请求中的形状相同 (例如, 脚本)。 此请求将包含`X-MicrosoftSpeechServices-Event`和`X-MicrosoftSpeechServices-Signature`标头 (如之前所述)。

### <a name="run-a-test"></a>运行测试

可以使用网站 https://bin.webhookrelay.com 完成快速测试。 然后, 你可以获取回调 Url, 以将其作为参数传递给 HTTP POST, 以创建之前在文档中描述的 webhook。

单击 "创建 Bucket", 然后按照屏幕上的说明获取挂钩。 然后, 使用此页中提供的信息向语音服务注册挂钩。 中继消息的有效负载–响应脚本的完成, 如下所示:

```json
{
    "results": [],
    "recordingsUrls": [
        "my recording URL"
    ],
    "models": [
        {
            "modelKind": "AcousticAndLanguage",
            "datasets": [],
            "id": "a09c8c8b-1090-443c-895c-3b1cf442dec4",
            "createdDateTime": "2019-03-26T12:48:46Z",
            "lastActionDateTime": "2019-03-26T14:04:47Z",
            "status": "Succeeded",
            "locale": "en-US",
            "name": "v4.13 Unified",
            "description": "Unified",
            "properties": {
                "Purpose": "OnlineTranscription,BatchTranscription,LanguageAdaptation",
                "ModelClass": "unified-v4"
            }
        }
    ],
    "statusMessage": "None.",
    "id": "d41615e1-a60e-444b-b063-129649810b3a",
    "createdDateTime": "2019-04-16T09:35:51Z",
    "lastActionDateTime": "2019-04-16T09:38:09Z",
    "status": "Succeeded",
    "locale": "en-US",
    "name": "Simple transcription",
    "description": "Simple transcription description",
    "properties": {
        "PunctuationMode": "DictatedAndAutomatic",
        "ProfanityFilterMode": "Masked",
        "AddWordLevelTimestamps": "True",
        "AddSentiment": "True",
        "Duration": "00:00:02"
    }
}
```
该消息包含记录的 URL 和用于转录该记录的模型。

## <a name="next-steps"></a>后续步骤

* [获取语音试用订阅](https://azure.microsoft.com/try/cognitive-services/)
