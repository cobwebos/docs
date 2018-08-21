---
title: 使用 Azure 视频索引器上传视频和编制视频索引 | Microsoft Docs
description: 本主题演示如何通过 Azure 视频索引器使用 API 来上传视频和编制视频索引
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 07/25/2018
ms.author: juliako
ms.openlocfilehash: c4a755d0c13516ce3cb0177cea2ea17e4a3abcbb
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2018
ms.locfileid: "39390963"
---
# <a name="upload-and-index-your-videos"></a>上传视频和编制视频索引  

本文介绍如何通过 Azure 视频索引器使用[上传视频](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) API 来上传视频和编制视频索引， 同时还介绍可以在 API 上设置的用于更改 API 过程和输出的某些参数。

> [!Note]
> 创建视频索引器帐户时，可以选择免费试用帐户（提供特定分钟数的免费索引时间）或付费选项（不受配额的限制）。 <br/>使用免费试用版时，视频索引器为网站用户提供最多 600 分钟的免费索引，为 API 用户提供最多 2400 分钟的免费索引。 <br/>使用付费选项时，可以创建[连接到 Azure 订阅和 Azure 媒体服务帐户](connect-to-azure.md)的视频索引器帐户。 需要为编制索引的分钟数付费，此外还需要支付媒体帐户相关的费用。 

## <a name="configurations-and-params"></a>配置和参数

本部分介绍某些可选参数以及何时需设置它们。

### <a name="externalid"></a>externalID 

此参数用于指定与视频关联的 ID。 此 ID 可以应用到外部“视频内容管理”(VCM) 系统集成。 位于视频索引器门户中的视频可以使用指定的外部 ID 进行搜索。

### <a name="indexingpreset"></a>indexingPreset

如果原始的或外部的记录包含背景噪音，请使用此参数。 此参数用于配置索引编制过程。 可以指定以下值：

- `Default` - 使用音频和视频编制见解的索引和提取见解
- `AudioOnly` - 仅使用音频（忽略视频）编制见解的索引和提取见解
- `DefaultWithNoiseReduction` - 通过音频和视频编制见解的索引和提取见解，同时对音频流应用降噪算法

价格取决于所选索引编制选项。  

### <a name="callbackurl"></a>callbackUrl

一个 POST URL，用于通知索引编制何时完成。 视频索引器向其添加两个查询字符串参数：id 和 state。 例如，如果回调 URL 为“https://test.com/notifyme?projectName=MyProject”，则通知会和其他参数一起发送至“https://test.com/notifyme?projectName=MyProject&id=1234abcd&state=Processed”。

也可在 POST 对视频索引器的调用之前将更多参数添加到 URL，然后这些参数就会包括在回调中。 随后即可在代码中分析查询字符串，并取回查询字符串中的所有指定参数（最初追加到 URL 的数据以及视频索引器提供的信息）。 

### <a name="streamingpereset"></a>streamingPereset

视频上传以后，视频索引器会选择性地对视频进行编码。 接下来会对视频进行索引编制和分析。 当视频索引器分析完以后，你会获得一个包含视频 ID 的通知。  

使用[上传视频](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?)或[重新索引视频](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?) API 时，一个可选的参数是 `streamingPreset`。 如果将 `streamingPereset` 设置为 `Default`、`SingleBitrate` 或 `AdaptiveBitrate`，则会触发编码过程。 索引编制和编码作业完成以后，视频就会发布，这样你就也可以流式传输视频。 要从其流式传输视频的流式处理终结点必须处于“正在运行”状态。

为了运行索引编制和编码作业，[连接到视频索引器帐户的 Azure 媒体服务帐户](connect-to-azure.md)需要预留单位。 有关详细信息，请参阅[缩放媒体处理](https://docs.microsoft.com/azure/media-services/previous/media-services-scale-media-processing-overview)。 由于这些是计算密集型作业，因此强烈建议使用 S3 单位类型。 RU 数定义可以并行运行的最大作业数。 基线建议是 10 个 S3 RU。 

如果只需对视频进行索引，但不需对其进行编码，请将 `streamingPereset` 设置为 `NoStreaming`。

## <a name="code-sample"></a>代码示例

以下 C# 代码片段演示了如何将所有的视频索引器 API 结合使用。

```csharp
public async Task Sample()
{
    var apiUrl = "https://api.videoindexer.ai";
    var location = "westus2";
    var apiKey = "...";

    System.Net.ServicePointManager.SecurityProtocol =
        System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;

    // create the http client
    var handler = new HttpClientHandler();
    handler.AllowAutoRedirect = false;
    var client = new HttpClient(handler);
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);

    // obtain account information and access token
    string queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"generateAccessTokens", "true"},
            {"allowEdit", "true"},
        });
    HttpResponseMessage result = await client.GetAsync($"{apiUrl}/auth/trial/Accounts?{queryParams}");
    var json = await result.Content.ReadAsStringAsync();
    var accounts = JsonConvert.DeserializeObject<AccountContractSlim[]>(json);
    // take the relevant account, here we simply take the first
    var accountInfo = accounts.First();

    // we will use the access token from here on, no need for the apim key
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // upload a video
    var content = new MultipartFormDataContent();
    Debug.WriteLine("Uploading...");
    // get the video from URL
    var videoUrl = "VIDEO_URL"; // replace with the video URL

    // as an alternative to specifying video URL, you can upload a file.
    // remove the videoUrl parameter from the query params below and add the following lines:
    //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
    //byte[] buffer =newbyte[video.Length];
    //video.Read(buffer, 0, buffer.Length);
    //content.Add(newByteArrayContent(buffer));

    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", accountInfo.AccessToken},
            {"name", "video_name"},
            {"description", "video_description"},
            {"privacy", "private"},
            {"partition", "partition"},
            {"videoUrl", videoUrl},
        });
    var uploadRequestResult = await client.PostAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos?{queryParams}", content);
    var uploadResult = await uploadRequestResult.Content.ReadAsStringAsync();

    // get the video id from the upload result
    string videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
    Debug.WriteLine("Uploaded");
    Debug.WriteLine("Video ID:");
    Debug.WriteLine(videoId);

    // wait for the video index to finish
    while (true)
    {
        await Task.Delay(10000);

        queryParams = CreateQueryString(
            new Dictionary<string, string>()
            {
                {"accessToken", accountInfo.AccessToken},
                {"language", "English"},
            });

        var videoGetIndexRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/Index?{queryParams}");
        var videoGetIndexResult = await videoGetIndexRequestResult.Content.ReadAsStringAsync();

        string processingState = JsonConvert.DeserializeObject<dynamic>(videoGetIndexResult)["state"];

        Debug.WriteLine("");
        Debug.WriteLine("State:");
        Debug.WriteLine(processingState);

        // job is finished
        if (processingState != "Uploaded" && processingState != "Processing")
        {
            Debug.WriteLine("");
            Debug.WriteLine("Full JSON:");
            Debug.WriteLine(videoGetIndexResult);
            break;
        }
    }

    // search for the video
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", accountInfo.AccessToken},
            {"id", videoId},
        });

    var searchRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/Search?{queryParams}");
    var searchResult = await searchRequestResult.Content.ReadAsStringAsync();
    Debug.WriteLine("");
    Debug.WriteLine("Search:");
    Debug.WriteLine(searchResult);

    // Generate video access token (used for get widget calls)
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
    var videoTokenRequestResult = await client.GetAsync($"{apiUrl}/auth/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/AccessToken?allowEdit=true");
    var videoAccessToken = (await videoTokenRequestResult.Content.ReadAsStringAsync()).Replace("\"", "");
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // get insights widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
            {"widgetType", "Keywords"},
            {"allowEdit", "true"},
        });
    var insightsWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/InsightsWidget?{queryParams}");
    var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
    Debug.WriteLine("Insights Widget url:");
    Debug.WriteLine(insightsWidgetLink);

    // get player widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
        });
    var playerWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/PlayerWidget?{queryParams}");
    var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
    Debug.WriteLine("");
    Debug.WriteLine("Player Widget url:");
    Debug.WriteLine(playerWidgetLink);
}

private string CreateQueryString(IDictionary<string, string> parameters)
{
    var queryParameters = HttpUtility.ParseQueryString(string.Empty);
    foreach (var parameter in parameters)
    {
        queryParameters[parameter.Key] = parameter.Value;
    }

    return queryParameters.ToString();
}

public class AccountContractSlim
{
    public Guid Id { get; set; }
    public string Name { get; set; }
    public string Location { get; set; }
    public string AccountType { get; set; }
    public string Url { get; set; }
    public string AccessToken { get; set; }
}
```



## <a name="next-steps"></a>后续步骤

[探讨 v2 API 生成的 Azure 视频索引器输出](video-indexer-output-json-v2.md)
