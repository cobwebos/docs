---
title: 自定义实体查找认知搜索技能
titleSuffix: Azure Cognitive Search
description: 从 Azure 认知搜索认知搜索管道中的文本中提取不同的自定义实体。 此技能当前处于公共预览版中。
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 8674438032ebd925296c95e9ffa0a2a0b95322f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79369771"
---
#     <a name="custom-entity-lookup-cognitive-skill-preview"></a>自定义实体查找认知技能（预览版）

> [!IMPORTANT] 
> 此技能当前处于公共预览版中。 提供的预览版功能不附带服务级别协议，我们不建议将其用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 目前不支持门户或 .NET SDK。

**自定义实体查找**技能从自定义、用户定义的单词和短语列表中查找文本。 使用此列表，它将使用任何匹配的实体标记所有文档。 该技能还支持一定程度的模糊匹配，可用于查找相似但不精确的匹配。  

此技能不绑定到认知服务 API，可在预览期间免费使用。 但是，您仍应[附加认知服务资源](https://docs.microsoft.com/azure/search/cognitive-search-attach-cognitive-services)，以覆盖每日扩充限制。 每日限制适用于通过 Azure 认知搜索访问时对认知服务的自由访问。

## <a name="odatatype"></a>@odata.type  
微软.技能.文本.自定义实体查找技能 

## <a name="data-limits"></a>数据限制
+ 支持的最大输入记录大小为 256 MB。 如果需要在将数据发送到自定义实体查找技能之前拆分数据，请考虑使用[文本拆分技能](cognitive-search-skill-textsplit.md)。
+ 如果使用*实体DefitionUri*参数提供，则支持的最大实体定义表为 10 MB。 
+ 如果内联定义实体，则使用*内联实体定义*参数，则支持的最大大小为 10 KB。

## <a name="skill-parameters"></a>技能参数

参数区分大小写。

| 参数名称     | 描述 |
|--------------------|-------------|
| 实体 定义Uri    | JSON 或 CSV 文件的路径，其中包含要匹配的所有目标文本。 此实体定义在索引器运行开始时读取;在索引器运行开始时，将读取此实体定义。在运行中，此文件的任何更新在后续运行之前不会实现。 此配置必须通过 HTTPS 访问。 有关预期的 CSV 或 JSON 架构，请参阅下面的[自定义实体定义](#custom-entity-definition-format)格式。|
|内联定义 | 内联 JSON 实体定义。 如果存在，此参数将取代实体定义Uri 参数。 内联配置不能超过 10 KB。 有关预期的 JSON 架构，请参阅下面的[自定义实体定义](#custom-entity-definition-format)。 |
|defaultLanguageCode |    （可选）用于标记和描述输入文本的输入文本的语言代码。 支持以下语言： `da, de, en, es, fi, fr, it, ko, pt`. 默认值为英语 （）。`en` 如果你传递的是 languagecode-countrycode 格式，只会使用格式的 languagecode 部分。  |


## <a name="skill-inputs"></a>技能输入

| 输入名称      | 描述                   |
|---------------|-------------------------------|
| text          | 要分析的文本。          |
| languageCode    | 可选。 默认为 `"en"`。  |


## <a name="skill-outputs"></a>技能输出


| 输出名称      | 描述                   |
|---------------|-------------------------------|
| 实体 | 包含有关找到的匹配项的信息和相关元数据的对象数组。 标识的每个实体可能包含以下字段：  <ul> <li> *名称*： 标识的顶级实体。 实体表示"规范化"窗体。 </li> <li> *id*：用户在"自定义实体定义格式"中定义的实体的唯一标识符。</li> <li> *说明*：用户在"自定义实体定义格式"中定义的实体描述。 </li> <li> *类型：* 由用户在"自定义实体定义格式"中定义的实体类型。</li> <li> *子类型：* 由用户在"自定义实体定义格式"中定义的实体子类型。</li>  <li> *匹配*： 描述源文本上该实体的每个匹配项的集合。 每个匹配将具有以下成员： </li> <ul> <li> *文本*： 原始文本与源文档匹配。 </li> <li> *偏移*：在文本中找到匹配的位置。 </li> <li> *长度*：匹配文本的长度。 </li> <li> *匹配距离*：此匹配的字符数与原始实体名称或别名不同。  </li> </ul> </ul>
  |

## <a name="custom-entity-definition-format"></a>自定义实体定义格式

有 3 种不同的方法向自定义实体查找技能提供自定义实体的列表。 您可以在 中提供列表。CSV 文件， .JSON 文件或作为技能定义的内联定义。  

如果定义文件为 。CSV 或 。JSON 文件，文件的路径需要作为*实体DefitionUri*参数的一部分提供。 在这种情况下，该文件在每个索引器运行开始时下载一次。 只要索引器打算运行，该文件必须是可访问的。 此外，文件必须编码 UTF-8。

如果定义是内联提供的，则应与*内联身份定义*技能参数的内容一样提供内联。 

### <a name="csv-format"></a>CSV 格式

通过提供文件的路径并将其设置在*实体DefitionUri*技能参数中，可以提供要在 Comma 分隔值 （CSV） 文件中查找的自定义实体的定义。 路径应位于 https 位置。 定义文件的大小可达 10 MB。

CSV 格式很简单。 每行表示一个唯一实体，如下所示：

```
Bill Gates, BillG, William H. Gates
Microsoft, MSFT
Satya Nadella 
```

在这种情况下，有三个实体可以作为找到的实体（比尔·盖茨、萨蒂亚·纳德拉、微软）返回，但如果文本上匹配行中的任何术语（别名），则将标识它们。 例如，如果在文档中找到字符串"威廉·盖茨"，则返回"比尔·盖茨"实体的匹配项。

### <a name="json-format"></a>JSON 格式

也可以提供要在 JSON 文件中查找的自定义实体的定义。 JSON 格式为您提供了更大的灵活性，因为它允许您定义每个术语的匹配规则。 例如，您可以为每个术语指定模糊匹配距离（Damerau-Levenshtein 距离），或者匹配是否应区分大小写。 

 与 CSV 文件一样，您需要提供 JSON 文件的路径，并将其设置在*实体DefitionUri*技能参数中。 路径应位于 https 位置。 定义文件的大小可达 10 MB。

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

JSON 定义的更复杂的示例可以选择提供每个实体的 ID、描述、类型和子类型 ，以及其他*别名*。 如果匹配别名术语，则实体也将返回：

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

下表详细介绍了在定义要匹配的实体时可以设置的不同配置参数：

|  字段名  |        描述  |
|--------------|----------------------|
| name | 顶级实体描述符。 技能输出中的匹配项将按此名称分组，并且它应表示正在找到的文本的"规范化"形式。  |
| description  | （可选）此字段可用作有关匹配文本的自定义元数据的传递。 此字段的值将与其实体在技能输出中的每个匹配项一起显示。 |
| type | （可选）此字段可用作有关匹配文本的自定义元数据的传递。 此字段的值将与其实体在技能输出中的每个匹配项一起显示。 |
| subtype | （可选）此字段可用作有关匹配文本的自定义元数据的传递。 此字段的值将与其实体在技能输出中的每个匹配项一起显示。 |
| id | （可选）此字段可用作有关匹配文本的自定义元数据的传递。 此字段的值将与其实体在技能输出中的每个匹配项一起显示。 |
| 区分大小写 | （可选）默认值为 false。 布尔值表示与实体名称的比较是否应对字符大小写敏感。 "微软"的样本大小写不敏感匹配可以是：微软、微软、微软 |
| 模糊编辑距离 | （可选）默认值为 0。 最大值为 5。 表示仍构成与实体名称匹配的可接受的发散字符数。 返回任何给定匹配中尽可能小的模糊性。  例如，如果编辑距离设置为 3，"Windows 10"仍将匹配"Windows"、"Windows10"和"窗口 7"。 <br/> 当区分大小写设置为 false 时，大小写差异不会计入模糊容差，但并非如此。 |
| 默认事件敏感 | （可选）更改此实体的默认区分大小写值。 它用于更改所有别名的默认值区分值。 |
| 默认模糊编辑距离 | （可选）更改此实体的默认模糊编辑距离值。 它可用于更改所有别名模糊编辑距离值的默认值。 |
| 别名 | （可选）可用于指定根实体名称的替代拼写或同义词的复杂对象的数组。 |

| 别名属性 | 描述 |
|------------------|-------------|
| text  | 某些目标实体名称的替代拼写或表示形式。  |
| 区分大小写 | （可选）行为与上面的根实体"区分"参数相同，但仅适用于此别名。 |
| 模糊编辑距离 | （可选）作用与上面的根实体"模糊编辑距离"参数相同，但仅适用于此别名。 |


### <a name="inline-format"></a>内联格式

在某些情况下，提供自定义实体列表以直接匹配到技能定义中可能更方便。 在这种情况下，您可以使用与上述格式类似的 JSON 格式，但它在技能定义中内联。
只能内联定义大小小于 10 KB（序列化大小）的配置。 

##    <a name="sample-definition"></a>示例定义

使用内联格式的示例技能定义如下所示：

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
或者，如果您决定提供指向实体定义文件的指针，则下面将显示使用实体定义库格式的示例技能定义：

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

##    <a name="sample-input"></a>示例输入

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

##    <a name="sample-output"></a>示例输出

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

## <a name="errors-and-warnings"></a>错误和警告

### <a name="warning-reached-maximum-capacity-for-matches-skipping-all-further-duplicate-matches"></a>警告： 达到匹配的最大容量，跳过所有进一步的重复匹配项。

如果检测到的匹配数大于允许的最大值，将发出此警告。 在这种情况下，我们将停止包括重复的匹配项。 如果您不能接受，请提交[支持票证](https://ms.portal.azure.com/#create/Microsoft.Support)，以便我们帮助您处理您的个人使用案例。

## <a name="see-also"></a>请参阅

+ [内置技能](cognitive-search-predefined-skills.md)
+ [如何定义技能集](cognitive-search-defining-skillset.md)
+ [实体识别技能（搜索已知实体）](cognitive-search-skill-entity-recognition.md)
