---
title: Azure Batch 听录 API
description: 示例
services: cognitive-services
author: PanosPeriorellis
ms.service: cognitive-services
ms.technology: Speech to Text
ms.topic: article
ms.date: 04/26/2018
ms.author: panosper
ms.openlocfilehash: 5af829ca076b39758973c28a44d918b9ba5782b1
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2018
ms.locfileid: "42351244"
---
# <a name="batch-transcription"></a>批量听录

Batch 听录非常适合处理大量的音频。 可以指向音频文件并以异步模式返回听录内容。

## <a name="batch-transcription-api"></a>批量听录 API

Batch 听录 API 提供异步语音转文本听录和其他功能。

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

与统一语音服务的其他所有功能一样，需要通过 [Azure 门户](https://portal.azure.com)创建订阅密钥。 此外，可以从语音门户获取 API 密钥： 

1. 登录到[自定义语音](https://customspeech.ai)。

2. 选择“订阅”。

3. 选择“生成 API 密钥”。

    ![自定义语音订阅页的屏幕截图](media/stt/Subscriptions.jpg)

4. 将该密钥复制并粘贴到以下示例的客户端代码中。

> [!NOTE]
> 如果打算使用自定义模型，则还需要该模型的 ID。 请注意，这不是“终结点详细信息”视图中显示的部署 ID 或终结点 ID。 它是在选择该模型的详细信息时可以检索的模型 ID。

## <a name="sample-code"></a>代码示例

使用订阅密钥和 API 密钥自定义以下示例代码。 这样，便可以获取持有者令牌。

```cs
    public static async Task<CrisClient> CreateApiV1ClientAsync(string username, string key, string hostName, int port)
        {
            var client = new HttpClient();
            client.Timeout = TimeSpan.FromMinutes(25);
            client.BaseAddress = new UriBuilder(Uri.UriSchemeHttps, hostName, port).Uri;

            var tokenProviderPath = "/oauth/ctoken";
            var clientToken = await CreateClientTokenAsync(client, hostName, port, tokenProviderPath, username, key).ConfigureAwait(false);
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("bearer", clientToken.AccessToken);

            return new CrisClient(client);
        }
```

获取令牌后，必须指定指向需要听录的音频文件的 SAS URI。 剩余代码将循环访问状态并显示结果。

```cs
   static async Task TranscribeAsync()
        { 
            // Creating a Batch transcription API Client
            var client = 
                await CrisClient.CreateApiV1ClientAsync(
                    "<your msa>", // MSA email
                    "<your api key>", // API key
                    "stt.speech.microsoft.com",
                    443).ConfigureAwait(false);
            
            var newLocation = 
                await client.PostTranscriptionAsync(
                    "<selected locale i.e. en-us>", // Locale 
                    "<your subscription key>", // Subscription Key
                    new Uri("<SAS URI to your file>")).ConfigureAwait(false);

            var transcription = await client.GetTranscriptionAsync(newLocation).ConfigureAwait(false);

            while (true)
            {
                transcription = await client.GetTranscriptionAsync(transcription.Id).ConfigureAwait(false);

                if (transcription.Status == "Failed" || transcription.Status == "Succeeded")
                {
                    Console.WriteLine("Transcription complete!");

                    if (transcription.Status == "Succeeded")
                    {
                        var resultsUri = transcription.ResultsUrls["channel_0"];

                        WebClient webClient = new WebClient();

                        var filename = Path.GetTempFileName();
                        webClient.DownloadFile(resultsUri, filename);

                        var results = File.ReadAllText(filename);
                        Console.WriteLine(results);
                    }

                    await client.DeleteTranscriptionAsync(transcription.Id).ConfigureAwait(false);

                    break;
                }
                else
                {
                    Console.WriteLine("Transcription status: " + transcription.Status);
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);
            }

            Console.ReadLine();
        }
```

> [!NOTE]
> 在上述代码中，订阅密钥取自在 Azure 门户上创建的 Speech(Preview) 资源。 从自定义语音服务资源获取的密钥不起作用。

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
