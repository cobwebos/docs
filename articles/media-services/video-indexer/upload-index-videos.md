---
title: 使用视频索引器上传视频和编制视频索引
titlesuffix: Azure Media Services
description: 本主题演示如何通过视频索引器使用 API 来上传视频和编制视频索引。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 03/05/2019
ms.author: juliako
ms.openlocfilehash: f9bf23094f47f5c200f7a02f81a8e185f469c580
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2019
ms.locfileid: "58516954"
---
# <a name="upload-and-index-your-videos"></a>上传视频和编制视频索引  

使用视频索引器 API 上传视频时，有以下上传选项： 

* 从 URL 上传视频（首选），
* 作为请求正文中的字节数组发送视频文件。
* 提供[资产 ID](https://docs.microsoft.com/azure/media-services/latest/assets-concept)，以便使用现有的 Azure 媒体服务资产（仅付费帐户支持此功能）。

本文介绍如何基于 URL 使用[上传视频](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) API 来上传视频和编制视频索引。 本文中的代码示例包括注释掉的代码，该代码显示了如何上传字节数组。 <br/>本文还介绍可以在 API 上设置的用于更改 API 过程和输出的某些参数。

视频上传以后，视频索引器会选择性地对视频进行编码（在本文中介绍）。 创建视频索引器帐户时，可以选择免费试用帐户（提供特定分钟数的免费索引时间）或付费选项（不受配额的限制）。 使用免费试用版时，视频索引器为网站用户提供最多 600 分钟的免费索引，为 API 用户提供最多 2400 分钟的免费索引。 使用付费选项时，可以[创建连接到 Azure 订阅和 Azure 媒体服务帐户的视频索引器帐户](connect-to-azure.md)。 需要为编制索引的分钟数付费，此外还需要支付媒体帐户相关的费用。 

## <a name="uploading-considerations"></a>上传注意事项

- 根据 URL（首选方式）上传视频时，必须使用 TLS 1.2（或更高版本）保护终结点
- 具有 URL 选项上传大小被限制为 30 GB
- 在大多数浏览器 URL 长度是限制为 2000年个字符
- 对于字节数组选项，上传大小限制为 2GB
- 字节组选项会在 30 分钟后超时
- 需要对 `videoURL` 参数中提供的 URL 进行编码
- 索引编制媒体服务资产具有与索引从 URL 相同的限制
- 视频索引器的最大持续时间限制为 4 小时为单个文件

> [!Tip]
> 建议使用 .NET framework 版本 4.6.2. 或更高版本，因为较旧的 .NET framework 不会默认为 TLS 1.2。
>
> 如果必须使用较旧的 .NET framework，请在进行 REST API 调用之前在代码中添加一行：  <br/> System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls12;

## <a name="configurations-and-params"></a>配置和参数

本部分介绍某些可选参数以及何时需设置它们。

### <a name="externalid"></a>externalID 

此参数用于指定与视频关联的 ID。 此 ID 可以应用到外部“视频内容管理”(VCM) 系统集成。 位于视频索引器门户中的视频可以使用指定的外部 ID 进行搜索。

### <a name="callbackurl"></a>callbackUrl

一个 URL，用于通知客户（使用 POST 请求）以下事件：

- 索引状态更改： 
    - 属性:    
    
        |名称|描述|
        |---|---|
        |id|视频 ID|
        |省/自治区/直辖市|视频状态|  
    - 示例： https://test.com/notifyme?projectName=MyProject&id=1234abcd&state=Processed
- 在视频中标识的人：
  - 属性
    
      |名称|描述|
      |---|---|
      |id| 视频 ID|
      |faceId|出现在视频索引中的人脸 ID|
      |knownPersonId|在人脸模型中唯一的个人 ID|
      |personName|人名|
        
    - 示例： https://test.com/notifyme?projectName=MyProject&id=1234abcd&faceid=12&knownPersonId=CCA84350-89B7-4262-861C-3CAC796542A5&personName=Inigo_Montoya 

#### <a name="notes"></a>说明

- 视频索引器返回在原始 URL 中提供的任何现有参数。
- 提供的 URL 必须进行编码。

### <a name="indexingpreset"></a>indexingPreset

如果原始的或外部的记录包含背景噪音，请使用此参数。 此参数用于配置索引编制过程。 可以指定以下值：

- `Default` - 使用音频和视频编制见解的索引和提取见解
- `AudioOnly` - 仅使用音频（忽略视频）编制见解的索引和提取见解
- `DefaultWithNoiseReduction` - 通过音频和视频编制见解的索引和提取见解，同时对音频流应用降噪算法

价格取决于所选索引编制选项。  

### <a name="priority"></a>优先级

视频由视频索引器根据优先级进行索引。 使用 **priority** 参数指定索引优先级。 以下为有效值：低、正常（默认值）和高。

仅付费帐户支持 **Priority** 参数。

### <a name="streamingpreset"></a>streamingPreset

视频上传以后，视频索引器会选择性地对视频进行编码。 接下来会对视频进行索引编制和分析。 当视频索引器分析完以后，你会获得一个包含视频 ID 的通知。  

使用[上传视频](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?)或[重新索引视频](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?) API 时，一个可选的参数是 `streamingPreset`。 如果将 `streamingPreset` 设置为 `Default`、`SingleBitrate` 或 `AdaptiveBitrate`，则会触发编码过程。 索引编制和编码作业完成以后，视频就会发布，这样你就也可以流式传输视频。 要从其流式传输视频的流式处理终结点必须处于“正在运行”状态。

为了运行索引编制和编码作业，[连接到视频索引器帐户的 Azure 媒体服务帐户](connect-to-azure.md)需要预留单位。 有关详细信息，请参阅[缩放媒体处理](https://docs.microsoft.com/azure/media-services/previous/media-services-scale-media-processing-overview)。 由于这些是计算密集型作业，因此强烈建议使用 S3 单位类型。 RU 数定义可以并行运行的最大作业数。 基线建议是 10 个 S3 RU。 

如果只需对视频进行索引，但不需对其进行编码，请将 `streamingPreset` 设置为 `NoStreaming`。

### <a name="videourl"></a>videoUrl

要编制索引的视频/音频文件的 URL。 该 URL 必须指向媒体文件（不支持 HTML 页面）。 该文件可以通过作为 URI 的一部分提供的访问令牌进行保护，并且为该文件提供服务的终结点必须使用 TLS 1.2 或更高版本进行保护。 需要对 URL 进行编码。 

如果未指定 `videoUrl`，则视频索引器希望你将文件作为多部分/表单正文内容传递。

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
## <a name="common-errors"></a>常见错误

上传操作可能会返回下表中列出的状态代码。

|状态代码|ErrorType（在响应正文中）|描述|
|---|---|---|
|400|VIDEO_ALREADY_IN_PROGRESS|相同的视频已在给定帐户的处理进度中。|
|400|VIDEO_ALREADY_FAILED|不到 2 小时前，相同的视频已在给定帐户中处理失败。 API 客户端应至少等待 2 小时才能重新上传视频。|

## <a name="next-steps"></a>后续步骤

[探讨 v2 API 生成的 Azure 视频索引器输出](video-indexer-output-json-v2.md)
