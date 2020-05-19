---
title: 转换器转换方法
titleSuffix: Azure Cognitive Services
description: 了解 Azure 认知服务转换器转换方法转换文本时的参数、标头和正文消息。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/17/2020
ms.author: swmachan
ms.openlocfilehash: 563f4693c358c570caa2566f58002ddfe6c7bc69
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83584631"
---
# <a name="translator-30-translate"></a>翻译人员3.0：翻译

翻译文本。

## <a name="request-url"></a>请求 URL

将 `POST` 请求发送到：

```HTTP
https://api.cognitive.microsofttranslator.com/translate?api-version=3.0
```

## <a name="request-parameters"></a>请求参数

查询字符串上传递的请求参数如下：

### <a name="required-parameters"></a>必需的参数

<table width="100%">
  <th width="20%">查询参数</th>
  <th>说明</th>
  <tr>
    <td>api-version</td>
    <td>必需参数。<br/>客户端所请求的 API 的版本。 值必须是 <code>3.0</code>。</td>
  </tr>
  <tr>
    <td>to</td>
    <td>必需参数。<br/>指定输出文本的语言。 目标语言必须是 <a href="./v3-0-languages.md"> 范围中包含的</a>支持的语言<code>translation</code>之一。 例如，若要翻译为德语，请使用 <code>to=de</code>。<br/>可以在查询字符串中重复使用此参数，这样就可以同时翻译为多种语言。 例如，若要翻译为德语和意大利语，请使用 <code>to=de&to=it</code>。</td>
  </tr>
</table>

### <a name="optional-parameters"></a>可选参数

<table width="100%">
  <th width="20%">查询参数</th>
  <th>说明</th>
  <tr>
    <td>从</td>
    <td>可选参数。<br/>指定输入文本的语言。 可以使用 <a href="./v3-0-languages.md"> 范围来查找</a>支持的语言<code>translation</code>，了解哪些语言可以翻译。 如果未指定 <code>from</code> 参数，则会应用自动语言检测来确定源语言。 <br/><br/>使用<code>from</code>动态字典<a href="https://docs.microsoft.com/azure/cognitive-services/translator/dynamic-dictionary">功能时，必须使用 </a> 参数而不是自动检测。</td>
  </tr>  
  <tr>
    <td>textType</td>
    <td>可选参数。<br/>定义要翻译的文本是纯文本还是 HTML 文本。 HTML 必须是格式正确的完整元素。 可能的值为 <code>plain</code>（默认）<code>html</code>。</td>
  </tr>
  <tr>
    <td>category</td>
    <td>可选参数。<br/>一个字符串，指定翻译的类别（领域）。 此参数用于从一个使用<a href="../customization.md">自定义翻译工具</a>构建的自定义系统获取翻译。 将自定义转换器<a href="https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/how-to-create-project#view-project-details">项目详细信息</a>中的类别 ID 添加到此参数，以使用已部署的自定义系统。 默认值为 <code>general</code>。</td>
  </tr>
  <tr>
    <td>profanityAction</td>
    <td><em>可选参数</em>。<br/>指定在翻译时应如何处理不雅内容。 可能的值为 <code>NoAction</code>（默认）<code>Marked</code> 或 <code>Deleted</code>。 若要了解处理不雅内容的方式，请参阅<a href="#handle-profanity">处理不雅内容</a>。</td>
  </tr>
  <tr>
    <td>profanityMarker</td>
    <td><em>可选参数</em>。<br/>指定在翻译时应如何标记不雅内容。 可能的值为 <code>Asterisk</code>（默认）<code>Tag</code>。 若要了解处理不雅内容的方式，请参阅<a href="#handle-profanity">处理不雅内容</a>。</td>
  </tr>
  <tr>
    <td>includeAlignment</td>
    <td><em>可选参数</em>。<br/>指定是否包括从源文本到翻译文本的比对投射。 可能的值为 <code>true</code> 或 <code>false</code>（默认）。 </td>
  </tr>
  <tr>
    <td>includeSentenceLength</td>
    <td><em>可选参数</em>。<br/>指定是否包括输入文本和翻译文本的句子边界。 可能的值为 <code>true</code> 或 <code>false</code>（默认）。</td>
  </tr>
  <tr>
    <td>suggestedFrom</td>
    <td><em>可选参数</em>。<br/>在输入文本的语言无法确定的情况下，指定一种回退语言。 在省略 <code>from</code> 参数的情况下，应用语言自动检测功能。 如果检测失败，则采用 <code>suggestedFrom</code> 语言。</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td><em>可选参数</em>。<br/>指定输入文本的脚本。</td>
  </tr>
  <tr>
    <td>toScript</td>
    <td><em>可选参数</em>。<br/>指定翻译文本的脚本。</td>
  </tr>
  <tr>
    <td>allowFallback</td>
    <td><em>可选参数</em>。<br/>指定当自定义系统不存在时允许服务回退到一个常规系统。 可能的值为 <code>true</code>（默认）<code>false</code>。<br/><br/><code>allowFallback=false</code> 指定翻译应仅使用针对由此请求指定的 <code>category</code> 而训练的系统。 如果将语言 X 翻译成语言 Y 需要通过枢轴语言 E 进行链接，那么此链中的所有系统（X->E 和 E->Y）将需要进行自定义并且具有相同的类别。 如果未通过特定类别找到任何系统，此请求将返回 400 状态代码。 <code>allowFallback=true</code> 指定当自定义系统不存在时允许服务回退到一个常规系统。
</td>
  </tr>
</table> 

请求标头包括：

<table width="100%">
  <th width="20%">标头</th>
  <th>说明</th>
  <tr>
    <td>身份验证标头</td>
    <td><em>必需的请求标头</em>。<br/>请参阅<a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">用于身份验证的可用选项</a>。</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td><em>必需的请求标头</em>。<br/>指定有效负载的内容类型。<br/> 接受的值为 <code>application/json; charset=UTF-8</code> 。</td>
  </tr>
  <tr>
    <td>Content-Length</td>
    <td><em>必需的请求标头</em>。<br/>请求正文的长度。</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td><em>可选</em>。<br/>客户端生成的 GUID，用于唯一标识请求。 如果在查询字符串中使用名为 <code>ClientTraceId</code> 的查询参数包括了跟踪 ID，则可以省略此标头。</td>
  </tr>
</table> 

## <a name="request-body"></a>请求正文

请求的正文是一个 JSON 数组。 每个数组元素都是一个 JSON 对象，具有一个名为 `Text` 的字符串属性，该属性表示要翻译的字符串。

```json
[
    {"Text":"I would really like to drive your car around the block a few times."}
]
```

以下限制适用：

* 数组最多可具有 100 个元素。
* 包括空格在内，请求中包含的整个文本不能超过 5,000 个字符。

## <a name="response-body"></a>响应正文

成功的响应是一个 JSON 数组，其中的每个结果对应于输入数组中的一个字符串。 结果对象包括以下属性：

  * `detectedLanguage`：一个对象，它通过以下属性描述检测到的语言：

      * `language`：一个字符串，表示检测到的语言的代码。

      * `score`：一个浮点值，表示结果的置信度。 分数介于 0 和 1 之间，较低的分数表示较低的置信度。

    当请求了语言自动检测时，`detectedLanguage` 属性仅存在于结果对象中。

  * `translations`：翻译结果的数组。 数组的大小与通过 `to` 查询参数指定的目标语言的数目匹配。 数组中的每个元素包括：

    * `to`：一个字符串，表示目标语言的语言代码。

    * `text`：一个字符串，提供翻译的文本。

    * `transliteration`：一个对象，在 `toScript` 参数指定的脚本中提供翻译的文本。

      * `script`：一个字符串，指定目标脚本。   

      * `text`：一个字符串，在目标脚本中提供翻译的文本。

    如果不进行音译，则不包括 `transliteration` 对象。

    * `alignment`：一个对象，包含的名为 `proj` 的单个字符串属性可以将输入文本映射到翻译文本。 只有在请求参数 `includeAlignment` 为 `true` 时，才提供比对信息。 将以 `[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]]` 格式的字符串值返回比对内容。  冒号分隔开始和结束索引，连字符分隔语言，空格分隔单词。 一个单词可能与另一种语言中的 0 个、1 个或多个单词比对，而比对的词可能是不连续的。 当没有可用的比对信息时，Alignment 元素会为空。 请参阅[获取比对信息](#obtain-alignment-information)，了解示例和限制。

    * `sentLen`：一个对象，返回输入和输出文本中的句子边界。

      * `srcSentLen`：一个整数数组，表示输入文本中句子的长度。 数组的长度是句子的数量，而各个值是每个句子的长度。

      * `transSentLen`：一个整数数组，表示翻译文本中句子的长度。 数组的长度是句子的数量，而各个值是每个句子的长度。

    只有在请求参数 `includeSentenceLength` 为 `true` 时，才包括句子边界。

  * `sourceText`：一个对象，包含的名为 `text` 的单个字符串属性在源语言的默认脚本中提供输入文本。 `sourceText` 属性存在的前提是，表述输入时采用的脚本不是该语言的通用脚本。 例如，如果输入是采用拉丁语脚本编写的阿拉伯语，则 `sourceText.text` 就是转换为阿拉伯脚本的同一阿拉伯文本。

[示例](#examples)部分提供了 JSON 响应的示例。

## <a name="response-headers"></a>响应头

<table width="100%">
  <th width="20%">标头</th>
  <th>说明</th>
    <tr>
    <td>X-RequestId</td>
    <td>服务生成的用于标识请求的值。 它用于故障排除目的。</td>
  </tr>
  <tr>
    <td>X-MT-System</td>
    <td>指定用于将每种语言翻译“到”所请求翻译语言的系统类型。 此值是以逗号分隔的字符串列表。 每个字符串指示一个类型：<br/><ul><li>自定义 - 请求包括一个自定义系统，并且在翻译期间至少使用了一个自定义系统。</li><li>团队 - 所有其他要求</li></td>
  </tr>
</table> 

## <a name="response-status-codes"></a>响应状态代码

下面是请求可能返回的 HTTP 状态代码。 

<table width="100%">
  <th width="20%">状态代码</th>
  <th>说明</th>
  <tr>
    <td>200</td>
    <td>成功。</td>
  </tr>
  <tr>
    <td>400</td>
    <td>其中一个查询参数丢失或无效。 请更正请求参数，然后重试。</td>
  </tr>
  <tr>
    <td>401</td>
    <td>无法对请求进行身份验证。 请确保凭据已指定且有效。</td>
  </tr>
  <tr>
    <td>403</td>
    <td>请求未经授权。 请检查详细错误消息。 这通常表示试用订阅提供的所有可用翻译已用完。</td>
  </tr>
  <tr>
    <td>408</td>
    <td>无法满足请求，因为缺少资源。 请检查详细错误消息。 使用自定义 <code>category</code> 时，这通常指示自定义翻译系统尚不可用于为请求提供服务。 应在等待一段时间（例如 1 分钟）后重试此请求。</td>
  </tr>
  <tr>
    <td>429</td>
    <td>由于客户端已超出请求限制，服务器拒绝了请求。</td>
  </tr>
  <tr>
    <td>500</td>
    <td>发生了意外错误。 如果错误持续存在，请报告相关信息：发生故障的日期和时间、响应标头 <code>X-RequestId</code> 中的请求标识符、请求标头 <code>X-ClientTraceId</code> 中的客户端标识符。</td>
  </tr>
  <tr>
    <td>503</td>
    <td>服务器暂不可用。 重试请求。 如果错误持续存在，请报告相关信息：发生故障的日期和时间、响应标头 <code>X-RequestId</code> 中的请求标识符、请求标头 <code>X-ClientTraceId</code> 中的客户端标识符。</td>
  </tr>
</table> 

如果发生错误，请求也将返回 JSON 错误响应。 错误代码是一个 6 位数字，包括 3 位数的 HTTP 状态代码，后接用于进一步将错误分类的 3 位数。 可在[V3 转换器参考页](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors)上找到常见错误代码。 

## <a name="examples"></a>示例

### <a name="translate-a-single-input"></a>翻译单个输入

以下示例演示了如何将单个句子从英文翻译为简体中文。

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

响应正文为：

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    }
]
```

`translations` 数组包括一个元素，该元素提供输入中一段文本的翻译。

### <a name="translate-a-single-input-with-language-auto-detection"></a>使用语言自动检测功能翻译单个输入

以下示例演示了如何将单个句子从英文翻译为简体中文。 请求未指定输入语言， 而是使用自动检测源语言的功能。

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

响应正文为：

```
[
    {
        "detectedLanguage": {"language": "en", "score": 1.0},
        "translations":[
            {"text": "你好, 你叫什么名字？", "to": "zh-Hans"}
        ]
    }
]
```
响应类似于前一示例中的响应。 由于请求了语言自动检测，因此响应还包括针对输入文本检测到的语言的信息。 

### <a name="translate-with-transliteration"></a>通过音译进行翻译

让我们添加音译，对上一示例进行扩展。 以下请求要求提供以拉丁字母拼写的中文翻译。

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans&toScript=Latn" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

响应正文为：

```
[
    {
        "detectedLanguage":{"language":"en","score":1.0},
        "translations":[
            {
                "text":"你好, 你叫什么名字？",
                "transliteration":{"script":"Latn", "text":"nǐ hǎo , nǐ jiào shén me míng zì ？"},
                "to":"zh-Hans"
            }
        ]
    }
]
```

翻译结果现在包括一个 `transliteration` 属性，该属性提供使用拉丁字符的翻译文本。

### <a name="translate-multiple-pieces-of-text"></a>翻译多行文本

一次翻译多个字符串时，只需在请求正文中指定一个字符串数组即可。

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}, {'Text':'I am fine, thank you.'}]"
```

响应正文为：

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    },            
    {
        "translations":[
            {"text":"我很好，谢谢你。","to":"zh-Hans"}
        ]
    }
]
```

### <a name="translate-to-multiple-languages"></a>翻译为多种语言

以下示例演示如何在一个请求中将同一输入翻译为多种语言。

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

响应正文为：

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"},
            {"text":"Hallo, was ist dein Name?","to":"de"}
        ]
    }
]
```

### <a name="handle-profanity"></a>处理不雅内容

通常，翻译工具服务在翻译中会保留源中存在的不雅内容。 不雅程度和使词语不雅的语境在不同的文化之间有所不同，因此，目标语言中的不雅程度可能会放大或缩小。

如果无论源文本中是否存在不雅内容，你都希望避免在翻译中出现不雅内容，可以使用不雅内容筛选选项。 使用该选项可以选择是要看到不雅内容被删除、标有相应标记（允许你添加自己的后处理操作）还是不被执行任何操作。 就 `ProfanityAction` 来说，接受的值为 `Deleted`、`Marked`、`NoAction`（默认）。

<table width="100%">
  <th width="20%">ProfanityAction</th>
  <th>操作</th>
  <tr>
    <td><code>NoAction</code></td>
    <td>此选项为默认行为。 不雅内容会从源传递到目标。<br/><br/>
    <strong>示例源（日语）</strong>：彼はジャッカスです。<br/>
    <strong>示例翻译（中文）</strong>：他是一个笨蛋。
    </td>
  </tr>
  <tr>
    <td><code>Deleted</code></td>
    <td>不雅词语会从输出中删除，不进行替换。<br/><br/>
    <strong>示例源（日语）</strong>：彼はジャッカスです。<br/>
    <strong>示例翻译（中文）</strong>：他是一个。
    </td>
  </tr>
  <tr>
    <td><code>Marked</code></td>
    <td>不雅词语会在输出中使用标记进行替换。 标记取决于 <code>ProfanityMarker</code> 参数。<br/><br/>
如果 <code>ProfanityMarker=Asterisk</code>，不雅词语会被替换为 <code>***</code>：<br/>
    <strong>示例源（日语）</strong>：彼はジャッカスです。<br/>
    <strong>示例翻译（英语）</strong>：他是 \* \* \* 。<br/><br/>
如果 <code>ProfanityMarker=Tag</code>，则不雅词语会被括在 XML 标记 &lt;profanity&gt; 和 &lt;/profanity&gt; 中：<br/>
    <strong>示例源（日语）</strong>：彼はジャッカスです。<br/>
    <strong>示例翻译（中文）</strong>：他是一个&lt;profanity&gt;笨蛋&lt;/profanity&gt;。
  </tr>
</table> 

例如：

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'This is a freaking good idea.'}]"
```
这样会返回：

```
[
    {
        "translations":[
            {"text":"Das ist eine *** gute Idee.","to":"de"}
        ]
    }
]
```

与以下示例比较：

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked&profanityMarker=Tag" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'This is a freaking good idea.'}]"
```

上一个请求返回：

```
[
    {
        "translations":[
            {"text":"Das ist eine <profanity>verdammt</profanity> gute Idee.","to":"de"}
        ]
    }
]
```

### <a name="translate-content-with-markup-and-decide-whats-translated"></a>翻译带标记的内容并确定翻译的内容

通常需要翻译包含标记的内容，例如 HTML 页中的内容或 XML 文档中的内容。 在翻译包含标记的内容时包括查询参数 `textType=html`。 另外，有时候需从翻译中排除特定的内容。 可以使用属性 `class=notranslate` 指定应保留不译的内容。 在以下示例中，第一个 `div` 元素中的内容不会翻译，第二个 `div` 元素中的内容则会翻译。

```
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

下面是用于演示的示例请求。

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&textType=html" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'<div class=\"notranslate\">This will not be translated.</div><div>This will be translated.</div>'}]"
```

响应为：

```
[
    {
        "translations":[
            {"text":"<div class=\"notranslate\">This will not be translated.</div><div>这将被翻译。</div>","to":"zh-Hans"}
        ]
    }
]
```

### <a name="obtain-alignment-information"></a>获取比对信息

对齐将作为以下格式的字符串值返回给源的每个词。 每个词的信息由一个空格分隔，其中包括非空格分隔的语言（脚本），比如中文：

[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]] *

对齐字符串示例：“0:0-7:10 1:2-11:20 3:4-0:3 3:4-4:6 5:5-21:21”。

换而言之，冒号分隔开始和结束索引，连字符分隔语言，空格分隔词。 一个单词可能与另一种语言中的 0 个、1 个或多个单词比对，而比对的词可能是不连续的。 当没有可用的对齐信息时，Alignment 元素将为空。 在这种情况下，该方法不会返回任何错误。

若要接收比对信息，请在查询字符串中指定 `includeAlignment=true`。

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeAlignment=true" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'The answer lies in machine translation.'}]"
```

响应为：

```
[
    {
        "translations":[
            {
                "text":"La réponse se trouve dans la traduction automatique.",
                "to":"fr",
                "alignment":{"proj":"0:2-0:1 4:9-3:9 11:14-11:19 16:17-21:24 19:25-40:50 27:37-29:38 38:38-51:51"}
            }
        ]
    }
]
```

比对信息以 `0:2-0:1` 开头，这意味着源文本中的头三个字符 (`The`) 映射到翻译文本中的头两个字符 (`La`)。

#### <a name="limitations"></a>限制
获取对齐信息是一项实验性功能，我们已启用此功能，以使用可能的短语映射来创建研究和体验。 我们可能会选择在将来停止支持。 下面是不支持对齐的一些值得注意的限制：

* 对齐方式不适用于 HTML 格式的文本，即 textType = html
* 仅针对一部分语言对返回比对内容：
  - 英语与除繁体中文、粤语（传统）或塞尔维亚语（西里尔文）以外的任何其他语言。
  - 从日语到韩语或从韩语到日语。
  - 从日语到简体中文以及简体中文到日语。 
  - 简体中文和繁体中文之间的简体中文和繁体中文。 
* 如果句子是预录翻译，则不会收到比对内容。 预录翻译示例有“This is a test”、“I love you”，以及其他高频句子。
* 当应用任何方法来阻止[此处](../prevent-translation.md)所述的转换时，对齐功能不可用

### <a name="obtain-sentence-boundaries"></a>获取句子边界

若要接收源文本和翻译文本中句子长度的相关信息，请在查询字符串中指定 `includeSentenceLength=true`。

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeSentenceLength=true" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'The answer lies in machine translation. The best machine translation technology cannot always provide translations tailored to a site or users like a human. Simply copy and paste a code snippet anywhere.'}]"
```

响应为：

```
[
    {
        "translations":[
            {
                "text":"La réponse se trouve dans la traduction automatique. La meilleure technologie de traduction automatique ne peut pas toujours fournir des traductions adaptées à un site ou des utilisateurs comme un être humain. Il suffit de copier et coller un extrait de code n’importe où.",
                "to":"fr",
                "sentLen":{"srcSentLen":[40,117,46],"transSentLen":[53,157,62]}
            }
        ]
    }
]
```

### <a name="translate-with-dynamic-dictionary"></a>使用动态词典进行翻译

若已知道要应用于某个单词或短语的翻译，可以在请求中将其作为标记提供。 动态字典仅适用于正确名词，如个人姓名和产品名称。

要提供的标记使用以下语法。

``` 
<mstrans:dictionary translation="translation of phrase">phrase</mstrans:dictionary>
```

例如，考虑英语句子“The word wordomatic is a dictionary entry.”。 若要在翻译中保留单词 _wordomatic_，请发送请求：

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.'}]"
```

结果为：

```
[
    {
        "translations":[
            {"text":"Das Wort \"wordomatic\" ist ein Wörterbucheintrag.","to":"de"}
        ]
    }
]
```

不管使用 `textType=text` 还是使用 `textType=html`，此功能都以相同的方式工作。 应尽量少使用此功能。 对翻译进行自定义时，一个合适且要好得多的方法是使用自定义翻译工具。 自定义翻译工具能够充分利用上下文和统计概率。 如果你必须或者有能力创建在上下文中显示你的工作或短语的训练数据，则会得到好得多的结果。 [详细了解自定义翻译工具](../customization.md)。
