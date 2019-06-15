---
title: Webhook-语音服务
titlesuffix: Azure Cognitive Services
description: Webhook 是 HTTP 回调理想优化你的解决方案时面对的长时间运行的进程，如导入、 自适应、 准确性测试或转录的长时间运行的文件。
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: fbe6fe25b5ff0cd5148e3bba22dec4648399510d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67072308"
---
# <a name="webhooks-for-speech-services"></a>语音服务的 Webhook

Webhook 就像允许应用程序接受语音服务中的数据变为可用时的 HTTP 回调。 使用 webhook，你可以通过无需连续轮询响应优化我们的 REST Api 的使用。 在下一步的几个部分，将了解如何将 webhook 与语音服务。

## <a name="supported-operations"></a>支持的操作

语音服务支持 webhook 的长时间运行的所有操作。 下面列出的操作的每个可以触发的 HTTP 回调完成后。 

* DataImportCompletion
* ModelAdaptationCompletion
* AccuracyTestCompletion
* TranscriptionCompletion
* EndpointDeploymentCompletion
* EndpointDataCollectionCompletion

接下来，让我们创建 webhook。

## <a name="create-a-webhook"></a>创建 Webhook

让我们创建脱机的脚本的 webhook。 方案： 用户已是他们想要使用 Batch 脚本 API 以异步方式转录的长时间运行音频文件。 

可以通过 https:// 在 POST 请求创建 Webhook\<区域\>.cris.ai/api/speechtotext/v2.1/transcriptions/hooks。

请求的配置参数以 JSON 形式提供：

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
对批处理脚本 API 的所有 POST 请求都需要`name`。 `description`和`properties`参数是可选的。

`Active`属性用于切换调用返回到你的 URL 打开和关闭而无需删除并重新创建 webhook 注册。 如果您只需要调用返回一次后过程已完成，然后删除 webhook 和交换机`Active`属性设置为 false。

事件类型`TranscriptionCompletion`提供事件数组中。 它将返回到你的终结点时调用脚本进入终止状态 (`Succeeded`或`Failed`)。 在调用返回的注册 URL，将包含请求`X-MicrosoftSpeechServices-Event`标头包含已注册的事件类型之一。 没有每个已注册的事件类型的一个请求。 

没有一种不能订阅的事件类型。 它是`Ping`事件类型。 包含此类型的请求发送到在完成后使用 ping URL （见下文） 时创建 webhook 的 URL。  

在配置中，`url`属性是必需的。 POST 请求发送到此 URL。 `secret`用于使用 HMAC 密钥作为机密创建 SHA256 哈希的负载。 哈希值设置为`X-MicrosoftSpeechServices-Signature`标头时回调到已注册的 URL。 此标头是 Base64 编码。

此示例演示了如何来验证有效负载使用C#:

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
在此代码片段，`secret`解码并验证。 此外会发现已切换 webhook 事件类型。 目前没有一个事件，每完成脚本。 代码将重试每个事件 （的一秒延迟） 放弃前的五倍。

### <a name="other-webhook-operations"></a>其他 webhook 操作

若要获取所有已注册的 webhook:GET https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks

若要获取一个特定的 webhook:GET https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id

若要删除一个特定的 webhook:DELETE https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id

> [!Note] 
> 在上面的示例中，区域是 'westus'。 这应替换为已在 Azure 门户中创建您的语音服务资源的区域。

开机自检 https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id/ping 正文： 空

将 POST 请求发送到已注册的 URL。 该请求包含`X-MicrosoftSpeechServices-Event`标头值 ping。 如果使用密钥注册 webhook，它将包含`X-MicrosoftSpeechServices-Signature`标头与使用 HMAC 密钥作为机密的有效负载的 SHA256 哈希。 哈希值是 Base64 编码。 

开机自检 https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id/test 正文： 空

如果订阅的事件类型 （脚本） 的实体在系统中存在并处于相应状态，请将 POST 请求发送到已注册的 URL。 将会调用 web 挂钩的最后一个实体中生成负载。 如果没有实体存在，将使用 204 响应 POST。 如果可以进行测试请求，它将使用 200 响应。 请求正文是形状的相同，如 web 挂钩已订阅 （例如脚本） 的特定实体的 GET 请求中所示。 此请求将具有`X-MicrosoftSpeechServices-Event`和`X-MicrosoftSpeechServices-Signature`根据前面所述的标头。

### <a name="run-a-test"></a>运行测试

可进行快速测试使用网站 https://bin.webhookrelay.com 。 在这里，你可以获取调用后将作为参数传递给用于创建文档中前面所述的 webhook HTTP POST 的 Url。

单击创建存储桶，然后按照屏幕说明进行操作以获取挂钩。 然后使用此页中提供的信息来注册语音服务挂钩。 -在脚本完成响应 – 中继消息的负载如下所示：

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
该消息包含录制 URL 和模型用于转录该录制。

## <a name="next-steps"></a>后续步骤

* [获取语音试用订阅](https://azure.microsoft.com/try/cognitive-services/)
