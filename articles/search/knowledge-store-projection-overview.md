---
title: 知识存储中的投影（预览）
titleSuffix: Azure Cognitive Search
description: 将丰富的数据从 AI 浓缩索引管道中保存并成形状到知识存储中，用于全文搜索以外的方案。 知识存储目前以公开预览版提供。
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: d264768bf27967d1a778400ae4e9e6f2e054d746
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942976"
---
# <a name="projections-in-a-knowledge-store-in-azure-cognitive-search"></a>Azure 认知搜索中知识存储中的投影

> [!IMPORTANT] 
> 知识存储目前以公开预览版提供。 提供的预览版功能不附带服务级别协议，我们不建议将其用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 [REST API 版本 2019-05-06-Preview](search-api-preview.md) 提供预览版功能。 目前提供有限的门户支持，不提供 .NET SDK 支持。

Azure 认知搜索通过内置的认知技能和自定义技能实现内容丰富，作为索引的一部分。 富集创建以前不存在的新信息：从图像中提取信息、从文本中检测情绪、关键短语和实体，仅举几例。 富集还会将结构添加到未区分的文本。 所有这些过程都会导致文档使全文搜索更加有效。 在许多情况下，丰富的文档可用于搜索以外的方案，例如知识挖掘。

投影（[知识存储](knowledge-store-concept-intro.md)的一个组件）是可以保存到物理存储的、用于实现知识挖掘的扩充文档的视图。 使用投影可将数据“投影”到符合需求的形状，并保持相应的关系，使 Power BI 等工具能够在不增大负载的情况下读取数据。

投影可以是表格式的，其中的数据存储在 Azure Blob 存储中的行与列中；也可以是存储在 Azure 表存储中的 JSON 对象。 可以在扩充数据时定义数据的多个投影。 当您希望单个用例的相同数据形状不同时，多个投影非常有用。

知识库支持三种类型的投影：

+ **表**：对于最能表示为行和列的数据，表投影允许您在表存储中定义架构化形状或投影。 只有有效的 JSON 对象才能投影为表，富集文档可以包含不命名为 JSON 对象的节点，并且在投影这些对象时，使用扫描器技能或内联整形创建有效的 JSON 对象。

+ **对象**：当您需要数据并扩充的 JSON 表示形式时，对象投影将另存为 blob。 只有有效的 JSON 对象才能投影为对象，富集文档可以包含不命名为 JSON 对象的节点，并且在投影这些对象时，使用扫描器技能或内联整形创建有效的 JSON 对象。

+ **文件**：当您需要保存从文档中提取的图像时，文件投影允许您将规范化图像保存到 Blob 存储。

要查看在上下文中定义的投影，请逐步在[REST 中创建知识存储](knowledge-store-create-rest.md)。

## <a name="projection-groups"></a>投影组

在某些情况下，你需要投影采用不同形状的扩充数据，以符合不同的目标。 使用知识存储可以定义多个投影组。 投影组具有以下重要的互斥性和相关性特征。

### <a name="mutual-exclusivity"></a>相互排他性

投影到单个组的所有内容独立于投影到其他投影组的数据。
这种独立性意味着可以以不同的形状使用相同的数据，但在每个投影组中重复。

### <a name="relatedness"></a>相关性

投影组现在允许您跨投影类型投影文档，同时保留跨投影类型的关系。 在单个投影组中投影的所有内容都保留跨投影类型的数据中的关系。 在表中，关系基于生成的密钥，每个子节点保留对父节点的引用。 跨类型（表、对象和文件）在跨不同类型投影单个节点时，将保留关系。 例如，请考虑一个包含图像和文本的文档的方案。 您可以将文本投影到表或对象，将图像投影到表或对象具有包含文件 URL 的列/属性的文件。

## <a name="input-shaping"></a>输入整形

获取采用适当形状或结构（表或对象）的数据对于有效利用数据而言至关重要。 根据访问和使用数据的方式塑造或结构化数据的功能，是在技能集中作为“整形程序”公开的关键功能。****  

如果扩充树中存在与投影架构匹配的对象，则可以更轻松地定义投影。 使用更新的[整形程序技能](cognitive-search-skill-shaper.md)可以从扩充树的不同节点编写对象，并将其指定为新节点下的父级。 使用“整形程序”技能可以定义包含嵌套对象的复杂类型。****

如果定义的新形状包含需要投影出的所有元素，则你现在可以使用此形状作为投影的源，或作为另一技能的输入。

## <a name="projection-slicing"></a>投影切片

定义投影组时，可以将扩充树中的单个节点分割到多个相关的表或对象中。 使用源路径添加是现有投影的子节点的投影将导致子节点被从父节点中切片并投影到新的但相关的表或对象中。 此技术允许您在扫描器技能中定义单个节点，该节点可以是所有投影的源。

## <a name="table-projections"></a>表投影

我们建议在 Power BI 中使用表投影浏览数据，因为这可以更方便地导入。 此外，表投影允许更改表关系之间的基数。 

可将索引中的单个文档投影到多个表，并保留关系。 投影到多个表时，除非子节点是同一个组中其他表的源，否则整个形状将投影到每个表。

### <a name="defining-a-table-projection"></a>定义表投影

在技能集的 `knowledgeStore` 元素中定义表投影时，请先将扩充树中的某个节点映射到表源。 此节点通常是添加到技能列表的、用于生成需要投影到表的“整形程序”技能的输出。**** 选择投影的节点可以分片，以投影到多个表。 表定义是要投影的表列表。

每个表需要三个属性：

+ 表名称：Azure 存储中的表的名称。

+ 生成 KeyName：唯一标识此行的键的列名。

+ 源：从您从源源源源的浓缩树中的节点。 此节点通常是沙棘的输出，但可以是任何技能的输出。

下面是表投影的示例。

```json
{
    "name": "your-skillset",
    "skills": [
      …your skills
    ],
"cognitiveServices": {
… your cognitive services key info
    },

    "knowledgeStore": {
      "storageConnectionString": "an Azure storage connection string",
      "projections" : [
        {
          "tables": [
            { "tableName": "MainTable", "generatedKeyName": "SomeId", "source": "/document/EnrichedShape" },
            { "tableName": "KeyPhrases", "generatedKeyName": "KeyPhraseId", "source": "/document/EnrichedShape/*/KeyPhrases/*" },
            { "tableName": "Entities", "generatedKeyName": "EntityId", "source": "/document/EnrichedShape/*/Entities/*" }
          ]
        },
        {
          "objects": [ ]
        },
        {
            "files": [ ]
        }
      ]
    }
}
```

如此示例中所示，关键短语和实体已建模到不同的表中，将包含对每行的父级 (MainTable) 的反向引用。

## <a name="object-projections"></a>对象投影

对象投影是可以从任何节点寻源的扩充树的 JSON 表示形式。 在许多情况下，可以使用用于创建表投影的同一个“整形程序”技能来生成对象投影。**** 

```json
{
    "name": "your-skillset",
    "skills": [
      …your skills
    ],
"cognitiveServices": {
… your cognitive services key info
    },

    "knowledgeStore": {
      "storageConnectionString": "an Azure storage connection string",
      "projections" : [
        {
          "tables": [ ]
        },
        {
          "objects": [
            {
              "storageContainer": "hotelreviews", 
              "source": "/document/hotel"
            }
          ]
        },
        {
            "files": [ ]
        }
      ]
    }
}
```

生成对象投影需要几个特定于对象的属性：

+ 存储容器：将保存对象的 blob 容器
+ 源：作为投影根的富集树节点的路径

## <a name="file-projection"></a>文件投影

文件投影与对象投影类似，仅对`normalized_images`集合执行。 与对象投影类似，文件投影保存在 Blob 容器中，其文件夹前缀为文档 ID 的 base64 编码值。 文件投影不能与对象投影共享同一容器，需要投影到其他容器中。

```json
{
    "name": "your-skillset",
    "skills": [
      …your skills
    ],
"cognitiveServices": {
… your cognitive services key info
    },

    "knowledgeStore": {
      "storageConnectionString": "an Azure storage connection string",
      "projections" : [
        {
          "tables": [ ]
        },
        {
          "objects": [ ]
        },
        {
            "files": [
                 {
                  "storageContainer": "ReviewImages",
                  "source": "/document/normalized_images/*"
                }
            ]
        }
      ]
    }
}
```

## <a name="projection-lifecycle"></a>投影生命周期

投影具有一个与数据源中的源数据绑定的生命周期。 更新数据并重新编制索引后，投影会随着扩充结果进行更新，确保您的预测最终与数据源中的数据一致。 投影将继承已为索引配置的删除策略。 删除索引器或搜索服务本身时，不会删除投影。

## <a name="using-projections"></a>使用投影

运行索引器后，可以在通过投影指定的容器或表中读取投影的数据。

分析时，可以方便地在 Power BI 中浏览数据，只需将 Azure 表存储设置为数据源即可。 您可以使用 其中的关系轻松地在数据上创建一组可视化效果。

或者，如果需要在数据科学管道中使用富集数据，则可以[将来自 Blob 的数据加载到熊猫数据框架](../machine-learning/team-data-science-process/explore-data-blob.md)中。

最后，如果需要从知识存储导出数据，可以使用 Azure 数据工厂提供的连接器来导出数据，然后将其载入所选的数据库。 

## <a name="next-steps"></a>后续步骤

接下来，请使用示例数据遵照说明创建第一个知识存储。

> [!div class="nextstepaction"]
> [在 REST 中创建知识存储](knowledge-store-create-rest.md)。

有关高级投影概念（如切片、内联整形和关系）的教程，请从[知识存储中定义投影](knowledge-store-projections-examples.md)开始

> [!div class="nextstepaction"]
> [定义知识存储中的投影](knowledge-store-projections-examples.md)
