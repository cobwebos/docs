---
title: 使用 Azure Batch 听录 API
titlesuffix: Azure Cognitive Services
description: 用于听录大量音频内容的示例。
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: panosper
ms.openlocfilehash: 8a180dfada9da92e0b8ed69373a20602b3b0a177
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/28/2018
ms.locfileid: "52495598"
---
# <a name="why-use-batch-transcription"></a>为何使用 Batch 听录？

如果存储中有大量音频，批量听录是理想的选择。 使用专用 REST API 可以通过共享访问签名 (SAS) URI 指向音频文件并异步接收听录。

## <a name="the-batch-transcription-api"></a>Batch 听录 API

Batch 听录 API 提供异步语音转文本听录和其他功能。 它是一个 REST API，可以公开用于以下目的的方法：

1. 创建批处理请求
1. 查询状态 
1. 下载听录

> [!NOTE]
> Batch 听录 API 非常适合经常累积数千小时音频的呼叫中心。 该 API 秉持“用后即焚”理念，能够轻松听录大量的录音。

### <a name="supported-formats"></a>支持的格式

Batch 听录 API 支持以下格式：

| 格式 | 编解码器 | Bitrate | 采样率 |
|--------|-------|---------|-------------|
| WAV | PCM | 16 位 | 8 或 16 kHz、单声道、立体声 |
| MP3 | PCM | 16 位 | 8 或 16 kHz、单声道、立体声 |
| OGG | OPUS | 16 位 | 8 或 16 kHz、单声道、立体声 |

> [!NOTE]
> Batch 听录 API 需要 S0 密钥（付费层）。 它不能使用免费 (f0) 密钥。

对于立体声音频流，Batch 听录 API 将在听录期间分离左右声道。 根据单个通道创建两个带有结果的 JSON 文件。 开发人员可利用每个话语的时间戳创建有序的最终脚本。 以下 JSON 示例显示了声道的输出，包括用于设置不雅内容筛选器和标点模型的属性。

```json
{
  "recordingsUrl": "https://contoso.com/mystoragelocation",
  "models": [],
  "locale": "en-US",
  "name": "Transcription using locale en-US",
  "description": "An optional description of the transcription.",
  "properties": {
    "ProfanityFilterMode": "Masked",
    "PunctuationMode": "DictatedAndAutomatic"
  },
```

> [!NOTE]
> Batch 听录 API 使用 REST 服务来请求听录内容、听录状态和相关结果。 可在任何语言中使用该 API。 下一部分将介绍该 API 的用法。

### <a name="query-parameters"></a>查询参数

可将以下参数包含在 REST 请求的查询字符串中。

| 参数 | 说明 | 必需/可选 |
|-----------|-------------|---------------------|
| `ProfanityFilterMode` | 指定如何处理识别结果中的不雅内容。 接受的值为 `none`（禁用不雅内容筛选）、`masked`（将不雅内容替换为星号）、`removed`（从结果中删除所有不雅内容）或 `tags`（添加“不雅内容”标记）。 默认设置是 `masked`。 | 可选 |
| `PunctuationMode` | 指定如何处理识别结果中的标点。 接受的值为 `none`（禁用标点）、`dictated`（表示使用显式标点）、`automatic`（允许解码器处理标点）或 `dictatedandautomatic`（表示使用专用标点符号或自动使用标点）。 | 可选 |


## <a name="authorization-token"></a>授权令牌

与语音服务的其他所有功能一样，需要按照[入门指南](get-started.md)通过 [Azure 门户](https://portal.azure.com)创建订阅密钥。 如果计划从基线模型获取听录，则需要创建一个密钥。 

如果你打算自定义某个模型并使用该模型，请执行以下操作，将订阅密钥添加到自定义语音门户：

1. 登录到[自定义语音](https://customspeech.ai)。

2. 在右上角选择“订阅”。

3. 选择“连接现有订阅”。

4. 在弹出窗口中，添加订阅密钥和别名。

    ![“添加订阅”窗口](media/stt/Subscriptions.jpg)

5. 将该密钥复制并粘贴到以下示例的客户端代码中。

> [!NOTE]
> 如果打算使用自定义模型，则还需要该模型的 ID。 此 ID 不是“终结点详细信息”视图中显示的终结点 ID。 它是在选择该模型的详细信息时可以检索的模型 ID。

## <a name="sample-code"></a>代码示例

使用订阅密钥和 API 密钥自定义以下示例代码。 执行此操作可获取持有者令牌。

```cs
     public static CrisClient CreateApiV2Client(string key, string hostName, int port)

        {
            var client = new HttpClient();
            client.Timeout = TimeSpan.FromMinutes(25);
            client.BaseAddress = new UriBuilder(Uri.UriSchemeHttps, hostName, port).Uri;
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);
         
            return new CrisClient(client);
        }
```

获取令牌后，指定指向需要听录的音频文件的 SAS URI。 剩余代码将循环访问状态并显示结果。 首先，如以下代码片段中所示，设置要使用的密钥、区域和模型以及 SA。 接下来，实例化客户端和 POST 请求。 

```cs
            private const string SubscriptionKey = "<your Speech subscription key>";
            private const string HostName = "westus.cris.ai";
            private const int Port = 443;
    
            // SAS URI 
            private const string RecordingsBlobUri = "SAS URI pointing to the file in Azure Blob Storage";

            // adapted model Ids
            private static Guid AdaptedAcousticId = new Guid("guid of the acoustic adaptation model");
            private static Guid AdaptedLanguageId = new Guid("guid of the language model");

            // Creating a Batch Transcription API Client
            var client = CrisClient.CreateApiV2Client(SubscriptionKey, HostName, Port);
            
            var transcriptionLocation = await client.PostTranscriptionAsync(Name, Description, Locale, new Uri(RecordingsBlobUri), new[] { AdaptedAcousticId, AdaptedLanguageId }).ConfigureAwait(false);
```

发出请求后，可以查询并下载听录结果，如以下代码片段中所示：

```cs
  
            // get all transcriptions for the user
            transcriptions = await client.GetTranscriptionAsync().ConfigureAwait(false);

            // for each transcription in the list we check the status
            foreach (var transcription in transcriptions)
            {
                switch(transcription.Status)
                {
                    case "Failed":
                    case "Succeeded":

                            // we check to see if it was one of the transcriptions we created from this client.
                        if (!createdTranscriptions.Contains(transcription.Id))
                        {
                            // not created from here, continue
                            continue;
                        }
                            
                        completed++;
                            
                        // if the transcription was successful, check the results
                        if (transcription.Status == "Succeeded")
                        {
                            var resultsUri = transcription.ResultsUrls["channel_0"];
                            WebClient webClient = new WebClient();
                            var filename = Path.GetTempFileName();
                            webClient.DownloadFile(resultsUri, filename);
                            var results = File.ReadAllText(filename);
                            Console.WriteLine("Transcription succeeded. Results: ");
                            Console.WriteLine(results);
                        }
                    
                    break;
                    case "Running":
                    running++;
                     break;
                    case "NotStarted":
                    notStarted++;
                    break;
                    
                    }
                }
            }
        }
```

有关上述调用的完整详细信息，请参阅 [swagger 文档](https://westus.cris.ai/swagger/ui/index)。 有关此处所示的完整示例，请转到 [GitHub](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI)。

> [!NOTE]
> 在上述代码中，订阅密钥取自在 Azure 门户上创建的语音资源。 从自定义语音服务资源获取的密钥不起作用。

请注意用于发布音频和接收听录状态的异步设置。 创建的客户端是一个 .NET HTTP 客户端。 `PostTranscriptions` 方法用于发送音频文件详细信息，`GetTranscriptions` 方法用于接收结果。 `PostTranscriptions` 返回句柄，`GetTranscriptions` 使用此句柄创建一个句柄来获取听录状态。

当前示例代码未指定任何自定义模型。 该服务使用基线模型来听录一个或多个文件。 若要指定模型，可为声学和语言模型传递与模型 ID 相同的方法。 

如果不想使用基线，请为声学和语言模型传递模型 ID。

> [!NOTE]
> 对于基线听录，无需声明基线模型的终结点。 若要使用自定义模型，请提供其终结点 ID 作为[样本](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI)。 若要使用具有基线语言模型的声学基线，只需声明自定义模型的终结点 ID 即可。 Microsoft 将检测合作伙伴基线模型（无论是声学还是语言模型），并使用它来满足听录请求。

### <a name="supported-storage"></a>支持的存储

目前仅支持 Azure Blob 存储。

## <a name="download-the-sample"></a>下载示例

可在 [GitHub](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI) 上找到本文中的示例。

> [!NOTE]
> 一般情况下，音频听录所需的时间跨度等于音频文件的持续时间再加上 2 到 3 分钟。

## <a name="next-steps"></a>后续步骤

* [获取语音试用订阅](https://azure.microsoft.com/try/cognitive-services/)
