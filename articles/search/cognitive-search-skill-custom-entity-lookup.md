---
title: 自定义实体查找认知搜索技能
titleSuffix: Azure Cognitive Search
description: 从 Azure 认知搜索认知搜索管道中的文本提取不同的自定义实体。 此技能目前为公共预览版。
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 5c820b7e11c06f2d785da036f5174298caf56da6
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960602"
---
#    <a name="custom-entity-lookup-cognitive-skill-preview"></a>自定义实体查找认知技能（预览版）

> [!IMPORTANT] 
> 此技能目前为公共预览版。 提供的预览版功能不附带服务级别协议，我们不建议将其用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 目前没有门户或 .NET SDK 支持。

**自定义实体查找**技能将查找自定义的、用户定义的单词和短语列表中的文本。 使用此列表，它将标记具有任何匹配实体的所有文档。 该技能还支持一定程度的模糊匹配，可用于查找类似但不完全完全相同的匹配项。  

此技能未绑定到认知服务 API，可以在预览期间免费使用。 但仍应[附加认知服务资源](https://docs.microsoft.com/azure/search/cognitive-search-attach-cognitive-services)，以覆盖每日扩充限制。 每日限制适用于通过 Azure 认知搜索访问认知服务的免费访问。

## <a name="odatatype"></a>@odata.type  
CustomEntityLookupSkill。 

## <a name="data-limits"></a>数据限制
+ 支持的输入记录的最大大小为 256 MB。 如果你需要在将数据发送到自定义实体查找技能之前分解你的数据，请考虑使用[文本拆分技能](cognitive-search-skill-textsplit.md)。
+ 支持的最大实体定义表是 10 MB （如果是使用*entitiesDefitionUri*参数提供的）。 
+ 如果使用*inlineEntitiesDefinition*参数以内联方式定义实体，则受支持的最大大小为 10 KB。

## <a name="skill-parameters"></a>技能参数

参数区分大小写。

| 参数名称     | Description |
|--------------------|-------------|
| entitiesDefinitionUri | JSON 或 CSV 文件的路径，该文件包含要匹配的所有目标文本。 在索引器运行开始时，将读取此实体定义;在后续运行之前，不会实现对此文件的任何更新。 此配置必须可通过 HTTPS 访问。 请参阅下面的 "[自定义实体定义](#custom-entity-definition-format)格式" 以了解预期的 CSV 或 JSON 架构。|
|inlineEntitiesDefinition | 内联 JSON 实体定义。 如果存在，此参数将取代 entitiesDefinitionUri 参数。 以内联方式提供的配置不能超过 10 KB。 请参阅下面的[自定义实体定义](#custom-entity-definition-format)以获取预期的 JSON 架构。 |
|defaultLanguageCode |  可有可无用于标记和描绘输入文本的输入文本的语言代码。 支持以下语言： `da, de, en, es, fi, fr, it, ko, pt`。 默认值为 "英语（`en`）"。 如果你传递的是 languagecode-countrycode 格式，只会使用格式的 languagecode 部分。  |


## <a name="skill-inputs"></a>技能输入

| 输入名称      | Description                   |
|---------------|-------------------------------|
| text          | 要分析的文本。          |
| languageCode  | 可选。 默认为 `"en"`。  |


## <a name="skill-outputs"></a>技能输出


| 输出名称     | Description                   |
|---------------|-------------------------------|
| 实体 | 对象的数组，其中包含有关找到的匹配项和相关元数据的信息。 标识的每个实体可能包含以下字段：  <ul> <li> *名称*：标识的顶级实体。 实体表示 "规范化" 窗体。 </li> <li> *id*：用户在 "自定义实体定义格式" 中定义的实体的唯一标识符。</li> <li> *说明*：用户使用 "自定义实体定义格式" 定义的实体说明。 </li> <li> *键入：* 用户定义的实体类型，采用 "自定义实体定义格式"。</li> <li> *子类型：* 用户在 "自定义实体定义格式" 中定义的实体子类型。</li>  <li> *匹配*：描述源文本上该实体的每个匹配项的集合。 每个匹配项都具有以下成员： </li> <ul> <li> *text*：原始文本与源文档中的匹配项。 </li> <li> *offset*：在文本中找到匹配项的位置。 </li> <li> *长度*：匹配的文本的长度。 </li> <li> *matchDistance*：此匹配项不同于原始实体名称或别名的字符数。  </li> </ul> </ul>
  |

## <a name="custom-entity-definition-format"></a>自定义实体定义格式

有3种不同的方法可向自定义实体查找技能提供自定义实体的列表。 你可以在中提供列表。CSV 文件。作为技能定义的一部分的 JSON 文件或作为内联定义。  

如果定义文件是一个。CSV 或。JSON 文件，需要将该文件的路径作为*entitiesDefitionUri*参数的一部分提供。 在这种情况下，将在每次索引器运行开始时下载该文件一次。 只要索引器打算运行，就必须能够访问该文件。

如果以内联方式提供定义，则应将其作为*inlineEntitiesDefinition*技能参数的内容提供为内联。 

### <a name="csv-format"></a>CSV 格式

您可以提供自定义实体的定义，通过提供文件的路径并在*entitiesDefitionUri*技能参数中进行设置来在逗号分隔值（CSV）文件中查找。 路径应位于 https 位置。 定义文件的大小最大可为 10 MB。

CSV 格式很简单。 每行代表一个唯一的实体，如下所示：

```
Bill Gates, BillG, William H. Gates
Microsoft, MSFT
Satya Nadella 
```

在这种情况下，可以将三个实体作为找到的实体（比尔·盖茨、Satya Nadella、Microsoft）返回，但如果文本上的行（别名）上的任何字词均匹配，则将标识它们。 例如，如果在文档中找到字符串 "William"，则将返回 "比尔·盖茨" 实体的匹配项。

### <a name="json-format"></a>JSON 格式

还可以提供自定义实体的定义，以在 JSON 文件中查找。 JSON 格式可提供更大的灵活性，因为它允许您定义每个术语的匹配规则。 例如，您可以为每个术语指定模糊匹配距离（Damerau-Levenshtein 距离）或者匹配是否应区分大小写。 

 与 CSV 文件一样，需要提供 JSON 文件的路径，并在*entitiesDefitionUri*技能参数中进行设置。 路径应位于 https 位置。 定义文件的大小最大可为 10 MB。

最基本的 JSON 自定义实体列表定义可以是要匹配的实体列表：

```json
[ 
    { 
        "name" : "Bill Gates"
    }, 
    { 
        "name" : "Microsoft"
    }, 
    { 
        "name" : "Satya Nadella"
    }
]
```

更复杂的 JSON 定义示例还可以提供每个实体的 id、说明、类型和子类型以及其他*别名*。 如果匹配别名术语，还将返回该实体：

```json
[ 
    { 
        "name" : "Bill Gates",
        "description" : "Microsoft founder." ,
        "aliases" : [ 
            { "text" : "William H. Gates", "caseSensitive" : false },
            { "text" : "BillG", "caseSensitive" : true }
        ]
    }, 
    { 
        "name" : "Xbox One", 
        "type": "Harware",
        "subtype" : "Gaming Device",
        "id" : "4e36bf9d-5550-4396-8647-8e43d7564a76",
        "description" : "The Xbox One product"
    }, 
    { 
        "name" : "LinkedIn" , 
        "description" : "The LinkedIn company", 
        "id" : "differentIdentifyingScheme123", 
        "fuzzyEditDistance" : 0 
    }, 
    { 
        "name" : "Microsoft" , 
        "description" : "Microsoft Corporation", 
        "id" : "differentIdentifyingScheme987", 
        "defaultCaseSensitive" : false, 
        "defaultFuzzyEditDistance" : 1, 
        "aliases" : [ 
            { "text" : "MSFT", "caseSensitive" : true }
        ]
    } 
] 
```

下表更详细地介绍了在定义要匹配的实体时可以设置的不同配置参数：

|  字段名  |        Description  |
|--------------|----------------------|
| name | 顶级实体描述符。 技能输出中的匹配项将按此名称进行分组，并且应表示所找到文本的 "已规范化" 窗体。  |
| description  | 可有可无此字段可用作有关匹配文本的自定义元数据的传递。 此字段的值将随其在技能表中的实体的每个匹配项显示。 |
| type | 可有可无此字段可用作有关匹配文本的自定义元数据的传递。 此字段的值将随其在技能表中的实体的每个匹配项显示。 |
| subtype | 可有可无此字段可用作有关匹配文本的自定义元数据的传递。 此字段的值将随其在技能表中的实体的每个匹配项显示。 |
| id | 可有可无此字段可用作有关匹配文本的自定义元数据的传递。 此字段的值将随其在技能表中的实体的每个匹配项显示。 |
| caseSensitive | 可有可无默认值为 false。 指示与实体名称进行比较是否应区分字符大小写的布尔值。 不区分大小写的匹配项 "Microsoft" 可能是： microsoft、microSoft、MICROSOFT |
| fuzzyEditDistance | 可有可无默认值为0。 最大值为5。 表示可接受的具有实体名称匹配项的分歧字符数。 返回任何给定匹配的可能的最小容差。  例如，如果 "编辑距离" 设置为 "3"，则 "Windows 10" 仍会匹配 "Windows"、"Windows 10" 和 "windows 7"。 <br/> 如果将 "区分大小写" 设置为 "false"，则大小写差异不会计入 "区分大小容差"，否则为。 |
| defaultCaseSensitive | 可有可无更改此实体的默认区分大小写的值。 它用于更改所有别名 caseSensitive 值的默认值。 |
| defaultFuzzyEditDistance | 可有可无更改此实体的默认模糊编辑距离值。 它可用于更改所有别名 fuzzyEditDistance 值的默认值。 |
| 别名 | 可有可无可用于指定根实体名称的替代拼写或同义词的复杂对象数组。 |

| 别名属性 | Description |
|------------------|-------------|
| text  | 某些目标实体名称的替代拼写或表示形式。  |
| caseSensitive | 可有可无与上面的根实体 "caseSensitive" 参数的作用相同，但仅适用于这一个别名。 |
| fuzzyEditDistance | 可有可无与上面的根实体 "fuzzyEditDistance" 参数的作用相同，但仅适用于这一个别名。 |


### <a name="inline-format"></a>内联格式

在某些情况下，更方便的方法是提供自定义实体的列表，以便将内联直接匹配到技能定义。 在这种情况下，您可以对上述方法使用类似的 JSON 格式，但它是在技能定义中内联的。
只能以内联方式定义小于 10 KB 大小（序列化大小）的配置。 

##  <a name="sample-definition"></a>示例定义

下面显示了使用内联格式的示例技能定义：

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.CustomEntityLookupSkill",
    "context": "/document",
    "inlineEntitiesDefinition": 
    [
      { 
        "name" : "Bill Gates",
        "description" : "Microsoft founder." ,
        "aliases" : [ 
            { "text" : "William H. Gates", "caseSensitive" : false },
            { "text" : "BillG", "caseSensitive" : true }
        ]
      }, 
      { 
        "name" : "Xbox One", 
        "type": "Harware",
        "subtype" : "Gaming Device",
        "id" : "4e36bf9d-5550-4396-8647-8e43d7564a76",
        "description" : "The Xbox One product"
      }
    ],    
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "entities",
        "targetName": "matchedEntities"
      }
    ]
  }
```
或者，如果您决定提供指向实体定义文件的指针，则下面显示了使用 entitiesDefinitionUri 格式的示例技能定义：

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.CustomEntityLookupSkill",
    "context": "/document",
    "entitiesDefinitionUri": "https://myblobhost.net/keyWordsConfig.csv",    
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "entities",
        "targetName": "matchedEntities"
      }
    ]
  }

```

##  <a name="sample-input"></a>示例输入

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "The company microsoft was founded by Bill Gates. Microsoft's gaming console is called Xbox",
             "languageCode": "en"
           }
      }
    ]
}
```

##  <a name="sample-output"></a>示例输出

```json
  { 
    "values" : 
    [ 
      { 
        "recordId": "1", 
        "data" : { 
          "entities": [
            { 
              "name" : "Microsoft", 
              "description" : "This document refers to Microsoft the company", 
              "id" : "differentIdentifyingScheme987", 
              "matches" : [ 
                { 
                  "text" : "microsoft", 
                  "offset" : 13, 
                  "length" : 9, 
                  "matchDistance" : 0 
                }, 
                { 
                  "text" : "Microsoft",
                  "offset" : 49, 
                  "length" : 9, 
                  "matchDistance" : 0
                }
              ] 
            },
            { 
              "name" : "Bill Gates",
              "description" : "William Henry Gates III, founder of Microsoft.", 
              "matches" : [
                { 
                  "text" : "Bill Gates",
                  "offset" : 37, 
                  "length" : 10,
                  "matchDistance" : 0 
                }
              ]
            }
          ] 
        } 
      } 
    ] 
  } 
```

## <a name="see-also"></a>另请参阅

+ [内置技能](cognitive-search-predefined-skills.md)
+ [如何定义技能集](cognitive-search-defining-skillset.md)
+ [实体识别技能（用于搜索众所周知的实体）](cognitive-search-skill-entity-recognition.md)