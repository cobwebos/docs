---
title: 将必应自动推荐 API v5 升级到 v7 | Microsoft Docs
description: 标识需要更新以使用版本 7 的应用程序部分。
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 751EDCF0-0C8B-4C23-942C-FA06F5DAD3FD
ms.service: cognitive-services
ms.component: bing-autosuggest
ms.topic: article
ms.date: 01/12/2017
ms.author: scottwhi
ms.openlocfilehash: 5663a671711dba4f44c89e8221a729c6670ec8fc
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366448"
---
# <a name="autosuggest-api-upgrade-guide"></a>自动推荐 API 升级指南

此升级指南介绍了必应自动推荐 API 的版本 5 与版本 7 之间的更改。 使用本指南可帮助你识别需要更新以使用版本 7 的应用程序部分。

## <a name="breaking-changes"></a>重大变化

### <a name="endpoints"></a>终结点

- 终结点的版本号已从 v5 更改为 v7。 例如 https://api.cognitive.microsoft.com/bing/\*\*v7.0\*\*/Suggestions。

### <a name="error-response-objects-and-error-codes"></a>错误响应对象和错误代码

- 现在，所有失败的请求都应在响应正文中包含 `ErrorResponse` 对象。

- 将以下字段添加到 `Error` 对象。  
  - `subCode`&mdash;如果可能，将错误代码划分到不同的段中
  - `moreDetails`&mdash;有关 `message` 字段中描述的错误的其他信息

- 将 v5 错误代码替换成了以下可能的 `code` 和 `subCode` 值。

|代码|子代码|说明
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|只要出现任何子代码条件，必应就会返回 ServerError。 如果 HTTP 状态代码为 500，则响应包括这些错误。
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Blocked|只要请求的任何部分无效，必应就会返回 InvalidRequest。 例如，缺少必需参数或参数值无效。<br/><br/>如果错误是 ParameterMissing 或 ParameterInvalidValue，则 HTTP 状态代码为 400。<br/><br/>如果错误是 HttpNotAllowed，则 HTTP 状态代码为 410。
|RateLimitExceeded||每当超过每秒查询数 (QPS) 或每月查询数 (QPM) 配额时，必应都会返回 RateLimitExceeded。<br/><br/>如果超过 QPS，则必应将返回 HTTP 状态代码 429；如果超过 QPM，则返回 403。
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|在必应无法对调用方进行身份验证时，必应会返回 InvalidAuthorization。 例如，缺少 `Ocp-Apim-Subscription-Key` 标头或订阅密钥无效。<br/><br/>如果指定了多个身份验证方法，则会发生冗余。<br/><br/>如果错误是 InvalidAuthorization，则 HTTP 状态代码为 401。
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|当调用方没有权限访问资源时，必应会返回 InsufficientAuthorization。 如果订阅密钥已禁用或过期，也会发生此情况。 <br/><br/>如果错误是 InsufficientAuthorization，则 HTTP 状态代码为 403。

- 下表将以前的错误代码映射到新代码。 如果之前依赖于 v5 错误代码，请相应地更新你的代码。

|版本 5 代码|版本 7 代码.子代码
|-|-
|RequestParameterMissing|InvalidRequest.ParameterMissing
RequestParameterInvalidValue|InvalidRequest.ParameterInvalidValue
ResourceAccessDenied|InsufficientAuthorization
ExceededVolume|RateLimitExceeded
ExceededQpsLimit|RateLimitExceeded
Disabled|InsufficientAuthorization.AuthorizationDisabled
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
Blocked|InvalidRequest.Blocked

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用和显示要求](./UseAndDisplayRequirements.md)
