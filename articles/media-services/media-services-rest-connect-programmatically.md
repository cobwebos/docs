---
title: "使用 REST API 连接到媒体服务帐户 | Microsoft Docs"
description: "本主题演示如何使用 REST API 连接到媒体服务。"
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 79dc64f1-15d8-4a81-b9d9-3d3c44d2e1e8
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/26/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9ec07aacbde3f218d7a1bbddadad7552e737e1ea


---
# <a name="connecting-to-media-services-account-using-media-services-rest-api"></a>使用媒体服务 REST API 连接到媒体服务帐户
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-connect-programmatically.md)
> * [REST](media-services-rest-connect-programmatically.md)
> 
> 

本主题介绍如何在使用媒体服务 REST API 编程时获取与 Microsoft Azure 媒体服务的编程连接。

访问 Microsoft Azure 媒体服务时需要以下两项内容：由 Azure 访问控制服务 (ACS) 提供的访问令牌和媒体服务本身的 URI。 在创建这些请求时，可以使用任何想要的方法，前提是在调用媒体服务时指定了正确的标头值，并且正确地传入了访问令牌。

以下步骤描述了在使用媒体服务 REST API 连接到媒体服务时运用的最常见工作流：

1. 获取访问令牌 
2. 连接到媒体服务 URI 
   
   > [!NOTE]
   > 成功连接到 https://media.windows.net 后，将收到指定另一个媒体服务 URI 的 301 重定向。 你必须对这个新 URI 进行后续调用。
   > 你还可能会收到包含 ODATA API 元数据说明的 HTTP/1.1 200 响应。
   > 
   > 
3. 将后续 API 调用发布到新的 URL。 
   
    例如，如果在尝试连接后收到以下消息：
   
        HTTP/1.1 301 Moved Permanently
        Location: https://wamsbayclus001rest-hs.cloudapp.net/api/
   
    应该将后续 API 调用发布到 https://wamsbayclus001rest-hs.cloudapp.net/api/。

## <a name="access-control-address"></a>访问控制地址
媒体服务访问控制地址是 https://wamsprodglobal001acs.accesscontrol.windows.net（中国北部地区除外，该地区的地址是 https://wamsprodglobal001acs.accesscontrol.chinacloudapi.cn）。

## <a name="getting-an-access-token"></a>获取访问令牌
若要直接通过 REST API 访问媒体服务，请从 ACS 检索一个访问令牌，并在向该服务提出每个 HTTP 请求期间使用该令牌。 此令牌类似于 ACS 基于 HTTP 请求标头中的访问声明提供的其他令牌，并且使用 OAuth v2 协议。 在直接连接到媒体服务之前，你不需要满足任何其他先决条件。

以下示例显示了用于检索令牌的 HTTP 请求标头和正文。

**标头**：

    POST https://wamsprodglobal001acs.accesscontrol.windows.net/v2/OAuth2-13 HTTP/1.1
    Content-Type: application/x-www-form-urlencoded
    Host: wamsprodglobal001acs.accesscontrol.windows.net
    Content-Length: 120
    Expect: 100-continue
    Connection: Keep-Alive
    Accept: application/json


**正文**：

需要在此请求的正文中提供 client_id 和 client_secret 值；client_id 和 client_secret 分别对应于 AccountName 和 AccountKey 值。 在你设置帐户时，媒体服务将提供这些值。 

请注意，当用作访问令牌请求中的 client_secret 值时，媒体服务帐户的 AccountKey 必须进行 URL 编码（请参阅[百分号编码](http://tools.ietf.org/html/rfc3986#section-2.1)）。

    grant_type=client_credentials&client_id=ams_account_name&client_secret=URL_encoded_ams_account_key&scope=urn%3aWindowsAzureMediaServices


例如： 

    grant_type=client_credentials&client_id=amstestaccount001&client_secret=wUNbKhNj07oqjqU3Ah9R9f4kqTJ9avPpfe6Pk3YZ7ng%3d&scope=urn%3aWindowsAzureMediaServices


以下示例显示了在响应正文中包含访问令牌的 HTTP 响应。

    HTTP/1.1 200 OK
    Cache-Control: no-cache, no-store
    Pragma: no-cache
    Content-Type: application/json; charset=utf-8
    Expires: -1
    request-id: a65150f5-2784-4a01-a4b7-33456187ad83
    X-Content-Type-Options: nosniff
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Thu, 15 Jan 2015 08:07:20 GMT
    Content-Length: 670

    {  
       "token_type":"http://schemas.xmlsoap.org/ws/2009/11/swt-token-profile-1.0",
       "access_token":"http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f19258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421330840&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=uf69n82KlqZmkJDNxhJkOxpyIpA2HDyeGUTtSnq1vlE%3d",
       "expires_in":"21600",
       "scope":"urn:WindowsAzureMediaServices"
    }


> [!NOTE]
> 建议在外部存储中缓存“access_token”和“expires_in”值。 以后可以从存储中检索令牌数据，并在你的媒体服务 REST API 调用中重新使用。 这对于令牌可以在多个进程或多台计算机之间安全共享的方案尤其有用。
> 
> 

确保监视访问令牌的“expires_in”值，并在必要时使用新令牌更新你的 REST API 调用。

### <a name="connecting-to-the-media-services-uri"></a>连接到媒体服务 URI
媒体服务的根 URI 为 https://media.windows.net/。 你最初应连接到此 URI，如果在响应中收到“301 重定向”，则应随后调用新 URI。 此外，请勿在请求中使用任何自动重定向/跟踪逻辑。 HTTP 谓词和请求正文将不会转发到新 URI。

请注意，用于上传和下载资产文件的根 URI 为 https://yourstorageaccount.blob.core.windows.net/，其中的存储帐户名为你在媒体服务帐户设置期间使用的同一帐户名。

以下示例演示了对媒体服务根 URI (https://media.windows.net/) 发出的 HTTP 请求。 该请求将在响应中获取 301 重定向。 后续请求使用新的 URI (https://wamsbayclus001rest-hs.cloudapp.net/api/)。     

**HTTP 请求**：

    GET https://media.windows.net/ HTTP/1.1
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f19258-6753-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421500579&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=ElVWXOnMVggFQl%2ft9vhdcv1qH1n%2fE8l3hRef4zPmrzg%3d
    x-ms-version: 2.11
    Accept: application/json
    Host: media.windows.net


**HTTP 响应**：

    HTTP/1.1 301 Moved Permanently
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/
    Server: Microsoft-IIS/8.5
    request-id: 987d7652-497a-44e5-b815-f492e02aef97
    x-ms-request-id: 987d7652-497a-44e5-b815-f492e02aef97
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sat, 17 Jan 2015 07:44:53 GMT
    Content-Length: 164

    <html><head><title>Object moved</title></head><body>
    <h2>Object moved to <a href="https://wamsbayclus001rest-hs.cloudapp.net/api/">here</a>.</h2>
    </body></html>


**HTTP 请求**（使用新 URI）：

    GET https://wamsbayclus001rest-hs.cloudapp.net/api/ HTTP/1.1
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f19258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421500579&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=ElVWXOnMVggFQl%2ft9vhdcv1qH1n%2fE8l3hRef4zPmrzg%3d
    x-ms-version: 2.11
    Accept: application/json
    Host: wamsbayclus001rest-hs.cloudapp.net


**HTTP 响应**：

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 1250
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 5f52ea9d-177e-48fe-9709-24953d19f84a
    x-ms-request-id: 5f52ea9d-177e-48fe-9709-24953d19f84a
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sat, 17 Jan 2015 07:44:52 GMT

    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata","value":[{"name":"AccessPolicies","url":"AccessPolicies"},{"name":"Locators","url":"Locators"},{"name":"ContentKeys","url":"ContentKeys"},{"name":"ContentKeyAuthorizationPolicyOptions","url":"ContentKeyAuthorizationPolicyOptions"},{"name":"ContentKeyAuthorizationPolicies","url":"ContentKeyAuthorizationPolicies"},{"name":"Files","url":"Files"},{"name":"Assets","url":"Assets"},{"name":"AssetDeliveryPolicies","url":"AssetDeliveryPolicies"},{"name":"IngestManifestFiles","url":"IngestManifestFiles"},{"name":"IngestManifestAssets","url":"IngestManifestAssets"},{"name":"IngestManifests","url":"IngestManifests"},{"name":"StorageAccounts","url":"StorageAccounts"},{"name":"Tasks","url":"Tasks"},{"name":"NotificationEndPoints","url":"NotificationEndPoints"},{"name":"Jobs","url":"Jobs"},{"name":"TaskTemplates","url":"TaskTemplates"},{"name":"JobTemplates","url":"JobTemplates"},{"name":"MediaProcessors","url":"MediaProcessors"},{"name":"EncodingReservedUnitTypes","url":"EncodingReservedUnitTypes"},{"name":"Operations","url":"Operations"},{"name":"StreamingEndpoints","url":"StreamingEndpoints"},{"name":"Channels","url":"Channels"},{"name":"Programs","url":"Programs"}]}



> [!NOTE]
> 获取新 URI 后，应该使用该 URI 来与媒体服务通信。 
> 
> 

## <a name="media-services-learning-paths"></a>媒体服务学习路径
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Nov16_HO3-->


