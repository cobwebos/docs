---
title: 必应视觉搜索 API 概述 | Microsoft Docs
titleSuffix: Bing Web Search APIs - Cognitive Services
description: 介绍如何获取与图像相关的详细信息或见解，如类似图像或购物源。
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 04/10/2018
ms.author: scottwhi
ms.openlocfilehash: aa563d89b1834f5be952f13c31a2451d809709b1
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2018
ms.locfileid: "39006523"
---
# <a name="what-is-bing-visual-search-api"></a>什么是必应视觉搜索 API？

必应视觉搜索 API 提供的体验类似于 Bing.com/images 上所示的图像详细信息。 借助视觉搜索，可上传图片并获取图像见解，例如视觉上相似的图像、购物源和带图像的网页等。 无需上传图像，你还可以提供从图像搜索结果中的图像获取的见解令牌（请参阅[必应图像 API](../bing-image-search/overview.md)）。

视觉搜索可以标识名人、遗迹和地标、艺术品、家居、时尚、产品、字符识别 (OCR) 等。

以下是视觉搜索提供的见解。

- 视觉上相似的图像&mdash;列出了与输入图像在视觉上相似的图像
- 视觉上相似的产品&mdash;列出了包含产品的图像，其中这些产品与输入图像中所示的产品在视觉上相似
- 购物源&mdash;列出了可以购买输入图像中所示项的位置
- 相关搜索&mdash;列出了他人执行的相关搜索或基于图像内容的相关搜索
- 包含图像的网页&mdash;列出了包含输入图像的网页
- 食谱&mdash;列出了包含输入图像中所示食物的食谱的网页

除了这些见解，视觉搜索还可以返回一组派生自输入图像的不同术语（标记）。 这些标记允许用户浏览在图像中找到的概念。 例如，如果输入图像是一位著名运动员，其中一个标记可以是运动员的姓名，另一个标记可以是体育。 或者，如果输入图像是一个苹果馅饼，标记可以是苹果馅饼、馅饼、甜点，以便用户可以浏览相关概念。

视觉搜索结果还包含图像中感兴趣区域对应的边框。 例如，如果图像包含多个名人，结果可能包含图像中每个已识别名人对应的边框。 或者，如果必应识别图像中的产品或服装，结果可能包含已识别产品或服装项对应的边框。

> [!IMPORTANT]
> 如果使用 /images/details 终结点[获取图像见解](../bing-image-search/image-insights.md)，应更新代码以改为使用视觉搜索，因为它可以提供更全面的见解。


## <a name="the-request"></a>请求

以下是用于获取有关图像的信息的选项。 

- 将从先前调用中图像获取的见解令牌发送到[必应图像 API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) 终结点
- 发送图像的 URL
- 上传图像（二进制）


如果向视觉搜索中发送图像令牌或 URL，以下内容将显示必须在 POST 正文中包含的 JSON 对象。 

```json
{
    "imageInfo" : {
        "url" : "",
        "imageInsightsToken" : "",
        "cropArea" : {
            "top" : 0.1,
            "left" : 0.5,
            "right" : 0.9,
            "bottom" : 0.9
        }
    },
    "knowledgeRequest" : {
      "filters" : {
        "site" : ""
      }
    }
}
```

`imageInfo` 对象必须包含 `url` 或 `imageInsightsToken` 字段，但不可同时包含两者。 将 `url` 字段设置为 Internet 可访问图像的 URL。 受支持的最大图像大小为 1 MB。

`imageInsightsToken` 必须设置为见解令牌。 要获取见解令牌，请调用必应图像 API。 响应包含 `Image` 对象的列表。 每个 `Image` 对象包含一个 `imageInsightsToken` 字段，其中包含该令牌。

`cropArea` 字段为可选。 裁剪区域指定感兴趣区域的上、下、左、右。 在 0.0 和 1.0 之间的范围中指定值。 此值是总体宽度或高度的百分比。 例如，上述示例将图像的右半部分标记为感兴趣区域。 如果要将见解请求限制为感兴趣的区域，则包含它。

`filters` 对象包含站点筛选器（请参阅 `site` 字段），可用于将相似图像和相似产品限制为特定域。 例如，如果图像是 Surface Book，可以将 `site` 设置为 www.microsoft.com。 

如果要获取与图像本地副本相关的见解，则以二进制数据形式上传图像。

要详细了解如何在 POST 正文中包含这些选项，请参阅[内容表单类型](#content-form-types)。


### <a name="endpoint"></a>终结点

视觉搜索终结点是：https:\/\/api.cognitive.microsoft.com/bing/v7.0/images/visualsearch。

必须仅以 HTTP POST 请求形式发送请求。 


### <a name="query-parameters"></a>查询参数

以下是请求应指定的查询参数。 至少应包含 `mkt` 查询参数。

|名称|值|Type|必选|  
|----------|-----------|----------|--------------|  
|<a name="cc" />cc|结果来源的国家/地区的 2 个字符国家/地区代码。<br /><br /> 如果设置此参数，则还必须指定 [Accept-language](#acceptlanguage) 标头。 必应使用从语言列表中找到的第一个受支持语言，并将语言与指定的国家/地区代码相结合以确保从哪个市场返回结果。 如果语言列表不包括支持的语言，必应会查找最接近的语言和支持请求的市场。 或者，可以将聚合或默认市场用于结果，而不是指定一个。<br /><br /> 仅当指定多个语言时，才可以使用此查询参数和 `Accept-Language` 查询参数；否则，应使用 `mkt` 和 `setLang` 查询参数。<br /><br /> 此参数和 [mkt](#mkt) 查询参数相互排斥&mdash;不可同时指定两者。|String|否|  
|<a name="mkt" />mkt|产生结果的市场。 <br /><br /> 注意：如果已知，建议始终指定市场。 指定市场有助于必应路由请求，并返回适当的最佳响应。<br /><br /> 此参数和 [cc](#cc) 查询参数相互排斥&mdash;不可同时指定两者。|String|是|  
|<a name="safesearch" />safeSearch|用于筛选成人内容的筛选器。 下面是可能的不区分大小写的筛选值。<br /><ul><li>关闭&mdash;返回具有成人文本或图像的网页。<br /><br/></li><li>中等&mdash;返回具有成人文本但不具有成人图像的网页。<br /><br/></li><li>严格&mdash;不返回具有成人文本或图像的网页。</li></ul><br /> 默认级别为“中等”。<br /><br /> 注意：如果请求来自必应成人策略要求将 `safeSearch` 设置为“严格”的某一市场，必应将忽略 `safeSearch` 值并使用“严格”。<br/><br/>注意：如果使用 `site:` 查询运算符，则不管 `safeSearch` 查询参数设置如何，仍有可能出现响应中包含成人内容的情况。 只有在知道网站内容且方案允许使用成人内容的情况下，才应使用 `site:`。 |String|否|  
|<a name="setlang" />setLang|可用于用户界面字符串的语言。 使用 ISO 639-1 2 字母语言代码指定语言。 例如，英语的语言代码是 EN。 默认为 EN（英语）。<br /><br /> 尽管是可选项，但应始终指定语言。 通常情况下，请将 `setLang` 设置为 `mkt` 所指定的语言，除非用户希望以另一语言显示用户界面字符串。<br /><br /> 此参数和 [Accept-Language](#acceptlanguage) 标头相互排斥&mdash;不可同时指定两者。<br /><br /> 用户界面字符串是用作用户界面中标签的字符串。 JSON 响应对象中有几个用户界面字符串。 此外，响应对象中 Bing.com 属性的任何链接均会应用指定的语言。|String|否| 

### <a name="headers"></a>标头

以下是请求应指定的标头。 Content-Type 和 Ocp-Apim-Subscription-Key 标头是唯一必需的标头，但还应包含 User-Agent、X-MSEdge-ClientID、X-MSEdge-ClientIP 和 X-Search-Location。


|标头|Description|  
|------------|-----------------|  
|<a name="acceptlanguage" />Accept-Language|可选请求标头。<br /><br /> 以逗号分隔的语言列表，用于用户界面字符串。 此列表以降序方式显示首选项。 有关详细信息，包括预期格式，请参阅 [RFC2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)。<br /><br /> 此标头和 [setLang](#setlang) 查询参数相互排斥&mdash;不可同时指定两者。<br /><br /> 如果设置此标头，则还必须指定 [cc](#cc) 查询参数。 为了确定针对哪个市场返回结果，必应使用从列表中找到的第一个受支持语言并将其与 `cc` 参数值相结合。 如果列表不包括支持的语言，必应会查找最接近的语言和支持请求的市场，或将聚合或默认市场用于结果。 若要确定必应使用的市场，请查看 BingAPIs-Market 标头。<br /><br /> 仅当指定多个语言时，才可使用此标头和 `cc` 查询参数。 否则，请使用 [mkt](#mkt) 和 [setLang](#setlang) 查询参数。<br /><br /> 用户界面字符串是用作用户界面中标签的字符串。 JSON 响应对象中有几个用户界面字符串。 响应对象中 Bing.com 属性的任何链接均将应用指定的语言。|  
|<a name="contenttype" />Content-Type|必需请求标头。<br /><br />必须设置为 multipart/form-data 并包含边界参数（例如，multipart/form-data; boundary=\<边界参数\>）。 有关详细信息，请参阅[内容表单类型](#content-form-types)。
|<a name="market" />BingAPIs-Market|响应标头。<br /><br /> 请求使用的市场。 形式为 \<languageCode\>-\<countryCode\>。 例如，en-US。|  
|<a name="traceid" />BingAPIs-TraceId|响应标头。<br /><br /> 包含请求详细信息的日志条目 ID。 发生错误时，捕获此 ID。 如果无法确定并解决问题，请纳入此 ID 以及提供给支持团队的其他信息。|  
|<a name="subscriptionkey" />Ocp-Apim-Subscription-Key|必需请求标头。<br /><br /> 在[认知服务](https://www.microsoft.com/cognitive-services/)中注册此服务时收到的订阅密钥。|  
|<a name="pragma" />Pragma|可选请求标头<br /><br /> 默认情况下，必应返回缓存的内容（如果适用）。 若要防止必应返回缓存的内容，请将 Pragma 标头设置为 no-cache（例如，Pragma: no-cache）。
|<a name="useragent" />User-Agent|可选请求标头。<br /><br /> 发出请求的用户代理。 必应使用用户代理为移动用户提供优化体验。 尽管是可选的，但还是建议始终指定此标头。<br /><br /> user-agent 应该是任何常用浏览器发送的字符串。 有关用户代理的信息，请参阅 [RFC 2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)。<br /><br /> 下面是 user-agent 字符串示例。<br /><ul><li>Windows Phone&mdash;Mozilla/5.0（兼容；MSIE 10.0；Windows Phone 8.0；Trident/6.0；IEMobile/10.0；ARM；Touch；NOKIA；Lumia 822）<br /><br /></li><li>Android&mdash;Mozilla/5.0（Linux；U；Android 2.3.5；en-us；SCH-I500 Build/GINGERBREAD）AppleWebKit/533.1（KHTML，如 Gecko）版本/4.0 Mobile Safari/533.1<br /><br /></li><li>iPhone&mdash;Mozilla/5.0（iPhone；CPU iPhone OS 6_1，如 Mac OS X）AppleWebKit/536.26（KHTML；如 Gecko）Mobile/10B142 iPhone4；1 BingWeb/3.03.1428.20120423<br /><br /></li><li>PC&mdash;Mozilla/5.0（Windows NT 6.3；WOW64；Trident/7.0；Touch；rv:11.0），如 Gecko<br /><br /></li><li>iPad&mdash;Mozilla/5.0（iPad；CPU OS 7_0，如 Mac OS X）AppleWebKit/537.51.1（KHTML，如 Gecko）版本/7.0 Mobile/11A465 Safari/9537.53</li></ul>|
|<a name="clientid" />X-MSEdge-ClientID|可选请求和响应标头。<br /><br /> 必应使用此标头跨必应 API 调用为用户提供一致的行为。 必应通常会发布新功能和改进，并将客户端 ID 用作密钥以在不同航班上分配客流量。 如果未跨多个请求将相同的客户端 ID 用于用户，则必应可能将用户分配给多个冲突的航班。 分配给多个冲突航班可能导致用户体验不一致。 例如，如果第二个请求与第一个请求的航班分配不同，体验可能会出现意外。 此外，必应可以使用客户端 ID 根据客户端 ID 搜索历史记录来定制 Web 结果，为用户提供更丰富的体验。<br /><br /> 通过分析由客户端 ID 生成的活动，必应还会使用此标头来提高结果排名。 相关改进有助于提高必应 API 交付的结果质量，从而提高 API 客户的点击率。<br /><br /> 重要提示：尽管是可选的，但应将此标头视为必选。 对于同一最终用户和设备组合，如果跨多个请求保留客户端 ID，则 1) API 客户可以获取一致的用户体验；2) 可通过必应 API 提高结果质量，从而提高点击率。<br /><br /> 下面是适用于此标头的基本用法规则。<br /><ul><li>在设备上使用你的应用程序的每个用户必须具有必应生成的唯一客户端 ID。<br /><br/>如果未在请求中包含此标头，必应会生成 ID，然后在 X-MSEdge-ClientID 响应标头中将其返回。 仅当用户首次在设备上使用应用时，才不可以在请求中包含此标头。<br /><br/></li><li>注意：必须确保此客户 ID 不可链接到任何经过身份验证的用户帐户信息。</li><li>针对应用为设备上的此用户生成的每个必应 API 请求，使用客户 ID。<br /><br/></li><li>保留客户 ID。 若要在浏览器应用中保留 ID，请使用持久性 HTTP Cookie 来确保所有会话均使用此 ID。 请勿使用会话 Cookie。 对于移动应用等其他应用，请使用设备的持久存储来保留 ID。<br /><br/>下次用户在该设备上使用你的应用时，会获取保留的客户端 ID。</li></ul><br /> **注意：** 必应响应不一定包含此标头。 如果响应包含此标头，请针对该设备上的用户捕获客户端 ID 并将其用于所有后续必应请求。<br /><br /> **注意：** 如果包含 X-MSEdge-ClientID，不可在请求中包含 Cookie。|  
|<a name="clientip" />X-MSEdge-ClientIP|可选请求标头。<br /><br /> 客户端设备的 IPv4 或 IPv6 地址。 IP 地址用于发现用户的位置。 必应使用位置信息来确定安全搜索行为。<br /><br /> **注意：** 尽管是可选的，但还是建议始终指定此标头和 X-Search-Location 标头。<br /><br /> 不要混淆地址（例如，通过将最后一个八位字节更改为 0 来混淆地址）。 混淆地址会导致位置未处于设备实际位置附近，这可能导致必应提供错误的结果。|  
|<a name="location" />X-Search-Location|可选请求标头。<br /><br /> 以分号分隔的键/值对列表，描述客户端的地理位置。 必应使用位置信息来确定安全搜索行为并返回相关的本地内容。 以 \<键\>:\<值\> 形式指定键/值对。 以下是用于指定用户位置的键。<br /><br /><ul><li>lat&mdash;必需。 客户位置的纬度，以度为单位。 纬度必须大于或等于 -90.0 且小于或等于 +90.0。 负值表示南纬，正值表示北纬。<br /><br /></li><li>long&mdash;必需。 客户位置的经度，以度为单位。 经度必须大于或等于 -180.0 且小于或等于 +180.0。 负值表示西经，正值表示东经。<br /><br /></li><li>re&mdash;必需。 半径（以米为单位），指定坐标的水平准确性。 传递设备位置服务返回的值。 典型的值可能是：22m - GPS/Wi-Fi、380m - 蜂窝基站三角网定位、18,000m - 反向 IP 查询。<br /><br /></li><li>ts&mdash;可选。 客户位于相应位置时的 UTC UNIX 时间戳。 （UNIX 时间戳是自 1970 年 1 月 1 日起的秒数。）<br /><br /></li><li>head&mdash;可选。 客户端的相对航向或旅行方向。 以度数指定旅行方向（从 0 到 360），相对于正北方向顺时针计数。 如果 `sp` 键为非零值，则指定此键。<br /><br /></li><li>sp&mdash;可选。 客户设备移动的水平速度（速度），以米/秒为单位。<br /><br /></li><li>alt&mdash;可选。 客户设备的高度，以米为单位。<br /><br /></li><li>are&mdash;可选。 半径（以米为单位），指定坐标的垂直准确性。 如果仅指定 `alt` 键，则指定此键。<br /><br /></li></ul> 注意：尽管许多键是可选的，但提供的信息越多，位置结果越精确。<br /><br /> 注意：尽管是可选的，但还是建议始终指定用户的地理位置。 如果客户的 IP 地址未准确反映用户的物理位置（例如，如果客户使用 VPN），提供位置尤其重要。 为了获得最佳结果，应包含此标头和 X-MSEdge-ClientIP 标头，但应至少包含此标头。|

> [!NOTE] 
> 请记住，使用条款要求遵守所有适用的法律，包含这些标头的用法。 例如，在某些管辖区（如欧洲）中，在用户设备上放置某些跟踪设备之前，需要获取用户同意。


<a name="content-form-types" />

### <a name="content-form-types"></a>内容表单类型

每个请求必须包含 Content-Type 标头。 标头必须设置为：multipart/form-data；boundary=\<边界字符串\>，其中\<边界字符串\>是唯一的不透明字符串，用于标识表单数据的边界。 例如，boundary=boundary_1234-abcd。


如果向视觉搜索发送图像令牌或 URL，以下内容将显示必须在 POST 正文中包含的表单数据。 表单数据必须包含 Content-Disposition 标头，且必须将其 `name` 参数设置为“knowledgeRequest”。 有关 `imageInfo` 对象的详细信息，请参阅[请求](#the-request)。


```
--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
    "imageInfo" : {
        "url" : "https://contoso.com/2018/05/fashion/red.jpg"
    }
}

--boundary_1234-abcd--
```

如果上传本地图像，以下内容将显示必须在 POST 正文中包含的表单数据。 窗体数据必须包含 Content-Disposition 标头。 其 `name` 参数必须设置为“image”，`filename` 参数可以设置为任何字符串。 Content-Type 标头可以设置为任何常用的图像 MIME 类型。 表单内容是图像的二进制文件。 可以上传的最大图像大小为 1 MB。 宽度或高度不得超过 1,500 像素。


```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
Content-Type: image/jpeg

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

以下介绍了如何指定已上传图像的感兴趣区域。

```
--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
    "imageInfo" : {
        "cropArea" : {
            "top" : 0.2,
            "left" : 0.3,
            "bottom" : 0.7,
            "right" : 0.6
        }
    }
}

--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="image"
Content-Type: image/jpeg


ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```



### <a name="example-request"></a>示例请求

以下介绍了传递图像令牌的完整图像见解请求和感兴趣的区域。 从 /images/search 的先前调用中获取见解令牌。


```  
POST https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch?mkt=en-us HTTP/1.1  
Content-Type: multipart/form-data; boundary=boundary_1234-abcd
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com 

--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
    "imageInfo" : {
        "imageInsightsToken" : "mid_D6426898706EC7..."
        "cropArea" : {
            "top" : 0.1,
            "left" : 0.2,
            "bottom" : 0.7,
            "right" : 0.5
        }
    }
}

--boundary_1234-abcd--
```


## <a name="the-response"></a>响应

如果存在适用于图像的见解，响应包含一个或多个 `tags`（包含见解）。 `image` 字段包含输入图像的见解令牌。

```json
{
  "_type" : "ImageKnowledge",
  "tags" : [
    {...},
    {...},
    {...},
    {...},
    {...}
  ],
  "image" : {
    "imageInsightsToken" : "bcid_AF8C9CA409421B..."
  }
}
```

`tags` 字段包含显示名称和操作（见解）列表。 其中一个标记包含 `displayName` 字段（设置为空字符串）。 此标记包含默认见解，如包含图像的网页、视觉上相似的图像、在图像中找到的项的购物源。 因为对整个图像感兴趣，所以默认见解标记不包含感兴趣区域对应的边框。


```json
{
  "_type" : "ImageKnowledge",
  "tags" : [
    {
      "displayName" : "",
      "actions" : [
        {...},
        {...},
        {...},
        {...}
      ]
    },
    {...},
    {...},
    {...},
    {...}
  ],
  "image" : {
    "imageInsightsToken" : "bcid_AF8C9CA409421B..."
  }
}
```

有关默认见解的列表，请参阅[默认见解](./default-insights-tag.md)。



剩余的标记包含用户可能感兴趣的其他见解。 例如，如果图像包含文本，其中一个标记可能包含 TextResults 见解（包含已识别文本）。 或者，如果必应识别图像中的实体（人物、位置或事物），其中一个标记可能标识实体。 视觉搜索还可以返回一组派生自输入图像的不同术语（标记）。 这些标记允许用户浏览在图像中找到的概念。 例如，如果输入图像是一位著名运动员，其中一个标记可能是体育（包含指向体育图像的链接）。

每个标记包含可用于对见解进行分类的显示名称、用于标识该见解适用于的感兴趣区域边框、见解本身和图像的缩略图。 例如，如果图像中的人员穿着运动衫，其中一个标记可能包含限定运动衫的边框，并包括 VisualSearch 和 ProductVisualSearch 见解。 另一个标记可能包含 ImageResults 见解（其中包含 /images/search API 请求的 URL，用于获取与主题相关的图像；或 Bing.com 搜索 URL，用于将用户转到 Bing.com 图像搜索结果）。

默认见解以外的所有标记都包括用于标识感兴趣区域的边框。 例如，如果图像包含多个已识别的人员，标记可能包括每个人员的边框，或如果该图像包含已识别的服装项，标记可能包括每个已识别服装项的边框。 可以使用边框创建单击时图像上的热点，提供有关图像区域中内容的详细信息。 对于标识整个图像的边框，图像中不应包含热点。

### <a name="text-recognition"></a>文本识别

如果图像包含服务识别的文本，其中一个标记将包含 TextResults 见解（操作）。 该见解的 `displayName` 包含已识别的文本。 

```json
    {
        "image" : {
            "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23Text..."
        },
        "displayName" : "##TextRecognition",
        "boundingBox" : {
            "queryRectangle" : {
                "topLeft" : {"x" : 0, "y" : 0},
                "topRight" : {"x" : 1, "y" : 0},
                "bottomRight" : {"x" : 1, "y" : 1},
                "bottomLeft" : {"x" : 0, "y" : 1}
            },
            "displayRectangle" : {
                "topLeft" : {"x" : 0, "y" : 0},
                "topRight" : {"x" : 1, "y" : 0},
                "bottomRight" : {"x" : 1, "y" : 1},
                "bottomLeft" : {"x" : 0, "y" : 1}
            }
        },
        "actions" : [{
            "displayName" : "WALK BIKE ACROSS BRIDGE",
            "actionType" : "TextResults"
        }],
        "sources" : ["OCR"]
    }
```

由于标记的 `displayName` 字段包含 ##TextRecognition，因此不要将其用作 UX 中的类别标题。 这适用于以 ## 开头的任何显示名称。 请改用操作的显示名称。


文本识别也可以识别商业名片上的联系人信息，如电话号码和电子邮件地址。 边框标识卡上联系人信息的位置。 

```json
    {
      "image" : {
        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23TextRecognition..."
      },
      "displayName" : "##TextRecognition",
      "boundingBox" : {
        "queryRectangle" : {
          "topLeft" : {"x" : 0.635, "y" : 0},
          "topRight" : {"x" : 0.77, "y" : 0},
          "bottomRight" : {"x" : 0.77, "y" : 0.4873333},
          "bottomLeft" : {"x" : 0.635, "y" : 0.4873333}
        },
        "displayRectangle" : {
          "topLeft" : {"x" : 0.635, "y" : 0},
          "topRight" : {"x" : 0.77, "y" : 0},
          "bottomRight" : {"x" : 0.77, "y" : 0.4873333},
          "bottomLeft" : {"x" : 0.635, "y" : 0.4873333}
        }
      },
      "actions" : [
        {
          "url" : "tel:888%20555%201212",
          "actionType" : "Uri"
        }
      ],
      "sources" : ["OCR"]
    },
    {
      "image" : {
        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23TextRecognition..."
      },
      "displayName" : "##TextRecognition",
      "boundingBox" : {
        "queryRectangle" : {
          "topLeft" : {"x" : 0.63, "y" : 0},
          "topRight" : {"x" : 0.866, "y" : 0},
          "bottomRight" : {"x" : 0.866, "y" : 0.5553334},
          "bottomLeft" : {"x" : 0.63, "y" : 0.5553334}
        },
        "displayRectangle" : {
          "topLeft" : {"x" : 0.63, "y" : 0},
          "topRight" : {"x" : 0.866, "y" : 0},
          "bottomRight" : {"x" : 0.866, "y" : 0.5553334},
          "bottomLeft" : {"x" : 0.63, "y" : 0.5553334}
        }
      },
      "actions" : [
        {
          "url" : "mailto:someone@outlook.com",
          "actionType" : "Uri"
        }
      ],
      "sources" : ["OCR"]
    },
    {
      "image" : {
        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23TextRecognition..."
      },
      "displayName" : "##TextRecognition",
      "boundingBox" : {
        "queryRectangle" : {
          "topLeft" : {"x" : 0, "y" : 0},
          "topRight" : {"x" : 1, "y" : 0},
          "bottomRight" : {"x" : 1, "y" : 1},
          "bottomLeft" : {"x" : 0, "y" : 1}
        },
        "displayRectangle" : {
          "topLeft" : {"x" : 0, "y" : 0},
          "topRight" : {"x" : 1, "y" : 0},
          "bottomRight" : {"x" : 1, "y" : 1},
          "bottomLeft" : {"x" : 0, "y" : 1}
        }
      },
      "actions" : [
        {
          "displayName" : "CHARLENE WHITNEY Graphic Designer 888 555 1212 someone@outlook.com www.contoso.com",
          "actionType" : "TextResults"
        }
      ],
      "sources" : ["OCR"]
    }
```

如果图像包含识别的实体，如人物、位置或事物，其中一个标记可能包含 Entity 见解。 

```json
    {
      "image" : {
        "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?q=Statue+of+Liberty..."
      },
      "displayName" : "Statue of Liberty",
      "boundingBox" : {
        "queryRectangle" : {
          "topLeft" : {"x" : 0.40625, "y" : 0.1757813},
          "topRight" : {"x" : 0.6171875, "y" : 0.1757813},
          "bottomRight" : {"x" : 0.6171875, "y" : 0.3867188},
          "bottomLeft" : {"x" : 0.40625, "y" : 0.3867188}
        },
        "displayRectangle" : {
          "topLeft" : {"x" : 0.40625, "y" : 0.1757813},
          "topRight" : {"x" : 0.6171875, "y" : 0.1757813},
          "bottomRight" : {"x" : 0.6171875, "y" : 0.3867188},
          "bottomLeft" : {"x" : 0.40625, "y" : 0.3867188}
        }
      },
      "actions" : [
        {
          "_type" : "ImageEntityAction",
          "webSearchUrl" : "https:\/\/www.bing.com\/search?q=Statue+of+Liberty",
          "displayName" : "Statue of Liberty",
          "actionType" : "Entity",
        }
      ]
    }
```



## <a name="next-steps"></a>后续步骤

要快速了解如何使用第一个请求，请参阅快速入门：[C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md) | [Python](quickstarts/python.md)。

试用 API。 转到[视觉搜索 API 测试控制台](https://dev.cognitive.microsoft.com/docs/services/878c38e705b84442845e22c7bff8c9ac)。 


自行熟悉[视觉搜索 API 参考](https://aka.ms/bingvisualsearchreferencedoc)。 此参考文档包含可以用来请求搜索结果的终结点、标头和查询参数的列表。 此外还包含响应对象的定义。 

确保阅读[必应使用和显示要求](./use-and-display-requirements.md)，以免违反关于搜索结果使用要求的任何规则。


