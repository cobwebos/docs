---
title: 使用 Azure 视频索引器 API | Microsoft Docs
description: 本文介绍如何开始使用视频索引器 API。
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 07/25/2018
ms.author: juliako
ms.openlocfilehash: 73359955861b88f2bc5ca297c32fa78c2632148c
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39449456"
---
# <a name="use-azure-video-indexer-api"></a>使用 Azure 视频索引器 API

> [!Note]
> 视频索引器 V1 API 已于 2018 年 8 月 1 日弃用。 从现在起，应该使用视频索引器 v2 API。 <br/>若要使用视频索引器 v2 API 进行开发，请参阅[此处](https://api-portal.videoindexer.ai/)的说明。 

视频索引器在一个集成式服务中整合了 Microsoft 提供的多种音频和视频人工智能 (AI) 技术，使开发变得更简单。 API 可让开发人员专注于使用媒体 AI 技术，而无需考虑云平台的规模、全球覆盖度、可用性和可靠性的问题。 可以使用 API 来上传文件、获取详细的视频见解、获取见解和播放器小组件的 URL 以将其嵌入应用程序，以及执行其他任务。

创建视频索引器帐户时，可以选择免费试用帐户（提供特定分钟数的免费索引时间）或付费选项（不受配额的限制）。 使用免费试用版时，视频索引器为网站用户提供最多 600 分钟的免费索引，为 API 用户提供最多 2400 分钟的免费索引。 使用付费选项时，可以创建[连接到 Azure 订阅和 Azure 媒体服务帐户](connect-to-azure.md)的视频索引器帐户。 需要为编制索引的分钟数付费，此外还需要支付媒体帐户相关的费用。 

本文介绍开发人员可以如何利用[视频索引器 API](https://api-portal.videoindexer.ai/)。 有关视频索引器服务的更详细概述，请参阅[概述](video-indexer-overview.md)文章。

## <a name="subscribe-to-the-api"></a>订阅 API

1. 登录。

    若要开始使用视频索引器进行开发，必须先登录到[视频索引器](https://api-portal.videoindexer.ai/)门户。 
    
    ![注册](./media/video-indexer-use-apis/video-indexer-api01.png)

    > [!Important]
    > * 必须使用注册视频索引器时所用的同一提供程序。
    > * 只能使用 Google 和 Microsoft (Outlook/Live) 个人帐户作为试用帐户。 连接到 Azure 的帐户需要 Azure AD。
    > * 只能为电子邮件地址提供一个活动帐户。 如果用户尝试使用 user@gmail.com 进行 LinkedIn 登录，然后又使用 user@gmail.com 进行 Google 登录，则后一次登录时会显示错误页，指出该用户已存在。


2. 订阅。

    选择[产品](https://api-portal.videoindexer.ai/products)选项卡。然后选择“授权”并订阅。 
    
    ![注册](./media/video-indexer-use-apis/video-indexer-api02.png)

    > [!NOTE]
    > 新用户已自动订阅“授权”。
    
    订阅后，可以看到自己的订阅以及主要和辅助密钥。 密钥应受保护。 密钥应仅供服务器代码使用。 在客户端（.js、.html 等）应该不可使用密钥。

    ![注册](./media/video-indexer-use-apis/video-indexer-api03.png)

## <a name="obtain-access-token-using-the-authorization-api"></a>使用授权 API 获取访问令牌

订阅授权 API 后，可以获取访问令牌。 这些访问令牌用于对操作 API 进行身份验证。 

对操作 API 的每个调用应该与匹配调用授权范围的访问令牌相关联。

- 用户级别 - 使用用户级访问令牌可在**用户**级别执行操作。 例如，获取关联的帐户。
- 帐户级别 – 使用帐户级访问令牌可在**帐户**级别或**视频**级别执行操作。 例如，上传视频、列出所有视频、获取视频见解，等等。
- 视频级别 – 使用视频级访问令牌可对特定的**视频**执行操作。 例如，获取视频见解、下载隐藏式字幕、获取小组件，等等。 

可以通过指定 **allowEdit=true/false**，来控制这些令牌是只读的还是可编辑的。

对于大多数的服务器到服务器方案，也许可以使用相同的**帐户**令牌，因为它同时涵盖了**帐户**操作和**视频**操作。 但是，若要对视频索引器发出客户端调用（例如，从 javascript），可能需要使用**视频**访问令牌，以防止客户端访问整个帐户。 这也是在客户端中嵌入 VideoIndexer 客户端代码（例如，使用“获取见解小组件”或“获取播放器小组件”）时，必须提供**视频**访问令牌的原因。

为简化操作，可以使用**授权** API > **GetAccounts** 来获取帐户，而不是先获取用户令牌。 还可以请求获取具有有效令牌的帐户，以便跳过附加的调用即可获取帐户令牌。

访问令牌在 1 小时后过期。 在使用操作 API 之前，请确保访问令牌有效。 如果令牌已过期，请再次调用授权 API 来获取新访问令牌。
 
现在，便可以开始与 API 集成。 查找[每个视频索引器 REST API 的详细说明](http://api-portal.videoindexer.ai/)。

## <a name="location"></a>位置

所有操作 API 都需要一个 Location 参数，该参数指示要将调用路由到的，并在其中创建帐户的区域。

下表中所述的值适用。 “参数值”是使用 API 时传递的值。

|**Name**|**参数值**|**说明**|
|---|---|---|
|试用|试用|表示试用帐户。|
|美国西部|westus2|表示 Azure 美国西部 2 区域。|
|北欧 |northeurope|表示 Azure 北欧区域。|
|东亚|eastasia|表示 Azure 东亚区域。|

## <a name="account-id"></a>帐户 ID 

在所有操作 API 调用中都必须指定“帐户 ID”参数。 帐户 ID 是一个 GUID，可通过以下方式之一获取：

* 使用视频索引器门户获取帐户 ID：

    1. 登录到 [videoindexer](https://www.videoindexer.ai/)。
    2. 浏览到“设置”页。
    3. 复制帐户 ID。

        ![帐户 ID](./media/video-indexer-use-apis/account-id.png)

* 使用 API 以编程方式获取帐户 ID。

    使用[获取帐户](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Accounts?) API。
    
    > [!TIP]
    > 可以通过定义 `generateAccessTokens=true` 来生成帐户的访问令牌。
    
* 从帐户中的播放器页 URL 获取帐户 ID。

    观看视频时，该 ID 显示在 `accounts` 节之后、`videos` 节之前。

    ```
    https://www.videoindexer.ai/accounts/00000000-f324-4385-b142-f77dacb0a368/videos/d45bf160b5/
    ```

## <a name="recommendations"></a>建议

本部分列出了有关使用视频索引器 API 的一些建议。

- 若要上传视频，我们建议将文件放在某个公共网络位置（例如 OneDrive）。 获取视频的链接，并提供 URL 作为上传文件参数。 

    提供给视频索引器的 URL 必须指向某个媒体（音频或视频）文件。 OneDrive 生成的某些链接适用于包含该文件的 HTML 页。 验证 URL 的一种简单方式是将其粘贴到浏览器中 – 如果文件开始下载，则它可能是正常的 URL。 如果浏览器中显示某种可视化效果，则该 URL 可能不是文件的链接，而是 HTML 页的链接。
    
- 调用获取指定视频的视频见解的 API 时，会获取详细的 JSON 输出（响应内容）。 [在此主题中查看有关返回的 JSON 的详细信息](video-indexer-output-json.md)。

## <a name="code-sample"></a>代码示例

以下 C# 代码片段演示了如何将所有的视频索引器 API 结合使用。

```csharp
var apiUrl = "https://api.videoindexer.ai";
var accountId = "..."; 
var location = "westus2";
var apiKey = "..."; 

System.Net.ServicePointManager.SecurityProtocol = System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;

// create the http client
var handler = new HttpClientHandler(); 
handler.AllowAutoRedirect = false; 
var client = new HttpClient(handler);
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey); 

// obtain account access token
var accountAccessTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/AccessToken?allowEdit=true").Result;
var accountAccessToken = accountAccessTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

// upload a video
var content = new MultipartFormDataContent();
Debug.WriteLine("Uploading...");
// get the video from URL
var videoUrl = "VIDEO_URL"; // replace with the video URL

// as an alternative to specifying video URL, you can upload a file.
// remove the videoUrl parameter from the query string below and add the following lines:
  //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
  //byte[] buffer =newbyte[video.Length];
  //video.Read(buffer, 0, buffer.Length);
  //content.Add(newByteArrayContent(buffer));

var uploadRequestResult = client.PostAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos?accessToken={accountAccessToken}&name=some_name&description=some_description&privacy=private&partition=some_partition&videoUrl={videoUrl}", content).Result;
var uploadResult = uploadRequestResult.Content.ReadAsStringAsync().Result;

// get the video id from the upload result
var videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
Debug.WriteLine("Uploaded");
Debug.WriteLine("Video ID: " + videoId);           

// obtain video access token            
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
var videoTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/Videos/{videoId}/AccessToken?allowEdit=true").Result;
var videoAccessToken = videoTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

// wait for the video index to finish
while (true)
{
  Thread.Sleep(10000);

  var videoGetIndexRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/Index?accessToken={videoAccessToken}&language=English").Result;
  var videoGetIndexResult = videoGetIndexRequestResult.Content.ReadAsStringAsync().Result;

  var processingState = JsonConvert.DeserializeObject<dynamic>(videoGetIndexResult)["state"];

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
var searchRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/Search?accessToken={accountAccessToken}&id={videoId}").Result;
var searchResult = searchRequestResult.Content.ReadAsStringAsync().Result;
Debug.WriteLine("");
Debug.WriteLine("Search:");
Debug.WriteLine(searchResult);

// get insights widget url
var insightsWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/InsightsWidget?accessToken={videoAccessToken}&widgetType=Keywords&allowEdit=true").Result;
var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
Debug.WriteLine("Insights Widget url:");
Debug.WriteLine(insightsWidgetLink);

// get player widget url
var playerWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/PlayerWidget?accessToken={videoAccessToken}").Result;
var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
Debug.WriteLine("");
Debug.WriteLine("Player Widget url:");
Debug.WriteLine(playerWidgetLink);

```

## <a name="next-steps"></a>后续步骤

[检查输出 JSON 的详细信息](video-indexer-output-json.md)。

## <a name="see-also"></a>另请参阅

[视频索引器概述](video-indexer-overview.md)
