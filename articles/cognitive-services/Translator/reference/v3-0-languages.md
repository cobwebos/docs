---
title: 转换器语言方法
titleSuffix: Azure Cognitive Services
description: 语言方法获取当前由转换器的其他操作支持的语言集。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: swmachan
ms.openlocfilehash: 1f4dfc4b80aff01e4b7fe7ebae4850b28cd6a498
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588592"
---
# <a name="translator-30-languages"></a>翻译人员3.0：语言

获取转换器的其他操作当前支持的语言集。 

## <a name="request-url"></a>请求 URL

将 `GET` 请求发送到：
```HTTP
https://api.cognitive.microsofttranslator.com/languages?api-version=3.0
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
    <td>scope</td>
    <td> 可选参数。<br/>逗号分隔的名称列表，用于定义要返回的语言组。 允许的组名称为：`translation`、`transliteration` 和 `dictionary`。 如果未指定范围，则返回所有组，这相当于传递了 `scope=translation,transliteration,dictionary`。 若要确定哪个支持的语言集适合你的场景，请参阅[响应对象](#response-body)的说明。</td>
  </tr>
</table> 

请求标头为：

<table width="100%">
  <th width="20%">标头</th>
  <th>说明</th>
  <tr>
    <td>Accept-Language</td>
    <td>可选请求标头。<br/>可用于用户界面字符串的语言。 响应中的某些字段是语言的名称，或区域的名称。 使用此参数可以定义要以哪种语言返回这些名称。 通过提供格式正确的 BCP 47 语言标记来指定语言。 例如，使用值 `fr` 来请求法语名称，或使用值 `zh-Hant` 来请求繁体中文名称。<br/>如果未指定目标语言或者本地化不可用，则以英语提供名称。
    </td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>可选请求标头。<br/>客户端生成的 GUID，用于唯一标识请求。</td>
  </tr>
</table> 

无需身份验证即可获取语言资源。

## <a name="response-body"></a>响应正文

客户端使用 `scope` 查询参数来定义它关注的语言组。

* `scope=translation` 提供支持将文本从一种语言翻译成另一种语言的语言；

* `scope=transliteration` 提供将一种语言的文本从一个脚本转换为另一个脚本的功能；

* `scope=dictionary` 提供语言对，`Dictionary` 操作针对这些语言对返回数据。

客户端可以通过指定逗号分隔的名称列表同时检索多个组。 例如，`scope=translation,transliteration,dictionary` 将返回所有组支持的语言。

成功的响应是一个 JSON 对象，其中包含请求的每个组的一个属性：

```json
{
    "translation": {
        //... set of languages supported to translate text (scope=translation)
    },
    "transliteration": {
        //... set of languages supported to convert between scripts (scope=transliteration)
    },
    "dictionary": {
        //... set of languages supported for alternative translations and examples (scope=dictionary)
    }
}
```

每个属性的值如下。

* `translation` 属性

  `translation` 属性的值是 (键, 值) 对的字典。 每个键是一个 BCP 47 语言标记。 键标识文本的翻译目标语言或源语言。 与键关联的值是一个 JSON 对象，其中包含描述语言的属性：

  * `name`：通过 `Accept-Language` 标头请求的区域设置中语言的显示名称。

  * `nativeName`：此语言的当地区域设置中的语言的显示名称。

  * `dir`：方向性，`rtl` 表示语言从右向左，`ltr` 表示语言从左到右。

  示例如下：
          
  ```json
  {
    "translation": {
      ...
      "fr": {
        "name": "French",
        "nativeName": "Français",
        "dir": "ltr"
      },
      ...
    }
  }
  ```

* `transliteration` 属性

  `transliteration` 属性的值是 (键, 值) 对的字典。 每个键是一个 BCP 47 语言标记。 键标识某种语言，该语言的文本可以从一个脚本转换为另一个脚本。 与键关联的值是一个 JSON 对象，其中包含描述语言及其支持的脚本的属性：

  * `name`：通过 `Accept-Language` 标头请求的区域设置中语言的显示名称。

  * `nativeName`：此语言的当地区域设置中的语言的显示名称。

  * `scripts`：要转换的源脚本列表。 `scripts` 列表中的每个元素具有属性：

    * `code`：标识脚本的代码。

    * `name`：通过 `Accept-Language` 标头请求的区域设置中脚本的显示名称。

    * `nativeName`：语言的当地区域设置中的语言的显示名称。

    * `dir`：方向性，`rtl` 表示语言从右向左，`ltr` 表示语言从左到右。

    * `toScripts`：文本可转换到的目标脚本列表。 `toScripts` 列表的每个元素具有前面所述的属性 `code`、`name`、`nativeName` 和 `dir`。

  示例如下：

  ```json
  {
    "transliteration": {
      ...
      "ja": {
        "name": "Japanese",
        "nativeName": "日本語",
        "scripts": [
          {
            "code": "Jpan",
            "name": "Japanese",
            "nativeName": "日本語",
            "dir": "ltr",
            "toScripts": [
              {
                "code": "Latn",
                "name": "Latin",
                "nativeName": "ラテン語",
                "dir": "ltr"
              }
            ]
          },
          {
            "code": "Latn",
            "name": "Latin",
            "nativeName": "ラテン語",
            "dir": "ltr",
            "toScripts": [
              {
                "code": "Jpan",
                "name": "Japanese",
                "nativeName": "日本語",
                "dir": "ltr"
              }
            ]
          }
        ]
      },
      ...
    }
  }
  ```

* `dictionary` 属性

  `dictionary` 属性的值是 (键, 值) 对的字典。 每个键是一个 BCP 47 语言标记。 键标识支持替代翻译和回译的语言。 值是一个 JSON 对象，描述提供可用翻译的源语言和目标语言：

  * `name`：通过 `Accept-Language` 标头请求的区域设置中源语言的显示名称。

  * `nativeName`：此语言的当地区域设置中的语言的显示名称。

  * `dir`：方向性，`rtl` 表示语言从右向左，`ltr` 表示语言从左到右。

  * `translations`：提供替代翻译以及为源语言中表达的查询提供示例的语言列表。 `translations` 列表中的每个元素具有属性：

    * `name`：通过 `Accept-Language` 标头请求的区域设置中目标语言的显示名称。

    * `nativeName`：目标语言的当地区域设置中的目标语言的显示名称。

    * `dir`：方向性，`rtl` 表示语言从右向左，`ltr` 表示语言从左到右。
    
    * `code`：标识目标语言的语言代码。

  示例如下：

  ```json
  "es": {
    "name": "Spanish",
    "nativeName": "Español",
    "dir": "ltr",
    "translations": [
      {
        "name": "English",
        "nativeName": "English",
        "dir": "ltr",
        "code": "en"
      }
    ]
  },
  ```

在不更改 API 版本的情况下，响应对象的结构不会更改。 对于相同的 API 版本，可用语言的列表可能会不断变化，因为 Microsoft Translator 在不断扩展其服务支持的语言列表。

支持的语言列表不会频繁变化。 若要节省网络带宽并提高响应能力，客户端应用程序应考虑缓存语言资源和相应的实体标记 (`ETag`)。 然后，客户端应用程序可以定期（例如，每隔 24 小时）查询服务，以提取最新的受支持语言集。 在 `ETag` 标头字段中传递当前的 `If-None-Match` 值可让服务优化响应。 如果未修改资源，则服务将返回状态代码 304 和空响应正文。

## <a name="response-headers"></a>响应标头

<table width="100%">
  <th width="20%">标头</th>
  <th>说明</th>
  <tr>
    <td>ETag</td>
    <td>所请求的受支持语言组的实体标记的当前值。 若要提高后续请求的效率，客户端可在 `ETag` 标头字段中发送 `If-None-Match` 值。
    </td>
  </tr>
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
    <td>304</td>
    <td>自请求标头 `If-None-Match` 指定版本以来，资源尚未修改。</td>
  </tr>
  <tr>
    <td>400</td>
    <td>查询参数之一缺失或无效。 请更正请求参数，然后重试。</td>
  </tr>
  <tr>
    <td>429</td>
    <td>由于客户端已超出请求限制，服务器拒绝了请求。</td>
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

如果发生错误，请求也将返回 JSON 错误响应。 错误代码是一个 6 位数字，包括 3 位数的 HTTP 状态代码，后接用于进一步将错误分类的 3 位数。 可在[V3 转换器参考页](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors)上找到常见错误代码。 

## <a name="examples"></a>示例

以下示例演示如何检索文本翻译支持的语言。

```curl
curl "https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation"
```
