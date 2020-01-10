---
title: 整形程序认知技能
titleSuffix: Azure Cognitive Search
description: 从非结构化数据中提取元数据和结构化信息，并将其作为 Azure 认知搜索中 AI 扩充管道中的复杂类型。
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 384b79037bb30656934c5e4b596dac2b776593b0
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2020
ms.locfileid: "75754112"
---
# <a name="shaper-cognitive-skill"></a>整形程序认知技能

**整形**程序技能将多个输入合并为一个[复杂类型](search-howto-complex-data-types.md)，以后可在扩充管道中进行引用。 借助整形程序技能，可实质上创建结构、定义该结构的成员名称，并为每个成员分配值。 搜索方案中有用的合并字段的示例包括：将名字和姓氏合并为单个结构，将城市和州合并为单个结构，或将名称和出生日期合并为单个结构以建立唯一标识。

此外，[方案 3](#nested-complex-types)中所示的**整形**程序技能向输入添加了一个可选的*sourceContext*属性。 *Source*和*sourceContext*属性互相排斥。 如果输入位于技能的上下文中，只需使用*source*。 如果输入的上下文与技能上下文*不同*，请使用*sourceContext*。 *SourceContext*要求您定义一个嵌套输入，并将特定元素作为源进行寻址。 

输出名称始终为 "output"。 在内部，管道可以映射不同的名称，如以下示例中所示的 "analyzedText"，但**整形**技能本身会在响应中返回 "output"。 如果正在调试大量文档并发现存在命名差异，或者要生成自定义技能并自行构建响应，这一点非常重要。

> [!NOTE]
> **整形**程序技能未绑定到认知服务 API，你不用付费。 但是，你仍然应该[附加认知服务资源](cognitive-search-attach-cognitive-services.md)，以覆盖**免费**资源选项，该选项限制你每天进行少量的每日扩充。

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ShaperSkill

## <a name="scenario-1-complex-types"></a>方案1：复杂类型

请思考这样一种情况：想要创建名为 analyzedText 的结构，该结构具有两个成员：分别为 text 和 sentiment。 在索引中，多部分可搜索字段称为*复杂类型*，并且通常在源数据具有映射到它的相应复杂结构时创建。

但是，创建复杂类型的另一种方法是使用**整形**程序技能。 通过在技能组合中包括这一技能，在技能组合处理过程中，内存中操作可以使用嵌套结构输出数据形状，然后可以将这些形状映射到索引中的复杂类型。 

下面的示例技术定义提供了成员名称作为输入。 


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

技能组合由索引器调用，并且索引器需要索引。 索引中的复杂字段表示形式可能类似于下面的示例。 

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

为此**整形**程序技能提供可用输入的传入 JSON 文档可能是：

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

整形程序技能使用 *text* 和 *sentiment* 组合元素生成一个名为 *analyzedText* 的新元素。 此输出符合索引架构。 它将在 Azure 认知搜索索引中导入并编制索引。

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

## <a name="scenario-2-input-consolidation"></a>方案2：输入合并

在另一个示例中，假设处于管道处理的不同阶段，已提取书名以及该书不同页面上的章节标题。 现在可创建由这些不同输入组成的单个结构。

此方案的**整形**程序技能定义可能类似于以下示例：

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
在这种情况下，**整形**会平展所有章节标题，以创建单个阵列。 

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

## <a name="scenario-3-input-consolidation-from-nested-contexts"></a>方案3：从嵌套上下文输入合并

假设您拥有书籍的标题、章节和内容，并对内容运行实体识别和关键短语，现在需要将不同技能的结果聚合为一个具有章节名称、实体和关键短语的形状。

此方案的**整形**程序技能定义可能类似于以下示例：

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
在这种情况下，**整形**会创建复杂类型。 此结构存在于内存中。 如果要将其保存到[知识库](knowledge-store-concept-intro.md)，应在技能组合中创建一个定义存储特征的投影。

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

## <a name="see-also"></a>另请参阅

+ [内置技能](cognitive-search-predefined-skills.md)
+ [如何定义技能集](cognitive-search-defining-skillset.md)
+ [如何使用复杂类型](search-howto-complex-data-types.md)
+ [知识存储（预览版）](knowledge-store-concept-intro.md)
+ [在 REST 中创建知识存储](knowledge-store-create-rest.md)