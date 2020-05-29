---
title: 必应本地业务搜索 API v7 引用
titleSuffix: Azure Cognitive Services
description: 本文提供了有关响应对象的技术详细信息，以及影响搜索结果的查询参数和标头。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: d5de1cc606f97655427c0c86aea0c5c722e1bab8
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2020
ms.locfileid: "84171457"
---
# <a name="bing-local-business-search-api-v7-reference"></a>必应当地企业搜索 API v7 参考

当地企业搜索 API 向必应发送搜索查询，以获取包含餐厅、酒店或其他当地企业的结果。 对于地点，查询可以指定当地企业名称或类别（例如，我附近的餐厅）。 实体结果包括人员、位置或事物。 此环境中的位置是业务实体、省/市/自治区、国家/地区等。  

本部分提供有关影响搜索结果的响应对象、查询参数和标头的技术详细信息。 有关演示如何发出请求的示例，请参阅[当地企业搜索 C# 快速入门](quickstarts/local-quickstart.md)或[当地企业搜索 Java 快速入门](quickstarts/local-search-java-quickstart.md)。 
  
若要了解请求应包含的标头，请参阅[标头](#headers)。  
  
若要了解请求应包含的查询参数，请参阅[查询参数](#query-parameters)。  
  
若要了解响应应包含的 JSON 对象，请参阅[响应对象](#response-objects)。

若要了解结果的使用和显示有哪些规定，请参阅[使用和显示要求](use-display-requirements.md)。


  
## <a name="endpoint"></a>终结点  
若要请求当地企业结果，请将 GET 请求发送到： 

``` 
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search

```
  
请求必须使用 HTTPS 协议。  
  
> [!NOTE]
> 最大 URL 长度为 2,048 个字符。 为了确保 URL 长度不超出限制，查询参数的最大长度应不到 1,500 个字符。 如果 URL 超出 2,048 个字符，服务器会返回“404 未找到”。  
  
  
## <a name="headers"></a>标头  
下面是请求和响应可能包含的标头。  
  
|标头|说明|  
|------------|-----------------|  
|接受|可选请求标头。<br /><br /> 默认的媒体类型为“application/json”。 若要指定响应使用 [JSON-LD](https://json-ld.org/)，请将 Accept 标头设置为“application/ld+json”。|  
|<a name="acceptlanguage"></a>Accept-Language|可选请求标头。<br /><br /> 以逗号分隔的语言列表，用于用户界面字符串。 此列表以降序方式显示首选项。 有关详细信息，包括预期格式，请参阅 [RFC2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)。<br /><br /> 此标头和 [setLang](#setlang) 查询参数相互排斥&mdash;不可同时指定两者。<br /><br /> 如果设置此标头，则还必须指定 cc 查询参数。 为了确定针对哪个市场返回结果，必应使用从列表中找到的第一个受支持语言并将其与 `cc` 参数值相结合。 如果列表不包括支持的语言，必应会查找最接近的语言和支持请求的市场，或将聚合或默认市场用于结果。 若要确定必应使用的市场，请查看 BingAPIs-Market 标头。<br /><br /> 仅当指定多个语言时，才可使用此标头和 `cc` 查询参数。 否则，请使用 [mkt](#mkt) 和 [setLang](#setlang) 查询参数。<br /><br /> 用户界面字符串是用作用户界面中标签的字符串。 JSON 响应对象中有几个用户界面字符串。 响应对象中 Bing.com 属性的任何链接均将应用指定的语言。|  
|<a name="market"></a>BingAPIs-Market|响应标头。<br /><br /> 请求使用的市场。 窗体是 \<languageCode\> - \<countryCode\> 。 例如，en-US。|  
|<a name="traceid"></a>BingAPIs-TraceId|响应标头。<br /><br /> 包含请求详细信息的日志条目 ID。 发生错误时，捕获此 ID。 如果无法确定并解决问题，请纳入此 ID 以及提供给支持团队的其他信息。|  
|<a name="subscriptionkey"></a>Ocp-Apim-Subscription-Key|必需请求标头。<br /><br /> 在[认知服务](https://www.microsoft.com/cognitive-services/)中注册此服务时收到的订阅密钥。|  
|<a name="pragma"></a>Pragma|可选请求标头<br /><br /> 默认情况下，必应返回缓存的内容（如果适用）。 若要防止必应返回缓存的内容，请将 Pragma 标头设置为 no-cache（例如，Pragma: no-cache）。
|<a name="useragent"></a>User-Agent|可选请求标头。<br /><br /> 发出请求的用户代理。 必应使用用户代理为移动用户提供优化体验。 尽管是可选的，但还是建议始终指定此标头。<br /><br /> user-agent 应该是任何常用浏览器发送的字符串。 有关用户代理的信息，请参阅 [RFC 2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)。<br /><br /> 下面是 user-agent 字符串示例。<br /><ul><li>Windows Phone&mdash;Mozilla/5.0（兼容；MSIE 10.0；Windows Phone 8.0；Trident/6.0；IEMobile/10.0；ARM；Touch；NOKIA；Lumia 822）<br /><br /></li><li>Android&mdash;Mozilla/5.0（Linux；U；Android 2.3.5；en-us；SCH-I500 Build/GINGERBREAD）AppleWebKit/533.1（KHTML，如 Gecko）版本/4.0 Mobile Safari/533.1<br /><br /></li><li>iPhone&mdash;Mozilla/5.0（iPhone；CPU iPhone OS 6_1，如 Mac OS X）AppleWebKit/536.26（KHTML；如 Gecko）Mobile/10B142 iPhone4；1 BingWeb/3.03.1428.20120423<br /><br /></li><li>PC&mdash;Mozilla/5.0（Windows NT 6.3；WOW64；Trident/7.0；Touch；rv:11.0），如 Gecko<br /><br /></li><li>iPad&mdash;Mozilla/5.0（iPad；CPU OS 7_0，如 Mac OS X）AppleWebKit/537.51.1（KHTML，如 Gecko）版本/7.0 Mobile/11A465 Safari/9537.53</li></ul>|
|<a name="clientid"></a>X-MSEdge-ClientID|可选请求和响应标头。<br /><br /> 必应使用此标头跨必应 API 调用为用户提供一致的行为。 必应通常会发布新功能和改进，并将客户端 ID 用作密钥以在不同航班上分配客流量。 如果未跨多个请求将相同的客户端 ID 用于用户，则必应可能将用户分配给多个冲突的航班。 分配给多个冲突航班可能导致用户体验不一致。 例如，如果第二个请求与第一个请求的航班分配不同，体验可能会出现意外。 此外，必应可以使用客户端 ID 根据客户端 ID 搜索历史记录来定制 Web 结果，为用户提供更丰富的体验。<br /><br /> 通过分析由客户端 ID 生成的活动，必应还会使用此标头来提高结果排名。 相关改进有助于提高必应 API 交付的结果质量，从而提高 API 客户的点击率。<br /><br /> 重要提示：**** 尽管是可选的，但应将此标头视为必选。 对于同一最终用户和设备组合，如果跨多个请求保留客户端 ID，则 1) API 客户可以获取一致的用户体验；2) 可通过必应 API 提高结果质量，从而提高点击率。<br /><br /> 下面是适用于此标头的基本用法规则。<br /><ul><li>在设备上使用你的应用程序的每个用户必须具有必应生成的唯一客户端 ID。<br /><br/>如果未在请求中包含此标头，必应会生成 ID，然后在 X-MSEdge-ClientID 响应标头中将其返回。 仅当用户首次在设备上使用应用时，才不可以在请求中包含此标头。<br /><br/></li><li>针对应用为设备上的此用户生成的每个必应 API 请求，使用客户端 ID。<br /><br/></li><li>**注意：** 必须确保此客户端 ID 不能链接到任何可以进行身份验证的用户帐户信息。</li><br/><li>保留客户端 ID。 若要在浏览器应用中保留 ID，请使用持久性 HTTP Cookie 来确保所有会话均使用此 ID。 请勿使用会话 Cookie。 对于移动应用等其他应用，请使用设备的持久存储来保留 ID。<br /><br/>下次用户在该设备上使用你的应用时，会获取保留的客户端 ID。</li></ul><br /> **注意：** 必应响应不一定包含此标头。 如果响应包含此标头，请针对该设备上的用户捕获客户端 ID 并将其用于所有后续必应请求。<br /><br /> **注意：** 如果包含 X-MSEdge-ClientID，不可在请求中包含 Cookie。|  
|<a name="clientip"></a>X-MSEdge-ClientIP|可选请求标头。<br /><br /> 客户端设备的 IPv4 或 IPv6 地址。 IP 地址用于发现用户的位置。 必应使用位置信息来确定安全搜索行为。<br /><br /> **注意：** 尽管是可选的，但还是建议始终指定此标头和 X-Search-Location 标头。<br /><br /> 不要混淆地址（例如，通过将最后一个八位字节更改为 0 来混淆地址）。 混淆地址会导致位置未处于设备实际位置附近，这可能导致必应提供错误的结果。|  
|<a name="location"></a>X-Search-Location|可选请求标头。<br /><br /> 以分号分隔的键/值对列表，描述客户端的地理位置。 必应使用位置信息来确定安全搜索行为并返回相关的本地内容。 将键/值对指定为 \<key\> ： \<value\> 。 下面是用于指定用户位置的键。<br /><br /><ul><li>lat&mdash;客户端位置的纬度，以度为单位。 纬度必须大于或等于 -90.0 且小于或等于 +90.0。 负值表示南纬，正值表示北纬。<br /><br /></li><li>long&mdash;客户端位置的经度，以度为单位。 经度必须大于或等于 -180.0 且小于或等于 +180.0。 负值表示西经，正值表示东经。<br /><br /></li><li>re&mdash; 半径（以米为单位），指定坐标的水平准确度。 传递设备定位服务返回的值。 典型的值可能是：22m - GPS/Wi-Fi、380m - 蜂窝基站三角网定位、18,000m - 反向 IP 查询。<br /><br /></li><li>ts&mdash; 客户端位于相应位置时的 UTC UNIX 时间戳。 （UNIX 时间戳是自 1970 年 1 月 1 日起的秒数。）<br /><br /></li><li>head&mdash;可选。 客户端的相对航向或旅行方向。 以度数指定旅行方向（从 0 到 360），相对于正北方向顺时针计数。 如果 `sp` 键为非零值，则指定此键。<br /><br /></li><li>sp&mdash; 客户端设备移动的水平速度（速度），以米/秒为单位。<br /><br /></li><li>alt&mdash; 客户端设备的高度，以米为单位。<br /><br /></li><li>are&mdash;可选。 半径（以米为单位），指定坐标的垂直准确度。 半径默认为 50 公里。 只有在指定 `alt` 键的情况下才指定此键。<br /><br /></li></ul> **注意：** 尽管这些键是可选的，但提供的信息越多，位置结果越精确。<br /><br /> **注意：** 建议始终指定用户的地理位置。 如果客户端的 IP 地址未准确反映用户的物理位置（例如，如果客户端使用 VPN），则提供位置尤其重要。 为了获得最佳结果，应包含此标头和 X-MSEdge-ClientIP 标头，但应至少包含此标头。|

> [!NOTE] 
> 请记住，使用条款要求遵守所有适用的法律，包含这些标头的用法。 例如，在某些管辖区（如欧洲），在用户设备上放置某些跟踪设备之前，需要获得用户同意。
  

## <a name="query-parameters"></a>查询参数  
请求可以包含以下查询参数。 请查看所需参数的“必需”列。 必须对查询参数进行 URL 编码。  
  
  
|名称|值|类型|必须|  
|----------|-----------|----------|--------------|
|<a name="count"></a>count|要返回的结果数，从参数指定的索引开始 `offset` 。|String|否|   
|<a name="localCategories"></a>localCategories|按企业类别定义搜索的选项列表。  请参阅[当地企业类别搜索](local-categories.md)|String|否|  
|<a name="mkt"></a>mkt|产生结果的市场。 <br /><br />如需获取可能的市场值列表，请参阅“市场代码”。<br /><br /> **注意：** 当地企业搜索 API 目前仅支持 en-us（美国英语）市场和语言。<br /><br />|String|是|
|<a name="offset"></a>offset|开始返回结果的索引位置，由 `count` 参数指定。|Integer|否|  
|<a name="query"></a>q|用户的搜索词。|String|否|  
|<a name="responseformat"></a>responseFormat|可用于响应的媒体类型。 下面是可能的不区分大小写的值。<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> 默认值为 JSON。 若要了解响应应包含的 JSON 对象，请参阅[响应对象](#response-objects)。<br /><br />  如果指定 JsonLd，则响应正文会包含 JSON-LD 对象，后者包含搜索结果。 有关 JSON-LD 的信息，请参阅 [JSON-LD](https://json-ld.org/)。|String|否|  
|<a name="safesearch"></a>safeSearch|用于筛选成人内容的筛选器。 下面是可能的不区分大小写的筛选值。<br /><ul><li>关闭&mdash;返回包含成人文本、图像或视频的网页。<br /><br/></li><li>中等&mdash;返回包含成人文本但不包含成人图像或视频的网页。<br /><br/></li><li>严格&mdash;不返回包含成人文本、图像或视频的网页。</li></ul><br /> 默认级别为“中等”。<br /><br /> **注意：** 如果请求来自必应成人策略要求将 `safeSearch` 设置为“严格”的某一市场，必应会忽略 `safeSearch` 值并使用“严格”。<br/><br/>**注意：** 如果使用 `site:` 查询运算符，则不管 `safeSearch` 查询参数设置如何，仍有可能出现响应中包含成人内容的情况。 只有在知道网站内容且方案允许使用成人内容的情况下，才应使用 `site:`。 |String|否|  
|<a name="setlang"></a>setLang|可用于用户界面字符串的语言。 使用 ISO 639-1 2 字母语言代码指定语言。 例如，英语的语言代码是 EN。 默认为 EN（英语）。<br /><br /> 尽管是可选项，但应始终指定语言。 通常情况下，请将 `setLang` 设置为 `mkt` 所指定的语言，除非用户希望以另一语言显示用户界面字符串。<br /><br /> 此参数和 [Accept-Language](#acceptlanguage) 标头相互排斥&mdash;不可同时指定两者。<br /><br /> 用户界面字符串是用作用户界面中标签的字符串。 JSON 响应对象中有几个用户界面字符串。 此外，响应对象中 Bing.com 属性的任何链接均会应用指定的语言。|String|否| 


## <a name="response-objects"></a>响应对象  
以下是响应中可以包含的 JSON 响应对象。 如果请求成功，则响应中的顶级对象是 [SearchResponse](#searchresponse) 对象。 如果请求失败，则顶级对象为 [ErrorResponse](#errorresponse) 对象。


|对象|说明|  
|------------|-----------------|  
|[Place](#place)|定义有关餐厅或酒店等当地企业的信息。|  

  
### <a name="error"></a>错误  
定义已发生的错误。  
  
|元素|说明|类型|  
|-------------|-----------------|----------|  
|<a name="error-code"></a>code|用于标识错误类别的错误代码。 如需可能的代码的列表，请参阅[错误代码](#error-codes)。|String|  
|<a name="error-message"></a>message|对错误的说明。|String|  
|<a name="error-moredetails"></a>moreDetails|一个说明，提供关于错误的其他信息。|String|  
|<a name="error-parameter"></a>parameter|请求中导致错误的查询参数。|String|  
|<a name="error-subcode"></a>subCode|用于标识错误的错误代码。 例如，如果 `code` 为 InvalidRequest，则 `subCode` 可以为 ParameterInvalid 或 ParameterInvalidValue。 |String|  
|<a name="error-value"></a>value|查询参数的无效值。|String|  
  

### <a name="errorresponse"></a>ErrorResponse  
请求失败时响应包含的顶级对象。  
  
|名称|值|类型|  
|----------|-----------|----------|  
|_type|类型提示。|String|  
|<a name="errors"></a>errors|错误的列表，用于说明请求失败原因。|[错误](#error)[]|  

  
  
### <a name="license"></a>许可证  
定义文本或照片的使用许可证。  
  
|名称|值|类型|  
|----------|-----------|----------|  
|name|许可证的名称。|String|  
|url|为用户提供许可证详细信息的网站的 URL。<br /><br /> 使用名称和 URL 创建超链接。|String|  


### <a name="link"></a>链接  
定义超链接的组件。  
  
|名称|值|类型|  
|----------|-----------|----------|  
|_type|类型提示。|String|  
|text|显示文本。|String|  
|url|一个 URL。 使用 URL 和显示文本创建超链接。|String|  
  


  
### <a name="organization"></a>组织  
定义发布者。  
  
注意，发布者可能提供其名称和/或网站。  
  
|名称|值|类型|  
|----------|-----------|----------|  
|name|发布者名称。|String|  
|url|发布者网站的 URL。<br /><br /> 请注意，发布者可能未提供网站。|String|  
  
  

### <a name="place"></a>位置  
定义有关餐厅或酒店等当地企业的信息。  
  
|名称|值|类型|  
|----------|-----------|----------|  
|_type|类型提示，可设置为以下值之一：<br /><br /><ul><li>Hotel</li><li>LocalBusiness<br /></li><li>餐厅</ul><li>|String|  
|address|实体所在位置的邮政地址。|PostalAddress|  
|entityPresentationInfo|有关实体的其他信息，例如，可用于确定实体类型的提示。 例如，该实体是餐厅还是酒店。 `entityScenario` 字段设置为 ListItem。|EntityPresentationInfo|  
|name|实体的名称。|String|  
|telephone|实体的电话号码。|String|  
|url|实体网站的 URL。<br /><br /> 将此 URL 与实体名称结合使用可以创建一个超链接，单击该链接会将用户转到实体网站。|String|  
|webSearchUrl|此地点的必应搜索结果的 URL。|String| 
  
  
### <a name="querycontext"></a>QueryContext  
定义必应用于请求的查询上下文。  
  
|元素|说明|类型|  
|-------------|-----------------|----------|  
|adultIntent|一个布尔值，表示指定的查询是否有成人意向。 如果查询有成人意向，则此值为 **true**，否则为 **false**。|布尔|  
|alterationOverrideQuery|一个查询字符串，用于强制必应使用原始字符串。 例如，如果查询字符串为 *saling downwind*，则替代查询字符串为 *+saling downwind*。 记住将查询字符串编码，编码后的字符串为 *%2Bsaling+downwind*。<br /><br /> 只有在原始查询字符串包含拼写错误的情况下，才会包含此字段。|String|  
|alteredQuery|必应用来执行查询的查询字符串。 如果原始查询字符串包含拼写错误，必应会使用更改的查询字符串。 例如，如果查询字符串为 `saling downwind`，则更改的查询字符串为 `sailing downwind`。<br /><br /> 只有在原始查询字符串包含拼写错误的情况下，才会包含此字段。|String|  
|askUserForLocation|一个布尔值，指示必应是否需要用户的位置才能提供准确结果。 如果已使用 [X-MSEdge-ClientIP](#clientip) 和 [X-Search-Location](#location) 标头指定用户的位置，则可忽略此字段。<br /><br /> 对于需要用户的位置才能提供准确结果的位置感知型查询，例如“今天的天气”或“我附近的餐馆”，此字段设置为 **true**。<br /><br /> 对于包含位置的位置感知型查询（例如“西雅图的天气”），此字段设置为 **false**。 对于非位置感知型查询，例如“最佳销售者”，此字段也设置为 **false**。|布尔|  
|originalQuery|请求中指定的查询字符串。|String|  

### <a name="identifiable"></a>Identifiable

|名称|值|类型|  
|-------------|-----------------|----------|
|id|一个资源标识符|String|
 
### <a name="rankinggroup"></a>RankingGroup
定义搜索结果组，例如 mainline。

|名称|值|类型|  
|-------------|-----------------|----------|
|items|要显示在组中的搜索结果的列表。|RankingItem|

### <a name="rankingitem"></a>RankingItem
定义要显示的搜索结果项。

|名称|值|类型|  
|-------------|-----------------|----------|
|resultIndex|要显示答案中的项的从零开始的索引。 如果项不包含此字段，则显示答案中的所有项。 例如，显示“新闻”答案中的所有新闻文章。|Integer|
|answerType|一个答案，包含要显示的项。 例如，新闻。<br /><br />使用此类型查找 SearchResponse 对象中的答案。 此类型是 SearchResponse 字段的名称。<br /><br /> 不过，只有在此对象包含值字段的情况下，才使用答案类型；否则，请忽略它。|String|
|textualIndex|textualAnswers 中要显示的答案的索引。| 无符号整数|
|value|一个 ID，用于标识要显示的答案或要显示的答案的项。 如果此 ID 标识某个答案，则显示该答案的所有项。|Identifiable|

### <a name="rankingresponse"></a>RankingResponse  
定义应将内容置于搜索结果页的何处以及应采用什么顺序。  
  
|名称|Value|  
|----------|-----------|  
|<a name="ranking-mainline"></a>mainline|要显示在主线中的搜索结果。|  
|<a name="ranking-pole"></a>pole|要获得最明显的处理（例如，显示在主线和边栏上方）的搜索结果。|  
|<a name="ranking-sidebar"></a>sidebar|要显示在边栏中的搜索结果。| 

### <a name="searchresponse"></a>SearchResponse  
定义请求成功时响应包含的顶级对象。  
  
请注意，如果该服务怀疑存在拒绝服务攻击，则请求会成功（HTTP 状态代码为“200 正常”），但响应正文将为空。  
  
|名称|值|类型|  
|----------|-----------|----------|  
|_type|一种类型提示，设置为 SearchResponse。|String|  
|places|与搜索查询相关的实体列表。|JSON 对象|  
|queryContext|包含必应用于发出请求的查询字符串的对象。<br /><br /> 此对象包含用户输入的查询字符串。 如果查询字符串包含拼写错误，则此对象还可以包含必应用于发出查询的更改后的查询字符串。|[QueryContext](#querycontext)|  


## <a name="error-codes"></a>错误代码

下面是请求可能返回的 HTTP 状态代码。  
  
|状态代码|说明|  
|-----------------|-----------------|  
|200|成功。|  
|400|其中一个查询参数丢失或无效。|  
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

|代码|SubCode|说明
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|HTTP 状态代码为 500。
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>已阻止|只要请求的任何部分无效，必应就会返回 InvalidRequest。 例如，缺少必需参数或参数值无效。<br/><br/>如果错误是 ParameterMissing 或 ParameterInvalidValue，HTTP 状态代码为 400。<br/><br/>如果使用 HTTP 协议而不是 HTTPS 协议，则必应会返回 HttpNotAllowed，且 HTTP 状态代码为 410。
|RateLimitExceeded|无子代码|只要超过每秒查询数 (QPS) 或每月查询数 (QPM) 配额，必应就会返回 RateLimitExceeded。<br/><br/>如果超过 QPS，则必应会回 HTTP 状态代码 429；如果超过 QPM，则必应会返回 403。
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|当必应无法验证调用方身份时，必应会返回 InvalidAuthorization。 例如，缺少 `Ocp-Apim-Subscription-Key` 头或订阅密钥无效。<br/><br/>如果指定多个身份验证方法，便会发生冗余。<br/><br/>如果错误是 InvalidAuthorization，HTTP 状态代码为 401。
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|当调用方无权访问资源时，必应会返回 InsufficientAuthorization。 如果订阅密钥已遭禁用或到期，就会发生此类错误。 <br/><br/>如果错误是 InsufficientAuthorization，HTTP 状态代码为 403。

## <a name="next-steps"></a>后续步骤
- [当地企业搜索快速入门](quickstarts/local-quickstart.md)
- [当地企业搜索 Java 快速入门](quickstarts/local-search-java-quickstart.md)
- [当地企业搜索 Node 快速入门](quickstarts/local-search-node-quickstart.md)
- [当地企业搜索 Python 快速入门](quickstarts/local-search-python-quickstart.md)
