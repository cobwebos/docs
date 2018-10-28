---
title: Azure Batch 听录 API
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
ms.openlocfilehash: e7523bf97d6252422ebb853b818453c935640f50
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2018
ms.locfileid: "49648796"
---
# <a name="batch-transcription"></a>批量听录

如果存储中有大量音频，批量听录是理想的选择。 使用我们的 Rest API，可以通过 SAS URI 指向音频文件并异步接收听录。

## <a name="batch-transcription-api"></a>批量听录 API

Batch 听录 API 提供异步语音转文本听录和其他功能。 它是一种 REST API 公开方法，用于：

1. 创建批处理请求

2. 查询状态 

3. 下载听录

> [!NOTE]
> Batch 听录 API 非常适合经常累积数千小时音频的呼叫中心。 该 API 秉持“用后即焚”理念，能够轻松听录大量的录音。

### <a name="supported-formats"></a>支持的格式

Batch 听录 API 支持以下格式：

名称| 通道  |
----|----------|
mp3 |   Mono   |   
mp3 |  立体声  | 
wav |   Mono   |
wav |  立体声  |

对于立体声音频流，Batch 听录将在听录期间分离左右声道。 根据单个通道创建两个带有结果的 JSON 文件。 开发人员可利用每个话语的时间戳创建有序的最终脚本。 下面的 JSON 示例显示了通道的输出。

```json
       {
        "recordingsUrl": "https://mystorage.blob.core.windows.net/cris-e2e-datasets/TranscriptionsDataset/small_sentence.wav?st=2018-04-19T15:56:00Z&se=2040-04-21T15:56:00Z&sp=rl&sv=2017-04-17&sr=b&sig=DtvXbMYquDWQ2OkhAenGuyZI%2BYgaa3cyvdQoHKIBGdQ%3D",
        "resultsUrls": {
            "channel_0": "https://mystorage.blob.core.windows.net/bestor-87a0286f-304c-4636-b6bd-b3a96166df28/TranscriptionData/24265e4c-e459-4384-b572-5e3e7795221f?sv=2017-04-17&sr=b&sig=IY2qd%2Fkgtz2PwRe2C88BphH4Hv%2F1VCb1UVJ33xsw%2BEY%3D&se=2018-04-23T14:48:24Z&sp=r"
        },
        "statusMessage": "None.",
        "id": "0bb95356-ff06-469d-acc7-81f9144a269a",
        "createdDateTime": "2018-04-20T14:11:57.167",
        "lastActionDateTime": "2018-04-20T14:12:54.643",
        "status": "Succeeded",
        "locale": "en-US"
    },
```

> [!NOTE]
> 批量听录 API 使用 REST 服务来请求听录内容、听录状态和相关结果。 可在任何语言中使用该 API。 下一部分介绍其使用方法。

## <a name="authorization-token"></a>授权令牌

与语音服务的其他所有功能一样，需要按照[入门指南](get-started.md)通过 [Azure 门户](https://portal.azure.com)创建订阅密钥。 如果计划从基线模型获取听录，则需要进行此操作。 

如果计划自定义和使用自定义模型，则需要将此订阅密钥添加到自定义语音门户，如下所示：

1. 登录到[自定义语音](https://customspeech.ai)。

2. 选择 **订阅**。

3. 选择“连接现有订阅”。

4. 在弹出的视图中添加订阅密钥和别名

    ![自定义语音订阅页的屏幕截图](media/stt/Subscriptions.jpg)

5. 将该密钥复制并粘贴到以下示例的客户端代码中。

> [!NOTE]
> 如果打算使用自定义模型，则还需要该模型的 ID。 请注意，这不是“终结点详细信息”视图中显示的终结点 ID。 它是在选择该模型的详细信息时可以检索的模型 ID。

## <a name="sample-code"></a>代码示例

使用订阅密钥和 API 密钥自定义以下示例代码。 这样，便可以获取持有者令牌。

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

获取令牌后，必须指定指向需要听录的音频文件的 SAS URI。 剩余代码将循环访问状态并显示结果。 最初可以设置要使用的密钥、区域、模型和 SA， 如下面的代码片段所示。 然后对客户端和 POST 请求进行实例化。 

```cs
            private const string SubscriptionKey = "<your Speech subscription key>";
            private const string HostName = "westus.cris.ai";
            private const int Port = 443;
    
            // SAS URI 
            private const string RecordingsBlobUri = "some SAS URI";

            // adapted model Ids
            private static Guid AdaptedAcousticId = new Guid("some guid");
            private static Guid AdaptedLanguageId = new Guid("some guid");

            // Creating a Batch transcription API Client
            var client = CrisClient.CreateApiV2Client(SubscriptionKey, HostName, Port);
            
            var transcriptionLocation = await client.PostTranscriptionAsync(Name, Description, Locale, new Uri(RecordingsBlobUri), new[] { AdaptedAcousticId, AdaptedLanguageId }).ConfigureAwait(false);
```

现在已发出了请求，用户可以查询和下载听录结果，如代码片段所示。

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
                            // not creted form here, continue
                            continue;
                        }
                            
                        completed++;
                            
                        // if the transcription was successfull, check the results
                        if (transcription.Status == "Succeeded")
                        {
                            var resultsUri = transcription.ResultsUrls["channel_0"];
                            WebClient webClient = new WebClient();
                            var filename = Path.GetTempFileName();
                            webClient.DownloadFile(resultsUri, filename);
                            var results = File.ReadAllText(filename);
                            Console.WriteLine("Transcription succedded. Results: ");
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

我们的 [Swagger 文档](https://westus.cris.ai/swagger/ui/index)提供了有关上述调用的完整详细信息。 [GitHub](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI) 中提供了此处所示的完整示例。

> [!NOTE]
> 在上述代码中，订阅密钥取自在 Azure 门户上创建的语音资源。 从自定义语音服务资源获取的密钥不起作用。

请注意用于发布音频和接收听录状态的异步设置。 创建的客户端是 NET HTTP 客户端。 `PostTranscriptions` 方法用于发送音频文件详细信息，`GetTranscriptions` 方法用于接收结果。 `PostTranscriptions` 返回句柄，`GetTranscriptions` 使用此句柄创建一个句柄来获取听录状态。

当前示例代码未指定任何自定义模型。 该服务使用基线模型来听录一个或多个文件。 若要指定模型，可为声学和语言模型传递相同的方法和模型 ID。 

如果不想使用基线，则必须为声学和语言模型传递模型 ID。

> [!NOTE]
> 对于基线听录，无需声明基线模型的终结点。 若要使用自定义模型，请提供其终结点 ID 作为[样本](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI)。 若要使用具有基线语言模型的声学基线，只需声明自定义模型的终结点 ID 即可。 Microsoft 将检测合作伙伴基线模型（无论是声学还是语言模型），并使用它来满足听录请求。

### <a name="supported-storage"></a>支持的存储

目前仅支持 Azure Blob 存储。

## <a name="downloading-the-sample"></a>下载示例

[GitHub](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI) 中提供了本文所示的示例。

> [!NOTE]
> 通常，音频听录所需的时间跨度等于音频文件的持续时间再加上 2-3 分钟。

## <a name="next-steps"></a>后续步骤

* [获取语音试用订阅](https://azure.microsoft.com/try/cognitive-services/)
