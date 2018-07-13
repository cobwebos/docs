---
title: Microsoft 文本翻译 API V2.0 参考 | Microsoft Docs
titleSuffix: Cognitive Services
description: Microsoft 文本翻译 API V2.0 的参考文档。
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 05/15/2018
ms.author: v-jansko
ms.openlocfilehash: e32e28608d2fecf27b61acff74af7eb6849f0ba1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366650"
---
# <a name="translator-text-api-v20"></a>文本翻译 API v2.0

> [!IMPORTANT]
> 此版文本翻译 API 已弃用。 [查看文本翻译 API v3 的文档](v3-0-reference.md)。

Microsoft 文本翻译 API V2 可以无缝集成到应用程序、网站、工具或其他解决方案中，提供多语言用户体验。 它采用行业标准，可以在任何硬件平台上使用，以及在任何操作系统中使用，用于执行语言翻译以及其他语言相关操作，例如文本语言检测或文本转语音操作。 有关 Microsoft 翻译 API 的详细信息，请单击此处。

## <a name="getting-started"></a>入门
若要访问 Microsoft 文本翻译 API，需[注册 Microsoft Azure](../translator-text-how-to-signup.md)。

## <a name="authorization"></a>授权
只要调用 Microsoft 文本翻译 API，就需要使用订阅密钥进行身份验证。 此 API 支持两种身份验证模式：

* 使用访问令牌。 使用**步骤** 9 中提到的订阅密钥，通过向授权服务发出 POST 请求来生成访问令牌。 有关详细信息，请参阅令牌服务文档。 使用 Authorization 标头或 access_token 查询参数，将访问令牌传递给翻译工具服务。 访问令牌的有效期为 10 分钟。 每 10 分钟获取一次新的访问令牌，在这 10 分钟内，始终对重复的请求使用同一访问令牌。

* 直接使用订阅密钥。 将订阅密钥作为请求中包括的 `Ocp-Apim-Subscription-Key` 标头中的值传递给翻译 API。 在此模式中，不需调用身份验证令牌服务即可生成访问令牌。

将订阅密钥和访问令牌视为不应允许查看的机密。

## <a name="profanity-handling"></a>不雅内容处理
通常，翻译工具服务在翻译中会保留源中存在的不雅内容。 不雅程度和使词语不雅的语境在不同的文化之间有所不同，因此，目标语言中的不雅程度可能会放大或缩小。

如果无论源文本中是否存在不雅内容，你都希望避免在翻译中出现不雅内容，可以将不雅内容筛选项用于支持它的方法。 使用该选项可以选择是要看到不雅内容被删除、标有相应标记还是不被执行任何操作。 就 `ProfanityAction` 来说，接受的值为 `NoAction`（默认）、Marked、`Deleted`。


|ProfanityAction    |操作 |示例源（日语）  |示例翻译（中文）  |
|:--|:--|:--|:--|
|NoAction   |默认。 与不设置此选项等效。 不雅内容会从源传递到目标。        |彼はジャッカスです。     |他是一个笨蛋。   |
|Marked     |不雅词语会括在 XML 标记 <profanity> 和 </profanity> 中。     |彼はジャッカスです。 |他是一个<profanity>笨蛋</profanity>。    |
|Deleted    |不雅词语会从输出中删除，不进行替换。     |彼はジャッカスです。 |他是一个。   |

    
## <a name="excluding-content-from-translation"></a>将内容从翻译中排除
翻译 HTML (`contentType=text/html`) 之类的带标记的内容时，有时候需从翻译中排除特定的内容。 可以使用属性 `class=notranslate` 指定应保留不译的内容。 在以下示例中，第一个 `div` 元素中的内容不会翻译，第二个 `div` 元素中的内容则会翻译。

```HTML
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

## <a name="get-translate"></a>GET /Translate

### <a name="implementation-notes"></a>实现说明
将一种语言的文本字符串翻译为另一种语言。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/Translate`。

**返回值：** 一个字符串，表示已翻译的文本。

如果以前已使用 `AddTranslation` 或 `AddTranslationArray` 为相同的源句输入了一个评分为 5 或更高的翻译，则 `Translate` 只返回适用于系统的首选内容。 “相同的源句”意味着完全相同（100% 匹配），但大小写、空格、标记值以及句尾的标点除外。 如果没有存储 5 分或更高分的评分，则返回的结果会是 Microsoft 翻译工具的自动翻译。

### <a name="response-class-status-200"></a>响应类（状态 200）

字符串

响应内容类型：application/xml 

### <a name="parameters"></a>parameters

|参数|值|说明    |参数类型|数据类型|
|:--|:--|:--|:--|:--|
|appid  |(empty)    |必需。 如果使用了 Authorization 或 Ocp-Apim-Subscription-Key 标头，请将 appid 字段留空，否则请包括一个包含 "Bearer" + " " + "access_token" 的字符串。|query|字符串|
|text|(empty)   |必需。 一个字符串，表示要翻译的文本。 文本大小不得超过 10000 个字符。|query|字符串|
|from|(empty)   |可选。 一个表示翻译文本语言代码的字符串。 例如，en 表示英语。|query|字符串|
|to|(empty) |必需。 一个字符串，表示进行文本翻译时目标语言的语言代码。|query|字符串|
|contentType|(empty)    |可选。 要翻译的文本的格式。 支持的格式为 text/plain（默认）和 text/html。 HTML 必须是格式正确的完整元素。|query|字符串|
|category|(empty)   |可选。 一个字符串，包含翻译的类别（领域）。 默认为“general”。|query|字符串|
|授权|(empty)  |必需，前提是 appid 字段或 Ocp-Apim-Subscription-Key 标头未指定。 授权令牌："Bearer" + " " + "access_token"。|标头的值开始缓存响应|字符串|
|Ocp-Apim-Subscription-Key|(empty)  |必需，前提是 appid 字段或 Authorization 标头未指定。|标头的值开始缓存响应|字符串|


### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400    |请求错误。 请查看输入参数和详细的错误响应。|
|401    |凭据无效|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供请求的大致日期和时间，以及包括在响应标头 X-MS-Trans-Info 中的请求 ID。|
|503    |服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|

## <a name="post-translatearray"></a>POST /TranslateArray

### <a name="implementation-notes"></a>实现说明
使用 `TranslateArray` 方法检索多个源文本的翻译。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/TranslateArray`。

请求正文的格式应如下所示：

```
<TranslateArrayRequest>
  <AppId />
  <From>language-code</From>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <ReservedFlags xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" />
    <State xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >int-value</State>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</Uri>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</User>
  </Options>
  <Texts>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
  </Texts>
  <To>language-code</To>
</TranslateArrayRequest>
```

`TranslateArrayRequest` 中的元素包括：


* `appid`：必需。 如果使用了 `Authorization` 或 `Ocp-Apim-Subscription-Key` 标头，请将 appid 字段留空，否则请包括一个包含 `"Bearer" + " " + "access_token"` 的字符串。
* `from`：可选。 一个字符串，表示进行文本翻译时源语言的语言代码。 如果留空，则响应会包括语言自动检测的结果。
* `options`：可选。 一个 `Options` 对象，包含下面列出的值。 它们都是可选的，并且默认为最常用的设置。 指定的元素必须按字母顺序列出。
    - `Category`：一个字符串，包含翻译的类别（领域）。 默认为 `general`。
    - `ContentType`：要翻译的文本的格式。 支持的格式为 `text/plain`（默认）、`text/xml`、`text/html`。 HTML 必须是格式正确的完整元素。
    - `ProfanityAction`：指定如何处理上述不雅内容。 就 `ProfanityAction` 来说，接受的值为 `NoAction`（默认）、`Marked`、`Deleted`。
    - `State`：用户状态，可帮助关联请求和响应。 响应中将返回相同的内容。
    - `Uri`：按此 URI 筛选结果。 默认：`all`。
    - `User`：按此用户筛选结果。 默认：`all`。
* `texts`：必需。 一个包含要翻译文本的数组。 所有字符串必须采用同一语言。 要翻译的所有文本总共不得超过 10000 个字符。 最大数组元素数为 2000。
* `to`：必需。 一个字符串，表示进行文本翻译时目标语言的语言代码。

可选元素可以省略。 属于 TranslateArrayRequest 的直接子项的元素必须按字母顺序列出。

TranslateArray 方法接受 `application/xml` 或 `text/xml` 作为 `Content-Type`。

**返回值：** 一个 `TranslateArrayResponse` 数组。 每个 `TranslateArrayResponse` 都有以下元素：

* `Error`：指示一个错误（如果有）。 否则设置为 null。
* `OriginalSentenceLengths`：一个整数数组，指示原始的源文本中每个句子的长度。 数组的长度指示句子数。
* `TranslatedText`：翻译的文本。
* `TranslatedSentenceLengths`：一个整数数组，指示翻译的文本中每个句子的长度。 数组的长度指示句子数。
* `State`：用户状态，可帮助关联请求和响应。 返回的内容与请求中的一样。

响应正文的格式如下所示。

```
<ArrayOfTranslateArrayResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"
  xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
  <TranslateArrayResponse>
    <From>language-code</From>
    <OriginalTextSentenceLengths xmlns:a="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
      <a:int>int-value</a:int>
    </OriginalTextSentenceLengths>
    <State/>
    <TranslatedText>string-value</TranslatedText>
    <TranslatedTextSentenceLengths xmlns:a="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
      <a:int>int-value</a:int>
    </TranslatedTextSentenceLengths>
  </TranslateArrayResponse>
</ArrayOfTranslateArrayResponse>
```

### <a name="response-class-status-200"></a>响应类（状态 200）
成功的响应包括一个 `TranslateArrayResponse` 数组，采用的格式如上所述。

字符串

响应内容类型：application/xml

### <a name="parameters"></a>parameters

|参数|值|说明|参数类型|数据类型|
|:--|:--|:--|:--|:--|
|授权|(empty) |必需，前提是 appid 字段或 Ocp-Apim-Subscription-Key 标头未指定。 授权令牌："Bearer" + " " + "access_token"。|标头的值开始缓存响应|字符串|
|Ocp-Apim-Subscription-Key|(empty)|必需，前提是 appid 字段或 Authorization 标头未指定。|标头的值开始缓存响应|字符串|

### <a name="response-messages"></a>响应消息

|HTTP 状态代码   |原因|
|:--|:--|
|400    |请求错误。 请查看输入参数和详细的错误响应。 常见错误包括： <ul><li>数组元素不能为空</li><li>类别无效</li><li>源语言无效</li><li>目标语言无效</li><li>请求包含的元素太多</li><li>不支持源语言</li><li>不支持目标语言</li><li>翻译请求的数据太多</li><li>HTML 格式有误</li><li>在翻译请求中传递的字符串过多</li></ul>|
|401    |凭据无效|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供请求的大致日期和时间，以及包括在响应标头 X-MS-Trans-Info 中的请求 ID。|
|503    |服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|

## <a name="post-getlanguagenames"></a>POST /GetLanguageNames

### <a name="implementation-notes"></a>实现说明
检索已作为 `languageCodes` 参数传入并已使用传递的区域设置语言本地化的语言的友好名称。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/GetLanguageNames`。

请求正文包括一个字符串数组，该数组代表需检索其友好名称的语言所对应的 ISO 639-1 语言代码。 例如：

```
<ArrayOfstring xmlns:i="http://www.w3.org/2001/XMLSchema-instance"  xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>zh</string>
    <string>en</string>
</ArrayOfstring>
```

**返回值：** 一个字符串数组，其包含的语言名称受翻译工具服务的支持，并已本地化为请求的语言。

### <a name="response-class-status-200"></a>响应类（状态 200）
一个字符串数组，其包含的语言名称受翻译工具服务的支持，并已本地化为请求的语言。

字符串

响应内容类型：application/xml
 
### <a name="parameters"></a>parameters

|参数|值|说明|参数类型|数据类型|
|:--|:--|:--|:--|:--|
|appid|(empty)|必需。 如果使用了 `Authorization` 或 `Ocp-Apim-Subscription-Key` 标头，请将 appid 字段留空，否则请包括一个包含 `"Bearer" + " " + "access_token"` 的字符串。|query|字符串|
|区域设置|(empty) |必需。 一个表示组合的字符串，该组合包含一个与某种语言关联的 ISO 639 双字母小写区域性代码，以及一个用于将语言名称本地化的 ISO 3166 双字母大写子区域性代码，或者一个单纯的 ISO 639 小写区域性代码。|query|字符串|
|授权|(empty)  |必需，前提是 appid 字段或 `Ocp-Apim-Subscription-Key` 标头未指定。 授权令牌：`"Bearer" + " " + "access_token"`。|标头的值开始缓存响应|字符串|
|Ocp-Apim-Subscription-Key|(empty)  |必需，前提是 appid 字段或 `Authorization` 标头未指定。|标头的值开始缓存响应|字符串|

### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400    |请求错误。 请查看输入参数和详细的错误响应。|
|401    |凭据无效|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供请求的大致日期和时间，以及包括在响应标头 X-MS-Trans-Info 中的请求 ID。|
|503    |服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|

## <a name="get-getlanguagesfortranslate"></a>GET /GetLanguagesForTranslate

### <a name="implementation-notes"></a>实现说明
获取一个语言代码的列表，其中的代码所代表的语言受翻译服务的支持。  `Translate` 和 `TranslateArray` 可以在这些语言的任意两种之间互译。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForTranslate`。

**返回值：** 一个字符串数组，其包含的语言代码受翻译工具服务的支持。

### <a name="response-class-status-200"></a>响应类（状态 200）
一个字符串数组，其包含的语言代码受翻译工具服务的支持。

字符串

响应内容类型：application/xml
 
### <a name="parameters"></a>parameters

|参数|值|说明|参数类型|数据类型|
|:--|:--|:--|:--|:--|
|appid|(empty)|必需。 如果使用了 `Authorization` 或 `Ocp-Apim-Subscription-Key` 标头，请将 appid 字段留空，否则请包括一个包含 `"Bearer" + " " + "access_token"` 的字符串。|query|字符串|
|授权|(empty)  |必需，前提是 `appid` 字段或 `Ocp-Apim-Subscription-Key` 标头未指定。 授权令牌：`"Bearer" + " " + "access_token"`。|标头的值开始缓存响应|字符串|
|Ocp-Apim-Subscription-Key|(empty)|必需，前提是 `appid` 字段或 `Authorization` 标头未指定。|标头的值开始缓存响应|字符串|

### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400    |请求错误。 请查看输入参数和详细的错误响应。|
|401    |凭据无效|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供请求的大致日期和时间，以及包括在响应标头 X-MS-Trans-Info 中的请求 ID。|
|503|服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|

## <a name="get-getlanguagesforspeak"></a>GET /GetLanguagesForSpeak

### <a name="implementation-notes"></a>实现说明
检索可用于语音合成的语言。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForSpeak`。

**返回值：** 一个字符串数组，其包含的语言代码受翻译工具服务的支持，可以进行语音合成。

### <a name="response-class-status-200"></a>响应类（状态 200）
一个字符串数组，其包含的语言代码受翻译工具服务的支持，可以进行语音合成。

字符串

响应内容类型：application/xml

### <a name="parameters"></a>parameters

|参数|值|说明|参数类型|数据类型|
|:--|:--|:--|:--|:--|
|appid|(empty)|必需。 如果使用了 `Authorization` 或 `Ocp-Apim-Subscription-Key` 标头，请将 appid 字段留空，否则请包括一个包含 `"Bearer" + " " + "access_token"` 的字符串。|query|字符串|
|授权|(empty)|必需，前提是 `appid` 字段或 `Ocp-Apim-Subscription-Key` 标头未指定。 授权令牌：`"Bearer" + " " + "access_token"`。|标头的值开始缓存响应|字符串|
|Ocp-Apim-Subscription-Key|(empty)|必需，前提是 `appid` 字段或 `Authorization` 标头未指定。|标头的值开始缓存响应|字符串|
 
### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400|请求错误。 请查看输入参数和详细的错误响应。|
|401|凭据无效|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供请求的大致日期和时间，以及包括在响应标头 `X-MS-Trans-Info` 中的请求 ID。|
|503    |服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|

## <a name="get-speak"></a>GET /Speak

### <a name="implementation-notes"></a>实现说明
返回以所需语言朗读的传入文本的 wave 或 mp3 流。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/Speak`。

**返回值：** 以所需语言朗读的传入文本的 wave 或 mp3 流。

### <a name="response-class-status-200"></a>响应类（状态 200）

binary

响应内容类型：application/xml 

### <a name="parameters"></a>parameters

|参数|值|说明|参数类型|数据类型|
|:--|:--|:--|:--|:--|
|appid|(empty)|必需。 如果使用了 `Authorization` 或 `Ocp-Apim-Subscription-Key` 标头，请将 appid 字段留空，否则请包括一个包含 `"Bearer" + " " + "access_token"` 的字符串。|query|字符串|
|text|(empty)   |必需。 一个字符串，包含 wave 流对应的指定要朗读的语言的一个或多个句子。 要朗读的文本的大小不得超过 2000 个字符。|query|字符串|
|语言|(empty)   |必需。 一个字符串，表示要朗读的文本所采用语言的受支持语言代码。 此代码必须存在于从 `GetLanguagesForSpeak` 方法返回的代码的列表中。|query|字符串|
|格式|(empty)|可选。 一个字符串，指定 content-type ID。 目前，`audio/wav` 和 `audio/mp3` 可用。 默认值为 `audio/wav`。|query|字符串|
|options|(empty)    |<ul><li>可选。 一个字符串，指定已合成语音的属性：<li>`MaxQuality` 和 `MinSize` 可以用来指定音频信号的质量。 使用 `MaxQuality` 可以获取质量最高的语音，而使用 `MinSize` 则可获取大小最小的语音。 默认为 `MinSize`。</li><li>`female` 和 `male` 可以用来指定语音的所需性别。 默认为 `female`。 使用垂直条形图。|` to include multiple options. For example  `MaxQuality|男性。</li></li></ul> |query|字符串|
|授权|(empty)|必需，前提是 `appid` 字段或 `Ocp-Apim-Subscription-Key` 标头未指定。 授权令牌：`"Bearer" + " " + "access_token"`。|标头的值开始缓存响应|字符串|
|Ocp-Apim-Subscription-Key|(empty)  |必需，前提是 `appid` 字段或 `Authorization` 标头未指定。|标头的值开始缓存响应|字符串|

### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400    |请求错误。 请查看输入参数和详细的错误响应。|
|401    |凭据无效|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供请求的大致日期和时间，以及包括在响应标头 `X-MS-Trans-Info` 中的请求 ID。|
|503    |服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|

## <a name="get-detect"></a>GET /Detect

### <a name="implementation-notes"></a>实现说明
使用 `Detect` 方法标识所选的一段文本的语言。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/Detect`。

**返回值：** 一个字符串，其包含的双字符语言代码适用于给定的文本。 。

### <a name="response-class-status-200"></a>响应类（状态 200）

字符串

响应内容类型：application/xml

### <a name="parameters"></a>parameters

|参数|值|说明|参数类型|数据类型|
|:--|:--|:--|:--|:--|
|appid|(empty)  |必需。 如果使用了 `Authorization` 或 `Ocp-Apim-Subscription-Key` 标头，请将 appid 字段留空，否则请包括一个包含 `"Bearer" + " " + "access_token"` 的字符串。|query|字符串|
|text|(empty)|必需。 一个字符串，所包含的一些文本需标识其语言。 文本大小不得超过 10000 个字符。|query| 字符串|
|授权|(empty)|必需，前提是 `appid` 字段或 `Ocp-Apim-Subscription-Key` 标头未指定。 授权令牌：`"Bearer" + " " + "access_token"`。|标头的值开始缓存响应|字符串|
|Ocp-Apim-Subscription-Key  |(empty)    |必需，前提是 `appid` 字段或 `Authorization` 标头未指定。|标头的值开始缓存响应|字符串|

### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400|请求错误。 请查看输入参数和详细的错误响应。|
|401    |凭据无效|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供请求的大致日期和时间，以及包括在响应标头 `X-MS-Trans-Info` 中的请求 ID。|
|503    |服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|


## <a name="post-detectarray"></a>POST /DetectArray

### <a name="implementation-notes"></a>实现说明
使用 `DetectArray` 方法同时标识一组字符串的语言。 对每个单独的数组元素进行独立的检测，为数组的每一行返回一个结果。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/DetectArray`。

请求正文的格式应如下所示。

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>string-value-1</string>
    <string>string-value-2</string>
</ArrayOfstring>
```

文本大小不得超过 10000 个字符。

**返回值：** 一个字符串数组，其包含的双字符语言代码适用于输入数组的每一行。

响应正文的格式如下所示。

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
  <string>language-code-1</string>
  <string>language-code-2</string>
</ArrayOfstring>
```

### <a name="response-class-status-200"></a>响应类（状态 200）
DetectArray 成功。 返回一个字符串数组，其包含的双字符语言代码适用于输入数组的每一行。

字符串

响应内容类型：application/xml
 
### <a name="parameters"></a>parameters

|参数|值|说明|参数类型|数据类型|
|:--|:--|:--|:--|:--|
|appid|(empty)|必需。 如果使用了 `Authorization` 或 `Ocp-Apim-Subscription-Key` 标头，请将 appid 字段留空，否则请包括一个包含 `"Bearer" + " " + "access_token"` 的字符串。|query|字符串|
|授权|(empty)|必需，前提是 `appid` 字段或 `Ocp-Apim-Subscription-Key` 标头未指定。 授权令牌：`"Bearer" + " " + "access_token"`。|标头的值开始缓存响应|字符串|
|Ocp-Apim-Subscription-Key|(empty)|必需，前提是 `appid` 字段或 Authorization 标头未指定。|标头的值开始缓存响应|字符串|

### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400    |请求错误。 请查看输入参数和详细的错误响应。|
|401    |凭据无效|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供请求的大致日期和时间，以及包括在响应标头 X-MS-Trans-Info 中的请求 ID。|
|503    |服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|

## <a name="get-addtranslation"></a>GET /AddTranslation

### <a name="implementation-notes"></a>实现说明

> [!IMPORTANT]
> **弃用说明：** 在 2018 年 1 月 31 日之后，此方法不接受新的句子提交，并且会发送错误消息。 请查看此公告，了解对协作翻译功能所做的更改。

向翻译记忆库添加翻译。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/AddTranslation`。

### <a name="response-class-status-200"></a>响应类（状态 200）

字符串

响应内容类型：application: xml
 
### <a name="parameters"></a>parameters

|参数|值|说明|参数类型|数据类型   |
|:--|:--|:--|:--|:--|
|appid|(empty)|必需。 如果使用了 `Authorization` 或 `Ocp-Apim-Subscription-Key` 标头，请将 appid 字段留空，否则请包括一个包含 `"Bearer" + " " + "access_token"` 的字符串。|query|字符串|
|originalText|(empty)|必需。 一个字符串，包含要翻译的文本。 字符串的最大长度为 1000 个字符。|query|字符串|
|translatedText|(empty) |必需。 一个字符串，包含以目标语言翻译的文本。 字符串的最大长度为 2000 个字符。|query|字符串|
|from|(empty)   |必需。 一个表示翻译文本语言代码的字符串。 en = 英语，de = 德语，依此类推。|query|字符串|
|to|(empty)|必需。 一个字符串，表示进行文本翻译时目标语言的语言代码。|query|字符串|
|rating|(empty) |可选。 一个整数，表示此字符串的质量评分。 值为 -10 到 10。 默认值为 1。|query|integer|
|contentType|(empty)    |可选。 要翻译的文本的格式。 支持的格式为 "text/plain" 和 "text/html"。 HTML 必须是格式正确的完整元素。   |query|字符串|
|category|(empty)|可选。 一个字符串，包含翻译的类别（领域）。 默认为“general”。|query|字符串|
|user|(empty)|必需。 一个字符串，用于跟踪提交发起方。|query|字符串|
|uri|(empty)|可选。 一个字符串，包含此翻译的内容位置。|query|字符串|
|授权|(empty)|必需，前提是 appid 字段或 `Ocp-Apim-Subscription-Key` 标头未指定。 授权令牌：`"Bearer" + " " + "access_token"`。    |标头的值开始缓存响应|字符串|
|Ocp-Apim-Subscription-Key|(empty)|必需，前提是 `appid` 字段或 `Authorization` 标头未指定。|标头的值开始缓存响应|字符串|

### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400    |请求错误。 请查看输入参数和详细的错误响应。|
|401    |凭据无效|
|410|不再支持 AddTranslation。|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供请求的大致日期和时间，以及包括在响应标头 X-MS-Trans-Info 中的请求 ID。|
|503    |服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|

## <a name="post-addtranslationarray"></a>POST /AddTranslationArray

### <a name="implementation-notes"></a>实现说明

> [!IMPORTANT]
> **弃用说明：** 在 2018 年 1 月 31 日之后，此方法不接受新的句子提交，并且会发送错误消息。 请查看此公告，了解对协作翻译功能所做的更改。

向翻译记忆库添加一个翻译数组。 它是数组版本的 `AddTranslation`。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/AddTranslationArray`。

请求正文的格式如下所示。

```
<AddtranslationsRequest>
  <AppId></AppId>
  <From>A string containing the language code of the source language</From>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</User>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Uri>
  </Options>
  <To>A string containing the language code of the target language</To>
  <Translations>
    <Translation xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">
      <OriginalText>string-value</OriginalText>
      <Rating>int-value</Rating>
      <Sequence>int-value</Sequence>
      <TranslatedText>string-value</TranslatedText>
    </Translation>
  </Translations>
</AddtranslationsRequest>
```

AddtranslationsRequest 元素中的元素包括：

* `AppId`：必需。 如果使用了 `Authorization` 或 `Ocp-Apim-Subscription-Key` 标头，请将 appid 字段留空，否则请包括一个包含 `"Bearer" + " " + "access_token"` 的字符串。
* `From`：必需。 一个字符串，包含源语言的语言代码。 必须是 `GetLanguagesForTranslate` 方法返回的语言之一。
* `To`：必需。 一个字符串，包含目标语言的语言代码。 必须是 `GetLanguagesForTranslate` 方法返回的语言之一。
* `Translations`：必需。 要添加到翻译记忆库的一个翻译数组。 每个翻译必须包含 originalText、translatedText 和评分。 每个 originalText 和 translatedText 的大小仅限 1000 个字符。 所有 originalText 和 translatedText 的总计不得超过 10000 个字符。 最大数组元素数为 100。
* `Options`：必需。 一组选项，包括 Category、ContentType、Uri 和 User。 User 为必填项。 Category、ContentType 和 Uri 为可选项。 指定的元素必须按字母顺序列出。

### <a name="response-class-status-200"></a>响应类（状态 200）
AddTranslationArray 方法成功。 2018 年 1 月 31 日以后，不接受句子提交。 此服务会以错误代码 410 进行响应。

字符串

响应内容类型：application/xml
 
### <a name="parameters"></a>parameters

|参数|值|说明|参数类型|数据类型|
|:--|:--|:--|:--|:--|
|授权|(empty)|必需，前提是 appid 字段或 Ocp-Apim-Subscription-Key 标头未指定。 授权令牌："Bearer" + " " + "access_token"。|标头的值开始缓存响应|字符串|
|Ocp-Apim-Subscription-Key|(empty)|必需，前提是 appid 字段或 Authorization 标头未指定。|标头的值开始缓存响应|字符串|

### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400    |请求错误。 请查看输入参数和详细的错误响应。|
|401    |凭据无效|
|410    |不再支持 AddTranslation。|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供请求的大致日期和时间，以及包括在响应标头 `X-MS-Trans-Info` 中的请求 ID。|
|503|服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|

## <a name="get-breaksentences"></a>GET /BreakSentences

### <a name="implementation-notes"></a>实现说明
将一段文本拆分成多个句子，并返回一个数组，其中包含每个句子的长度。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/BreakSentences`。

**返回值：** 一个整数数组，表示句子的长度。 数组的长度是句子的数量，而各个值是每个句子的长度。

### <a name="response-class-status-200"></a>响应类（状态 200）
一个整数数组，表示句子的长度。 数组的长度是句子的数量，而各个值是每个句子的长度。

integer

响应内容类型：application/xml 

### <a name="parameters"></a>parameters

|参数|值|说明|参数类型|数据类型|
|:--|:--|:--|:--|:--|
|appid|(empty)  |必需。 如果使用了 Authorization 或 Ocp-Apim-Subscription-Key 标头，请将 appid 字段留空，否则请包括一个包含 "Bearer" + " " + "access_token" 的字符串。|query| 字符串|
|text|(empty)   |必需。 一个字符串，表示要拆分成句子的文本。 文本大小不得超过 10000 个字符。|query|字符串|
|语言   |(empty)    |必需。 一个表示字符串，表示输入文本的语言代码。|query|字符串|
|授权|(empty)|必需，前提是 appid 字段或 Ocp-Apim-Subscription-Key 标头未指定。 授权令牌："Bearer" + " " + "access_token"。    |标头的值开始缓存响应|字符串|
|Ocp-Apim-Subscription-Key|(empty)|必需，前提是 appid 字段或 Authorization 标头未指定。|标头的值开始缓存响应|字符串|

### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400|请求错误。 请查看输入参数和详细的错误响应。|
|401|凭据无效|
|500|服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供请求的大致日期和时间，以及包括在响应标头 X-MS-Trans-Info 中的请求 ID。|
|503|服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|

## <a name="post-gettranslations"></a>POST /GetTranslations

### <a name="implementation-notes"></a>实现说明
从存储和 MT 引擎中检索给定语言对的翻译数组。 GetTranslations 不同于 Translate，因为它返回所有可用的翻译。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/GetTranslations`。

请求的正文包括可选的 TranslationOptions 对象，该对象的格式如下。

```
<TranslateOptions xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">
  <Category>string-value</Category>
  <ContentType>text/plain</ContentType>
  <ReservedFlags></ReservedFlags>
  <State>int-value</State>
  <Uri>string-value</Uri>
  <User>string-value</User>
</TranslateOptions>
```

`TranslateOptions` 对象包含下面列出的值。 它们都是可选的，并且默认为最常用的设置。 指定的元素必须按字母顺序列出。

* `Category`：一个字符串，包含翻译的类别（领域）。 默认为“general”。
* `ContentType`：唯一支持也是默认的选项为“text/plain”。
* `IncludeMultipleMTAlternatives`：布尔标志，用于确定是否应从 MT 引擎返回多个备选项。 有效值为 true 和 false（区分大小写）。 默认值为 false，并且仅包括 1 个备选项。 将此标志设置为 true 即可在翻译中生成人工备选项，这些备选项已完全集成到协作翻译框架 (CTF) 中。 此功能允许为在 CTF 中没有备选项的句子返回备选项，其方法是从解码器的 n-best 列表中添加人工备选项。
    - 评分 评分按下述方式应用：1) 最佳自动翻译的评分为 5。 2) CTF 中的备选项反映了审阅者的权威性，其评分为 -10 到 +10。 3) 自动生成的（N 个最佳）翻译备选项的评级为 0，匹配度为 100。
    - 备选项数 返回的备选项的数目最多为 maxTranslations，但可以更少。
    - 语言对 此功能不可用于简体中文与繁体中文之间的翻译（双向）。 它可用于 Microsoft 翻译工具支持的所有其他语言对。
* `State`：用户状态，可帮助关联请求和响应。 响应中将返回相同的内容。
* `Uri`：按此 URI 筛选结果。 如果未设置任何值，则默认值为 all。
* `User`：按此用户筛选结果。 如果未设置任何值，则默认值为 all。

请求 `Content-Type` 应为 `text/xml`。

**返回值：** 响应的格式如下所示。

```
<GetTranslationsResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"
  xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
  <From>Two character language code</From>
  <State/>
  <Translations>
    <TranslationMatch>
      <Count>int-value</Count>
      <MatchDegree>int-value</MatchDegree>
      <MatchedOriginalText/>
      <Rating>int value</Rating>
      <TranslatedText>string-value</TranslatedText>
    </TranslationMatch>
  </Translations>
</GetTranslationsResponse>
```

这包括一个包含以下值的 `GetTranslationsResponse` 元素：

* `Translations`：一系列已发现的匹配项，存储在 TranslationMatch（见下）对象中。 翻译可能包括原始文本的略变体（模糊匹配）。 翻译会排序：100% 匹配的在上面，模糊匹配的在下面。
* `From`：如果方法未指定源语言，则此项为自动语言检测的结果。 否则，它就会是给定的源语言。
* `State`：用户状态，可帮助关联请求和响应。 包含的值与在 TranslateOptions 参数中给出的值相同。

TranslationMatch 对象由以下元素组成：

* `Error`：如果特定的输入字符串出错，则会存储错误代码。 否则，此字段为空。
* `MatchDegree`：系统将输入句子与存储内容进行匹配，包括不精确匹配。  MatchDegree 表示输入文本与存储中发现的原始文本的匹配程度。 返回的值为 0 到 100，0 表示不相似，100 表示完全匹配（区分大小写）。
MatchedOriginalText：此结果的匹配的原始文本。 只有在匹配的原始文本不同于输入文本的情况下，才返回此项。 用于返回模糊匹配的源文本。 不是针对 Microsoft 翻译工具结果返回的。
* `Rating`：表示进行质量决策的人员的权威性。 机器翻译结果的评分将为 5。 匿名提供的翻译的评分通常为 1 到 4，而权威人士提供的翻译的评分则通常为 6 到 10。
* `Count`：此评分的这个翻译被选中的次数。 对于自动翻译的响应，此值将为 0。
* `TranslatedText`：翻译的文本。

### <a name="response-class-status-200"></a>响应类（状态 200）
一个 `GetTranslationsResponse` 对象，其格式如上所述。

字符串

响应内容类型：application/xml
 
### <a name="parameters"></a>parameters

|参数|值|说明|参数类型|数据类型|
|:--|:--|:--|:--|:--|
|appid|(empty)|必需。 如果使用了 `Authorization` 或 `Ocp-Apim-Subscription-Key` 标头，请将 appid 字段留空，否则请包括一个包含 `"Bearer" + " " + "access_token"` 的字符串。|query|字符串|
|text|(empty)|必需。 一个字符串，表示要翻译的文本。 文本大小不得超过 10000 个字符。|query|字符串|
|from|(empty)|必需。 一个表示翻译文本语言代码的字符串。|query|字符串|
|to |(empty)    |必需。 一个字符串，表示进行文本翻译时目标语言的语言代码。|query|字符串|
|maxTranslations|(empty)|必需。 一个整数，表示要返回的最大翻译数。|query|integer|
|授权| (empty)|必需，前提是 `appid` 字段或 `Ocp-Apim-Subscription-Key` 标头未指定。 授权令牌：`"Bearer" + " " + "access_token"`。|字符串| 标头的值开始缓存响应|
|Ocp-Apim-Subscription-Key|(empty)  |必需，前提是 `appid` 字段或 `Authorization` 标头未指定。|标头的值开始缓存响应|字符串|

### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400    |请求错误。 请查看输入参数和详细的错误响应。|
|401    |凭据无效|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供请求的大致日期和时间，以及包括在响应标头 `X-MS-Trans-Info` 中的请求 ID。|
|503|服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|

## <a name="post-gettranslationsarray"></a>POST /GetTranslationsArray

### <a name="implementation-notes"></a>实现说明
使用 `GetTranslationsArray` 方法检索多个源文本的多个翻译候选项。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/GetTranslationsArray`。

请求正文的格式如下所示。

```
<GetTranslationsArrayRequest>
  <AppId></AppId>
  <From>language-code</From>
  <MaxTranslations>int-value</MaxTranslations>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <ReservedFlags xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"/>
    <State xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">int-value</State>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Uri>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</User>
  </Options>
  <Texts>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
  </Texts>
  <To>language-code</To>
</GetTranslationsArrayRequest>
```

`GetTranslationsArrayRequest` 包括以下元素：

* `AppId`：必需。 如果使用了 Authorization 标头，请将 appid 字段留空，否则请包括一个包含 `"Bearer" + " " + "access_token"` 的字符串。
* `From`：必需。 一个表示翻译文本语言代码的字符串。
* `MaxTranslations`：必需。 一个整数，表示要返回的最大翻译数。
* `Options`：可选。 一个 Options 对象，包含下面列出的值。 它们都是可选的，并且默认为最常用的设置。 指定的元素必须按字母顺序列出。
    - Category：一个字符串，包含翻译的类别（领域）。 默认为“general”。
    - `ContentType`：唯一支持也是默认的选项为“text/plain”。
    - `IncludeMultipleMTAlternatives`：布尔标志，用于确定是否应从 MT 引擎返回多个备选项。 有效值为 true 和 false（区分大小写）。 默认值为 false，并且仅包括 1 个备选项。 将此标志设置为 true 即可在翻译中生成人工备选项，这些备选项已完全集成到协作翻译框架 (CTF) 中。 此功能允许为在 CTF 中没有备选项的句子返回备选项，其方法是从解码器的 n-best 列表中添加人工备选项。
        - 评分 评分按下述方式应用：1) 最佳自动翻译的评分为 5。 2) CTF 中的备选项反映了审阅者的权威性，其评分为 -10 到 +10。 3) 自动生成的（N 个最佳）翻译备选项的评级为 0，匹配度为 100。
        - 备选项数 返回的备选项的数目最多为 maxTranslations，但可以更少。
        - 语言对 此功能不可用于简体中文与繁体中文之间的翻译（双向）。 它可用于 Microsoft 翻译工具支持的所有其他语言对。
* `State`：用户状态，可帮助关联请求和响应。 响应中将返回相同的内容。
* `Uri`：按此 URI 筛选结果。 如果未设置任何值，则默认值为 all。
* `User`：按此用户筛选结果。 如果未设置任何值，则默认值为 all。
* `Texts`：必需。 一个包含要翻译文本的数组。 所有字符串必须采用同一语言。 要翻译的所有文本总共不得超过 10000 个字符。 最大数组元素数为 10。
* `To`：必需。 一个字符串，表示进行文本翻译时目标语言的语言代码。

可选元素可以省略。 属于 `GetTranslationsArrayRequest` 的直接子项的元素必须按字母顺序列出。

请求 `Content-Type` 应为 `text/xml`。

**返回值：** 响应的格式如下所示。

```
<ArrayOfGetTranslationsResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
  <GetTranslationsResponse>
    <From>language-code</From>
    <State/>
    <Translations>
      <TranslationMatch>
        <Count>int-value</Count>
        <MatchDegree>int-value</MatchDegree>
        <MatchedOriginalText>string-value</MatchedOriginalText>
        <Rating>int-value</Rating>
        <TranslatedText>string-value</TranslatedText>
      </TranslationMatch>
      <TranslationMatch>
        <Count>int-value</Count>
        <MatchDegree>int-value</MatchDegree>
        <MatchedOriginalText/>
        <Rating>int-value</Rating>
        <TranslatedText>string-value</TranslatedText>
      </TranslationMatch>
    </Translations>
  </GetTranslationsResponse>
</ArrayOfGetTranslationsResponse>
```

每个 `GetTranslationsResponse` 元素包含以下值：

* `Translations`：一系列已发现的匹配项，存储在 `TranslationMatch`（见下）对象中。 翻译可能包括原始文本的略变体（模糊匹配）。 翻译会排序：100% 匹配的在上面，模糊匹配的在下面。
* `From`：如果方法未指定 `From` 语言，则此项为自动语言检测的结果。 否则，它就会是给定的源语言。
* `State`：用户状态，可帮助关联请求和响应。 包含的值与在 `TranslateOptions` 参数中给出的值相同。

`TranslationMatch` 对象由以下项组成：
* `Error`：如果特定的输入字符串出错，则会存储错误代码。 否则，此字段为空。
* `MatchDegree`：系统将输入句子与存储内容进行匹配，包括不精确匹配。  `MatchDegree` 表示输入文本与存储中发现的原始文本的匹配程度。 返回的值为 0 到 100，0 表示不相似，100 表示完全匹配（区分大小写）。
* `MatchedOriginalText`：此结果的匹配的原始文本。 只有在匹配的原始文本不同于输入文本的情况下，才返回此项。 用于返回模糊匹配的源文本。 不是针对 Microsoft 翻译工具结果返回的。
* `Rating`：表示进行质量决策的人员的权威性。 机器翻译结果的评分将为 5。 匿名提供的翻译的评分通常为 1 到 4，而权威人士提供的翻译的评分则通常为 6 到 10。
* `Count`：此评分的这个翻译被选中的次数。 对于自动翻译的响应，此值将为 0。
* `TranslatedText`：翻译的文本。


### <a name="response-class-status-200"></a>响应类（状态 200）

字符串

响应内容类型：application/xml
 
### <a name="parameters"></a>parameters

|参数|值|说明|参数类型|数据类型|
|:--|:--|:--|:--|:--|
|授权  |(empty)    |必需，前提是 `appid` 字段或 `Ocp-Apim-Subscription-Key` 标头未指定。 授权令牌：`"Bearer" + " " + "access_token"`。|标头的值开始缓存响应|字符串|
|Ocp-Apim-Subscription-Key|(empty)  |必需，前提是 `appid` 字段或 `Authorization` 标头未指定。|标头的值开始缓存响应|字符串|

### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400    |请求错误。 请查看输入参数和详细的错误响应。|
|401    |凭据无效|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供请求的大致日期和时间，以及包括在响应标头 `X-MS-Trans-Info` 中的请求 ID。|
|503    |服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [迁移到 v3 文本翻译 API](../migrate-to-v3.md)










