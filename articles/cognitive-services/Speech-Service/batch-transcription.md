---
title: Azure 批量听录 API
description: 示例
services: cognitive-services
author: PanosPeriorellis
ms.service: cognitive-services
ms.technology: Speech to Text
ms.topic: article
ms.date: 04/26/2018
ms.author: panosper
ms.openlocfilehash: f21973855ceb3a257627c147490ac50465c54020
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2018
ms.locfileid: "39281933"
---
# <a name="batch-transcription"></a>批量听录

批量听录非常适合具有大量音频的情况。 开发人员可使用它指向音频文件并以异步模式返回听录的内容。

## <a name="batch-transcription-api"></a>批量听录 API

批量听录 API 可实现上述情景。 它提供异步语音转文本听录和其他功能。

> [!NOTE]
> 批量听录 API 非常适合经常累积数千小时音频的呼叫中心。 凭借 API 的“用后即焚”理念，它能轻松听录大量的录音。

### <a name="supported-formats"></a>支持的格式

批量听录 API 旨在实现与线下呼叫中心相关的各种方案，并支持各种相关格式。 当前支持的格式包括：

名称| 通道  |
----|----------|
mp3 |   Mono   |   
mp3 |  立体声  | 
wav |   Mono   |
wav |  立体声  |

对于立体声音频流，批量听录将在听录期间分离左右通道。 根据单个通道创建两个带有结果的 JSON 文件。 开发人员可利用每个话语的时间戳创建有序的最终脚本。 下面的 JSON 示例显示了通道的输出。

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
> 批量听录 API 使用 REST 服务来请求听录内容、听录状态和相关结果。 API 可以用于任何语言。 下一部分介绍其使用方法。

## <a name="authorization-token"></a>授权令牌

与统一语音服务的其他所有功能一样，用户需要在 [Azure 门户](https://portal.azure.com)创建订阅密钥。 此外，还需要从语音门户获取 API 密钥。 生成 API 密钥的步骤如下：

1. 登录 https://customspeech.ai。

2. 单击“订阅”。

3. 单击 `Generate API Key` 选项。

    ![上传视图](media/stt/Subscriptions.jpg)

4. 将该密钥复制粘贴到下方示例的客户端代码中。

> [!NOTE]
> 如果计划使用自定义模型，则还需要该 模型的 ID。 请注意，这不是在“终结点详细信息”视图中找到的部署或终结点 ID，而是单击该模型的详细信息时可检索到的模型 ID

## <a name="sample-code"></a>代码示例

API 的使用方式非常简单。 下面的示例代码需要使用订阅密钥和 API 密钥进行自定义，从而允许开发者获取持有者令牌，如下面的代码片段所示：

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

获取令牌后，开发者必须指定 SAS Uri 指向需要听录的音频文件。 其余代码只是循环访问状态并显示结果。

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
> 上方代码片段中提到的订阅密钥是你在 Azure 门户上创建的语音（预览）资源的密钥。 从自定义语音服务资源获取的密钥将无效。


请注意用于发布音频和接收听录状态的异步设置。 创建的客户端是 NET HTTP 客户端。 `PostTranscriptions` 方法用于发送音频文件详细信息，`GetTranscriptions` 方法用于接收结果。 `PostTranscriptions` 返回句柄，`GetTranscriptions` 方法使用此句柄创建句柄以获取听录状态。

当前示例代码未指定任何自定义模型。 该服务将使用基线模型来听录文件。 如果用户希望指定模型，则可使用同一方法为声学和语言模型传递 modelID。 

如果不想使用基线，则必须为声学和语言模型传递模型 ID。

> [!NOTE]
> 对于基线听录，用户无需声明基线模型的终结点。 如果用户想要使用自定义模型，则必须提供其终结点 ID 作为[示例](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI)。 如果用户想要使用具有基线语言模型的声学基线，只需声明自定义模型的终结点 ID 即可。 系统将在内部找到合作伙伴基线模型（无论是声学还是语言模型），并使用它来满足听录请求。

### <a name="supported-storage"></a>支持的存储

目前仅支持 Azure Blob 存储。

## <a name="downloading-the-sample"></a>下载示例

要查看此处所述示例，请访问 [GitHub](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI)。

> [!NOTE]
> 通常，音频听录所需的时间跨度等于音频文件的持续时间再加上 2-3 分钟。

## <a name="next-steps"></a>后续步骤

* [获取语音试用订阅](https://azure.microsoft.com/try/cognitive-services/)
