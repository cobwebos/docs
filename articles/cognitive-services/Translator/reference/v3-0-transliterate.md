---
title: 文本翻译 API Transliterate 方法
titlesuffix: Azure Cognitive Services
description: 使用文本翻译 API Transliterate 方法。
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: a37b069d533dc51067aba4f9d9bd87608b116ae6
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2019
ms.locfileid: "55466449"
---
# <a name="translator-text-api-30-transliterate"></a>文本翻译 API 3.0：Transliterate

将一种语言的文本从一个脚本转换为另一个脚本。

## <a name="request-url"></a>请求 URL

将 `POST` 请求发送到：

```HTTP
https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0
```

## <a name="request-parameters"></a>请求参数

查询字符串上传递的请求参数如下：

<table width="100%">
  <th width="20%">查询参数</th>
  <th>说明</th>
  <tr>
    <td>api-version</td>
    <td>必需参数。<br/>客户端所请求的 API 的版本。 值必须是 `3.0`。</td>
  </tr>
  <tr>
    <td>语言</td>
    <td>必需参数。<br/>指定要从一个脚本转换为另一个脚本的文本的语言。 通过查询服务的[支持的语言](./v3-0-languages.md)获得的 `transliteration` 范围中列出了可能的语言。</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td>*必需参数*。<br/>指定输入文本所使用的脚本。 使用 `transliteration` 范围查找[支持的语言](./v3-0-languages.md)，以找到可用于选定语言的输入脚本。</td>
  </tr>
  <tr>
    <td>toScript</td>
    <td>*必需参数*。<br/>指定输出脚本。 使用 `transliteration` 范围查找[支持的语言](./v3-0-languages.md)，以找到可用于选定输入语言和输入脚本组合的输出脚本。</td>
  </tr>
</table> 

请求标头包括：

<table width="100%">
  <th width="20%">标头</th>
  <th>说明</th>
  <tr>
    <td>一个授权<br/>标头</td>
    <td>必需的请求标头。<br/>请参阅[用于身份验证的可用选项](./v3-0-reference.md#authentication)。</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>必需的请求标头。<br/>指定有效负载的内容类型。 可能的值为：`application/json`</td>
  </tr>
  <tr>
    <td>Content-Length</td>
    <td>必需的请求标头。<br/>请求正文的长度。</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>可选。<br/>客户端生成的 GUID，用于唯一标识请求。 请注意，如果在查询字符串中使用名为 `ClientTraceId` 的查询参数包括了跟踪 ID，则可以省略此标头。</td>
  </tr>
</table> 

## <a name="request-body"></a>请求正文

请求的正文是一个 JSON 数组。 每个数组元素都是一个 JSON 对象，具有一个名为 `Text` 的字符串属性，该属性表示要转换的字符串。

```json
[
    {"Text":"こんにちは"},
    {"Text":"さようなら"}
]
```

以下限制适用：

* 该数组最多可具有 10 个元素。
* 数组元素的文本值不能超过 1,000 个字符（包括空格）。
* 包括空格在内，请求中包含的整个文本不能超过 5,000 个字符。

## <a name="response-body"></a>响应正文

成功的响应是一个 JSON 数组，其中的每个结果对应于输入数组中的一个元素。 结果对象包括以下属性：

  * `text`：一个字符串，它是将输入字符串转换为输出脚本的结果。
  
  * `script`：一个字符串，指定输出中使用的脚本。

示例 JSON 响应如下：

```json
[
    {"text":"konnnichiha","script":"Latn"},
    {"text":"sayounara","script":"Latn"}
]
```

## <a name="response-headers"></a>响应标头

<table width="100%">
  <th width="20%">标头</th>
  <th>说明</th>
  <tr>
    <td>X-RequestId</td>
    <td>服务生成的用于标识请求的值。 它用于故障排除目的。</td>
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
    <td>调用方发送的请求过多。</td>
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

## <a name="examples"></a>示例

以下示例显示如何将两个日语字符串转换为用罗马字母拼写的日语。

# <a name="curltabcurl"></a>[curl](#tab/curl)

此示例中请求的 JSON 有效负载：

```
[{"text":"こんにちは","script":"jpan"},{"text":"さようなら","script":"jpan"}]
```

若要在不支持 Unicode 字符的命令行窗口中使用 cURL，请获取以下 JSON 有效负载，并将它保存到 `request.txt` 文件中。 请务必使用 `UTF-8` 编码保存文件。

```
curl -X POST "https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0&language=ja&fromScript=Jpan&toScript=Latn" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d @request.txt
```

---
