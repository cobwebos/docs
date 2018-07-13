---
title: 从必应 Web 搜索 API v5 升级到 v7 | Microsoft Docs
description: 标识需要更新以使用版本 7 的应用程序部分。
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: E8827BEB-4379-47CE-B67B-6C81AD7DAEB1
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 01/15/2017
ms.author: scottwhi
ms.openlocfilehash: 155297f230c0ee02d6fa49d6d35eb24d9941f29b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366444"
---
# <a name="web-search-api-upgrade-guide"></a>Web 搜索 API 升级指南

此升级指南介绍了必应 Web 搜索 API 的版本 5 与版本 7 之间的更改。 使用本指南可帮助你识别需要更新以使用版本 7 的应用程序部分。

## <a name="breaking-changes"></a>重大变化

### <a name="endpoints"></a>终结点

- 终结点的版本号已从 v5 更改为 v7。 例如，https:\/\/api.cognitive.microsoft.com/bing/**v7.0**/search。

### <a name="error-response-objects-and-error-codes"></a>错误响应对象和错误代码

- 现在，所有失败的请求都应在响应正文中包含 `ErrorResponse` 对象。

- 将以下字段添加到 `Error` 对象。  
  - `subCode`&mdash;如果可能，将错误代码划分到不同的段中
  - `moreDetails`&mdash;有关 `message` 字段中描述的错误的其他信息
   

- 已将 v5 错误代码替换为以下可取的 `code` 和 `subCode` 值。

|代码|子代码|说明
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|只要出现任何子代码条件，必应就会返回 ServerError。 如果 HTTP 状态代码为 500，则响应将包括这些错误。
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>已阻止|只要请求的任何部分无效，必应就会返回 InvalidRequest。 例如，缺少必需参数或参数值无效。<br/><br/>如果错误是 ParameterMissing 或 ParameterInvalidValue，则 HTTP 状态代码为 400。<br/><br/>如果错误是 HttpNotAllowed，则 HTTP 状态代码为 410。
|RateLimitExceeded||每当超过每秒查询数 (QPS) 或每月查询数 (QPM) 配额时，必应都会返回 RateLimitExceeded。<br/><br/>如果超过 QPS，则必应将返回 HTTP 状态代码 429；如果超过 QPM，则返回 403。
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|在必应无法对调用方进行身份验证时，必应会返回 InvalidAuthorization。 例如，缺少 `Ocp-Apim-Subscription-Key` 标头或订阅密钥无效。<br/><br/>如果指定了多个身份验证方法，则会发生冗余。<br/><br/>如果错误是 InvalidAuthorization，则 HTTP 状态代码为 401。
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|当调用方无权访问资源时，必应会返回 InsufficientAuthorization。 如果订阅密钥已禁用或过期，则会发生此错误。 <br/><br/>如果错误是 InsufficientAuthorization，则 HTTP 状态代码为 403。

- 下表将以前的错误代码映射到新代码。 如果之前依赖于 v5 错误代码，请相应地更新你的代码。

|版本 5 代码|版本 7 代码.子代码
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


## <a name="non-breaking-changes"></a>非重大变更  

### <a name="headers"></a>标头

- 添加可选 [Pragma](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#pragma) 请求标头。 默认情况下，必应返回缓存的内容（如果适用）。 要防止必应返回缓存的内容，请将 Pragma 标头设置为 no-cache（例如，Pragma: no-cache）。

### <a name="query-parameters"></a>查询参数

- 添加 [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#answercount) 查询参数。 使用此参数指定希望响应包含的答案数。 答案根据排名进行选择。 例如，如果将此参数设置为三 (3)，则响应将包括排名前三位的答案。  
  
- 添加 [promote](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#promote) 查询参数。 使用此参数和 `answerCount` 可以显式包含一个或多个答案类型，无论其排名如何。 例如，要将视频和图像升级到响应中，你需要将升级设置为“视频、图片”。 要升级的答案列表不计入 `answerCount` 限制。 例如，如果 `answerCount` 为 2 且 `promote` 设置为“视频，图像”，则响应可能包括网页、新闻、视频和图片。

### <a name="object-changes"></a>对象更改

- 将 `someResultsRemoved` 字段添加到 [WebAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#webanswer) 对象。 该字段包含一个布尔值，指示响应是否从 Web 回答中排除了某些结果。  

