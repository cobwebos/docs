---
title: 自定义技能的接口定义
titleSuffix: Azure Cognitive Search
description: 为 Azure 认知搜索中 AI 扩充管道中的 web-api 自定义技能自定义数据提取接口。
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: 7a1a2aa92549bcab35532120c4af5bd0b6904f58
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2020
ms.locfileid: "82891250"
---
# <a name="how-to-add-a-custom-skill-to-an-azure-cognitive-search-enrichment-pipeline"></a>如何向 Azure 认知搜索扩充管道添加自定义技能

> [!VIDEO https://www.youtube.com/embed/fHLCE-NZeb4?version=3&start=172&end=221]

Azure 认知搜索中的[扩充管道](cognitive-search-concept-intro.md)可以从[内置认知技能](cognitive-search-predefined-skills.md)和你自己创建并添加到管道中的[自定义技能](cognitive-search-custom-skill-web-api.md)进行装配。 在本文中，你将了解如何创建自定义技能并使其公开某个接口，用以将该技能包括在 AI 扩充管道中。 

通过生成自定义技能，可插入对内容唯一的转换。 自定义技能独立执行，可应用所需的任何扩充步骤。 例如，可定义特定于域的自定义实体，生成自定义分类模型来区分商业和金融合同或文档，或者添加语音识别技能来深入了解相关内容的音频文件。 有关分步示例，请参阅[示例：创建用于 AI 扩充的自定义技能](cognitive-search-create-custom-skill-example.md)。

 无论需要哪种自定义功能，都有一个简单明了的接口，可将自定义技能与其余扩充管道相连接。 [技能组合](cognitive-search-defining-skillset.md)中包含的唯一需求是，能够以可在技能组合内作为整体使用的方式接受输入并发出输出。 本文的重点是扩充管道所需的输入和输出格式。

## <a name="web-api-custom-skill-interface"></a>Web API 自定义技能接口

如果未在 30 秒的期限内返回响应，自定义 WebAPI 技能终结点将默认超时。 索引管道是同步的，如果未在该期限内收到响应，索引会生成超时错误。  通过设置超时参数，最多可以将超时配置为 230 秒：

```json
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "This skill has a 230 second timeout",
        "uri": "https://[your custom skill uri goes here]",
        "timeout": "PT230S",
```

请确保 URI 是安全的 (HTTPS)。

目前，与自定义技能交互的唯一机制是通过 Web API 接口。 Web API 需求必须满足本节中所述的要求。

### <a name="1--web-api-input-format"></a>1.Web API 输入格式


> [!VIDEO https://www.youtube.com/embed/fHLCE-NZeb4?version=3&start=294&end=340]


Web API 必须接受要处理的一组记录。 每条记录都必须包含一个“属性包”，该属性包是提供给 Web API 的输入。 

假设要创建一个简单的扩充器来识别合同文本中提到的第一个日期。 在此示例中，技能接受单个输入 contractText 作为合同文本  。 技能也具有单个输出，即合同的日期。 若要使扩充器更有趣，请将此 contractDate 以多部分复杂类型的形式返回  。

你的 Web API 应该可以接收一批输入记录。 values 数组中的每个成员都表示特定记录的输入  。 每条记录都需要具有以下元素：

+ recordId 成员是特定记录的唯一标识符  。 当扩充器返回结果时，它必须提供此 recordId 以便允许调用方将记录结果与其输入进行匹配  。

+ data 成员，基本上是每条记录的输入域的包  。

更具体地说，根据上面的示例，你的 Web API 的请求应该类似于下：

```json
{
    "values": [
      {
        "recordId": "a1",
        "data":
           {
             "contractText": 
                "This is a contract that was issues on November 3, 2017 and that involves... "
           }
      },
      {
        "recordId": "b5",
        "data":
           {
             "contractText": 
                "In the City of Seattle, WA on February 5, 2018 there was a decision made..."
           }
      },
      {
        "recordId": "c3",
        "data":
           {
             "contractText": null
           }
      }
    ]
}
```
实际上，可能会调用服务的数百或数千条记录，而不仅仅是这里显示的三条记录。

### <a name="2-web-api-output-format"></a>2.Web API 输出格式

输出的格式是一组包含 recordId 和属性包的记录  

```json
{
  "values": 
  [
      {
        "recordId": "b5",
        "data" : 
        {
            "contractDate":  { "day" : 5, "month": 2, "year" : 2018 }
        }
      },
      {
        "recordId": "a1",
        "data" : {
            "contractDate": { "day" : 3, "month": 11, "year" : 2017 }                    
        }
      },
      {
        "recordId": "c3",
        "data" : 
        {
        },
        "errors": [ { "message": "contractText field required "}   ],  
        "warnings": [ {"message": "Date not found" }  ]
      }
    ]
}
```

这个具体示例仅有一个输出，但你可以输出多个属性。 

### <a name="errors-and-warning"></a>错误和警告

如之前示例所示，可返回每条记录的错误和警告消息。

## <a name="consuming-custom-skills-from-skillset"></a>从技能组合中使用自定义技能

创建 Web API 扩充器时，可将 HTTP 标头和参数描述为请求的一部分。 下面的代码片段显示了如何将请求参数和可选 HTTP 标头描述为技能组合定义的一部分。  HTTP 标头不是必需的，但它们可用来向技能中添加其他配置功能，并允许你在技能组定义中设置它们。

```json
{
    "skills": [
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "This skill calls an Azure function, which in turn calls TA sentiment",
        "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/DateExtractor?language=en",
        "context": "/document",
        "httpHeaders": {
            "DateExtractor-Api-Key": "foo"
        },
        "inputs": [
          {
            "name": "contractText",
            "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "contractDate",
            "targetName": "date"
          }
        ]
      }
  ]
}
```

## <a name="next-steps"></a>后续步骤

本文介绍了将自定义技能组成到技能组时所需的接口要求。 单击下面的链接来了解有关自定义技能和技能组构成的详细信息。

+ [观看有关自定义技能的视频](https://youtu.be/fHLCE-NZeb4)
+ [强大技能：定制技能的存储库](https://github.com/Azure-Samples/azure-search-power-skills)
+ [示例：为 AI 扩充创建自定义技能](cognitive-search-create-custom-skill-example.md)
+ [如何定义技能集](cognitive-search-defining-skillset.md)
+ [创建技能组合 (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [如何映射扩充的域](cognitive-search-output-field-mapping.md)