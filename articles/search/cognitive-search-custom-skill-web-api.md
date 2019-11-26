---
title: Custom Web API skill in skillsets
titleSuffix: Azure Cognitive Search
description: Extend capabilities of Azure Cognitive Search skillsets by calling out to Web APIs. Use the Custom Web API skill to integrate your custom code.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 29928d78c2cfc2f21def363341f8383c4efa89d2
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484122"
---
# <a name="custom-web-api-skill-in-an-azure-cognitive-search-enrichment-pipeline"></a>Custom Web API skill in an Azure Cognitive Search enrichment pipeline

The **Custom Web API** skill allows you to extend AI enrichment by calling out to a Web API endpoint providing custom operations. 与内置技能类似，“自定义 Web API”技能也有输入和输出。 Depending on the inputs, your Web API receives a JSON payload when the indexer runs, and outputs a JSON payload as a response, along with a success status code. 响应应包含自定义技能指定的输出。 其他任何响应都被视为错误，并且不会执行任何扩充。

本文档进一步详细介绍了 JSON 有效负载的结构。

> [!NOTE]
> 索引器会对 Web API 返回的某些标准 HTTP 状态代码重试两次。 这些 HTTP 状态代码为： 
> * `502 Bad Gateway`
> * `503 Service Unavailable`
> * `429 Too Many Requests`

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Custom.WebApiSkill

## <a name="skill-parameters"></a>技能参数

参数区分大小写。

| 参数名称     | 描述 |
|--------------------|-------------|
| uri | The URI of the Web API to which the _JSON_ payload will be sent. 只允许使用 https URI 方案 |
| httpMethod | 发送有效负载时使用的方法。 允许使用的方法为 `PUT` 或 `POST` |
| httpHeaders | 键值对集合，其中键表示头名称，值表示发送到 Web API 的头值以及有效负载。 此集合中禁止使用以下头：`Accept`、`Accept-Charset`、`Accept-Encoding`、`Content-Length`、`Content-Type`、`Cookie`、`Host`、`TE`、`Upgrade`、`Via` |
| timeout | （可选）如果指定，表明执行 API 调用的 http 客户端的超时值。 必须将其格式化为 XSD“dayTimeDuration”值（[ISO 8601 持续时间](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration)值的受限子集）。 例如，`PT60S` 表示 60 秒。 如果未设置，选择的是默认值 30 秒。 The timeout can be set to a maximum of 230 seconds and a minimum of 1 second. |
| batchSize | （可选）表示每 API 调用发送多少个“数据记录”（请参阅下面的 JSON 有效负载结构）。 如果未设置，选择的是默认值 1000。 建议使用此参数在索引编制吞吐量和 API 负载之间进行适当取舍 |
| degreeOfParallelism | (Optional) When specified, indicates the number of calls the indexer will make in parallel to the endpoint you have provided. You can decrease this value if your endpoint is failing under too high of a request load, or raise it if your endpoint is able to accept more requests and you would like an increase in the performance of the indexer.  If not set, a default value of 5 is used. The degreeOfParallelism can be set to a maximum of 10 and a minimum of 1. |

## <a name="skill-inputs"></a>技能输入

此技能没有“预定义”输入。 可以选择在执行此技能时已提供的一个或多个字段作为输入，发送到 Web API 的 JSON 有效负载将会有不同的字段。

## <a name="skill-outputs"></a>技能输出

此技能没有“预定义”输出。 根据 Web API 将返回的响应，添加输出字段，以便能够从 JSON 响应中选择这些字段。


## <a name="sample-definition"></a>示例定义

```json
  {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "A custom skill that can identify positions of different phrases in the source text",
        "uri": "https://contoso.count-things.com",
        "batchSize": 4,
        "context": "/document",
        "inputs": [
          {
            "name": "text",
            "source": "/document/content"
          },
          {
            "name": "language",
            "source": "/document/languageCode"
          },
          {
            "name": "phraseList",
            "source": "/document/keyphrases"
          }
        ],
        "outputs": [
          {
            "name": "hitPositions"
          }
        ]
      }
```
## <a name="sample-input-json-structure"></a>示例输入 JSON 结构

此 JSON 结构表示将发送到 Web API 的有效负载。
它始终遵循以下约束：

* 顶级实体名为 `values`，并且是对象数组。 此类对象的数量最多为 `batchSize`
* `values` 数组中的每个对象都有
    * `recordId` 属性，用于标识相应记录的唯一字符串。
    * `data` 属性，它是 JSON 对象。 `data` 属性的字段对应于技能定义的 `inputs` 部分中指定的“名称”。 这些字段的值将来自这些字段的 `source`（可能来自文档中的字段，也可能来自另一个技能）

```json
{
    "values": [
      {
        "recordId": "0",
        "data":
           {
             "text": "Este es un contrato en Inglés",
             "language": "es",
             "phraseList": ["Este", "Inglés"]
           }
      },
      {
        "recordId": "1",
        "data":
           {
             "text": "Hello world",
             "language": "en",
             "phraseList": ["Hi"]
           }
      },
      {
        "recordId": "2",
        "data":
           {
             "text": "Hello world, Hi world",
             "language": "en",
             "phraseList": ["world"]
           }
      },
      {
        "recordId": "3",
        "data":
           {
             "text": "Test",
             "language": "es",
             "phraseList": []
           }
      }
    ]
}
```

## <a name="sample-output-json-structure"></a>示例输出 JSON 结构

The "output" corresponds to the response returned from your Web API. The Web API should only return a _JSON_ payload (verified by looking at the `Content-Type` response header) and should satisfy the following constraints:

* 应有名为 `values` 且是对象数组的顶级实体。
* The number of objects in the array should be the same as the number of objects sent to the Web API.
* 每个对象都应有：
   * `recordId` 属性
   * `data` 属性，这个对象中的字段是与 `output` 中“名称”匹配的扩充，且值被视为扩充。
   * `errors` 属性，列出将添加到索引器执行历史记录的任何错误的数组。 此属性是必需的，但可以有 `null` 值。
   * `warnings` 属性，列出将添加到索引器执行历史记录的任何警告的数组。 此属性是必需的，但可以有 `null` 值。
* `values` 数组中的对象顺序不一定要与 `values` 数组中作为请求发送到 Web API 的对象顺序相同。 不过，由于 `recordId` 用于关联，因此响应中任何包含 `recordId`（不属于向 Web API 发送的原始请求）的记录都会遭放弃。

```json
{
    "values": [
        {
            "recordId": "3",
            "data": {
            },
            "errors": [
              {
                "message" : "'phraseList' should not be null or empty"
              }
            ],
            "warnings": null
        },
        {
            "recordId": "2",
            "data": {
                "hitPositions": [6, 16]
            },
            "errors": null,
            "warnings": null
        },
        {
            "recordId": "0",
            "data": {
                "hitPositions": [0, 23]
            },
            "errors": null,
            "warnings": null
        },
        {
            "recordId": "1",
            "data": {
                "hitPositions": []
            },
            "errors": null,
            "warnings": {
                "message": "No occurrences of 'Hi' were found in the input text"
            }
        },
    ]
}

```

## <a name="error-cases"></a>错误案例
除了 Web API 不可用或发送出非成功状态代码以外，还会将以下情况视为出错：

* 如果 Web API 返回成功状态代码，但响应指明它不是 `application/json`，那么响应会被视为无效，并且不会执行任何扩充。
* 如果响应 `values` 数组中有无效记录（包含不属于原始请求的 `recordId`，或值重复），则不会对这些记录执行任何扩充。

在 Web API 不可用或返回 HTTP 错误的情况下，包含 HTTP 错误的任何可用详细信息的易记错误都会添加到索引器执行历史记录。

## <a name="see-also"></a>另请参阅

+ [如何定义技能集](cognitive-search-defining-skillset.md)
+ [Add custom skill to an AI enrichment pipeline](cognitive-search-custom-skill-interface.md)
+ [Example: Creating a custom skill for AI enrichment](cognitive-search-create-custom-skill-example.md)
