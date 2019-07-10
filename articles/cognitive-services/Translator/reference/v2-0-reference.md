---
title: 文本翻译 API v2.0
titleSuffix: Azure Cognitive Services
description: 文本翻译 API v2.0 的参考文档。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 05/15/2018
ms.author: swmachan
ms.openlocfilehash: a29e123c44ca198ce19db451ee4c624b6f993538
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705431"
---
# <a name="translator-text-api-v20"></a>文本翻译 API v2.0

> [!IMPORTANT]
> 此版文本翻译 API 已弃用。 [查看文档，了解版本 3 的文本翻译 API](v3-0-reference.md)。

第 2 版文本翻译 API 可以无缝集成到您的应用程序、 网站、 工具或其他解决方案能够提供多语言用户体验。 您可以使用它在任何硬件平台，用于任何操作系统执行语言翻译和其他与语言相关的任务，如文本语言检测和文本到语音，根据行业标准。 有关详细信息，请参阅[文本翻译 API](../translator-info-overview.md)。

## <a name="getting-started"></a>入门
若要访问文本翻译 API，需要[名义注册 Microsoft Azure](../translator-text-how-to-signup.md)。

## <a name="authentication"></a>Authentication 
对文本翻译 API 的所有调用都需要进行身份验证的订阅密钥。 API 支持三种身份验证方法：

- 访问令牌。 使用引用在步骤 9 中的订阅密钥通过向身份验证服务发出的 POST 请求来创建访问令牌。 有关详细信息，请参阅令牌服务文档。 通过将访问令牌传递给 Translator 服务`Authorization`标头或`access_token`查询参数。 访问令牌的有效期为 10 分钟。 获取新的访问令牌每隔 10 分钟，并使用相同的访问令牌的重复请求期间保持 10 分钟。
- 直接使用订阅密钥。 将你的订阅密钥作为中的值传递`Ocp-Apim-Subscription-Key`文本翻译 api 在请求中包含的标头。 当直接使用订阅密钥时，你无需调用要创建访问令牌的令牌身份验证服务。
- [Azure 认知服务多服务订阅](https://azure.microsoft.com/pricing/details/cognitive-services/)。 此方法，可使用一个密钥用于多个服务请求进行身份验证。
当使用多服务的机密密钥时，需要包含两个身份验证标头与你的请求。 第一个标头会传递的机密密钥。 第二个标头指定与你的订阅关联的区域：
   - `Ocp-Apim-Subscription-Key`
   - `Ocp-Apim-Subscription-Region`

区域是必需的多服务的文本 API 订阅。 所选的区域是使用多服务的订阅密钥时，可以使用文本转换的唯一区域。 它必须是多服务订阅在 Azure 门户上注册时选择的同一区域。

可用的区域有`australiaeast`， `brazilsouth`， `canadacentral`， `centralindia`， `centraluseuap`， `eastasia`， `eastus`， `eastus2`， `japaneast`， `northeurope`， `southcentralus`， `southeastasia`，`uksouth`， `westcentralus`， `westeurope`， `westus`，和`westus2`。

你的订阅密钥和访问令牌是应从视图中隐藏的机密信息。

## <a name="profanity-handling"></a>不雅内容处理
通常情况下，Translator 服务将保留源中存在的猥亵语言。 根据区域性不同程度的猥亵语言和使敬词的上下文。 因此无法增加或减少以目标语言的猥亵词语的程度。

如果你想要防止在翻译中的猥亵词语，即使是在源文本中，可以使用猥亵语言筛选器支持它的方法的选项。 选项允许你选择是否想要查看猥亵语言已删除或标记有适当的标签，或是否想要在目标中允许猥亵语言。 接受的值的`ProfanityAction`都`NoAction`（默认值）， `Marked`，和`Deleted`。


|ProfanityAction    |操作 |示例源 （日语）  |示例转换 （英语）  |
|:--|:--|:--|:--|
|NoAction   |默认。 与不设置此选项等效。 不雅内容会从源传递到目标。        |彼はジャッカスです。     |他是一个笨蛋。   |
|Marked     |将 XML 标记包围不当的词语\<猥亵语言 > 和\</profanity >。       |彼はジャッカスです。 |他\<猥亵语言 > jackass\</profanity >。  |
|Deleted    |不雅词语会从输出中删除，不进行替换。     |彼はジャッカスです。 |他是一个。   |

    
## <a name="excluding-content-from-translation"></a>将内容从翻译中排除
当转换标记，类似于 HTML 的内容 (`contentType=text/html`)，有时是从转换中排除特定的内容很有用。 可以使用属性 `class=notranslate` 指定应保留不译的内容。 在下面的示例中，在第一个内容`div`元素不会翻译，但在第二个内容`div`元素将被转换。

```HTML
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

## <a name="get-translate"></a>GET /Translate

### <a name="implementation-notes"></a>实现说明
将一种语言的文本字符串翻译为另一种语言。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/Translate`。

**返回值：** 表示已翻译的文本的字符串。

如果以前使用过`AddTranslation`或`AddTranslationArray`输入等级为 5 或更高版本的相同的源句子的翻译`Translate`返回仅可供您的系统的最佳选择。 "相同的源句子"意味着完全相同 （100%匹配），除大小写、 空格、 标记值和一个句子末尾的标点。 如果未分级存储且其分级为 5 或更高版本中，返回的结果将是通过 Microsoft Translator 自动翻译。

### <a name="response-class-status-200"></a>Response 类 （状态 200）

string

响应内容类型： application/xml

### <a name="parameters"></a>parameters

|参数|值|描述    |参数类型|数据类型|
|:--|:--|:--|:--|:--|
|appid  |(empty)    |必需。 如果`Authorization`或`Ocp-Apim-Subscription-Key`使用标头，保留`appid`字段均为空。 否则，包含一个字符串，包含`"Bearer" + " " + "access_token"`。|query|string|
|text|(empty)   |必需。 表示要转换的文本字符串。 文本不能包含超过 10,000 个字符。|query|string|
|from|(empty)   |可选。 一个字符串，表示要转换的文本的语言代码。 例如，en 表示英语。|query|string|
|至|(empty) |必需。 一个字符串，表示要转换到的文本的语言代码。|query|string|
|contentType|(empty)    |可选。 要翻译的文本的格式。 支持的格式为`text/plain`（默认值） 和`text/html`。 任何 HTML 元素必须是格式正确的、 完整的元素。|query|string|
|category|(empty)   |可选。 包含转换的类别 （域） 的字符串。 默认为 `general`。|query|string|
|授权|(empty)  |如果这两个必需`appid`字段和`Ocp-Apim-Subscription-Key`保留为空的标头。 授权令牌：`"Bearer" + " " + "access_token"`。|标头的值开始缓存响应|string|
|Ocp-Apim-Subscription-Key|(empty)  |如果这两个必需`appid`字段和`Authorization`保留为空的标头。|标头的值开始缓存响应|string|


### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400    |请求错误。 请查看输入参数和详细的错误响应。|
|401    |凭据无效。|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供的大概日期和时间的请求和响应标头中包含的请求 ID `X-MS-Trans-Info`。|
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

这些元素位于`TranslateArrayRequest`:


* `AppId`：必需。 如果`Authorization`或`Ocp-Apim-Subscription-Key`使用标头，保留`AppId`字段均为空。 否则，包含一个字符串，包含`"Bearer" + " " + "access_token"`。
* `From`：可选。 一个字符串，表示要转换的文本的语言代码。 如果此字段留空，则响应将包含自动语言检测的结果。
* `Options`：可选。 `Options`对象，其中包含以下值。 它们都是可选参数，并默认为最常用的设置。 指定的元素必须按字母顺序列出。
    - `Category`：包含转换的类别 （域） 的字符串。 默认为 `general`。
    - `ContentType`：要翻译的文本的格式。 支持的格式为`text/plain`（默认值）， `text/xml`，和`text/html`。 任何 HTML 元素必须是格式正确的、 完整的元素。
    - `ProfanityAction`：指定 profanities 的处理方式，如前面所述。 接受的值是`NoAction`（默认值）， `Marked`，和`Deleted`。
    - `State`：若要帮助将请求和响应相关联的用户状态。 将在响应中返回相同的内容。
    - `Uri`：按此 URI 筛选结果。 默认：`all`。
    - `User`：按此用户筛选结果。 默认：`all`。
* `Texts`：必需。 一个数组，包含翻译的文本。 所有字符串都必须位于相同的语言。 要转换的所有文本的总量不能超过 10,000 个字符。 数组元素的最大数目为 2,000。
* `To`：必需。 一个字符串，表示要转换到的文本的语言代码。

可以省略可选元素。 元素的直接子级的`TranslateArrayRequest`必须按字母顺序列出。

`TranslateArray`方法接受`application/xml`或`text/xml`为`Content-Type`。

**返回值：** 一个 `TranslateArrayResponse` 数组。 每个`TranslateArrayResponse`包含以下元素：

* `Error`：如果发生这种错误，则表示出现错误。 否则设置为 null。
* `OriginalSentenceLengths`：指示每个句子中的源文本的长度的整数数组。 数组的长度指示句子数。
* `TranslatedText`：翻译的文本。
* `TranslatedSentenceLengths`：指示每个句子中的已翻译的文本的长度的整数数组。 数组的长度指示句子数。
* `State`：若要帮助将请求和响应相关联的用户状态。 返回与请求相同的内容。

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

### <a name="response-class-status-200"></a>Response 类 （状态 200）
成功的响应包含一系列的`TranslateArrayResponse`中前面所述的格式的数组。

string

响应内容类型： application/xml

### <a name="parameters"></a>parameters

|参数|值|描述|参数类型|数据类型|
|:--|:--|:--|:--|:--|
|授权|(empty)  |如果这两个必需`appid`字段和`Ocp-Apim-Subscription-Key`保留为空的标头。 授权令牌：`"Bearer" + " " + "access_token"`。|标头的值开始缓存响应|string|
|Ocp-Apim-Subscription-Key|(empty)|如果这两个必需`appid`字段和`Authorization`保留为空的标头。|标头的值开始缓存响应|string|

### <a name="response-messages"></a>响应消息

|HTTP 状态代码   |原因|
|:--|:--|
|400    |请求错误。 请查看输入参数和详细的错误响应。 常见错误包括： <ul><li>数组元素不能为空。</li><li>无效的类别。</li><li>从语言是无效的。</li><li>为语言是无效的。</li><li>该请求包含太多的元素。</li><li>不支持 From 语言。</li><li>不支持到语言。</li><li>转换请求包含太多的数据。</li><li>HTML 不是格式正确。</li><li>在转换请求中传递了太多的字符串。</li></ul>|
|401    |凭据无效。|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供的大概日期和时间的请求和响应标头中包含的请求 ID `X-MS-Trans-Info`。|
|503    |服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|

## <a name="post-getlanguagenames"></a>POST /GetLanguageNames

### <a name="implementation-notes"></a>实现说明
检索语言的友好名称作为参数传入`languageCodes`本地化所传递`locale`语言。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/GetLanguageNames`。

请求正文包含一个字符串数组，表示要检索的友好名称的 ISO 639-1 语言代码。 下面是一个示例：

```
<ArrayOfstring xmlns:i="https://www.w3.org/2001/XMLSchema-instance"  xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>zh</string>
    <string>en</string>
</ArrayOfstring>
```

**返回值：** 包含支持的翻译服务，本地化为请求的语言的语言名称的字符串数组。

### <a name="response-class-status-200"></a>Response 类 （状态 200）
包含支持的翻译服务，本地化为请求的语言的语言名称的字符串数组。

string

响应内容类型： application/xml
 
### <a name="parameters"></a>parameters

|参数|值|描述|参数类型|数据类型|
|:--|:--|:--|:--|:--|
|appid|(empty)|必需。 如果`Authorization`或`Ocp-Apim-Subscription-Key`使用标头，保留`appid`字段均为空。 否则，包含一个字符串，包含`"Bearer" + " " + "access_token"`。|query|string|
|区域设置|(empty) |必需。 用于本地化的语言名称的字符串，表示以下值之一： <ul><li>与语言相关的 ISO 639 双小写字母的区域性代码和 ISO 3166 双字母的大写字母子区域性代码的组合。 <li>单独的 ISO 639 小写字母的区域性代码。|query|string|
|授权|(empty)  |如果这两个必需`appid`字段和`Ocp-Apim-Subscription-Key`保留为空的标头。 授权令牌：`"Bearer" + " " + "access_token"`。|标头的值开始缓存响应|string|
|Ocp-Apim-Subscription-Key|(empty)  |如果这两个必需`appid`字段和`Authorization`保留为空的标头。|标头的值开始缓存响应|string|

### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400    |请求错误。 请查看输入参数和详细的错误响应。|
|401    |凭据无效。|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供的大概日期和时间的请求和响应标头中包含的请求 ID `X-MS-Trans-Info`。|
|503    |服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|

## <a name="get-getlanguagesfortranslate"></a>GET /GetLanguagesForTranslate

### <a name="implementation-notes"></a>实现说明
获取表示语言翻译服务支持的语言代码的列表。  `Translate` 和 `TranslateArray` 可以在这些语言的任意两种之间互译。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForTranslate`。

**返回值：** 一个字符串数组，包含 Translator 服务支持的语言代码。

### <a name="response-class-status-200"></a>Response 类 （状态 200）
一个字符串数组，包含 Translator 服务支持的语言代码。

string

响应内容类型： application/xml
 
### <a name="parameters"></a>parameters

|参数|值|描述|参数类型|数据类型|
|:--|:--|:--|:--|:--|
|appid|(empty)|必需。 如果`Authorization`或`Ocp-Apim-Subscription-Key`使用标头，保留`appid`字段均为空。 否则，包含一个字符串，包含`"Bearer" + " " + "access_token"`。|query|string|
|授权|(empty)  |如果这两个必需`appid`字段和`Ocp-Apim-Subscription-Key`保留为空的标头。 授权令牌：`"Bearer" + " " + "access_token"`。|标头的值开始缓存响应|string|
|Ocp-Apim-Subscription-Key|(empty)|如果这两个必需`appid`字段和`Authorization`保留为空的标头。|标头的值开始缓存响应|string|

### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400    |请求错误。 请查看输入参数和详细的错误响应。|
|401    |凭据无效。|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供的大概日期和时间的请求和响应标头中包含的请求 ID `X-MS-Trans-Info`。|
|503|服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|

## <a name="get-getlanguagesforspeak"></a>GET /GetLanguagesForSpeak

### <a name="implementation-notes"></a>实现说明
检索可用于语音合成的语言。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForSpeak`。

**返回值：** 一个字符串数组，包含 Translator 服务支持用于语音合成的语言代码。

### <a name="response-class-status-200"></a>Response 类 （状态 200）
一个字符串数组，包含 Translator 服务支持用于语音合成的语言代码。

string

响应内容类型： application/xml

### <a name="parameters"></a>parameters

|参数|值|描述|参数类型|数据类型|
|:--|:--|:--|:--|:--|
|appid|(empty)|必需。 如果`Authorization`或`Ocp-Apim-Subscription-Key`使用标头，保留`appid`字段均为空。 否则，包含一个字符串，包含`"Bearer" + " " + "access_token"`。|query|string|
|授权|(empty)|如果这两个必需`appid`字段和`Ocp-Apim-Subscription-Key`保留为空的标头。 授权令牌：`"Bearer" + " " + "access_token"`。|标头的值开始缓存响应|string|
|Ocp-Apim-Subscription-Key|(empty)|如果这两个必需`appid`字段和`Authorization`保留为空的标头。|标头的值开始缓存响应|string|
 
### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400|请求错误。 请查看输入参数和详细的错误响应。|
|401|凭据无效。|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供的大概日期和时间的请求和响应标头中包含的请求 ID `X-MS-Trans-Info`。|
|503    |服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|

## <a name="get-speak"></a>GET /Speak

### <a name="implementation-notes"></a>实现说明
返回传入的文本，所需的语言中讲述的 WAV 或 MP3 流。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/Speak`。

**返回值：** WAV 或 MP3 的传入的文本的所需的语言中讲述的流。

### <a name="response-class-status-200"></a>Response 类 （状态 200）

binary

响应内容类型： application/xml

### <a name="parameters"></a>parameters

|参数|值|描述|参数类型|数据类型|
|:--|:--|:--|:--|:--|
|appid|(empty)|必需。 如果`Authorization`或`Ocp-Apim-Subscription-Key`使用标头，保留`appid`字段均为空。 否则，包含一个字符串，包含`"Bearer" + " " + "access_token"`。|query|string|
|text|(empty)   |必需。 一个字符串，包含一个或多个句子要朗读的流，以指定的语言。 文本不得超过 2000 个字符。|query|string|
|language|(empty)   |必需。 一个字符串，表示在其中朗读该文本的语言的受支持的语言代码。 代码必须是该方法返回代码之一`GetLanguagesForSpeak`。|query|string|
|format|(empty)|可选。 一个字符串，指定内容类型 id。 目前，`audio/wav` 和 `audio/mp3` 可用。 默认值为 `audio/wav`。|query|string|
|options|(empty)    |可选。 一个字符串，指定的合成语音属性：<ul><li>`MaxQuality` 和`MinSize`指定质量的音频信号。 `MaxQuality` 提供最高的质量。 `MinSize` 提供的最小文件大小。 默认值是`MinSize`。</li><li>`female` 和`male`指定所需的语音的性别。 默认为 `female`。 使用垂直条 (<code>\|</code>) 包括多个选项。 例如，`MaxQuality|Male`。</li></li></ul>  |query|string|
|授权|(empty)|如果这两个必需`appid`字段和`Ocp-Apim-Subscription-Key`保留为空的标头。 授权令牌：`"Bearer" + " " + "access_token"`。|标头的值开始缓存响应|string|
|Ocp-Apim-Subscription-Key|(empty)  |如果这两个必需`appid`字段和`Authorization`保留为空的标头。|标头的值开始缓存响应|string|

### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400    |请求错误。 请查看输入参数和详细的错误响应。|
|401    |凭据无效。|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供的大概日期和时间的请求和响应标头中包含的请求 ID `X-MS-Trans-Info`。|
|503    |服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|

## <a name="get-detect"></a>GET /Detect

### <a name="implementation-notes"></a>实现说明
标识的一部分文本的语言。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/Detect`。

**返回值：** 一个字符串，包含文本的双字符语言代码。

### <a name="response-class-status-200"></a>Response 类 （状态 200）

string

响应内容类型： application/xml

### <a name="parameters"></a>parameters

|参数|值|描述|参数类型|数据类型|
|:--|:--|:--|:--|:--|
|appid|(empty)  |必需。 如果`Authorization`或`Ocp-Apim-Subscription-Key`使用标头，保留`appid`字段均为空。 否则，包含一个字符串，包含`"Bearer" + " " + "access_token"`。|query|string|
|text|(empty)|必需。 一个字符串，包含其语言是可以识别的文本。 文本不得超过 10,000 个字符。|query|  string|
|授权|(empty)|如果这两个必需`appid`字段和`Ocp-Apim-Subscription-Key`保留为空的标头。 授权令牌：`"Bearer" + " " + "access_token"`。|标头的值开始缓存响应|string|
|Ocp-Apim-Subscription-Key  |(empty)    |如果这两个必需`appid`字段和`Authorization`保留为空的标头。|标头的值开始缓存响应|string|

### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400|请求错误。 请查看输入参数和详细的错误响应。|
|401    |凭据无效。|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供的大概日期和时间的请求和响应标头中包含的请求 ID `X-MS-Trans-Info`。|
|503    |服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|


## <a name="post-detectarray"></a>POST /DetectArray

### <a name="implementation-notes"></a>实现说明

标识字符串数组中的语言。 独立检测的每个单个数组元素的语言，并返回数组的每个行的结果。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/DetectArray`。

下面是请求正文的格式：

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>string-value-1</string>
    <string>string-value-2</string>
</ArrayOfstring>
```

文本不能超过 10,000 个字符。

**返回值：** 一个字符串数组，包含输入数组中的每一行的双字符语言代码。

下面是响应正文的格式：

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <string>language-code-1</string>
  <string>language-code-2</string>
</ArrayOfstring>
```

### <a name="response-class-status-200"></a>Response 类 （状态 200）
`DetectArray` 已成功完成。 返回包含输入数组的每一行的双字符语言代码的字符串数组。

string

响应内容类型： application/xml
 
### <a name="parameters"></a>parameters

|参数|值|描述|参数类型|数据类型|
|:--|:--|:--|:--|:--|
|appid|(empty)|必需。 如果`Authorization`或`Ocp-Apim-Subscription-Key`使用标头，保留`appid`字段均为空。 否则，包含一个字符串，包含`"Bearer" + " " + "access_token"`。|query|string|
|授权|(empty)|如果这两个必需`appid`字段和`Ocp-Apim-Subscription-Key`保留为空的标头。  授权令牌：`"Bearer" + " " + "access_token"`。|标头的值开始缓存响应|string|
|Ocp-Apim-Subscription-Key|(empty)|如果这两个必需`appid`字段和`Authorization`保留为空的标头。|标头的值开始缓存响应|string|

### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400    |请求错误。 请查看输入参数和详细的错误响应。|
|401    |凭据无效。|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供的大概日期和时间的请求和响应标头中包含的请求 ID `X-MS-Trans-Info`。|
|503    |服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|

## <a name="get-addtranslation"></a>GET /AddTranslation

### <a name="implementation-notes"></a>实现说明

> [!IMPORTANT]
> **不推荐使用注意：** 在 2018 年 1 月 31 日之后此方法将不会接受新句子提交。 你将收到错误消息。 请有关更改到协作翻译框架 (CTF) 公告，参阅。

向翻译记忆库添加翻译。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/AddTranslation`。

### <a name="response-class-status-200"></a>Response 类 （状态 200）

string

响应内容类型： 应用程序： xml
 
### <a name="parameters"></a>parameters

|参数|值|描述|参数类型|数据类型   |
|:--|:--|:--|:--|:--|
|appid|(empty)|必需。 如果`Authorization`或`Ocp-Apim-Subscription-Key`使用标头，保留`appid`字段均为空。 否则，包含一个字符串，包含`"Bearer" + " " + "access_token"`。|query|string|
|originalText|(empty)|必需。 包含要翻译的文本的字符串。 字符串的最大长度为 1000 个字符。|query|string|
|translatedText|(empty) |必需。 包含文本的字符串转换为目标语言。 字符串的最大长度为 2000 个字符。|query|string|
|from|(empty)   |必需。 一个字符串，表示文本的原始语言的语言代码。 例如，en 表示英语，de 代表德语。|query|string|
|至|(empty)|必需。 一个字符串，表示要转换到的文本的语言的语言代码。|query|string|
|rating|(empty) |可选。 一个整数，表示字符串的质量级别。 值为-10 到 10 之间。 默认值为 1。|query|integer|
|contentType|(empty)    |可选。 要翻译的文本的格式。 支持的格式为`text/plain`和`text/html`。 任何 HTML 元素必须是格式正确的、 完整的元素。    |query|string|
|category|(empty)|可选。 包含转换的类别 （域） 的字符串。 默认为 `general`。|query|string|
|user|(empty)|必需。 一个字符串，用于跟踪在提交内容的原始发件人。|query|string|
|uri|(empty)|可选。 一个字符串，包含翻译的内容的位置。|query|string|
|授权|(empty)|如果这两个必需`appid`字段和`Ocp-Apim-Subscription-Key`保留为空的标头。  授权令牌：`"Bearer" + " " + "access_token"`。  |标头的值开始缓存响应|string|
|Ocp-Apim-Subscription-Key|(empty)|如果这两个必需`appid`字段和`Authorization`保留为空的标头。|标头的值开始缓存响应|string|

### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400    |请求错误。 请查看输入参数和详细的错误响应。|
|401    |凭据无效。|
|410|`AddTranslation` 不再受支持。|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供的大概日期和时间的请求和响应标头中包含的请求 ID `X-MS-Trans-Info`。|
|503    |服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|

## <a name="post-addtranslationarray"></a>POST /AddTranslationArray

### <a name="implementation-notes"></a>实现说明

> [!IMPORTANT]
> **不推荐使用注意：** 在 2018 年 1 月 31 日之后此方法将不会接受新句子提交。 你将收到错误消息。 请有关更改到协作翻译框架 (CTF) 公告，参阅。

将翻译的数组添加到翻译记忆。 此方法是数组版本`AddTranslation`。

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

这些元素位于`AddtranslationsRequest`:

* `AppId`：必需。 如果`Authorization`或`Ocp-Apim-Subscription-Key`使用标头，保留`AppId`字段均为空。 否则，包含一个字符串，包含`"Bearer" + " " + "access_token"`。
* `From`：必需。 包含源代码的语言的语言代码的字符串。 必须是 `GetLanguagesForTranslate` 方法返回的语言之一。
* `To`：必需。 一个字符串，包含目标语言的语言代码。 必须是 `GetLanguagesForTranslate` 方法返回的语言之一。
* `Translations`：必需。 要添加到翻译记忆库的一个翻译数组。 必须包含每个翻译`OriginalText`， `TranslatedText`，和`Rating`。 每个的最大大小`OriginalText`和`TranslatedText`为 1,000 个字符。 所有的总和`OriginalText`和`TranslatedText`元素不能超过 10,000 个字符。 最大数组元素数为 100。
* `Options`：必需。 选项，包括一套`Category`， `ContentType`， `Uri`，和`User`。 `User` 是必需的。 `Category``ContentType`，和`Uri`都是可选的。 指定的元素必须按字母顺序列出。

### <a name="response-class-status-200"></a>Response 类 （状态 200）
`AddTranslationArray` 方法成功。 

在 2018 年 1 月 31 日之后不会接受句子提交。 此服务会以错误代码 410 进行响应。

string

响应内容类型： application/xml
 
### <a name="parameters"></a>parameters

|参数|值|描述|参数类型|数据类型|
|:--|:--|:--|:--|:--|
|授权|(empty)|如果这两个必需`appid`字段和`Ocp-Apim-Subscription-Key`保留为空的标头。  授权令牌：`"Bearer" + " " + "access_token"`。|标头的值开始缓存响应|string|
|Ocp-Apim-Subscription-Key|(empty)|如果这两个必需`appid`字段和`Authorization`保留为空的标头。|标头的值开始缓存响应|string|

### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400    |请求错误。 请查看输入参数和详细的错误响应。|
|401    |凭据无效。|
|410    |`AddTranslation` 不再受支持。|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供的大概日期和时间的请求和响应标头中包含的请求 ID `X-MS-Trans-Info`。|
|503|服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|

## <a name="get-breaksentences"></a>GET /BreakSentences

### <a name="implementation-notes"></a>实现说明
分解成的句子的文本的一部分，并返回包含每个句子的长度的数组。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/BreakSentences`。

**返回值：** 表示的语句的长度的整数数组。 数组的长度表示的语句的数量。 这些值表示每个句子的长度。

### <a name="response-class-status-200"></a>Response 类 （状态 200）
表示的语句的长度的整数数组。 数组的长度表示的语句的数量。 这些值表示每个句子的长度。

integer

响应内容类型： application/xml

### <a name="parameters"></a>parameters

|参数|值|描述|参数类型|数据类型|
|:--|:--|:--|:--|:--|
|appid|(empty)  |必需。 如果`Authorization`或`Ocp-Apim-Subscription-Key`使用标头，保留`appid`字段均为空。 否则，包含一个字符串，包含`"Bearer" + " " + "access_token"`。|query| string|
|text|(empty)   |必需。 表示要拆分成句子的文本字符串。 文本的最大大小为 10,000 个字符。|query|string|
|language   |(empty)    |必需。 一个字符串，表示输入文本的语言代码。|query|string|
|授权|(empty)|如果这两个必需`appid`字段和`Ocp-Apim-Subscription-Key`保留为空的标头。 授权令牌：`"Bearer" + " " + "access_token"`。   |标头的值开始缓存响应|string|
|Ocp-Apim-Subscription-Key|(empty)|如果这两个必需`appid`字段和`Authorization`保留为空的标头。|标头的值开始缓存响应|string|

### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400|请求错误。 请查看输入参数和详细的错误响应。|
|401|凭据无效。|
|500|服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供的大概日期和时间的请求和响应标头中包含的请求 ID `X-MS-Trans-Info`。|
|503|服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|

## <a name="post-gettranslations"></a>POST /GetTranslations

### <a name="implementation-notes"></a>实现说明
从存储和 MT 引擎中检索给定语言对的翻译数组。 `GetTranslations` 不同于`Translate`，它将返回所有可用翻译。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/GetTranslations`。

请求的正文包括可选`TranslationOptions`对象，它具有以下格式：

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

`TranslateOptions`对象包含以下列表中的值。 它们都是可选参数，并默认为最常用的设置。 指定的元素必须按字母顺序列出。

* `Category`：包含转换的类别 （域） 的字符串。 默认为 `general`。
* `ContentType`：唯一支持的选项，并且默认值， `text/plain`。
* `IncludeMultipleMTAlternatives`：一个布尔标志，指定是否应从 MT 引擎返回多个替代方法。 有效的值为`true`和`false`（区分大小写）。 默认值是`false`，它将返回仅一种替代方法。 将标志设置为`true`允许创建自定义的替代，完全集成在一起协作翻译框架 (CTF)。 功能使返回的替代方法，通过添加人工备选方法从 CTF 中有任何翻译的句子*n*-最佳的解码器的列表。
    - 分级。 分级应用如下所示： 
         - 最佳自动翻译评级为 5。
       - 这些备选方法从 CTF 反映审阅者的颁发机构。 范围从-10 到 + 10。
       - 自动生成 (*n*-最佳) 转换的替代项已从 0 到 100 的匹配程度进行评级。
    - 替代项的数目。 中指定的值作为最高可返回的备选方法数量`maxTranslations`，但可能会较低。
    - 语言对。 此功能不可用于之间简体中文和繁体中文在任一方向的转换。 它是可用于通过 Microsoft Translator 支持的所有其他语言对。
* `State`：若要帮助将请求和响应相关联的用户状态。 将在响应中返回相同的内容。
* `Uri`：按此 URI 筛选结果。 如果未不设置任何值，默认值是`all`。
* `User`：按此用户筛选结果。 如果未不设置任何值，默认值是`all`。

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

此响应包含`GetTranslationsResponse`元素，其中包含以下值：

* `Translations`：找到匹配项的数组，存储在`TranslationMatch`（下一节中所述） 的对象。 翻译可能包括 （模糊匹配） 的原始文本的细微的变体。 将对翻译进行排序：100%匹配第一次，模糊匹配下一步。
* `From`：如果该方法不会指定`From`语言中，此值将来自自动语言检测。 否则，它将为指定`From`语言。
* `State`：若要帮助将请求和响应相关联的用户状态。 中提供的值包含`TranslateOptions`参数。

`TranslationMatch`对象包含这些值：

* `Error`：如果特定的输入字符串就会出错，错误代码。 否则，此字段为空。
* `MatchDegree`：指示输入的文本与在存储中找到的原始文本的匹配程度。 系统将输入句子与存储内容进行匹配，包括不精确匹配。 返回的值范围从 0 到 100 之间，其中 0 表示没有相似性，100 是完全相同的、 区分大小写的匹配项。
* `MatchedOriginalText`：此结果的匹配的原始文本。 仅当匹配的原始文本已不同于输入文本，则返回此值。 它用于返回模糊匹配的源文本。 返回此值不是为 Microsoft Translator 结果。
* `Rating`：表示进行质量决策的人员的权威性。 机器翻译结果具有评级为 5。 以匿名方式提供的转换通常具有从 1 到 4 的分级。 权威地提供的翻译通常具有从 6 到 10 的分级。
* `Count`：此评分的这个翻译被选中的次数。 值为 0 的自动翻译后的响应。
* `TranslatedText`：翻译的文本。

### <a name="response-class-status-200"></a>Response 类 （状态 200）
一个`GetTranslationsResponse`中前面所述的格式对象。

string

响应内容类型： application/xml
 
### <a name="parameters"></a>parameters

|参数|值|描述|参数类型|数据类型|
|:--|:--|:--|:--|:--|
|appid|(empty)|必需。 如果`Authorization`或`Ocp-Apim-Subscription-Key`使用标头，保留`appid`字段均为空。 否则，包含一个字符串，包含`"Bearer" + " " + "access_token"`。|query|string|
|text|(empty)|必需。 表示要转换的文本字符串。 文本的最大大小为 10,000 个字符。|query|string|
|from|(empty)|必需。 一个字符串，表示要转换的文本的语言代码。|query|string|
|至 |(empty)    |必需。 一个字符串，表示要转换到的文本的语言的语言代码。|query|string|
|maxTranslations|(empty)|必需。 一个整数，表示转换，可返回的最大数目。|query|integer|
|授权| (empty)|如果这两个必需`appid`字段和`Ocp-Apim-Subscription-Key`保留为空的标头。 授权令牌：`"Bearer" + " " + "access_token"`。|string|  标头的值开始缓存响应|
|Ocp-Apim-Subscription-Key|(empty)  |如果这两个必需`appid`字段和`Authorization`保留为空的标头。|标头的值开始缓存响应|string|

### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400    |请求错误。 请查看输入参数和详细的错误响应。|
|401    |凭据无效。|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供的大概日期和时间的请求和响应标头中包含的请求 ID `X-MS-Trans-Info`。|
|503|服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|

## <a name="post-gettranslationsarray"></a>POST /GetTranslationsArray

### <a name="implementation-notes"></a>实现说明
检索多个源文本的多个翻译候选项。

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

`GetTranslationsArrayRequest` 包括以下元素：

* `AppId`：必需。 如果`Authorization`使用标头，保留`AppId`字段均为空。 否则，包含一个字符串，包含`"Bearer" + " " + "access_token"`。
* `From`：必需。 一个字符串，表示要转换的文本的语言代码。
* `MaxTranslations`：必需。 一个整数，表示转换，可返回的最大数目。
* `Options`：可选。 `Options`对象，其中包含以下值。 它们都是可选参数，并默认为最常用的设置。 指定的元素必须按字母顺序列出。
    - `Category`：包含转换的类别 （域） 的字符串。 默认为 `general`。
    - `ContentType`：唯一支持的选项，并且默认值， `text/plain`。
    - `IncludeMultipleMTAlternatives`：一个布尔标志，指定是否应从 MT 引擎返回多个替代方法。 有效的值为`true`和`false`（区分大小写）。 默认值是`false`，它将返回仅一种替代方法。 将标志设置为`true`可以生成的人工翻译，完全集成在一起协作翻译框架 (CTF) 中的替代项。 功能，返回替代项的添加从人工备用方案没有替代项在 CTF 句子*n*-最佳的解码器的列表。
        - 分级级别应用如下所示：
          - 最佳自动翻译评级为 5。
          - 这些备选方法从 CTF 反映审阅者的颁发机构。 范围从-10 到 + 10。
          - 自动生成 (*n*-最佳) 转换的替代项已从 0 到 100 的匹配程度进行评级。
        - 替代项的数目。 中指定的值作为最高可返回的备选方法数量`maxTranslations`，但可能会较低。
        - 语言对。 此功能不可用于之间简体中文和繁体中文在任一方向的转换。 它是可用于通过 Microsoft Translator 支持的所有其他语言对。
* `State`：若要帮助将请求和响应相关联的用户状态。 将在响应中返回相同的内容。
* `Uri`：按此 URI 筛选结果。 如果未不设置任何值，默认值是`all`。
* `User`：按此用户筛选结果。 如果未不设置任何值，默认值是`all`。
* `Texts`：必需。 一个数组，包含翻译的文本。 所有字符串都必须位于相同的语言。 要转换的所有文本的总量不能超过 10,000 个字符。 最大数组元素数为 10。
* `To`：必需。 一个字符串，表示要转换到的文本的语言的语言代码。

可以省略可选元素。 元素的直接子级的`GetTranslationsArrayRequest`必须按字母顺序列出。

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

每个`GetTranslationsResponse`元素包含这些值：

* `Translations`：找到匹配项的数组，存储在`TranslationMatch`（下一节中所述） 的对象。 翻译可能包括 （模糊匹配） 的原始文本的细微的变体。 将对翻译进行排序：100%匹配第一次，模糊匹配下一步。
* `From`：如果该方法不会指定`From`语言中，此值将来自自动语言检测。 否则，它将为指定`From`语言。
* `State`：若要帮助将请求和响应相关联的用户状态。 中提供的值包含`TranslateOptions`参数。

`TranslationMatch`对象包含以下值：
* `Error`：如果特定的输入字符串就会出错，错误代码。 否则，此字段为空。
* `MatchDegree`：指示输入的文本与在存储中找到的原始文本的匹配程度。 系统将输入句子与存储内容进行匹配，包括不精确匹配。 返回的值范围从 0 到 100 之间，其中 0 表示没有相似性，100 是完全相同的、 区分大小写的匹配项。
* `MatchedOriginalText`：此结果的匹配的原始文本。 仅当匹配的原始文本已不同于输入文本，则返回此值。 它用于返回模糊匹配的源文本。 返回此值不是为 Microsoft Translator 结果。
* `Rating`：表示进行质量决策的人员的权威性。 机器翻译结果具有评级为 5。 以匿名方式提供的转换通常具有从 1 到 4 的分级。 权威地提供的转换通常具有从 6 到 10 的分级。
* `Count`：此评分的这个翻译被选中的次数。 值为 0 的自动翻译后的响应。
* `TranslatedText`：翻译的文本。


### <a name="response-class-status-200"></a>Response 类 （状态 200）

string

响应内容类型： application/xml
 
### <a name="parameters"></a>parameters

|参数|值|描述|参数类型|数据类型|
|:--|:--|:--|:--|:--|
|授权  |(empty)    |如果这两个必需`appid`字段和`Ocp-Apim-Subscription-Key`保留为空的标头。  授权令牌：`"Bearer" + " " + "access_token"`。|标头的值开始缓存响应|string|
|Ocp-Apim-Subscription-Key|(empty)  |如果这两个必需`appid`字段和`Authorization`保留为空的标头。|标头的值开始缓存响应|string|

### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400    |请求错误。 请查看输入参数和详细的错误响应。|
|401    |凭据无效。|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供请求的大致日期和时间，以及包括在响应标头 `X-MS-Trans-Info` 中的请求 ID。|
|503    |服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [将迁移到文本翻译 API v3 ](../migrate-to-v3.md)


