---
title: 如何分块传输音频流 | Microsoft Docs
description: 如何利用分块传输向语音服务发送音频流
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/15/2017
ms.author: zhouwang
ms.openlocfilehash: 7d02340932dfc547893c4c40cbe08978b7b93756
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365657"
---
# <a name="chunked-transfer-encoding"></a>分块传输编码

要将语音转录为文本，可使用 Microsoft 语音识别 API 将音频作为整个区块发送或将音频拆分为小区块进行发送。 为了有效流式传输音频并降低听录延迟，建议使用[分块传输编码](https://en.wikipedia.org/wiki/Chunked_transfer_encoding)，向服务流式传输音频。 其他实现可能会导致更高的用户感知延迟。 有关详细信息，请参阅[音频流](../concepts.md#audio-streams)页。

> [!NOTE]
> 在任何请求中都不得上传超过 10 秒的音频，并且请求总持续时间不得超过 14 秒。
> [!NOTE]
> 仅当使用 [REST API](../GetStarted/GetStartedREST.md) 时才需要指定分块传输编码，以调用语音服务。 使用[客户端库](../GetStarted/GetStartedClientLibraries.md)的应用程序不需要配置分块传输编码。

下面的代码演示如何设置分块传输编码，以及如何发送要分为 1024 字节区块的音频文件。

```cs

HttpWebRequest request = null;
request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.Host = @"speech.platform.bing.com";
request.ContentType = @"audio/wav; codec=audio/pcm; samplerate=16000";
request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR_SUBSCRIPTION_KEY";

using (fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
{

    /*
    * Open a request stream and write 1024 byte chunks in the stream one at a time.
    */
    byte[] buffer = null;
    int bytesRead = 0;
    using (Stream requestStream = request.GetRequestStream())
    {
        /*
        * Read 1024 raw bytes from the input audio file.
        */
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        // Flush
        requestStream.Flush();
    }
}
```
