---
title: 文本翻译 API v2.0
titleSuffix: Azure Cognitive Services
description: 翻译文本 API v2.0 的参考文档。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 05/15/2018
ms.author: swmachan
ms.openlocfilehash: f111169558118a80602bcb2136bc63ce54c9e0d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "72242498"
---
# <a name="translator-text-api-v20"></a>文本翻译 API v2.0

> [!IMPORTANT]
> 此版文本翻译 API 已弃用。 [查看翻译器文本 API 版本 3 的文档](v3-0-reference.md)。

翻译器文本 API 的版本 2 可以无缝集成到您的应用、网站、工具或其他解决方案中，以提供多语言用户体验。 根据行业标准，您可以在任何硬件平台和任何操作系统上使用它来执行语言翻译和其他与语言相关的任务，如文本语言检测和语音文本。 有关详细信息，请参阅[翻译文本 API](../translator-info-overview.md)。

## <a name="getting-started"></a>入门
要访问翻译器文本 API，您需要[注册 Microsoft Azure](../translator-text-how-to-signup.md)。

## <a name="authentication"></a>身份验证 
对翻译文本 API 的所有调用都需要一个订阅密钥进行身份验证。 API 支持三种身份验证方法：

- 访问令牌。 使用订阅密钥通过向身份验证服务发出 POST 请求来创建访问令牌。 有关详细信息，请参阅令牌服务文档。 使用`Authorization`标头或`access_token`查询参数将访问令牌传递给转换器服务。 访问令牌的有效期为 10 分钟。 每 10 分钟获取一个新访问令牌，并在 10 分钟内对重复请求使用相同的访问令牌。
- 直接使用的订阅密钥。 将订阅密钥作为请求中包含的标头中`Ocp-Apim-Subscription-Key`的值传递给翻译人员文本 API。 直接使用订阅密钥时，不必调用令牌身份验证服务来创建访问令牌。
- [Azure 认知服务多服务多服务订阅](https://azure.microsoft.com/pricing/details/cognitive-services/)。 此方法允许您使用单个密钥对多个服务的请求进行身份验证。
使用多服务密钥时，需要将两个身份验证标头包含在请求中。 第一个标头传递密钥。 第二个标头指定与您的订阅关联的区域：
   - `Ocp-Apim-Subscription-Key`
   - `Ocp-Apim-Subscription-Region`

多服务文本 API 订阅需要该区域。 您选择的区域是使用多服务订阅密钥时可用于文本翻译的唯一区域。 它需要与您在 Azure 门户上注册多服务订阅时选择的区域相同。

可用`australiaeast`区域是`brazilsouth``canadacentral``centralindia``centraluseuap``southcentralus``southeastasia``uksouth``westcentralus``westeurope``westus``westus2``eastus``eastus2``japaneast``northeurope`、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、 `eastasia`

订阅密钥和访问令牌是应隐藏在视图中的秘密。

## <a name="profanity-handling"></a>不雅内容处理
通常，翻译服务将保留源中的亵渎。 亵渎的程度和使亵渎词的语境因文化而异。 因此，目标语言中的亵渎程度可以增加或降低。

如果要防止翻译中的亵渎，即使它位于源文本中，也可以对支持该文本的方法使用亵渎筛选选项。 该选项允许您选择是希望看到亵渎被删除还是用适当的标记标记，还是要允许在目标中使用亵渎。 的`ProfanityAction`已接受值为`NoAction`（默认`Marked`）、和`Deleted`。


|ProfanityAction    |操作 |示例源（日语）  |翻译示例（英语）  |
|:--|:--|:--|:--|
|NoAction   |默认。 与不设置此选项等效。 不雅内容会从源传递到目标。        |彼はジャッカスです。     |他是一个笨蛋。   |
|Marked     |亵渎词将被xml标记\<>亵渎和\</亵渎>所包围。       |彼はジャッカスです。 |他是一个\<亵渎>的混蛋\</亵渎>。  |
|Deleted    |不雅词语会从输出中删除，不进行替换。     |彼はジャッカスです。 |他是一个。   |

    
## <a name="excluding-content-from-translation"></a>将内容从翻译中排除
当您使用标记（如 HTML （`contentType=text/html`）））翻译内容时，有时从翻译中排除特定内容很有用。 可以使用属性 `class=notranslate` 指定应保留不译的内容。 在下面的示例中，不会翻译第一`div`个元素中的内容，但将翻译第二`div`个元素中的内容。

```HTML
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

## <a name="get-translate"></a>GET /Translate

### <a name="implementation-notes"></a>实现说明
将一种语言的文本字符串翻译为另一种语言。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/Translate`。

**返回值：** 表示翻译文本的字符串。

如果您以前使用`AddTranslation`或`AddTranslationArray`输入同一源句子的评级为 5 或更高的翻译，`Translate`则仅返回系统可用的首选。 "同一源句"表示完全相同的（100% 匹配），但句子末尾的大小写、空格、标记值和标点符号除外。 如果未存储评级为 5 或以上，则返回的结果将由 Microsoft 翻译人员自动翻译。

### <a name="response-class-status-200"></a>响应类（状态 200）

字符串

响应内容类型：应用程序/xml

### <a name="parameters"></a>参数

|参数|“值”|描述    |参数类型|数据类型|
|:--|:--|:--|:--|:--|
|appid  |(empty)    |必需。 如果使用`Authorization`或`Ocp-Apim-Subscription-Key`标头，请保留该`appid`字段为空。 否则，请包含包含 的`"Bearer" + " " + "access_token"`字符串。|query|字符串|
|text|(empty)   |必需。 表示要翻译的文本的字符串。 文本不能包含超过 10，000 个字符。|query|字符串|
|从|(empty)   |可选。 表示要翻译的文本的语言代码的字符串。 例如，en 表示英语。|query|字符串|
|to|(empty) |必需。 表示要将文本转换为的语言代码的字符串。|query|字符串|
|contentType|(empty)    |可选。 要翻译的文本的格式。 支持的格式为`text/plain`（默认）和`text/html`。 任何 HTML 元素都必须是格式良好的完整元素。|query|字符串|
|category|(empty)   |可选。 包含翻译的类别（域）的字符串。 默认为 `general`。|query|字符串|
|授权|(empty)  |如果字段和标头`appid`都留空`Ocp-Apim-Subscription-Key`，则为必填项。 授权令牌：`"Bearer" + " " + "access_token"`。|标头的值开始缓存响应|字符串|
|Ocp-Apim-Subscription-Key|(empty)  |如果字段和标头`appid`都留空`Authorization`，则为必填项。|标头的值开始缓存响应|字符串|


### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400    |请求错误。 请查看输入参数和详细的错误响应。|
|401    |凭据无效。|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供请求的大致日期&时间以及响应标头`X-MS-Trans-Info`中包含的请求 ID。|
|503    |服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|

## <a name="post-translatearray"></a>POST /TranslateArray

### <a name="implementation-notes"></a>实现说明
检索多个源文本的翻译。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/TranslateArray`。

以下是请求正文的格式：

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

这些元素在`TranslateArrayRequest`中。


* `AppId`：必需。 如果使用`Authorization`或`Ocp-Apim-Subscription-Key`标头，请保留该`AppId`字段为空。 否则，请包含包含 的`"Bearer" + " " + "access_token"`字符串。
* `From`：可选。 表示要翻译的文本的语言代码的字符串。 如果此字段留空，响应将包括自动语言检测的结果。
* `Options`：可选。 包含`Options`以下值的对象。 它们都是可选的，默认为最常见的设置。 指定的元素必须按字母顺序列出。
    - `Category`：包含翻译的类别（域）的字符串。 默认为 `general`。
    - `ContentType`：要翻译的文本的格式。 支持的格式为`text/plain`（默认）、`text/xml`和`text/html`。 任何 HTML 元素都必须是格式良好的完整元素。
    - `ProfanityAction`： 指定如何处理亵渎，如前面所述。 接受的值为`NoAction`（默认）、`Marked`和`Deleted`。
    - `State`：用户状态以帮助关联请求和响应。 相同的内容将在响应中返回。
    - `Uri`：按此 URI 筛选结果。 默认：`all`。
    - `User`：按此用户筛选结果。 默认：`all`。
* `Texts`：必需。 包含要翻译的文本的数组。 所有字符串必须使用同一语言。 要翻译的所有文本总数不能超过 10，000 个字符。 数组元素的最大数量为 2，000。
* `To`：必需。 表示要将文本转换为的语言代码的字符串。

您可以省略可选元素。 作为 直接`TranslateArrayRequest`子级的元素必须按字母顺序列出。

该方法`TranslateArray`接受`application/xml`或`text/xml` `Content-Type`。

**返回值：** 一个 `TranslateArrayResponse` 数组。 每个`TranslateArrayResponse`元素都有以下元素：

* `Error`：指示发生错误。 否则设置为 null。
* `OriginalSentenceLengths`： 一个整数数组，用于指示源文本中每个句子的长度。 数组的长度指示句子数。
* `TranslatedText`：翻译的文本。
* `TranslatedSentenceLengths`：一个整数数组，用于指示翻译文本中每个句子的长度。 数组的长度指示句子数。
* `State`：用户状态以帮助关联请求和响应。 返回与请求相同的内容。

以下是响应正文的格式：

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

### <a name="response-class-status-200"></a>响应类（状态 200）
成功的响应包括前面所述格式的`TranslateArrayResponse`数组数组数组。

字符串

响应内容类型：应用程序/xml

### <a name="parameters"></a>参数

|参数|“值”|描述|参数类型|数据类型|
|:--|:--|:--|:--|:--|
|授权|(empty)  |如果字段和标头`appid`都留空`Ocp-Apim-Subscription-Key`，则为必填项。 授权令牌：`"Bearer" + " " + "access_token"`。|标头的值开始缓存响应|字符串|
|Ocp-Apim-Subscription-Key|(empty)|如果字段和标头`appid`都留空`Authorization`，则为必填项。|标头的值开始缓存响应|字符串|

### <a name="response-messages"></a>响应消息

|HTTP 状态代码   |原因|
|:--|:--|
|400    |请求错误。 请查看输入参数和详细的错误响应。 常见错误包括： <ul><li>数组元素不能为空。</li><li>无效类别。</li><li>从语言无效。</li><li>语言无效。</li><li>请求包含的元素太多。</li><li>不支持 From 语言。</li><li>不支持"到"语言。</li><li>翻译请求的数据太多。</li><li>HTML 格式不正确。</li><li>翻译请求中传递的字符串太多。</li></ul>|
|401    |凭据无效。|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供请求的大致日期&时间以及响应标头`X-MS-Trans-Info`中包含的请求 ID。|
|503    |服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|

## <a name="post-getlanguagenames"></a>POST /GetLanguageNames

### <a name="implementation-notes"></a>实现说明
检索作为参数`languageCodes`传入的语言的友好名称，本地化为传递`locale`的语言。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/GetLanguageNames`。

请求正文包括一个字符串数组，该数组表示要检索友好名称的 ISO 639-1 语言代码。 下面是一个示例：

```
<ArrayOfstring xmlns:i="https://www.w3.org/2001/XMLSchema-instance"  xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>zh</string>
    <string>en</string>
</ArrayOfstring>
```

**返回值：** 包含由翻译服务支持的语言名称的字符串数组，本地化为请求的语言。

### <a name="response-class-status-200"></a>响应类（状态 200）
包含由翻译服务支持的语言名称的字符串数组，本地化为请求的语言。

字符串

响应内容类型：应用程序/xml
 
### <a name="parameters"></a>参数

|参数|“值”|描述|参数类型|数据类型|
|:--|:--|:--|:--|:--|
|appid|(empty)|必需。 如果使用`Authorization`或`Ocp-Apim-Subscription-Key`标头，请保留该`appid`字段为空。 否则，请包含包含 的`"Bearer" + " " + "access_token"`字符串。|query|字符串|
|区域设置|(empty) |必需。 表示以下语言名称之一的字符串，用于本地化语言名称： <ul><li>与语言关联的 ISO 639 双字母小写区域性代码和 ISO 3166 双字母大写子区域性代码的组合。 <li>ISO 639 小写区域性代码本身。|query|字符串|
|授权|(empty)  |如果字段和标头`appid`都留空`Ocp-Apim-Subscription-Key`，则为必填项。 授权令牌：`"Bearer" + " " + "access_token"`。|标头的值开始缓存响应|字符串|
|Ocp-Apim-Subscription-Key|(empty)  |如果字段和标头`appid`都留空`Authorization`，则为必填项。|标头的值开始缓存响应|字符串|

### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400    |请求错误。 请查看输入参数和详细的错误响应。|
|401    |凭据无效。|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供请求的大致日期&时间以及响应标头`X-MS-Trans-Info`中包含的请求 ID。|
|503    |服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|

## <a name="get-getlanguagesfortranslate"></a>GET /GetLanguagesForTranslate

### <a name="implementation-notes"></a>实现说明
获取表示翻译服务支持的语言的语言代码的列表。  `Translate` 和 `TranslateArray` 可以在这些语言的任意两种之间互译。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForTranslate`。

**返回值：** 包含翻译服务支持的语言代码的字符串数组。

### <a name="response-class-status-200"></a>响应类（状态 200）
包含翻译服务支持的语言代码的字符串数组。

字符串

响应内容类型：应用程序/xml
 
### <a name="parameters"></a>参数

|参数|“值”|描述|参数类型|数据类型|
|:--|:--|:--|:--|:--|
|appid|(empty)|必需。 如果使用`Authorization`或`Ocp-Apim-Subscription-Key`标头，请保留该`appid`字段为空。 否则，请包含包含 的`"Bearer" + " " + "access_token"`字符串。|query|字符串|
|授权|(empty)  |如果字段和标头`appid`都留空`Ocp-Apim-Subscription-Key`，则为必填项。 授权令牌：`"Bearer" + " " + "access_token"`。|标头的值开始缓存响应|字符串|
|Ocp-Apim-Subscription-Key|(empty)|如果字段和标头`appid`都留空`Authorization`，则为必填项。|标头的值开始缓存响应|字符串|

### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400    |请求错误。 请查看输入参数和详细的错误响应。|
|401    |凭据无效。|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供请求的大致日期&时间以及响应标头`X-MS-Trans-Info`中包含的请求 ID。|
|503|服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|

## <a name="get-getlanguagesforspeak"></a>GET /GetLanguagesForSpeak

### <a name="implementation-notes"></a>实现说明
检索可用于语音合成的语言。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForSpeak`。

**返回值：** 包含翻译服务支持语音合成的语言代码的字符串数组。

### <a name="response-class-status-200"></a>响应类（状态 200）
包含翻译服务支持语音合成的语言代码的字符串数组。

字符串

响应内容类型：应用程序/xml

### <a name="parameters"></a>参数

|参数|“值”|描述|参数类型|数据类型|
|:--|:--|:--|:--|:--|
|appid|(empty)|必需。 如果使用`Authorization`或`Ocp-Apim-Subscription-Key`标头，请保留该`appid`字段为空。 否则，请包含包含 的`"Bearer" + " " + "access_token"`字符串。|query|字符串|
|授权|(empty)|如果字段和标头`appid`都留空`Ocp-Apim-Subscription-Key`，则为必填项。 授权令牌：`"Bearer" + " " + "access_token"`。|标头的值开始缓存响应|字符串|
|Ocp-Apim-Subscription-Key|(empty)|如果字段和标头`appid`都留空`Authorization`，则为必填项。|标头的值开始缓存响应|字符串|
 
### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400|请求错误。 请查看输入参数和详细的错误响应。|
|401|凭据无效。|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供请求的大致日期&时间以及响应标头`X-MS-Trans-Info`中包含的请求 ID。|
|503    |服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|

## <a name="get-speak"></a>GET /Speak

### <a name="implementation-notes"></a>实现说明
返回以所需语言说出的传入文本的 WAV 或 MP3 流。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/Speak`。

**返回值：** 传入文本的 WAV 或 MP3 流，以所需语言使用。

### <a name="response-class-status-200"></a>响应类（状态 200）

binary

响应内容类型：应用程序/xml

### <a name="parameters"></a>参数

|参数|“值”|描述|参数类型|数据类型|
|:--|:--|:--|:--|:--|
|appid|(empty)|必需。 如果使用`Authorization`或`Ocp-Apim-Subscription-Key`标头，请保留该`appid`字段为空。 否则，请包含包含 的`"Bearer" + " " + "access_token"`字符串。|query|字符串|
|text|(empty)   |必需。 包含一个或多个要以指定语言为流说话的句子的字符串。 文本不得超过 2，000 个字符。|query|字符串|
|语言|(empty)   |必需。 表示讲文本的语言的支持语言代码的字符串。 代码必须是 方法`GetLanguagesForSpeak`返回的代码之一。|query|字符串|
|format|(empty)|可选。 指定内容类型 ID 的字符串。 目前，`audio/wav` 和 `audio/mp3` 可用。 默认值为 `audio/wav`。|query|字符串|
|选项|(empty)    |可选。 指定合成语音属性的字符串：<ul><li>`MaxQuality`并`MinSize`指定音频信号的质量。 `MaxQuality`提供最高质量。 `MinSize`提供最小的文件大小。 默认值为`MinSize`。</li><li>`female`并`male`指定语音的所需性别。 默认为 `female`。 使用垂直条 （<code>\|</code>） 包含多个选项。 例如，`MaxQuality|Male`。</li></li></ul>  |query|字符串|
|授权|(empty)|如果字段和标头`appid`都留空`Ocp-Apim-Subscription-Key`，则为必填项。 授权令牌：`"Bearer" + " " + "access_token"`。|标头的值开始缓存响应|字符串|
|Ocp-Apim-Subscription-Key|(empty)  |如果字段和标头`appid`都留空`Authorization`，则为必填项。|标头的值开始缓存响应|字符串|

### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400    |请求错误。 请查看输入参数和详细的错误响应。|
|401    |凭据无效。|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供请求的大致日期&时间以及响应标头`X-MS-Trans-Info`中包含的请求 ID。|
|503    |服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|

## <a name="get-detect"></a>GET /Detect

### <a name="implementation-notes"></a>实现说明
标识文本部分的语言。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/Detect`。

**返回值：** 包含文本的双字符语言代码的字符串。

### <a name="response-class-status-200"></a>响应类（状态 200）

字符串

响应内容类型：应用程序/xml

### <a name="parameters"></a>参数

|参数|“值”|描述|参数类型|数据类型|
|:--|:--|:--|:--|:--|
|appid|(empty)  |必需。 如果使用`Authorization`或`Ocp-Apim-Subscription-Key`标头，请保留该`appid`字段为空。 否则，请包含包含 的`"Bearer" + " " + "access_token"`字符串。|query|字符串|
|text|(empty)|必需。 包含要标识其语言的文本的字符串。 文本不得超过 10，000 个字符。|query|  字符串|
|授权|(empty)|如果字段和标头`appid`都留空`Ocp-Apim-Subscription-Key`，则为必填项。 授权令牌：`"Bearer" + " " + "access_token"`。|标头的值开始缓存响应|字符串|
|Ocp-Apim-Subscription-Key  |(empty)    |如果字段和标头`appid`都留空`Authorization`，则为必填项。|标头的值开始缓存响应|字符串|

### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400|请求错误。 请查看输入参数和详细的错误响应。|
|401    |凭据无效。|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供请求的大致日期&时间以及响应标头`X-MS-Trans-Info`中包含的请求 ID。|
|503    |服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|


## <a name="post-detectarray"></a>POST /DetectArray

### <a name="implementation-notes"></a>实现说明

标识字符串数组中的语言。 独立检测每个数组元素的语言，并为数组的每一行返回结果。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/DetectArray`。

以下是请求正文的格式：

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>string-value-1</string>
    <string>string-value-2</string>
</ArrayOfstring>
```

文本不能超过 10，000 个字符。

**返回值：** 包含输入数组中每行的双字符语言代码的字符串数组。

以下是响应正文的格式：

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <string>language-code-1</string>
  <string>language-code-2</string>
</ArrayOfstring>
```

### <a name="response-class-status-200"></a>响应类（状态 200）
`DetectArray`是成功的。 返回一个字符串数组，该字符串数组包含输入数组每行的双字符语言代码。

字符串

响应内容类型：应用程序/xml
 
### <a name="parameters"></a>参数

|参数|“值”|描述|参数类型|数据类型|
|:--|:--|:--|:--|:--|
|appid|(empty)|必需。 如果使用`Authorization`或`Ocp-Apim-Subscription-Key`标头，请保留该`appid`字段为空。 否则，请包含包含 的`"Bearer" + " " + "access_token"`字符串。|query|字符串|
|授权|(empty)|如果字段和标头`appid`都留空`Ocp-Apim-Subscription-Key`，则为必填项。  授权令牌：`"Bearer" + " " + "access_token"`。|标头的值开始缓存响应|字符串|
|Ocp-Apim-Subscription-Key|(empty)|如果字段和标头`appid`都留空`Authorization`，则为必填项。|标头的值开始缓存响应|字符串|

### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400    |请求错误。 请查看输入参数和详细的错误响应。|
|401    |凭据无效。|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供请求的大致日期&时间以及响应标头`X-MS-Trans-Info`中包含的请求 ID。|
|503    |服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|

## <a name="get-addtranslation"></a>GET /AddTranslation

### <a name="implementation-notes"></a>实现说明

> [!IMPORTANT]
> **弃用注释：** 2018 年 1 月 31 日之后，此方法不会接受新的句子提交。 您会收到一条错误消息。 请参阅有关协作翻译框架 （CTF） 更改的公告。

向翻译记忆库添加翻译。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/AddTranslation`。

### <a name="response-class-status-200"></a>响应类（状态 200）

字符串

响应内容类型：应用程序：xml
 
### <a name="parameters"></a>参数

|参数|“值”|描述|参数类型|数据类型   |
|:--|:--|:--|:--|:--|
|appid|(empty)|必需。 如果使用`Authorization`或`Ocp-Apim-Subscription-Key`标头，请保留该`appid`字段为空。 否则，请包含包含 的`"Bearer" + " " + "access_token"`字符串。|query|字符串|
|originalText|(empty)|必需。 包含要翻译的文本的字符串。 字符串的最大长度为 1，000 个字符。|query|字符串|
|translatedText|(empty) |必需。 包含翻译成目标语言的文本的字符串。 字符串的最大长度为 2，000 个字符。|query|字符串|
|从|(empty)   |必需。 表示文本原始语言的语言代码的字符串。 例如，英语和德语的 en。|query|字符串|
|to|(empty)|必需。 表示要将文本转换为的语言代码的字符串。|query|字符串|
|rating|(empty) |可选。 表示字符串质量评级的整数。 该值介于 -10 和 10 之间。 默认值为 1。|query|integer|
|contentType|(empty)    |可选。 要翻译的文本的格式。 支持的格式为`text/plain`和`text/html`。 任何 HTML 元素都必须是格式良好的完整元素。    |query|字符串|
|category|(empty)|可选。 包含翻译的类别（域）的字符串。 默认为 `general`。|query|字符串|
|user|(empty)|必需。 用于跟踪提交创建者字符串。|query|字符串|
|uri|(empty)|可选。 包含翻译内容位置的字符串。|query|字符串|
|授权|(empty)|如果字段和标头`appid`都留空`Ocp-Apim-Subscription-Key`，则为必填项。  授权令牌：`"Bearer" + " " + "access_token"`。  |标头的值开始缓存响应|字符串|
|Ocp-Apim-Subscription-Key|(empty)|如果字段和标头`appid`都留空`Authorization`，则为必填项。|标头的值开始缓存响应|字符串|

### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400    |请求错误。 请查看输入参数和详细的错误响应。|
|401    |凭据无效。|
|410|`AddTranslation` 不再受支持。|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供请求的大致日期&时间以及响应标头`X-MS-Trans-Info`中包含的请求 ID。|
|503    |服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|

## <a name="post-addtranslationarray"></a>POST /AddTranslationArray

### <a name="implementation-notes"></a>实现说明

> [!IMPORTANT]
> **弃用注释：** 2018 年 1 月 31 日之后，此方法不会接受新的句子提交。 您会收到一条错误消息。 请参阅有关协作翻译框架 （CTF） 更改的公告。

向翻译记忆库添加一系列翻译。 此方法是 的`AddTranslation`数组版本。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/AddTranslationArray`。

以下是请求正文的格式：

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

这些元素在`AddtranslationsRequest`中。

* `AppId`：必需。 如果使用`Authorization`或`Ocp-Apim-Subscription-Key`标头，请保留该`AppId`字段为空。 否则，请包含包含 的`"Bearer" + " " + "access_token"`字符串。
* `From`：必需。 包含源语言的语言代码的字符串。 必须是 `GetLanguagesForTranslate` 方法返回的语言之一。
* `To`：必需。 包含目标语言的语言代码的字符串。 必须是 `GetLanguagesForTranslate` 方法返回的语言之一。
* `Translations`：必需。 要添加到翻译记忆库的一个翻译数组。 每个翻译必须包含`OriginalText``TranslatedText`和`Rating`。 每个`OriginalText`和最大大小`TranslatedText`为 1，000 个字符。 所有`OriginalText`和`TranslatedText`元素的总不能超过 10，000 个字符。 最大数组元素数为 100。
* `Options`：必需。 一组`Category`选项，包括 、`ContentType``Uri`和`User`。 需要 `User`。 `Category``ContentType`，`Uri`是可选的。 指定的元素必须按字母顺序列出。

### <a name="response-class-status-200"></a>响应类（状态 200）
`AddTranslationArray`方法成功。 

2018 年 1 月 31 日之后，将不接受提交句子。 此服务会以错误代码 410 进行响应。

字符串

响应内容类型：应用程序/xml
 
### <a name="parameters"></a>参数

|参数|“值”|描述|参数类型|数据类型|
|:--|:--|:--|:--|:--|
|授权|(empty)|如果字段和标头`appid`都留空`Ocp-Apim-Subscription-Key`，则为必填项。  授权令牌：`"Bearer" + " " + "access_token"`。|标头的值开始缓存响应|字符串|
|Ocp-Apim-Subscription-Key|(empty)|如果字段和标头`appid`都留空`Authorization`，则为必填项。|标头的值开始缓存响应|字符串|

### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400    |请求错误。 请查看输入参数和详细的错误响应。|
|401    |凭据无效。|
|410    |`AddTranslation` 不再受支持。|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供请求的大致日期&时间以及响应标头`X-MS-Trans-Info`中包含的请求 ID。|
|503|服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|

## <a name="get-breaksentences"></a>GET /BreakSentences

### <a name="implementation-notes"></a>实现说明
将文本部分分解为句子，并返回包含每个句子长度的数组。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/BreakSentences`。

**返回值：** 表示句子长度的整数数组。 数组的长度表示句子数。 这些值表示每个句子的长度。

### <a name="response-class-status-200"></a>响应类（状态 200）
表示句子长度的整数数组。 数组的长度表示句子数。 这些值表示每个句子的长度。

integer

响应内容类型：应用程序/xml

### <a name="parameters"></a>参数

|参数|“值”|描述|参数类型|数据类型|
|:--|:--|:--|:--|:--|
|appid|(empty)  |必需。 如果使用`Authorization`或`Ocp-Apim-Subscription-Key`标头，请保留该`appid`字段为空。 否则，请包含包含 的`"Bearer" + " " + "access_token"`字符串。|query| 字符串|
|text|(empty)   |必需。 表示要拆分为句子的文本的字符串。 文本的最大大小为 10，000 个字符。|query|字符串|
|语言   |(empty)    |必需。 表示输入文本的语言代码的字符串。|query|字符串|
|授权|(empty)|如果字段和标头`appid`都留空`Ocp-Apim-Subscription-Key`，则为必填项。 授权令牌：`"Bearer" + " " + "access_token"`。   |标头的值开始缓存响应|字符串|
|Ocp-Apim-Subscription-Key|(empty)|如果字段和标头`appid`都留空`Authorization`，则为必填项。|标头的值开始缓存响应|字符串|

### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400|请求错误。 请查看输入参数和详细的错误响应。|
|401|凭据无效。|
|500|服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供请求的大致日期&时间以及响应标头`X-MS-Trans-Info`中包含的请求 ID。|
|503|服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|

## <a name="post-gettranslations"></a>POST /GetTranslations

### <a name="implementation-notes"></a>实现说明
从存储和 MT 引擎中检索给定语言对的翻译数组。 `GetTranslations`不同之处在于`Translate`它返回所有可用的翻译。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/GetTranslations`。

请求的主体包括可选`TranslationOptions`对象，该对象具有以下格式：

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

该`TranslateOptions`对象包含以下列表中的值。 它们都是可选的，默认为最常见的设置。 指定的元素必须按字母顺序列出。

* `Category`：包含翻译的类别（域）的字符串。 默认为 `general`。
* `ContentType`： 唯一支持的选项，默认值为`text/plain`。
* `IncludeMultipleMTAlternatives`： 布尔标志，用于指定是否应从 MT 引擎返回多个替代项。 有效值为`true``false`和（区分大小写）。 默认值为`false`，它仅返回一个备选方案。 设置标志，以便`true`创建人工替代方案，与协作翻译框架 （CTF） 完全集成。 此功能通过从解码器的*n-* 最佳列表中添加人工替代项，为 CTF 中没有翻译的句子添加返回替代项。
    - 评级。 评级应用如下： 
         - 最佳自动翻译评级为 5。
       - CTF 的备选方案反映了审阅者的权威。 它们的范围从 -10 到 +10。
       - 自动生成的 *（n*-最佳） 转换替代项的评级为 0，匹配度为 100。
    - 备选方案数。 返回的备选方案数可以和 中`maxTranslations`指定的值一样高，但可以更低。
    - 语言对。 此功能不适用于简体中文和繁体中文之间的翻译。这两个方向。 它可用于 Microsoft 翻译支持的所有其他语言对。
* `State`：用户状态以帮助关联请求和响应。 相同的内容将在响应中返回。
* `Uri`：按此 URI 筛选结果。 如果未设置任何值，则默认值为`all`。
* `User`：按此用户筛选结果。 如果未设置任何值，则默认值为`all`。

请求 `Content-Type` 应为 `text/xml`。

**返回值：** 以下是响应的格式：

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

此响应包括包含`GetTranslationsResponse`以下值的元素：

* `Translations`： 找到的匹配项数组，存储在对象`TranslationMatch`中（如下节所述）。 翻译可能包括原始文本的细微变体（模糊匹配）。 翻译将被排序：100%匹配第一，模糊匹配下一个。
* `From`：如果方法未指定`From`语言，则此值将来自自动语言检测。 否则，它将是指定的`From`语言。
* `State`：用户状态以帮助关联请求和响应。 包含`TranslateOptions`参数中提供的值。

该`TranslationMatch`对象由以下值组成：

* `Error`：如果特定输入字符串发生错误，则错误代码。 否则，此字段为空。
* `MatchDegree`指示输入文本与存储中的原始文本的匹配程度。 系统将输入句子与存储内容进行匹配，包括不精确匹配。 返回的值范围为 0 到 100，其中 0 没有相似性，100 是一个精确、区分大小写的匹配。
* `MatchedOriginalText`：此结果的匹配的原始文本。 仅当匹配的原始文本与输入文本不同时，才会返回此值。 它用于返回模糊匹配的源文本。 不会为 Microsoft 翻译结果返回此值。
* `Rating`：表示进行质量决策的人员的权威性。 机器翻译结果的评分为 5。 匿名提供的翻译通常具有从 1 到 4 的评分。 权威提供的翻译通常有从 6 到 10 的评级。
* `Count`：此评分的这个翻译被选中的次数。 自动转换响应的值为 0。
* `TranslatedText`：翻译的文本。

### <a name="response-class-status-200"></a>响应类（状态 200）
以前`GetTranslationsResponse`描述的格式的对象。

字符串

响应内容类型：应用程序/xml
 
### <a name="parameters"></a>参数

|参数|“值”|描述|参数类型|数据类型|
|:--|:--|:--|:--|:--|
|appid|(empty)|必需。 如果使用`Authorization`或`Ocp-Apim-Subscription-Key`标头，请保留该`appid`字段为空。 否则，请包含包含 的`"Bearer" + " " + "access_token"`字符串。|query|字符串|
|text|(empty)|必需。 表示要翻译的文本的字符串。 文本的最大大小为 10，000 个字符。|query|字符串|
|从|(empty)|必需。 表示要翻译的文本的语言代码的字符串。|query|字符串|
|to |(empty)    |必需。 表示要将文本转换为的语言代码的字符串。|query|字符串|
|maxTranslations|(empty)|必需。 表示要返回的最大转换数的整数。|query|integer|
|授权| (empty)|如果字段和标头`appid`都留空`Ocp-Apim-Subscription-Key`，则为必填项。 授权令牌：`"Bearer" + " " + "access_token"`。|字符串|  标头的值开始缓存响应|
|Ocp-Apim-Subscription-Key|(empty)  |如果字段和标头`appid`都留空`Authorization`，则为必填项。|标头的值开始缓存响应|字符串|

### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400    |请求错误。 请查看输入参数和详细的错误响应。|
|401    |凭据无效。|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供请求的大致日期&时间以及响应标头`X-MS-Trans-Info`中包含的请求 ID。|
|503|服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|

## <a name="post-gettranslationsarray"></a>POST /GetTranslationsArray

### <a name="implementation-notes"></a>实现说明
检索多个源文本的多个翻译候选项。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/GetTranslationsArray`。

以下是请求正文的格式：

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

* `AppId`：必需。 如果使用标头`Authorization`，则将`AppId`该字段留空。 否则，请包含包含 的`"Bearer" + " " + "access_token"`字符串。
* `From`：必需。 表示要翻译的文本的语言代码的字符串。
* `MaxTranslations`：必需。 表示要返回的最大转换数的整数。
* `Options`：可选。 包含`Options`以下值的对象。 它们都是可选的，默认为最常见的设置。 指定的元素必须按字母顺序列出。
    - `Category`：包含翻译的类别（域）的字符串。 默认为 `general`。
    - `ContentType`： 唯一支持的选项，默认值为`text/plain`。
    - `IncludeMultipleMTAlternatives`： 布尔标志，用于指定是否应从 MT 引擎返回多个替代项。 有效值为`true``false`和（区分大小写）。 默认值为`false`，它仅返回一个备选方案。 设置标志，使`true`人工翻译替代方案与协作翻译框架 （CTF） 完全集成。 此功能通过从解码器的*n-* 最佳列表中添加人工替代项，从而支持返回 CTF 中没有替代的句子的替代方法。
        - 评分 评级应用如下：
          - 最佳自动翻译评级为 5。
          - CTF 的备选方案反映了审阅者的权威。 它们的范围从 -10 到 +10。
          - 自动生成的 *（n*-最佳） 转换替代项的评级为 0，匹配度为 100。
        - 备选方案数。 返回的备选方案数可以和 中`maxTranslations`指定的值一样高，但可以更低。
        - 语言对。 此功能不适用于简体中文和繁体中文之间的翻译。这两个方向。 它可用于 Microsoft 翻译支持的所有其他语言对。
* `State`：用户状态以帮助关联请求和响应。 相同的内容将在响应中返回。
* `Uri`：按此 URI 筛选结果。 如果未设置任何值，则默认值为`all`。
* `User`：按此用户筛选结果。 如果未设置任何值，则默认值为`all`。
* `Texts`：必需。 包含要翻译的文本的数组。 所有字符串必须使用同一语言。 要翻译的所有文本总数不能超过 10，000 个字符。 最大数组元素数为 10。
* `To`：必需。 表示要将文本转换为的语言代码的字符串。

您可以省略可选元素。 作为 直接`GetTranslationsArrayRequest`子级的元素必须按字母顺序列出。

请求 `Content-Type` 应为 `text/xml`。

**返回值：** 以下是响应的格式：

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

每个`GetTranslationsResponse`元素包含以下值：

* `Translations`： 找到的匹配项数组，存储在对象`TranslationMatch`中（如下节所述）。 翻译可能包括原始文本的细微变体（模糊匹配）。 翻译将被排序：100%匹配第一，模糊匹配下一个。
* `From`：如果方法未指定`From`语言，则此值将来自自动语言检测。 否则，它将是指定的`From`语言。
* `State`：用户状态以帮助关联请求和响应。 包含`TranslateOptions`参数中提供的值。

该`TranslationMatch`对象包含以下值：
* `Error`：如果特定输入字符串发生错误，则错误代码。 否则，此字段为空。
* `MatchDegree`指示输入文本与存储中的原始文本的匹配程度。 系统将输入句子与存储内容进行匹配，包括不精确匹配。 返回的值范围为 0 到 100，其中 0 没有相似性，100 是一个精确、区分大小写的匹配。
* `MatchedOriginalText`：此结果的匹配的原始文本。 仅当匹配的原始文本与输入文本不同时，才会返回此值。 它用于返回模糊匹配的源文本。 不会为 Microsoft 翻译结果返回此值。
* `Rating`：表示进行质量决策的人员的权威性。 机器翻译结果的评分为 5。 匿名提供的翻译通常具有从 1 到 4 的评分。 权威提供的翻译通常具有从 6 到 10 的评级。
* `Count`：此评分的这个翻译被选中的次数。 自动转换响应的值为 0。
* `TranslatedText`：翻译的文本。


### <a name="response-class-status-200"></a>响应类（状态 200）

字符串

响应内容类型：应用程序/xml
 
### <a name="parameters"></a>参数

|参数|“值”|描述|参数类型|数据类型|
|:--|:--|:--|:--|:--|
|授权  |(empty)    |如果字段和标头`appid`都留空`Ocp-Apim-Subscription-Key`，则为必填项。  授权令牌：`"Bearer" + " " + "access_token"`。|标头的值开始缓存响应|字符串|
|Ocp-Apim-Subscription-Key|(empty)  |如果字段和标头`appid`都留空`Authorization`，则为必填项。|标头的值开始缓存响应|字符串|

### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400    |请求错误。 请查看输入参数和详细的错误响应。|
|401    |凭据无效。|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供请求的大致日期和时间，以及包括在响应标头 `X-MS-Trans-Info` 中的请求 ID。|
|503    |服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [迁移到翻译文本 API v3](../migrate-to-v3.md)


