---
title: 文本翻译 API BreakSentence 方法
titlesuffix: Azure Cognitive Services
description: 使用文本翻译 API BreakSentence 方法。
services: cognitive-services
author: rajdeep-in
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: v-pawal
ms.openlocfilehash: 0ecb620757bc58b109747f69eb3b5afe0c56bac4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66387566"
---
# <a name="translator-text-api-30-breaksentence"></a>文本翻译 API 3.0：BreakSentence

标识文本段中的句子边界的位置。

## <a name="request-url"></a>请求 URL

将 `POST` 请求发送到：

```HTTP
https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0
```

## <a name="request-parameters"></a>请求参数

查询字符串上传递的请求参数如下：

<table width="100%">
  <th width="20%">查询参数</th>
  <th>说明</th>
  <tr>
    <td>api-version</td>
    <td>必需的查询参数  。<br/>客户端所请求的 API 的版本。 值必须是 `3.0`。</td>
  </tr>
  <tr>
    <td>language</td>
    <td>可选查询参数  。<br/>语言标记，标识输入文本的语言。 如果未指定代码，则将应用自动语言检测。</td>
  </tr>
  <tr>
    <td>script</td>
    <td>可选查询参数  。<br/>脚本标记，标识输入文本使用的脚本。 如果未指定脚本，则将采用语言的默认脚本。</td>
  </tr>
</table> 

请求标头包括：

<table width="100%">
  <th width="20%">标头</th>
  <th>描述</th>
  <tr>
    <td>身份验证标头</td>
    <td>必需的请求标头。<br/>请参阅<a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">用于身份验证的可用选项</a>。</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>必需的请求标头  。<br/>指定有效负载的内容类型。 可能的值为：`application/json`</td>
  </tr>
  <tr>
    <td>Content-Length</td>
    <td>必需的请求标头  。<br/>请求正文的长度。</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>可选  。<br/>客户端生成的 GUID，用于唯一标识请求。 请注意，如果在查询字符串中使用名为 `ClientTraceId` 的查询参数包括了跟踪 ID，则可以省略此标头。</td>
  </tr>
</table> 

## <a name="request-body"></a>请求正文

请求的正文是一个 JSON 数组。 每个数组元素都是一个 JSON 对象，具有名为 `Text` 的字符串属性。 句子边界是针对 `Text` 属性的值计算的。 具有一个文本段的示例请求正文如下所示：

```json
[
    { "Text": "How are you? I am fine. What did you do today?" }
]
```

以下限制适用：

* 数组最多可具有 100 个元素。
* 数组元素的文本值不能超过 10,000 个字符（包括空格）。
* 包括空格在内，请求中包含的整个文本不能超过 50,000 个字符。
* 如果指定了 `language` 查询参数，则所有数组元素必须采用同一语言。 否则，将分别向每个数组元素应用语言自动检测。

## <a name="response-body"></a>响应正文

成功的响应是一个 JSON 数组，其中的每个结果对应于输入数组中的一个字符串。 结果对象包括以下属性：

  * `sentLen`：一组整数，表示文本元素中各个句子的长度。 数组的长度是句子的数量，而各个值是每个句子的长度。 

  * `detectedLanguage`：一个对象，它通过以下属性描述检测到的语言：

     * `language`：已检测语言的代码。

     * `score`：一个浮点值，表示结果的置信度。 分数介于 0 和 1 之间，较低的分数表示较低的置信度。
     
    请注意，当请求了语言自动检测时，`detectedLanguage` 属性仅存在于结果对象中。

示例 JSON 响应如下：

```json
[
  {
    "sentenceLengths": [ 13, 11, 22 ]
    "detectedLanguage": {
      "language": "en",
      "score": 401
    },
  }
]
```

## <a name="response-headers"></a>响应标头

<table width="100%">
  <th width="20%">标头</th>
  <th>描述</th>
  <tr>
    <td>X-RequestId</td>
    <td>服务生成的用于标识请求的值。 它用于故障排除目的。</td>
  </tr>
</table> 

## <a name="response-status-codes"></a>响应状态代码

下面是请求可能返回的 HTTP 状态代码。 

<table width="100%">
  <th width="20%">状态代码</th>
  <th>描述</th>
  <tr>
    <td>200</td>
    <td>成功。</td>
  </tr>
  <tr>
    <td>400</td>
    <td>查询参数之一缺失或无效。 请更正请求参数，然后重试。</td>
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
    <td>429</td>
    <td>服务器拒绝了请求，因为客户端已超出请求限制。</td>
  </tr>
  <tr>
    <td>500</td>
    <td>发生了意外错误。 如果错误持续存在，请报告相关信息：发生故障的日期和时间、响应标头 `X-RequestId` 中的请求标识符、请求标头 `X-ClientTraceId` 中的客户端标识符。</td>
  </tr>
  <tr>
    <td>503</td>
    <td>服务器暂不可用。 重试请求。 如果错误持续存在，请报告相关信息：发生故障的日期和时间、响应标头 `X-RequestId` 中的请求标识符、请求标头 `X-ClientTraceId` 中的客户端标识符。</td>
  </tr>
</table> 

如果发生错误，请求也将返回 JSON 错误响应。 错误代码是一个 6 位数字，包括 3 位数的 HTTP 状态代码，后接用于进一步将错误分类的 3 位数。 常见错误代码可在 [v3 文本翻译 API 参考页面](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors)上找到。 

## <a name="examples"></a>示例

下面的示例展示了如何获取单个句子的句子边界。 服务会自动检测句子的语言。

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'How are you? I am fine. What did you do today?'}]"
```

---

