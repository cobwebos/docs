---
title: 自定义实体查找认知搜索技能
titleSuffix: Azure Cognitive Search
description: 从 Azure 认知搜索管道中的文本提取不同的自定义实体。 此技能目前以公共预览版提供。
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 5511551f240fe4fdd2f2aa3bc8a3a2615505f35f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88936106"
---
#     <a name="custom-entity-lookup-cognitive-skill-preview"></a>自定义实体查找认知技能（预览版）

> [!IMPORTANT] 
> 此技能目前以公共预览版提供。 提供的预览版功能不附带服务级别协议，我们不建议将其用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 目前不支持门户或 .NET SDK。

“自定义实体查找”技能可在用户自定义的单词和短语列表中查找文本。 它使用此列表为包含任何匹配实体的所有文档加上标签。 该技能还支持一定程度的模糊匹配，应用此匹配方法可以查找类似但不完全相同的匹配项。  

此技能未绑定到认知服务 API，在预览期可免费使用。 但是，仍然应该[附加一个认知服务资源](./cognitive-search-attach-cognitive-services.md)，以覆盖每日扩充限制。 每日限制适用于通过 Azure 认知搜索免费访问认知服务的情况。

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.CustomEntityLookupSkill 

## <a name="data-limits"></a>数据限制
+ 支持的最大输入记录大小为 256 MB。 如果在将数据发送到自定义实体查找技能之前需要将其拆分，请考虑使用[文本拆分技能](cognitive-search-skill-textsplit.md)。
+ 如果使用 entitiesDefinitionUri 参数提供实体定义表，则支持的最大表大小为 10 MB。 
+ 如果使用 *inlineEntitiesDefinition* 参数以内联方式定义实体，则支持的最大大小为 10 KB。

## <a name="skill-parameters"></a>技能参数

参数区分大小写。

| 参数名称     | 说明 |
|--------------------|-------------|
| `entitiesDefinitionUri`    | JSON 或 CSV 文件的路径，该文件包含要匹配的所有目标文本。 索引器运行一开始就会读取此实体定义；在后续运行之前，不会识别到在运行中途对此文件所做的任何更新。 必须可以通过 HTTPS 访问此配置。 有关预期的 CSV 或 JSON 架构，请参阅下面的[自定义实体定义](#custom-entity-definition-format)格式。|
|`inlineEntitiesDefinition` | 内联 JSON 实体定义。 此参数将取代 entitiesDefinitionUri 参数（如果存在）。 可通过内联方式提供不超过 10 KB 的配置。 有关预期的 JSON 架构，请参阅下面的[自定义实体定义](#custom-entity-definition-format)。 |
|`defaultLanguageCode` |    （可选）用于标记化和描绘输入文本的输入文本的语言代码。 支持以下语言：`da, de, en, es, fi, fr, it, ko, pt`。 默认值为英语 (`en`)。 如果你传递的是 languagecode-countrycode 格式，只会使用格式的 languagecode 部分。  |


## <a name="skill-inputs"></a>技能输入

| 输入名称      | 说明                   |
|---------------|-------------------------------|
| `text`          | 要分析的文本。          |
| `languageCode`    | 可选。 默认值为 `"en"`。  |


## <a name="skill-outputs"></a>技能输出


| 输出名称      | 说明                   |
|---------------|-------------------------------|
| `entities` | 对象的数组，包含有关找到的匹配项和相关元数据的信息。 识别到的每个实体可包含以下字段：  <ul> <li> *name*：识别到的顶级实体。 实体表示“规范化”形式。 </li> <li> *id*：用户以“自定义实体定义格式”定义的实体的唯一标识符。</li> <li> *description*：用户以“自定义实体定义格式”定义的实体说明。 </li> <li> *type*：用户以“自定义实体定义格式”定义的实体类型。</li> <li> *subtype*：用户以“自定义实体定义格式”定义的实体子类型。</li>  <li> *matches*：描述该实体在源文本中的每个匹配项的集合。 每个匹配项具有以下成员： </li> <ul> <li> *text*：源文档中匹配的原始文本。 </li> <li> *offset*：该匹配项在文本中的位置。 </li> <li> *length*:匹配文本的长度。 </li> <li> *matchDistance*：此匹配项与原始实体名称或别名相差的字符数。  </li> </ul> </ul>
  |

## <a name="custom-entity-definition-format"></a>自定义实体定义格式

可通过 3 种不同的方式向自定义实体查找技能提供自定义实体列表。 可以在 .CSV 文件、.JSON 文件中提供列表，或者以内联定义的形式在技能定义中提供列表。  

如果定义文件是 .CSV 或 .JSON 文件，则需要提供该文件的路径作为 entitiesDefinitionUri 参数的一部分。 在这种情况下，将在每次开始运行索引器时下载该文件一次。 每次想要运行索引器时，都必须能够访问该文件。 此外，该文件必须采用 UTF-8 编码。

如果以内联方式提供定义，应将其作为 *inlineEntitiesDefinition* 技能参数的内容提供。 

### <a name="csv-format"></a>CSV 格式

可以提供自定义实体的定义来查找逗号分隔值 (CSV) 文件中的内容，方法是提供该文件的路径并在 entitiesDefinitionUri 技能参数中设置该路径。 该路径应位于 https 位置。 定义文件的最大大小为 10 MB。

CSV 格式很简单。 每行代表一个唯一实体，如下所示：

```
Bill Gates, BillG, William H. Gates
Microsoft, MSFT
Satya Nadella 
```

在本例中，可以返回找到的三个实体（Bill Gates、Satya Nadella、Microsoft），但是，如果行（别名）上的任何字词在文本中匹配，则会识别到这些实体。 例如，如果在文档中找到字符串“William H. Gates”，则会返回“Bill Gates”实体的匹配项。

### <a name="json-format"></a>JSON 格式

还可以提供自定义实体的定义，以查找 JSON 文件中的内容。 JSON 格式提供的灵活性要大一些，因为它允许按字词定义匹配规则。 例如，可为每个字词指定模糊匹配距离（Damerau-Levenshtein 距离），或者匹配是否要区分大小写。 

 与 CSV 文件一样，需要提供 JSON 文件的路径，并在 entitiesDefinitionUri 技能参数中设置该路径。 该路径应位于 https 位置。 定义文件的最大大小为 10 MB。

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

更复杂的 JSON 定义示例可以选择性地提供每个实体的 ID、说明、类型和子类型，以及其他别名。 如果匹配了某个别名字词，则也会返回该实体：

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

下表更详细地描述了在定义要匹配的实体时可以设置的不同配置参数：

|  字段名称  |        说明  |
|--------------|----------------------|
| `name` | 顶级实体描述符。 技能输出中的匹配项将按此名称分组，此名称应表示所找到的文本的“规范化”形式。  |
| `description`  | （可选）此字段可用作有关匹配文本的自定义元数据的信息传达字段。 此字段的值将连同其在技能输出中的实体的每个匹配项一起显示。 |
| `type` | （可选）此字段可用作有关匹配文本的自定义元数据的信息传达字段。 此字段的值将连同其在技能输出中的实体的每个匹配项一起显示。 |
| `subtype` | （可选）此字段可用作有关匹配文本的自定义元数据的信息传达字段。 此字段的值将连同其在技能输出中的实体的每个匹配项一起显示。 |
| `id` | （可选）此字段可用作有关匹配文本的自定义元数据的信息传达字段。 此字段的值将连同其在技能输出中的实体的每个匹配项一起显示。 |
| `caseSensitive` | （可选）默认值为 false。 一个布尔值，表示在与实体名称进行比较时是否应区分字符大小写。 不区分大小写的“Microsoft”匹配示例：microsoft, microSoft, MICROSOFT |
| `fuzzyEditDistance` | （可选）默认值为 0。 最大值为 5。 表示仍看作与实体名称匹配的可接受分歧字符数。 将返回任意给定匹配项的最小可能模糊匹配数。  例如，如果编辑距离设置为 3，则“Windows 10”仍与“Windows”、“Windows10”和“windows 7”匹配。 <br/> 如果区分大小写设置为 false，则大小写差异不会计入模糊匹配容差；否则会计入。 |
| `defaultCaseSensitive` | （可选）更改此实体的默认区分大小写值。 它用于更改所有别名 caseSensitive 值的默认值。 |
| `defaultFuzzyEditDistance` | （可选）更改此实体的默认模糊编辑距离值。 它可用于更改所有别名 fuzzyEditDistance 值的默认值。 |
| `aliases` | （可选）可用于指定根实体名称的替代拼写或同义词的复杂对象数组。 |

| 别名属性 | 说明 |
|------------------|-------------|
| `text`  | 某个目标实体名称的替代拼写或表示形式。  |
| `caseSensitive` | （可选）作用与前面所述的根实体“caseSensitive”参数相同，但仅应用于这一个别名。 |
| `fuzzyEditDistance` | （可选）作用与前面所述的根实体“fuzzyEditDistance”参数相同，但仅应用于这一个别名。 |


### <a name="inline-format"></a>内联格式

在某些情况下，直接在技能定义中提供要内联匹配的自定义实体列表会更方便。 对于这种情况，可以使用类似于前面所述的 JSON 格式，但要将它内联在技能定义中。
只能以内联方式定义小于 10 KB（序列化大小）的配置。 

##    <a name="sample-definition"></a>示例定义

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
        "type": "Hardware",
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
或者，如果你决定提供指向实体定义文件的指针，可以参考下面所示的使用 `entitiesDefinitionUri` 格式的示例技能定义：

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
             "text": "The company, Microsoft, was founded by Bill Gates. Microsoft's gaming console is called Xbox",
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

### <a name="warning-reached-maximum-capacity-for-matches-skipping-all-further-duplicate-matches"></a>警告：已达到最大的匹配项容量，将跳过所有后续的重复匹配项。

如果检测到的匹配项数大于允许的最大值，则将发出此警告。 在这种情况下，我们将停止包含重复的匹配项。 如果这是你无法接受的，请提交[支持票证](https://ms.portal.azure.com/#create/Microsoft.Support)，以便我们帮助你处理个别用例。

## <a name="see-also"></a>另请参阅

+ [内置技能](cognitive-search-predefined-skills.md)
+ [如何定义技能集](cognitive-search-defining-skillset.md)
+ [实体识别技能（用于搜索已知实体）](cognitive-search-skill-entity-recognition.md)