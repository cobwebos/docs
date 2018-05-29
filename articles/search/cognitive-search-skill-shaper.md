---
title: 整形程序认知搜索技能（Azure 搜索）| Microsoft Docs
description: 从非结构化数据中提取元数据和结构化信息，并将其整形为 Azure 搜索扩充管道中的复杂类型。
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 311f4bd67081de567763783a9d86540eda36d9f8
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
ms.locfileid: "33786766"
---
#   <a name="shaper-cognitive-skill"></a>整形程序认知技能

整形程序技能可创建复杂类型，支持复合字段（也称为多部分字段）。 复杂类型字段包含多个部分，但在 Azure 搜索索引中将其视为单个项目。 搜索方案中有用的合并字段示例包括将姓和名合并成单个字段、将城市和州合并成单个字段、或者将姓名和出生日期合并成单个字段，从而建立唯一标识。

借助整形程序技能，可实质上创建结构、定义该结构的成员名称，并为每个成员分配值。

默认情况下，此技术支持一个级别深度的对象。 对于更复杂的对象，可链接多个整形程序步骤。

在响应中，输出名称始终为“输出”。 管道可在内部映射不同名称，例如将以下示例中的“analyzedText”映射为“output”，但整形程序技能自身会在响应中返回“output”。 如果正在调试大量文档并发现存在命名差异，或者要生成自定义技能并自行构建响应，这一点非常重要。


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ShaperSkill

## <a name="sample-1-complex-types"></a>示例 1：复杂类型

请思考这样一种情况：想要创建名为 analyzedText 的结构，该结构具有两个成员：分别为 text 和 sentiment。 在 Azure 搜索中，若字段包含多个可搜索部分，则该字段称为复杂类型，并且尚不支持立即可用。 在此预览版中，可使用整形程序技能在索引中生成复杂类型的字段。 

以下示例提供了成员名称作为输入。 通过 targetName 指定输出结构（Azure 搜索中的复杂字段）。 


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
      "targetName": analyzedText"
    }
  ]
}
```

### <a name="sample-input"></a>示例输入
为此整形程序技能提供可用输入的 JSON 文档可能如下所示：

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


### <a name="sample-output"></a>示例输出
整形程序技能使用 text 和 sentiment 组合元素生成一个名为 analyzedText 的新元素。 

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

## <a name="sample-2-input-consolidation"></a>示例 2：输入合并

在另一个示例中，假设处于管道处理的不同阶段，已提取书名以及该书不同页面上的章节标题。 现在可创建由这些不同输入组成的单个结构。

此方案的整形程序技能定义可能如以下示例所示：

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
            "source": "/document/content/pages/*/chapterTitles/*"
        }
    ],
    "outputs": [
        {
            "output": "titlesAndChapters",
            "targetName": "analyzedText"
        }
    ]
}
```

### <a name="sample-output"></a>示例输出
在这种情况下，整形程序合并所有章节标题，创建单个数组。 

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

## <a name="see-also"></a>另请参阅

+ [预定义技能](cognitive-search-predefined-skills.md)
+ [如何定义技能组合](cognitive-search-defining-skillset.md)

