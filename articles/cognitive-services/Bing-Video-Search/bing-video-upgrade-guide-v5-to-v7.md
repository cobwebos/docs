---
title: 将必应视频 API v5 升级到 v7 | Microsoft Docs
description: 发现需要更新为使用版本 7 的应用部分。
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: FA7DDF07-97AC-4EBE-8C50-A9737D43B38E
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 62646d026e141d0549c68e18f9318fa32d3e00df
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365480"
---
# <a name="video-search-api-upgrade-guide"></a>视频搜索 API 升级指南

本升级指南确定了必应视频搜索 API 版本 5 与版本 7 的不同之处。 本指南有助于发现需要更新为使用版本 7 的应用部分。

## <a name="breaking-changes"></a>重大变化

### <a name="endpoints"></a>终结点

- 终结点的版本号已从 v5 更改为 v7。 例如，https://api.cognitive.microsoft.com/bing/\*\*v7.0**/videos/search。

### <a name="error-response-objects-and-error-codes"></a>错误响应对象和错误代码

- 现在，所有失败请求的响应正文都应包含 `ErrorResponse` 对象。

- 已将以下字段添加到 `Error` 对象。  
  - `subCode`&mdash;如果可能，将错误代码分入各个独立桶中
  - `moreDetails`&mdash;有关 `message` 字段中错误的更多信息
   

- 已将 v5 错误代码替换为以下可取的 `code` 和 `subCode` 值。

|代码|SubCode|说明
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|只要出现任何子代码条件，必应就会返回 ServerError。 如果 HTTP 状态代码为 500，响应就会包含这些错误。
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>已阻止|只要请求的任何部分无效，必应就会返回 InvalidRequest。 例如，缺少必需参数或参数值无效。<br/><br/>如果错误是 ParameterMissing 或 ParameterInvalidValue，HTTP 状态代码为 400。<br/><br/>如果错误是 HttpNotAllowed，HTTP 状态代码为 410。
|RateLimitExceeded||只要超过每秒查询数 (QPS) 或每月查询数 (QPM) 配额，必应就会返回 RateLimitExceeded。<br/><br/>如果超过 QPS，必应返回 HTTP 状态代码 429；如果超过 QPM，必应返回 HTTP 状态代码 403。
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|当必应无法验证调用方身份时，必应会返回 InvalidAuthorization。 例如，缺少 `Ocp-Apim-Subscription-Key` 头或订阅密钥无效。<br/><br/>如果指定多个身份验证方法，便会发生冗余。<br/><br/>如果错误是 InvalidAuthorization，HTTP 状态代码为 401。
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|当调用方无权访问资源时，必应会返回 InsufficientAuthorization。 如果订阅密钥已遭禁用或到期，就会发生此类错误。 <br/><br/>如果错误是 InsufficientAuthorization，HTTP 状态代码为 403。

- 下表列出了新旧错误代码的对应关系。 如果之前依赖 v5 错误代码，请相应地更新代码。

|版本 5 code|版本 7 code.subCode
|-|-
|RequestParameterMissing|InvalidRequest.ParameterMissing
RequestParameterInvalidValue|InvalidRequest.ParameterInvalidValue
ResourceAccessDenied|InsufficientAuthorization
ExceededVolume|RateLimitExceeded
ExceededQpsLimit|RateLimitExceeded
已禁用|InsufficientAuthorization.AuthorizationDisabled
UnexpectedError|ServerError.UnexpectedError
DataSourceErrors|ServerError.ResourceError
AuthorizationMissing|InvalidAuthorization.AuthorizationMissing
HttpNotAllowed|InvalidRequest.HttpNotAllowed
UserAgentMissing|InvalidRequest.ParameterMissing
NotImplemented|ServerError.NotImplemented
InvalidAuthorization|InvalidAuthorization
InvalidAuthorizationMethod|InvalidAuthorization
MultipleAuthorizationMethod|InvalidAuthorization.AuthorizationRedundancy
ExpiredAuthorizationToken|InsufficientAuthorization.AuthorizationExpired
InsufficientScope|InsufficientAuthorization
已阻止|InvalidRequest.Blocked

### <a name="query-parameters"></a>查询参数

- 已将 `modulesRequested` 查询参数重命名为 [modules](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#modulesrequested)。  

### <a name="object-changes"></a>对象变化

- 已将 [Videos](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos) 的 `nextOffsetAddCount` 字段重命名为 `nextOffset`。 offset 的使用方式也已变化。 以前会将 [offset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#offset) 查询参数设置为，`nextOffset` 值加上前一 offset 值，再加上结果中的视频数。 现在可以直接将 `offset` 查询参数设置为 `nextOffset` 值。  
  
- 已将 `relatedVideos` 字段的数据类型从 `Video[]` 更改为 [VideosModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videosmodule)（请参阅 [VideoDetails](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videodetails)）。

