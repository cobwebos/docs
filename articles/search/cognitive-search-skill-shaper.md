---
title: 整形程序认知搜索技能 - Azure 搜索
description: 从非结构化数据中提取元数据和结构化信息，并将其整形为 Azure 搜索扩充管道中的复杂类型。
services: search
manager: nitinme
author: luiscabrer
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.subservice: cognitive-search
ms.openlocfilehash: 22a8f891eaf72dbec9c1ec103508c81effb14b08
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2019
ms.locfileid: "70186318"
---
#   <a name="shaper-cognitive-skill"></a>整形程序认知技能

“整形程序”技能将多个输入整合成以后可在扩充管道中引用的[复杂类型](search-howto-complex-data-types.md)。 借助整形程序技能，可实质上创建结构、定义该结构的成员名称，并为每个成员分配值。 搜索方案中有用的合并字段示例包括将姓和名合并成单个结构、将城市和州合并成单个结构、或者将姓名和出生日期合并成单个结构，从而建立唯一标识。

此外,[方案 3](#nested-complex-types)中所示的**整形**程序技能向输入添加了一个可选的*sourceContext*属性。 *source* 和 *sourceContext* 属性是互斥的。 如果输入位于技能上下文中，则只需使用 *source*。 如果输入所在的上下文与技能上下文不同，则使用 *sourceContext*。 *sourceContext* 要求使用寻址为源的特定元素定义嵌套的输入。 

输出名称始终为 "output"。 管道可在内部映射不同的名称，例如下图所示的“analyzedText”，但“整形程序”技能本身会在响应中返回“output”。 如果正在调试大量文档并发现存在命名差异，或者要生成自定义技能并自行构建响应，这一点非常重要。

> [!NOTE]
> “整形程序”技能未绑定到认知服务 API，使用它无需付费。 但是，你仍然应该[附加认知服务资源](cognitive-search-attach-cognitive-services.md)，以覆盖**免费**资源选项，该选项限制你每天进行少量的每日扩充。

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ShaperSkill

## <a name="scenario-1-complex-types"></a>方案 1：复杂类型

请思考这样一种情况：想要创建名为 analyzedText 的结构，该结构具有两个成员：分别为 text 和 sentiment。 在 Azure 搜索索引中，可搜索的多部分字段称为“复杂类型”，它通常是当源数据具有映射到它的相应复杂结构时创建的。

但是，创建复杂类型的另一种方法是通过“整形程序”技能。 通过在技能集中包含此技能，在技能集处理期间执行的内存中操作可以输出采用嵌套结构的数据形状，而此形状随后可映射到索引中的复杂类型。 

以下示例技能定义提供成员名称作为输入。 


```json
{
  "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
  "context": "/document/content/phrases/*",
  "inputs": [
    {
      "name": "text",
      "source": "/document/content/phrases/*"
    },
    {
      "name": "sentiment",
      "source": "/document/content/phrases/*/sentiment"
    }
  ],
  "outputs": [
    {
      "name": "output",
      "targetName": "analyzedText"
    }
  ]
}
```

### <a name="sample-index"></a>示例索引

技能集由索引器调用，索引器需要索引。 索引中的复杂字段表示形式可能如以下示例所示。 

```json

    "name": "my-index",
    "fields": [
        {   "name": "myId", "type": "Edm.String", "key": true, "filterable": true   },
        {   "name": "analyzedText", "type": "Edm.ComplexType",
            "fields": [{
                    "name": "text",
                    "type": "Edm.String",
                    "filterable": false,
                    "sortable": false,
                    "facetable": false,
                    "searchable": true  },
          {
                    "name": "sentiment",
                    "type": "Edm.Double",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
```

### <a name="skill-input"></a>技能输入

为此“整形程序”技能提供可用输入的传入 JSON 文档可能如下所示：

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "text": "this movie is awesome",
                "sentiment": 0.9
            }
        }
    ]
}
```


### <a name="skill-output"></a>技能输出

整形程序技能使用 *text* 和 *sentiment* 组合元素生成一个名为 *analyzedText* 的新元素。 此输出符合索引架构。 它将在 Azure 搜索索引中导入和编制索引。

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
            "analyzedText": 
              {
                "text": "this movie is awesome" ,
                "sentiment": 0.9
              }
           }
      }
    ]
}
```

## <a name="scenario-2-input-consolidation"></a>方案 2：输入整合

在另一个示例中，假设处于管道处理的不同阶段，已提取书名以及该书不同页面上的章节标题。 现在可创建由这些不同输入组成的单个结构。

此方案的“整形程序”技能定义可能如以下示例所示：

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document",
    "inputs": [
        {
            "name": "title",
            "source": "/document/content/title"
        },
        {
            "name": "chapterTitles",
            "source": "/document/content/pages/*/chapterTitles/*/title"
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "titlesAndChapters"
        }
    ]
}
```

### <a name="skill-output"></a>技能输出
在本例中，“整形程序”平整所有章节标题，以创建单个数组。 

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "titlesAndChapters": {
                    "title": "How to be happy",
                    "chapterTitles": [
                        "Start young",
                        "Laugh often",
                        "Eat, sleep and exercise"
                    ]
                }
            }
        }
    ]
}
```

<a name="nested-complex-types"></a>

## <a name="scenario-3-input-consolidation-from-nested-contexts"></a>方案 3：从嵌套的上下文进行输入整合

假设你有某个书籍的标题、章节和内容，并已针对内容中的关键短语运行实体识别，现在需要将不同技能的结果聚合成包含章节名称、实体和关键短语的单个形状。

此方案的“整形程序”技能定义可能如以下示例所示：

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document",
    "inputs": [
        {
            "name": "title",
            "source": "/document/content/title"
        },
        {
            "name": "chapterTitles",
            "sourceContext": "/document/content/pages/*/chapterTitles/*",
            "inputs": [
              {
                  "name": "title",
                  "source": "/document/content/pages/*/chapterTitles/*/title"
              },
              {
                  "name": "number",
                  "source": "/document/content/pages/*/chapterTitles/*/number"
              }
            ]
        }

    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "titlesAndChapters"
        }
    ]
}
```

### <a name="skill-output"></a>技能输出
在本例中，“整形程序”会创建一个复杂类型。 此结构存在于内存中。 如果要将其保存到[知识库](knowledge-store-concept-intro.md), 应在技能组合中创建一个定义存储特征的投影。

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "titlesAndChapters": {
                    "title": "How to be happy",
                    "chapterTitles": [
                      { "title": "Start young", "number": 1},
                      { "title": "Laugh often", "number": 2},
                      { "title": "Eat, sleep and exercise", "number: 3}
                    ]
                }
            }
        }
    ]
}
```

## <a name="see-also"></a>请参阅

+ [预定义技能](cognitive-search-predefined-skills.md)
+ [如何定义技能集](cognitive-search-defining-skillset.md)
+ [如何使用复杂类型](search-howto-complex-data-types.md)
+ [知识存储概述](knowledge-store-concept-intro.md)
+ [如何开始使用知识存储](knowledge-store-howto.md)