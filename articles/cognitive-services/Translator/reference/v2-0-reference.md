---
title: 翻译工具版
titleSuffix: Azure Cognitive Services
description: 翻译工具 v2.0 的参考文档。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 05/15/2018
ms.author: swmachan
ms.openlocfilehash: 7fa148579e7525933d388b8a93c9a3476f473cb6
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588609"
---
# <a name="translator-v20"></a>翻译工具版

> [!IMPORTANT]
> 此版本的转换器已弃用。 [查看翻译人员版本3的文档](v3-0-reference.md)。

版本2的转换器可无缝集成到你的应用、网站、工具或其他解决方案中，以提供多语言用户体验。 你可以在任何硬件平台和操作系统上使用它来执行语言翻译和其他与语言相关的任务，如文本语言检测和文本到语音，这取决于行业标准。 有关详细信息，请参阅[转换器](../translator-info-overview.md)。

## <a name="getting-started"></a>入门
若要访问转换器，需要[注册 Microsoft Azure](../translator-text-how-to-signup.md)。

## <a name="authentication"></a>身份验证 
对转换器的所有调用都需要订阅密钥才能进行身份验证。 此 API 支持以下三种身份验证方法：

- 一个访问令牌。 使用订阅密钥通过向身份验证服务发出 POST 请求来创建访问令牌。 有关详细信息，请参阅令牌服务文档。 使用 `Authorization` 标头或查询参数将访问令牌传递给转换器服务 `access_token` 。 访问令牌的有效期为 10 分钟。 每隔10分钟获取一个新的访问令牌，并在10分钟内继续为重复的请求使用相同的访问令牌。
- 直接使用的订阅密钥。 将你的订阅密钥作为你的请求附带的标头中的值传递 `Ocp-Apim-Subscription-Key` 给转换器。 直接使用订阅密钥时，无需调用令牌身份验证服务来创建访问令牌。
- [Azure 认知服务多服务订阅](https://azure.microsoft.com/pricing/details/cognitive-services/)。 此方法允许使用单个机密密钥对多个服务的请求进行身份验证。
使用多服务密钥时，需要在请求中包含两个身份验证标头。 第一个标头传递密钥。 第二个标头指定与你的订阅相关联的区域：
   - `Ocp-Apim-Subscription-Key`
   - `Ocp-Apim-Subscription-Region`

多服务文本 API 订阅需要区域。 选择的区域是在使用多服务订阅密钥时，唯一可以用于文本转换的区域。 它需要与你在 Azure 门户上注册多服务订阅时所选的区域相同。

可用区域包括 `australiaeast` 、、 `brazilsouth` 、 `canadacentral` `centralindia` 、、 `centraluseuap` `eastasia` 、、 `eastus` `eastus2` `japaneast` `northeurope` `southcentralus` `southeastasia` `uksouth` `westcentralus` `westeurope` `westus` `westus2` 、、、、、、、、和。

你的订阅密钥和访问令牌是应在视图中隐藏的机密。

## <a name="profanity-handling"></a>不雅内容处理
通常，转换器服务将保留源中存在的猥亵语言。 亵渎的猥亵度和上下文会因区域性而异。 因此，可以增加或减少目标语言中的猥亵度。

如果要防止翻译中的猥亵，即使是在源文本中，也可以对支持它的方法使用猥亵筛选选项。 选项允许你选择是要查看是否要删除猥亵语言或用适当的标记标记，或者是否要在目标中允许猥亵语言。 接受的值 `ProfanityAction` 为 `NoAction` （默认值）、 `Marked` 和 `Deleted` 。


|ProfanityAction    |操作 |示例源（日语）  |示例翻译（英语）  |
|:--|:--|:--|:--|
|NoAction   |默认。 与不设置此选项等效。 不雅内容会从源传递到目标。        |彼はジャッカスです。     |他是一个笨蛋。   |
|Marked     |亵渎字将被 XML 标记 \< 猥亵语言> 和 \< /profanity>。       |彼はジャッカスです。 |他是 \<> 猥亵>jackass 的 \< /profanity。  |
|已删除    |不雅词语会从输出中删除，不进行替换。     |彼はジャッカスです。 |他是一个。   |

    
## <a name="excluding-content-from-translation"></a>将内容从翻译中排除
当你用标记（如 HTML （））转换内容时 `contentType=text/html` ，从翻译中排除特定内容有时很有用。 可以使用属性 `class=notranslate` 指定应保留不译的内容。 在下面的示例中，不会转换第一个元素中的内容 `div` ，但第二个元素中的内容 `div` 将被转换。

```HTML
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

## <a name="get-translate"></a>GET /Translate

### <a name="implementation-notes"></a>实现说明
将一种语言的文本字符串翻译为另一种语言。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/Translate`。

**返回值：** 一个字符串，表示已翻译的文本。

如果以前使用过 `AddTranslation` 或 `AddTranslationArray` 要为同一源句子输入分级为5或更高的翻译，则 `Translate` 仅返回可用于您的系统的顶部选项。 "相同的源句子" 表示与句子末尾的大写、空格、标记值和标点除外的完全相同（100% 匹配）。 如果没有分级为5或更高的级别，则返回的结果将是 Microsoft Translator 自动翻译。

### <a name="response-class-status-200"></a>Response 类（状态200）

字符串

响应内容类型： application/xml

### <a name="parameters"></a>参数

|参数|值|说明    |参数类型|数据类型|
|:--|:--|:--|:--|:--|
|appid  |（空）    |必需。 如果 `Authorization` 使用或 `Ocp-Apim-Subscription-Key` 标头，请将 `appid` 字段留空。 否则，请包含包含的字符串 `"Bearer" + " " + "access_token"` 。|查询|字符串|
|text|（空）   |必需。 表示要转换的文本的字符串。 文本长度不能超过10000个字符。|查询|字符串|
|from|（空）   |可选。 一个字符串，表示要转换的文本的语言代码。 例如，en 表示英语。|查询|字符串|
|更改为|（空） |必需。 一个字符串，表示要将文本转换为的语言的代码。|查询|字符串|
|contentType|（空）    |可选。 要翻译的文本的格式。 支持的格式为 `text/plain` （默认值）和 `text/html` 。 任何 HTML 元素都需要是格式正确的完整元素。|查询|字符串|
|category|（空）   |可选。 一个字符串，其中包含翻译的类别（域）。 默认值为 `general`。|查询|字符串|
|授权|（空）  |如果 `appid` 字段和 `Ocp-Apim-Subscription-Key` 标头都为空，则是必需的。 授权令牌：`"Bearer" + " " + "access_token"`。|标头|字符串|
|Ocp-Apim-Subscription-Key|（空）  |如果 `appid` 字段和 `Authorization` 标头都为空，则是必需的。|标头|字符串|


### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400    |请求错误。 请查看输入参数和详细的错误响应。|
|401    |凭据无效。|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供请求的大概日期 & 时间，并将请求 ID 包含在响应标头中 `X-MS-Trans-Info` 。|
|503    |服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|

## <a name="post-translatearray"></a>POST /TranslateArray

### <a name="implementation-notes"></a>实现说明
检索多个源文本的翻译。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/TranslateArray`。

下面是请求正文的格式：

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

这些元素位于 `TranslateArrayRequest` ：


* `AppId`：必需。 如果 `Authorization` 使用或 `Ocp-Apim-Subscription-Key` 标头，请将 `AppId` 字段留空。 否则，请包含包含的字符串 `"Bearer" + " " + "access_token"` 。
* `From`：可选。 一个字符串，表示要转换的文本的语言代码。 如果此字段保留为空，则响应将包含自动语言检测的结果。
* `Options`：可选。 一个 `Options` 包含以下值的对象。 它们都是可选的，默认情况下是最常用的设置。 指定的元素必须按字母顺序列出。
    - `Category`：一个字符串，其中包含翻译的类别（域）。 默认值为 `general`。
    - `ContentType`：要翻译的文本的格式。 支持的格式为 `text/plain` （默认值）、 `text/xml` 和 `text/html` 。 任何 HTML 元素都需要是格式正确的完整元素。
    - `ProfanityAction`：指定如何处理 profanities，如前文所述。 接受的值为 `NoAction` （默认值）、 `Marked` 和 `Deleted` 。
    - `State`：用于帮助关联请求和响应的用户状态。 响应中将返回相同的内容。
    - `Uri`：按此 URI 筛选结果。 默认值：`all`。
    - `User`：按此用户筛选结果。 默认值：`all`。
* `Texts`：必需。 一个数组，其中包含要翻译的文本。 所有字符串都必须采用相同的语言。 要翻译的所有文本的总数不能超过10000个字符。 数组元素的最大数目为2000。
* `To`：必需。 一个字符串，表示要将文本转换为的语言的代码。

您可以省略可选元素。 必须按字母顺序列出作为直接子级的元素 `TranslateArrayRequest` 。

`TranslateArray`方法接受 `application/xml` 或 `text/xml` `Content-Type` 。

**返回值：** 一个 `TranslateArrayResponse` 数组。 每个 `TranslateArrayResponse` 都具有以下元素：

* `Error`：指示发生错误时的错误。 否则设置为 null。
* `OriginalSentenceLengths`：一个整数数组，指示源文本中的每个句子的长度。 数组的长度指示句子数。
* `TranslatedText`：翻译的文本。
* `TranslatedSentenceLengths`：一个整数数组，指示已翻译文本中每个句子的长度。 数组的长度指示句子数。
* `State`：用于帮助关联请求和响应的用户状态。 返回与请求相同的内容。

下面是响应正文的格式：

```
<ArrayOfTranslateArrayResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"
  xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
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

### <a name="response-class-status-200"></a>Response 类（状态200）
成功的响应包括 `TranslateArrayResponse` 前面所述的格式的数组。

字符串

响应内容类型： application/xml

### <a name="parameters"></a>参数

|参数|值|说明|参数类型|数据类型|
|:--|:--|:--|:--|:--|
|授权|（空）  |如果 `appid` 字段和 `Ocp-Apim-Subscription-Key` 标头都为空，则是必需的。 授权令牌：`"Bearer" + " " + "access_token"`。|标头|字符串|
|Ocp-Apim-Subscription-Key|（空）|如果 `appid` 字段和 `Authorization` 标头都为空，则是必需的。|标头|字符串|

### <a name="response-messages"></a>响应消息

|HTTP 状态代码   |原因|
|:--|:--|
|400    |请求错误。 请查看输入参数和详细的错误响应。 常见错误包括： <ul><li>数组元素不能为空。</li><li>无效的类别。</li><li>语言无效。</li><li>To language 无效。</li><li>请求包含太多元素。</li><li>不支持 From 语言。</li><li>不支持 To 语言。</li><li>翻译请求的数据太多。</li><li>HTML 的格式不正确。</li><li>在转换请求中传递的字符串太多。</li></ul>|
|401    |凭据无效。|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供请求的大概日期 & 时间，并将请求 ID 包含在响应标头中 `X-MS-Trans-Info` 。|
|503    |服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|

## <a name="post-getlanguagenames"></a>POST /GetLanguageNames

### <a name="implementation-notes"></a>实现说明
检索作为参数传入的语言的友好名称，并将其 `languageCodes` 本地化为传递的 `locale` 语言。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/GetLanguageNames`。

请求正文包含一个字符串数组，该数组表示要为其检索友好名称的 ISO 639-1 语言代码。 下面的示例说明：

```
<ArrayOfstring xmlns:i="https://www.w3.org/2001/XMLSchema-instance"  xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>zh</string>
    <string>en</string>
</ArrayOfstring>
```

**返回值：** 包含转换器服务支持的语言名称的字符串数组，本地化为请求的语言。

### <a name="response-class-status-200"></a>Response 类（状态200）
包含转换器服务支持的语言名称的字符串数组，本地化为请求的语言。

字符串

响应内容类型： application/xml
 
### <a name="parameters"></a>参数

|参数|值|说明|参数类型|数据类型|
|:--|:--|:--|:--|:--|
|appid|（空）|必需。 如果 `Authorization` 使用或 `Ocp-Apim-Subscription-Key` 标头，请将 `appid` 字段留空。 否则，请包含包含的字符串 `"Bearer" + " " + "access_token"` 。|查询|string|
|区域设置|（空） |必需。 一个表示以下内容之一的字符串，用于本地化语言名称： <ul><li>与语言关联的 ISO 639 2 字母小写区域性代码与 ISO 3166 2 字母大写子区域性代码的组合。 <li>ISO 639 小写区域性代码本身。|查询|字符串|
|授权|（空）  |如果 `appid` 字段和 `Ocp-Apim-Subscription-Key` 标头都为空，则是必需的。 授权令牌：`"Bearer" + " " + "access_token"`。|标头|字符串|
|Ocp-Apim-Subscription-Key|（空）  |如果 `appid` 字段和 `Authorization` 标头都为空，则是必需的。|标头|字符串|

### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400    |请求错误。 请查看输入参数和详细的错误响应。|
|401    |凭据无效。|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供请求的大概日期 & 时间，并将请求 ID 包含在响应标头中 `X-MS-Trans-Info` 。|
|503    |服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|

## <a name="get-getlanguagesfortranslate"></a>GET /GetLanguagesForTranslate

### <a name="implementation-notes"></a>实现说明
获取表示翻译服务支持的语言的语言代码列表。  `Translate` 和 `TranslateArray` 可以在这些语言的任意两种之间互译。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForTranslate`。

**返回值：** 一个字符串数组，其中包含转换器服务支持的语言代码。

### <a name="response-class-status-200"></a>Response 类（状态200）
一个字符串数组，其中包含转换器服务支持的语言代码。

字符串

响应内容类型： application/xml
 
### <a name="parameters"></a>参数

|参数|值|说明|参数类型|数据类型|
|:--|:--|:--|:--|:--|
|appid|（空）|必需。 如果 `Authorization` 使用或 `Ocp-Apim-Subscription-Key` 标头，请将 `appid` 字段留空。 否则，请包含包含的字符串 `"Bearer" + " " + "access_token"` 。|查询|字符串|
|授权|（空）  |如果 `appid` 字段和 `Ocp-Apim-Subscription-Key` 标头都为空，则是必需的。 授权令牌：`"Bearer" + " " + "access_token"`。|标头|字符串|
|Ocp-Apim-Subscription-Key|（空）|如果 `appid` 字段和 `Authorization` 标头都为空，则是必需的。|标头|字符串|

### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400    |请求错误。 请查看输入参数和详细的错误响应。|
|401    |凭据无效。|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供请求的大概日期 & 时间，并将请求 ID 包含在响应标头中 `X-MS-Trans-Info` 。|
|503|服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|

## <a name="get-getlanguagesforspeak"></a>GET /GetLanguagesForSpeak

### <a name="implementation-notes"></a>实现说明
检索可用于语音合成的语言。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForSpeak`。

**返回值：** 一个字符串数组，其中包含转换器服务合成语音支持的语言代码。

### <a name="response-class-status-200"></a>Response 类（状态200）
一个字符串数组，其中包含转换器服务合成语音支持的语言代码。

字符串

响应内容类型： application/xml

### <a name="parameters"></a>参数

|参数|值|说明|参数类型|数据类型|
|:--|:--|:--|:--|:--|
|appid|（空）|必需。 如果 `Authorization` 使用或 `Ocp-Apim-Subscription-Key` 标头，请将 `appid` 字段留空。 否则，请包含包含的字符串 `"Bearer" + " " + "access_token"` 。|查询|字符串|
|授权|（空）|如果 `appid` 字段和 `Ocp-Apim-Subscription-Key` 标头都为空，则是必需的。 授权令牌：`"Bearer" + " " + "access_token"`。|标头|字符串|
|Ocp-Apim-Subscription-Key|（空）|如果 `appid` 字段和 `Authorization` 标头都为空，则是必需的。|标头|字符串|
 
### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400|请求错误。 请查看输入参数和详细的错误响应。|
|401|凭据无效。|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供请求的大概日期 & 时间，并将请求 ID 包含在响应标头中 `X-MS-Trans-Info` 。|
|503    |服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|

## <a name="get-speak"></a>GET /Speak

### <a name="implementation-notes"></a>实现说明
返回传入文本的 WAV 或 MP3 流，采用所需的语言。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/Speak`。

**返回值：** 传入文本的 WAV 或 MP3 流，采用所需的语言。

### <a name="response-class-status-200"></a>Response 类（状态200）

binary

响应内容类型： application/xml

### <a name="parameters"></a>参数

|参数|值|说明|参数类型|数据类型|
|:--|:--|:--|:--|:--|
|appid|（空）|必需。 如果 `Authorization` 使用或 `Ocp-Apim-Subscription-Key` 标头，请将 `appid` 字段留空。 否则，请包含包含的字符串 `"Bearer" + " " + "access_token"` 。|查询|字符串|
|text|（空）   |必需。 一个字符串，其中包含一个或多个要在指定语言中使用的流。 文本不得超过2000个字符。|查询|字符串|
|语言|（空）   |必需。 一个字符串，该字符串表示文本的语言所支持的语言代码。 代码必须是方法返回的代码之一 `GetLanguagesForSpeak` 。|查询|字符串|
|format|（空）|可选。 指定 content 类型 ID 的字符串。 目前，`audio/wav` 和 `audio/mp3` 可用。 默认值为 `audio/wav`。|查询|字符串|
|选项|（空）    |可选。 指定合成语音的属性的字符串：<ul><li>`MaxQuality`并 `MinSize` 指定音频信号的质量。 `MaxQuality`提供最高的质量。 `MinSize`提供最小的文件大小。 默认值为 `MinSize` 。</li><li>`female`并 `male` 指定所需的语音性别。 默认值为 `female`。 使用竖线（ <code>\|</code> ）可包含多个选项。 例如，`MaxQuality|Male`。</li></li></ul>  |查询|字符串|
|授权|（空）|如果 `appid` 字段和 `Ocp-Apim-Subscription-Key` 标头都为空，则是必需的。 授权令牌：`"Bearer" + " " + "access_token"`。|标头|字符串|
|Ocp-Apim-Subscription-Key|（空）  |如果 `appid` 字段和 `Authorization` 标头都为空，则是必需的。|标头|字符串|

### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400    |请求错误。 请查看输入参数和详细的错误响应。|
|401    |凭据无效。|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供请求的大概日期 & 时间，并将请求 ID 包含在响应标头中 `X-MS-Trans-Info` 。|
|503    |服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|

## <a name="get-detect"></a>GET /Detect

### <a name="implementation-notes"></a>实现说明
标识文本部分的语言。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/Detect`。

**返回值：** 一个字符串，其中包含文本的两字符语言代码。

### <a name="response-class-status-200"></a>Response 类（状态200）

字符串

响应内容类型： application/xml

### <a name="parameters"></a>参数

|参数|值|说明|参数类型|数据类型|
|:--|:--|:--|:--|:--|
|appid|（空）  |必需。 如果 `Authorization` 使用或 `Ocp-Apim-Subscription-Key` 标头，请将 `appid` 字段留空。 否则，请包含包含的字符串 `"Bearer" + " " + "access_token"` 。|查询|字符串|
|text|（空）|必需。 一个字符串，包含要标识其语言的文本。 文本不得超过10000个字符。|查询|  字符串|
|授权|（空）|如果 `appid` 字段和 `Ocp-Apim-Subscription-Key` 标头都为空，则是必需的。 授权令牌：`"Bearer" + " " + "access_token"`。|标头|字符串|
|Ocp-Apim-Subscription-Key  |（空）    |如果 `appid` 字段和 `Authorization` 标头都为空，则是必需的。|标头|字符串|

### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400|请求错误。 请查看输入参数和详细的错误响应。|
|401    |凭据无效。|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供请求的大概日期 & 时间，并将请求 ID 包含在响应标头中 `X-MS-Trans-Info` 。|
|503    |服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|


## <a name="post-detectarray"></a>POST /DetectArray

### <a name="implementation-notes"></a>实现说明

标识字符串数组中的语言。 单独检测每个数组元素的语言，并为每个数组行返回结果。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/DetectArray`。

下面是请求正文的格式：

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>string-value-1</string>
    <string>string-value-2</string>
</ArrayOfstring>
```

文本不能超过10000个字符。

**返回值：** 一个字符串数组，其中包含输入数组中每一行的两字符语言代码。

下面是响应正文的格式：

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <string>language-code-1</string>
  <string>language-code-2</string>
</ArrayOfstring>
```

### <a name="response-class-status-200"></a>Response 类（状态200）
`DetectArray`已成功。 返回一个字符串数组，该数组包含每个输入数组行的双字符语言代码。

字符串

响应内容类型： application/xml
 
### <a name="parameters"></a>参数

|参数|值|说明|参数类型|数据类型|
|:--|:--|:--|:--|:--|
|appid|（空）|必需。 如果 `Authorization` 使用或 `Ocp-Apim-Subscription-Key` 标头，请将 `appid` 字段留空。 否则，请包含包含的字符串 `"Bearer" + " " + "access_token"` 。|查询|字符串|
|授权|（空）|如果 `appid` 字段和 `Ocp-Apim-Subscription-Key` 标头都为空，则是必需的。  授权令牌：`"Bearer" + " " + "access_token"`。|标头|字符串|
|Ocp-Apim-Subscription-Key|（空）|如果 `appid` 字段和 `Authorization` 标头都为空，则是必需的。|标头|字符串|

### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400    |请求错误。 请查看输入参数和详细的错误响应。|
|401    |凭据无效。|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供请求的大概日期 & 时间，并将请求 ID 包含在响应标头中 `X-MS-Trans-Info` 。|
|503    |服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|

## <a name="get-addtranslation"></a>GET /AddTranslation

### <a name="implementation-notes"></a>实现说明

> [!IMPORTANT]
> **弃用说明：** 2018年1月31日之后，此方法将不接受新的句子提交。 你会收到一条错误消息。 有关协作转换框架（CTF）的更改，请参阅公告。

向翻译记忆库添加翻译。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/AddTranslation`。

### <a name="response-class-status-200"></a>Response 类（状态200）

字符串

响应内容类型：应用程序： xml
 
### <a name="parameters"></a>参数

|参数|值|说明|参数类型|数据类型   |
|:--|:--|:--|:--|:--|
|appid|（空）|必需。 如果 `Authorization` 使用或 `Ocp-Apim-Subscription-Key` 标头，请将 `appid` 字段留空。 否则，请包含包含的字符串 `"Bearer" + " " + "access_token"` 。|查询|字符串|
|originalText|（空）|必需。 一个字符串，其中包含要转换的文本。 字符串的最大长度为1000个字符。|查询|字符串|
|translatedText|（空） |必需。 一个字符串，其中包含翻译为目标语言的文本。 字符串的最大长度为2000个字符。|查询|字符串|
|from|（空）   |必需。 一个字符串，表示文本的原始语言的语言代码。 例如，en 表示英语，而 de 表示德语。|查询|字符串|
|更改为|（空）|必需。 一个字符串，表示要将文本转换为的语言的语言代码。|查询|字符串|
|rating|（空） |可选。 一个整数，表示字符串的质量级别。 值介于-10 和10之间。 默认值为 1。|查询|integer|
|contentType|（空）    |可选。 要翻译的文本的格式。 支持的格式为 `text/plain` 和 `text/html` 。 任何 HTML 元素都需要是格式正确的完整元素。    |查询|字符串|
|category|（空）|可选。 一个字符串，其中包含翻译的类别（域）。 默认值为 `general`。|查询|字符串|
|user|（空）|必需。 一个字符串，用于跟踪提交的发起方。|查询|字符串|
|uri|（空）|可选。 一个字符串，其中包含翻译的内容位置。|查询|字符串|
|授权|（空）|如果 `appid` 字段和 `Ocp-Apim-Subscription-Key` 标头都为空，则是必需的。  授权令牌：`"Bearer" + " " + "access_token"`。  |标头|字符串|
|Ocp-Apim-Subscription-Key|（空）|如果 `appid` 字段和 `Authorization` 标头都为空，则是必需的。|标头|字符串|

### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400    |请求错误。 请查看输入参数和详细的错误响应。|
|401    |凭据无效。|
|410|`AddTranslation` 不再受支持。|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供请求的大概日期 & 时间，并将请求 ID 包含在响应标头中 `X-MS-Trans-Info` 。|
|503    |服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|

## <a name="post-addtranslationarray"></a>POST /AddTranslationArray

### <a name="implementation-notes"></a>实现说明

> [!IMPORTANT]
> **弃用说明：** 2018年1月31日之后，此方法将不接受新的句子提交。 你会收到一条错误消息。 有关协作转换框架（CTF）的更改，请参阅公告。

将翻译数组添加到翻译内存。 此方法是的数组版本 `AddTranslation` 。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/AddTranslationArray`。

下面是请求正文的格式：

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

这些元素位于 `AddtranslationsRequest` ：

* `AppId`：必需。 如果 `Authorization` 使用或 `Ocp-Apim-Subscription-Key` 标头，请将 `AppId` 字段留空。 否则，请包含包含的字符串 `"Bearer" + " " + "access_token"` 。
* `From`：必需。 一个字符串，其中包含源语言的语言代码。 必须是 `GetLanguagesForTranslate` 方法返回的语言之一。
* `To`：必需。 一个字符串，其中包含目标语言的语言代码。 必须是 `GetLanguagesForTranslate` 方法返回的语言之一。
* `Translations`：必需。 要添加到翻译记忆库的一个翻译数组。 每个转换都必须包含 `OriginalText` 、 `TranslatedText` 和 `Rating` 。 每个和的最大大小 `OriginalText` `TranslatedText` 为1000个字符。 所有 `OriginalText` 和元素的总和 `TranslatedText` 不能超过10000个字符。 最大数组元素数为 100。
* `Options`：必需。 一组选项，包括 `Category` 、、 `ContentType` `Uri` 和 `User` 。 需要 `User`。 `Category`、 `ContentType` 和 `Uri` 都是可选的。 指定的元素必须按字母顺序列出。

### <a name="response-class-status-200"></a>Response 类（状态200）
`AddTranslationArray`方法成功。 

2018年1月31日之后，将不接受句子提交。 此服务会以错误代码 410 进行响应。

字符串

响应内容类型： application/xml
 
### <a name="parameters"></a>参数

|参数|值|说明|参数类型|数据类型|
|:--|:--|:--|:--|:--|
|授权|（空）|如果 `appid` 字段和 `Ocp-Apim-Subscription-Key` 标头都为空，则是必需的。  授权令牌：`"Bearer" + " " + "access_token"`。|标头|字符串|
|Ocp-Apim-Subscription-Key|（空）|如果 `appid` 字段和 `Authorization` 标头都为空，则是必需的。|标头|字符串|

### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400    |请求错误。 请查看输入参数和详细的错误响应。|
|401    |凭据无效。|
|410    |`AddTranslation` 不再受支持。|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供请求的大概日期 & 时间，并将请求 ID 包含在响应标头中 `X-MS-Trans-Info` 。|
|503|服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|

## <a name="get-breaksentences"></a>GET /BreakSentences

### <a name="implementation-notes"></a>实现说明
将文本部分分为句子并返回包含每个句子的长度的数组。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/BreakSentences`。

**返回值：** 表示句子长度的整数数组。 数组的长度表示句子数。 值表示每个句子的长度。

### <a name="response-class-status-200"></a>Response 类（状态200）
表示句子长度的整数数组。 数组的长度表示句子数。 值表示每个句子的长度。

integer

响应内容类型： application/xml

### <a name="parameters"></a>参数

|参数|值|说明|参数类型|数据类型|
|:--|:--|:--|:--|:--|
|appid|（空）  |必需。 如果 `Authorization` 使用或 `Ocp-Apim-Subscription-Key` 标头，请将 `appid` 字段留空。 否则，请包含包含的字符串 `"Bearer" + " " + "access_token"` 。|查询| 字符串|
|text|（空）   |必需。 一个字符串，表示拆分为句子的文本。 文本的最大大小为10000个字符。|查询|字符串|
|语言   |（空）    |必需。 一个字符串，表示输入文本的语言代码。|查询|字符串|
|授权|（空）|如果 `appid` 字段和 `Ocp-Apim-Subscription-Key` 标头都为空，则是必需的。 授权令牌：`"Bearer" + " " + "access_token"`。   |标头|字符串|
|Ocp-Apim-Subscription-Key|（空）|如果 `appid` 字段和 `Authorization` 标头都为空，则是必需的。|标头|字符串|

### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400|请求错误。 请查看输入参数和详细的错误响应。|
|401|凭据无效。|
|500|服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供请求的大概日期 & 时间，并将请求 ID 包含在响应标头中 `X-MS-Trans-Info` 。|
|503|服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|

## <a name="post-gettranslations"></a>POST /GetTranslations

### <a name="implementation-notes"></a>实现说明
从存储和 MT 引擎中检索给定语言对的翻译数组。 `GetTranslations`与的不同之处在于 `Translate` ，它返回所有可用的翻译。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/GetTranslations`。

请求正文包含可选 `TranslationOptions` 对象，该对象具有以下格式：

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

`TranslateOptions`对象包含以下列表中的值。 它们都是可选的，默认情况下是最常用的设置。 指定的元素必须按字母顺序列出。

* `Category`：一个字符串，其中包含翻译的类别（域）。 默认值为 `general`。
* `ContentType`：唯一受支持的选项，默认值为 `text/plain` 。
* `IncludeMultipleMTAlternatives`：一个布尔型标志，用于指定是否应从 MT 引擎返回多个替代项。 有效值为 `true` 和 `false` （区分大小写）。 默认值为 `false` ，它仅返回一个替代项。 将标志设置为 `true` 可以创建人工替代项，并与协作转换框架（CTF）完全集成。 此功能允许通过从解码器的*n*最佳列表添加可选项，来返回在 CTF 中没有翻译的句子的替代项。
    - 满意率. 将按如下所示应用分级： 
         - 最佳自动翻译评级为 5。
       - CTF 中的替代项反映了审阅者的权限。 它们的范围为-10 到 + 10。
       - 自动生成的（*n*最佳）转换备选方法的级别为0，匹配度为100。
    - 替代项的数目。 返回的替代项的数量可能会与中指定的值一样高 `maxTranslations` ，但可以较低。
    - 语言对。 此功能不可用于简体中文和繁体中文之间的翻译。 它适用于 Microsoft Translator 支持的所有其他语言对。
* `State`：用于帮助关联请求和响应的用户状态。 响应中将返回相同的内容。
* `Uri`：按此 URI 筛选结果。 如果未设置任何值，则默认值为 `all` 。
* `User`：按此用户筛选结果。 如果未设置任何值，则默认值为 `all` 。

请求 `Content-Type` 应为 `text/xml`。

**返回值：** 下面是响应的格式：

```
<GetTranslationsResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"
  xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
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

此响应包含一个 `GetTranslationsResponse` 元素，其中包含以下值：

* `Translations`：找到的匹配项的数组，存储在 `TranslationMatch` 对象中（如以下部分所述）。 翻译可能包含原始文本的轻微变体（模糊匹配）。 将对翻译进行排序：首先是100% 匹配项，接下来是模糊匹配项。
* `From`：如果该方法未指定 `From` 语言，则此值将来自自动语言检测。 否则，它将是指定的 `From` 语言。
* `State`：用于帮助关联请求和响应的用户状态。 包含参数中提供的值 `TranslateOptions` 。

`TranslationMatch`对象包括以下值：

* `Error`：错误代码（如果特定输入字符串发生错误）。 否则，此字段为空。
* `MatchDegree`：指示输入文本与存储区中找到的原始文本的匹配程度。 系统将输入句子与存储内容进行匹配，包括不精确匹配。 返回的值的范围为0到100，其中0没有相似性，100是完全区分大小写的匹配项。
* `MatchedOriginalText`：此结果的匹配的原始文本。 仅当匹配的原始文本不同于输入文本时，才返回此值。 它用于返回模糊匹配的源文本。 Microsoft Translator 结果不返回此值。
* `Rating`：表示进行质量决策的人员的权威性。 计算机转换结果的等级为5。 匿名提供的翻译的评级一般为1到4。 权威提供的翻译通常会有6到10的评级。
* `Count`：此评分的这个翻译被选中的次数。 对于自动翻译的响应，该值为0。
* `TranslatedText`：翻译的文本。

### <a name="response-class-status-200"></a>Response 类（状态200）
`GetTranslationsResponse`采用前面所述格式的对象。

字符串

响应内容类型： application/xml
 
### <a name="parameters"></a>参数

|参数|值|说明|参数类型|数据类型|
|:--|:--|:--|:--|:--|
|appid|（空）|必需。 如果 `Authorization` 使用或 `Ocp-Apim-Subscription-Key` 标头，请将 `appid` 字段留空。 否则，请包含包含的字符串 `"Bearer" + " " + "access_token"` 。|查询|字符串|
|text|（空）|必需。 表示要转换的文本的字符串。 文本的最大大小为10000个字符。|查询|字符串|
|from|（空）|必需。 一个字符串，表示要转换的文本的语言代码。|查询|字符串|
|更改为 |（空）    |必需。 一个字符串，表示要将文本转换为的语言的语言代码。|查询|字符串|
|maxTranslations|（空）|必需。 一个整数，表示要返回的最大翻译数。|查询|integer|
|授权| （空）|如果 `appid` 字段和 `Ocp-Apim-Subscription-Key` 标头都为空，则是必需的。 授权令牌：`"Bearer" + " " + "access_token"`。|字符串|  标头|
|Ocp-Apim-Subscription-Key|（空）  |如果 `appid` 字段和 `Authorization` 标头都为空，则是必需的。|标头|字符串|

### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400    |请求错误。 请查看输入参数和详细的错误响应。|
|401    |凭据无效。|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供请求的大概日期 & 时间，并将请求 ID 包含在响应标头中 `X-MS-Trans-Info` 。|
|503|服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|

## <a name="post-gettranslationsarray"></a>POST /GetTranslationsArray

### <a name="implementation-notes"></a>实现说明
检索多个源文本的多个转换候选项。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/GetTranslationsArray`。

下面是请求正文的格式：

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

`GetTranslationsArrayRequest`包括以下元素：

* `AppId`：必需。 如果 `Authorization` 使用了标头，请将该 `AppId` 字段留空。 否则，请包含包含的字符串 `"Bearer" + " " + "access_token"` 。
* `From`：必需。 一个字符串，表示要转换的文本的语言代码。
* `MaxTranslations`：必需。 一个整数，表示要返回的最大翻译数。
* `Options`：可选。 一个 `Options` 包含以下值的对象。 它们都是可选的，默认情况下是最常用的设置。 指定的元素必须按字母顺序列出。
    - `Category`：一个字符串，其中包含翻译的类别（域）。 默认值为 `general`。
    - `ContentType`：唯一受支持的选项，默认值为 `text/plain` 。
    - `IncludeMultipleMTAlternatives`：一个布尔型标志，用于指定是否应从 MT 引擎返回多个替代项。 有效值为 `true` 和 `false` （区分大小写）。 默认值为 `false` ，它仅返回一个替代项。 设置标志以 `true` 支持在翻译中生成人工替代项，并与协作翻译框架（CTF）完全集成。 此功能允许通过从解码器的*n*最佳列表中添加人工替代项来返回在 CTF 中没有替代项的句子的替代项。
        - 分级适用的分级如下：
          - 最佳自动翻译评级为 5。
          - CTF 中的替代项反映了审阅者的权限。 它们的范围为-10 到 + 10。
          - 自动生成的（*n*最佳）转换备选方法的级别为0，匹配度为100。
        - 替代项的数目。 返回的替代项的数量可能会与中指定的值一样高 `maxTranslations` ，但可以较低。
        - 语言对。 此功能不可用于简体中文和繁体中文之间的翻译。 它适用于 Microsoft Translator 支持的所有其他语言对。
* `State`：用于帮助关联请求和响应的用户状态。 响应中将返回相同的内容。
* `Uri`：按此 URI 筛选结果。 如果未设置任何值，则默认值为 `all` 。
* `User`：按此用户筛选结果。 如果未设置任何值，则默认值为 `all` 。
* `Texts`：必需。 一个数组，其中包含要翻译的文本。 所有字符串都必须采用相同的语言。 要翻译的所有文本的总数不能超过10000个字符。 最大数组元素数为 10。
* `To`：必需。 一个字符串，表示要将文本转换为的语言的语言代码。

您可以省略可选元素。 必须按字母顺序列出作为直接子级的元素 `GetTranslationsArrayRequest` 。

请求 `Content-Type` 应为 `text/xml`。

**返回值：** 下面是响应的格式：

```
<ArrayOfGetTranslationsResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
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

每个 `GetTranslationsResponse` 元素都包含以下值：

* `Translations`：找到的匹配项的数组，存储在 `TranslationMatch` 对象中（如以下部分所述）。 翻译可能包含原始文本的轻微变体（模糊匹配）。 将对翻译进行排序：首先是100% 匹配项，接下来是模糊匹配项。
* `From`：如果该方法未指定 `From` 语言，则此值将来自自动语言检测。 否则，它将是指定的 `From` 语言。
* `State`：用于帮助关联请求和响应的用户状态。 包含参数中提供的值 `TranslateOptions` 。

`TranslationMatch`对象包含以下值：
* `Error`：错误代码（如果特定输入字符串发生错误）。 否则，此字段为空。
* `MatchDegree`：指示输入文本与存储区中找到的原始文本的匹配程度。 系统将输入句子与存储内容进行匹配，包括不精确匹配。 返回的值的范围为0到100，其中0没有相似性，100是完全区分大小写的匹配项。
* `MatchedOriginalText`：此结果的匹配的原始文本。 仅当匹配的原始文本不同于输入文本时，才返回此值。 它用于返回模糊匹配的源文本。 Microsoft Translator 结果不返回此值。
* `Rating`：表示进行质量决策的人员的权威性。 计算机转换结果的等级为5。 匿名提供的翻译的评级一般为1到4。 权威提供的翻译的评级一般为6到10。
* `Count`：此评分的这个翻译被选中的次数。 对于自动翻译的响应，该值为0。
* `TranslatedText`：翻译的文本。


### <a name="response-class-status-200"></a>Response 类（状态200）

字符串

响应内容类型： application/xml
 
### <a name="parameters"></a>参数

|参数|值|说明|参数类型|数据类型|
|:--|:--|:--|:--|:--|
|授权  |（空）    |如果 `appid` 字段和 `Ocp-Apim-Subscription-Key` 标头都为空，则是必需的。  授权令牌：`"Bearer" + " " + "access_token"`。|标头|字符串|
|Ocp-Apim-Subscription-Key|（空）  |如果 `appid` 字段和 `Authorization` 标头都为空，则是必需的。|标头|字符串|

### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400    |请求错误。 请查看输入参数和详细的错误响应。|
|401    |凭据无效。|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供请求的大致日期和时间，以及包括在响应标头 `X-MS-Trans-Info` 中的请求 ID。|
|503    |服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [迁移到 Translator v3](../migrate-to-v3.md)


