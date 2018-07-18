---
title: 项目 URL 预览参考 - Microsoft 认知服务 | Microsoft Docs
description: 项目 URL 预览终结点参考。
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.technology: project-url-preview
ms.topic: article
ms.date: 03/29/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: adc2f83f703e740e40d9ba4fd3ed08ba429e5d97
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366327"
---
# <a name="project-url-preview-v7-reference"></a>项目 URL 预览 v7 参考

URL 预览支持对博客文章、论坛讨论、预览页面等 Web 资源的简要描述。URL 可以是任何类型的 Internet 资源：网页、新闻、图像或视频。 查询必须是使用 http 或 https 方案的绝对 URL；不支持相对 URL 或其他方案（如 ftp://）。

使用 URL 预览的应用程序使用 URL 将 Web 请求发送到终结点，以便在查询参数中预览。  请求必须包含 *Ocp-Apim-Subscription-Key* 标头。   

可以解析 JSON 响应以获取预览信息：名称、资源描述、isFamilyFriendly 以及链接，通过这些链接可在线访问代表性图像和完整资源。

必须仅使用来自 URL 预览的数据，通过最终用户在社交媒体、聊天机器人或类似产品/服务上发起的 URL 共享，显示超链接到其源站点的预览片段和缩略图图像。 不得复制、存储或缓存从项目 URL 预览收到的任何数据。 必须按照请求禁用可能从网站或内容所有者处收到的预览。

## <a name="endpoint"></a>终结点
若要请求 URL 预览结果，请向以下终结点发送请求。 使用标头和 URL 参数来进一步定义规范。

终结点 GET： 
````
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=queryURL

````

请求必须使用 HTTPS 协议并包括以下查询参数：

 q - 标识要预览的 URL 的查询 

以下部分提供的技术详细信息涉及影响搜索结果的响应对象、查询参数和标头。 
  
若要了解请求应包含的标头，请参阅[标头](#headers)。  
  
若要了解请求应包含的查询参数，请参阅[查询参数](#query-parameters)。  
  
若要了解响应应包含的 JSON 对象，请参阅[响应对象](#response-objects)。

最大查询 URL 长度为 2,048 个字符。 为了确保 URL 长度不超出限制，查询参数的最大长度应不到 1,500 个字符。 如果 URL 超出 2,048 个字符，服务器会返回“404 未找到”。  

若要了解结果的使用和显示有哪些规定，请参阅[使用和显示要求](use-display-requirements.md)。 

> [!NOTE]
> 某些对其他搜索 API 有意义的请求标头不影响 URL 预览
> - Pragma - 调用方无法控制 URL 预览是否使用缓存
> - User-Agent - 目前，URL 预览 API 不会为从电脑、笔记本电脑或移动设备发出的调用提供不同的响应。

> 另外，某些参数目前对 URL 预览 API 没有意义，但也许可以在将来用于改进全球化。 
 
## <a name="headers"></a>标头  
下面是请求和响应可能包含的标头。  
  
|标头|说明|  
|------------|-----------------|   
|<a name="market" />BingAPIs-Market|响应标头。<br /><br /> 请求使用的市场。 形式为 \<languageCode\>-\<countryCode\>。 例如，en-US。|  
|<a name="traceid" />BingAPIs-TraceId|响应标头。<br /><br /> 包含请求详细信息的日志条目 ID。 发生错误时，捕获此 ID。 如果无法确定并解决问题，请纳入此 ID 以及提供给支持团队的其他信息。|  
|<a name="subscriptionkey" />Ocp-Apim-Subscription-Key|必需请求标头。<br /><br /> 在[认知服务](https://www.microsoft.com/cognitive-services/)中注册此服务时收到的订阅密钥。|  
|<a name="clientid" />X-MSEdge-ClientID|可选请求和响应标头。<br /><br /> 必应使用此标头跨必应 API 调用为用户提供一致的行为。 必应通常会发布新功能和改进，并将客户端 ID 用作密钥以在不同航班上分配客流量。 如果未跨多个请求将相同的客户端 ID 用于用户，则必应可能将用户分配给多个冲突的航班。 分配给多个冲突航班可能导致用户体验不一致。 例如，如果第二个请求与第一个请求的航班分配不同，体验可能会出现意外。 此外，必应可以使用客户端 ID 根据客户端 ID 搜索历史记录来定制 Web 结果，为用户提供更丰富的体验。<br /><br /> 通过分析由客户端 ID 生成的活动，必应还会使用此标头来提高结果排名。 相关改进有助于提高必应 API 交付的结果质量，从而提高 API 客户的点击率。<br /><br />下面是适用于此标头的基本用法规则。<br /><ul><li>在设备上使用你的应用程序的每个用户必须具有必应生成的唯一客户端 ID。<br /><br/>如果未在请求中包含此标头，必应会生成 ID，然后在 X-MSEdge-ClientID 响应标头中将其返回。 仅当用户首次在设备上使用应用时，才不可以在请求中包含此标头。<br /><br/></li><li>针对应用为设备上的此用户生成的每个必应 API 请求，使用客户端 ID。<br /><br/></li><li>**注意：** 必须确保此客户端 ID 不能链接到任何可以进行身份验证的用户帐户信息。</li><br/><li>保留客户端 ID。 若要在浏览器应用中保留 ID，请使用持久性 HTTP Cookie 来确保所有会话均使用此 ID。 请勿使用会话 Cookie。 对于移动应用等其他应用，请使用设备的持久存储来保留 ID。<br /><br/>下次用户在该设备上使用你的应用时，会获取保留的客户端 ID。</li></ul><br /> **注意：** 必应响应不一定包含此标头。 如果响应包含此标头，请针对该设备上的用户捕获客户端 ID 并将其用于所有后续必应请求。<br /><br /> **注意：** 如果包含 X-MSEdge-ClientID，不可在请求中包含 Cookie。|  
|<a name="clientip" />X-MSEdge-ClientIP|可选请求标头。<br /><br /> 客户端设备的 IPv4 或 IPv6 地址。 IP 地址用于发现用户的位置。 必应使用位置信息来确定安全搜索行为。<br /><br />  不要混淆地址（例如，通过将最后一个八位字节更改为 0 来混淆地址）。 混淆地址会导致位置未处于设备实际位置附近，这可能导致必应提供错误的结果。|  
<br /><br /></li></ul>   

## <a name="query-parameters"></a>查询参数  
请求可以包含以下查询参数。 请查看所需参数的“必需”列。 必须对查询参数进行 URL 编码。 查询必须是使用 http 或 https 方案的绝对 URL；我们不支持相对 URL 或其他方案（如 ftp://）
  
  
|名称|值|Type|必选|  
|----------|-----------|----------|--------------|  
|<a name="mkt" />mkt|产生结果的市场。 <br /><br />如需可能的市场值的列表，请参阅[市场代码](#market-codes)。<br /><br /> **注意：** URL 预览 API 目前仅支持美国地理和英语。<br /><br />|String|是|  
|<a name="query" />q|要预览的 URL|String|是|  
|<a name="responseformat" />responseFormat|可用于响应的媒体类型。 下面是可能的不区分大小写的值。<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> 默认值为 JSON。 若要了解响应应包含的 JSON 对象，请参阅[响应对象](#response-objects)。<br /><br />  如果指定 JsonLd，则响应正文会包含 JSON-LD 对象，后者包含搜索结果。 有关 JSON-LD 的信息，请参阅 [JSON-LD](http://json-ld.org/)。|String|否|
|<a name="safesearch"/>safeSearch|非法的成人内容或盗版内容将被阻止且系统会显示错误代码 400，并且不会返回 *isFamilyFriendly* 标志。 <p>对于合法的成人内容，行为如下。 状态代码返回 200，并且 *isFamilyFriendly* 标志设置为 false。<ul><li>safeSearch=strict：将不会返回标题、说明、URL 和图像。</li><li>safeSearch=moderate：将获取标题、URL 和说明，但不获取说明性图像。</li><li>safeSearch=off：获取所有响应对象/元素 – 标题、URL、说明和图像。</li></ul> |String|非必需。 </br> 默认为 safeSearch=strict。| 

## <a name="response-objects"></a>响应对象  
就像在 Web 搜索 API 中一样，响应架构为 [WebPage] 或 ErrorResponse。 如果请求失败，则顶级对象为 [ErrorResponse](#errorresponse) 对象。


|对象|说明|  
|------------|-----------------|  
|[WebPage](#webpage)|包含预览属性的顶级 JSON 对象。|  

  
### <a name="error"></a>错误  
定义已发生的错误。  
  
|元素|说明|Type|  
|-------------|-----------------|----------|  
|<a name="error-code" />code|用于标识错误类别的错误代码。 如需可能的代码的列表，请参阅[错误代码](#error-codes)。|String|  
|<a name="error-message" />message|对错误的说明。|String|  
|<a name="error-moredetails" />moreDetails|一个说明，提供关于错误的其他信息。|String|  
|<a name="error-parameter" />parameter|请求中导致错误的查询参数。|String|  
|<a name="error-subcode" />subCode|用于标识错误的错误代码。 例如，如果 `code` 为 InvalidRequest，则 `subCode` 可以为 ParameterInvalid 或 ParameterInvalidValue。 |String|  
|<a name="error-value" />value|查询参数的无效值。|String|  
  

### <a name="errorresponse"></a>ErrorResponse  
请求失败时响应包含的顶级对象。  
  
|名称|值|Type|  
|----------|-----------|----------|  
|_type|类型提示。|String|  
|<a name="errors" />errors|错误的列表，用于说明请求失败原因。|[Error](#error)[]|   
  

### <a name="webpage"></a>WebPage  
定义预览版网页的信息。  
  
|名称|值|Type|  
|----------|-----------|----------|
|名称|页面标题，不一定是 HTML 标题|String|
|url|进行了实际爬网的 URL（请求可能已随之进行了重定向）|String|  
|description|对页面和内容的简要说明|String|  
|isFamilyFriendly|对 Web 索引中的项来说最准确；实时提取完全根据 URL 而非页面内容来执行此检测|布尔值|
|primaryImageOfPage/contentUrl|将包括在预览版中的代表性图像的 URL|String| 


### <a name="identifiable"></a>Identifiable
|名称|值|Type|  
|-------------|-----------------|----------|
|id|一个资源标识符|String|
 

## <a name="error-codes"></a>错误代码

下面是请求可能返回的 HTTP 状态代码。  
  
|状态代码|说明|  
|-----------------|-----------------|  
|200|成功。|  
|400|查询参数之一缺失或无效。| 
|400|ServerError、子代码 ResourceError：无法访问所请求的 URL|
|400|ServerError、子代码 ResourceError：所请求的 URL 未返回成功代码（包括它是否返回了 HTTP 404）|
|400|InvalidRequest，子代码已阻止：所请求的 URL 可能包含有成人内容，已被阻止| 
|401|订阅密钥缺失或无效。|  
|403|用户已经过身份验证（例如，用户使用了有效的订阅密钥），但无权访问请求的资源。<br /><br /> 如果调用方超出其每月查询配额，必应也可能会返回此状态。|  
|410|请求使用了 HTTP 而非 HTTPS 协议。 HTTPS 是唯一支持的协议。|  
|429|调用方超出其每秒查询配额。|  
|500|意外的服务器错误。|

如果请求失败，响应会包含一个 [ErrorResponse](#errorresponse) 对象，该对象包含一系列用于描述错误原因的 [Error](#error) 对象。 如果错误与参数相关，则 `parameter` 字段会标识导致问题的参数。 如果错误与参数值相关，则 `value` 字段会标识无效的参数值。

```json
{
  "_type": "ErrorResponse", 
  "errors": [
    {
      "code": "InvalidRequest", 
      "subCode": "ParameterMissing", 
      "message": "Required parameter is missing.", 
      "parameter": "q" 
    }
  ]
}

{
  "_type": "ErrorResponse", 
  "errors": [
    {
      "code": "InvalidAuthorization", 
      "subCode": "AuthorizationMissing", 
      "message": "Authorization is required.", 
      "moreDetails": "Subscription key is not recognized."
    }
  ]
}
```

下面是可能的错误代码和子错误代码值。

|代码|子代码|说明
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|HTTP 状态代码为 500。
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>已阻止|只要请求的任何部分无效，必应就会返回 InvalidRequest。 例如，缺少必需参数或参数值无效。<br/><br/>如果错误是 ParameterMissing 或 ParameterInvalidValue，则 HTTP 状态代码为 400。<br/><br/>如果使用 HTTP 协议而不是 HTTPS 协议，则必应会返回 HttpNotAllowed，且 HTTP 状态代码为 410。
|RateLimitExceeded|无子代码|每当超过每秒查询数 (QPS) 或每月查询数 (QPM) 配额时，必应都会返回 RateLimitExceeded。<br/><br/>如果超过 QPS，则必应会回 HTTP 状态代码 429；如果超过 QPM，则必应会返回 403。
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|在必应无法对调用方进行身份验证时，必应会返回 InvalidAuthorization。 例如，缺少 `Ocp-Apim-Subscription-Key` 标头或订阅密钥无效。<br/><br/>如果指定了多个身份验证方法，则会发生冗余。<br/><br/>如果错误是 InvalidAuthorization，则 HTTP 状态代码为 401。
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|当调用方没有权限访问资源时，必应会返回 InsufficientAuthorization。 如果订阅密钥已禁用或过期，也会发生此情况。 <br/><br/>如果错误是 InsufficientAuthorization，则 HTTP 状态代码为 403。

## <a name="next-steps"></a>后续步骤
- [C# 快速入门](csharp.md)
- [Java 快速入门](java-quickstart.md)
- [JavaScript 快速入门](javascript.md)
- [Node 快速入门](node-quickstart.md)
- [Python 快速入门](python-quickstart.md)

