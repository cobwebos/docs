---
title: 将必应图像搜索 API v5 升级到 v7 | Microsoft Docs
description: 标识需要更新以使用版本 7 的应用程序部分。
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 7F78B91F-F13B-40A4-B8A7-770FDB793F0F
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: b4d785eafe9ced6fb12e2dac3282dfd286849eb6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365543"
---
# <a name="image-search-api-upgrade-guide"></a>图像搜索 API 升级指南

此升级指南介绍必应图像搜索 API 的版本 5 与版本 7 之间的更改。 使用本指南可帮助你识别需要更新以使用版本 7 的应用程序部分。

## <a name="breaking-changes"></a>重大变化

### <a name="endpoints"></a>终结点

- 终结点的版本号已从 v5 更改为 v7。 例如，https://api.cognitive.microsoft.com/bing/\*\*v7.0**/images/search。

### <a name="error-response-objects-and-error-codes"></a>错误响应对象和错误代码

- 现在，所有失败请求都应在响应正文中包含 `ErrorResponse` 对象。

- 将以下字段添加到 `Error` 对象。  
  - `subCode`&mdash;如果可能，将错误代码划分到不同的段中
  - `moreDetails`&mdash;有关 `message` 字段中描述的错误的其他信息
   

- 将 v5 错误代码替换成了以下可能的 `code` 和 `subCode` 值。

|代码|子代码|说明
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|只要出现任何子代码条件，必应就会返回 ServerError。 如果 HTTP 状态代码为 500，则响应包括这些错误。
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>已阻止|只要请求的任何部分无效，必应就会返回 InvalidRequest。 例如，缺少必需参数或参数值无效。<br/><br/>如果错误是 ParameterMissing 或 ParameterInvalidValue，则 HTTP 状态代码为 400。<br/><br/>如果错误是 HttpNotAllowed，则 HTTP 状态代码为 410。
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

- 已将 `modulesRequested` 查询参数重命名为 [modules](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modules)。  
  
- 将 Annotations 重命名为 Tags。 请参阅 [modules](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modules)，查询 Tags 的参数。  

- 将 ShoppingSources 筛选器值的受支持市场列表更改为仅限 en-US。 请参阅 [imageType](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetype)。  
 

### <a name="image-insights-changes"></a>图像见解更改
  
- 已将 [ImagesInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsightsresponse) 的 `annotations` 字段更改为 `imageTags`。  
  
- 已将 `AnnotationModule` 对象重命名为 [ImageTagsModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetagsmodule)。  
  
- 已将 `Annotation` 对象重命名为 [Tag](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#tag)，并且删除了 `confidence` 字段。  
  
- 已将 [Image](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image) 对象的 `insightsSourcesSummary` 字段重命名为 `insightsMetadata`。  
  
- 已将 `InsightsSourcesSummary` 对象重命名为 [InsightsMetadata](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightsmetadata)。  
  
- 添加了 `https://api.cognitive.microsoft.com/bing/v7.0/images/details` 终结点。 使用此终结点（而非 /images/search 终结点）请求图像见解。 请参阅[图像见解](./image-insights.md)。 
  
- 现在，以下查询参数仅对 `/images/details` 终结点有效。  
  
    -   [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken)  
    -   [modules](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modules)  
    -   [imgUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imgurl)  
    -   [cab](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cab)  
    -   [cal](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cal)  
    -   [car](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#car)  
    -   [cat](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cat)  
    -   [ct](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#ct)  
  
- 已将 `ImageInsightsResponse` 对象重命名为 [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsights)。  
  
- 更改了 [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsights) 对象中以下字段的数据类型。  
  
    -   已将 `relatedCollections` 字段的类型从 `ImageGallery[]` 更改为 [RelatedCollectionsModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#relatedcollectionsmodule)。  
  
    -   已将 `pagesIncluding` 字段的类型从 `Image[]` 更改为 [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagesmodule)。  
  
    -   已将 `relatedSearches` 字段的类型从 `Query[]` 更改为 [RelatedSearchesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#relatedsearchesmodule)。  
  
    -   已将 `recipes` 字段的类型从 `Recipe[]` 更改为 [RecipesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#recipesmodule)。  
  
    -   已将 `visuallySimilarImages` 字段的类型从 `Image[]` 更改为 [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagesmodule)。  
  
    -   已将 `visuallySimilarProducts` 字段的类型从 `ProductSummaryImage[]` 更改为 [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagesmodule)。  
  
    -   已删除 `ProductSummaryImage` 对象，并将产品相关字段移到 [Image](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image) 对象中。 仅当图像作为外观相似产品的一部分包含在图像见解响应中时，`Image` 对象才包含产品相关的字段。  
  
    -   已将 `recognizedEntityGroups` 字段的类型从 `RecognizedEntityGroup[]` 更改为 [RecognizedEntitiesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#recognizedentitiesmodule)。  
  
-   已将 [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsightsresponse) 的 `categoryClassification` 字段重命名为 `annotations`，并将其类型改为 `AnnotationsModule`。  

### <a name="images-answer"></a>Images 响应

-   已从 [Images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) 中删除 displayShoppingSourcesBadges 和 displayRecipeSourcesBadges 字段。  
  
-   已将 [Images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) 的 `nextOffsetAddCount` 字段重命名为 `nextOffset`。 偏移量的使用方式也发生了更改。 以前将[偏移量](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#offset)查询参数设置为 `nextOffsetAddCount` 值加上前一个偏移量的值再加上结果中的图像数。 现在将 `offset` 设置为 `nextOffset` 值。  
    
  
## <a name="non-breaking-changes"></a>非重大变化

### <a name="query-parameters"></a>查询参数
  
- 将 Transparent 添加为可能的 [imageType](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetype) 筛选器值。 Transparent 筛选器仅返回带有透明背景的图像。

- 将 Any 添加为可能的 [license](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#license) 筛选器值。 Any 筛选器仅返回受到许可的图像。
  
- 添加了 [maxFileSize](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#maxfilesize) 和 [minFileSize](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#minfilesize) 查询参数。 使用这些筛选器可返回特定文件大小范围内的图像。  
  
- 添加了 [maxHeight](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#maxheight)、[minHeight](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#minheight)、[maxWidth](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#maxwidth) 和 [minWidth](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#minwidth) 查询参数。 使用这些筛选器可返回具有特定范围内高度和宽度的图像。  

### <a name="object-changes"></a>对象更改
  
- 向 [Offer](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#offer) 对象添加了 `description` 和 `lastUpdated` 字段。  
  
- 向 [ImageGallery](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagegallery) 对象添加了 `name` 字段。  
  
- 向 [Images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) 对象添加了 `similarTerms`。 此字段包含与用户的查询字符串意义相似的术语列表。  
