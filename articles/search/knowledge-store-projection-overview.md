---
title: 知识存储（预览版）中的投影
titleSuffix: Azure Cognitive Search
description: 将 AI 扩充索引编制管道中的扩充数据保存并整型到知识存储中，以便在除了全文搜索以外的方案中使用。 知识存储目前以公开预览版提供。
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: d264768bf27967d1a778400ae4e9e6f2e054d746
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "78942976"
---
# <a name="projections-in-a-knowledge-store-in-azure-cognitive-search"></a>Azure 认知搜索中的知识存储中的投影

> [!IMPORTANT] 
> 知识存储目前以公开预览版提供。 提供的预览版功能不附带服务级别协议，我们不建议将其用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 [REST API 版本 2019-05-06-Preview](search-api-preview.md) 提供预览版功能。 目前提供有限的门户支持，不提供 .NET SDK 支持。

Azure 认知搜索通过内置认知技能和自定义技能作为索引的一部分，扩充内容。 根据创建以前不存在的新信息：从图像中提取信息，检测情绪、关键短语和文本中的实体，只需对其进行命名。 根据还会将结构添加到无差别文本。 所有这些过程都将导致使全文搜索更有效的文档。 在许多情况下，对于除搜索以外的方案（例如用于知识挖掘），已丰富的文档很有用。

投影（[知识存储](knowledge-store-concept-intro.md)的一个组件）是可以保存到物理存储的、用于实现知识挖掘的扩充文档的视图。 使用投影可将数据“投影”到符合需求的形状，并保持相应的关系，使 Power BI 等工具能够在不增大负载的情况下读取数据。

投影可以是表格式的，其中的数据存储在 Azure Blob 存储中的行与列中；也可以是存储在 Azure 表存储中的 JSON 对象。 可以在扩充数据时定义数据的多个投影。 如果希望单个用例的数据形状不同，则多个投影会很有用。

知识存储支持三种类型的投影：

+ **表**：对于最能表示为行和列的数据，表投影允许在表存储中定义架构化形状或投影。 只有有效的 JSON 对象可以投影为表，已扩充的文档可以包含未命名为 JSON 对象的节点，并在投影这些对象时，使用整形程序技能或内联造型创建有效的 JSON 对象。

+ **对象**：如果需要数据和根据的 JSON 表示形式，则会将对象投影保存为 blob。 只有有效的 JSON 对象可以投影为对象，已扩充的文档可以包含未命名为 JSON 对象的节点，并在投影这些对象时，使用整形程序技能或内联造型创建有效的 JSON 对象。

+ **文件**：当需要保存从文档中提取的图像时，文件投影允许您将规范化映像保存到 blob 存储中。

若要查看在上下文中定义的投影，请单步执行[在 REST 中创建知识库](knowledge-store-create-rest.md)。

## <a name="projection-groups"></a>投影组

在某些情况下，你需要投影采用不同形状的扩充数据，以符合不同的目标。 使用知识存储可以定义多个投影组。 投影组具有以下重要的互斥性和相关性特征。

### <a name="mutual-exclusivity"></a>相互独占性

投影到单个组的所有内容独立于投影到其他投影组的数据。
这种独立性意味着，你可以让相同的数据形状不同，并且在每个投影组中重复。

### <a name="relatedness"></a>相关性

投影组现在允许你跨投影类型投影文档，同时保留跨投影类型的关系。 在单个投影组内投影的所有内容都将跨投影类型保留数据中的关系。 在表中，关系基于生成的键，每个子节点保留对父节点的引用。 跨类型（表、对象和文件），当跨不同类型投影单个节点时，将保留关系。 例如，假设有一个文档包含图像和文本。 您可以为表或对象创建文本，并将图像投影到其中的表或对象具有包含文件 URL 的列/属性的文件。

## <a name="input-shaping"></a>输入整形

获取采用适当形状或结构（表或对象）的数据对于有效利用数据而言至关重要。 根据访问和使用数据的方式塑造或结构化数据的功能，是在技能集中作为“整形程序”公开的关键功能。****  

如果扩充树中存在与投影架构匹配的对象，则可以更轻松地定义投影。 使用更新的[整形程序技能](cognitive-search-skill-shaper.md)可以从扩充树的不同节点编写对象，并将其指定为新节点下的父级。 使用“整形程序”技能可以定义包含嵌套对象的复杂类型。****

如果定义的新形状包含需要投影出的所有元素，则你现在可以使用此形状作为投影的源，或作为另一技能的输入。

## <a name="projection-slicing"></a>投影切片

定义投影组时，可以将扩充树中的单个节点切片为多个相关的表或对象。 添加源路径为现有投影的子节点的投影将导致子节点从父节点上切分并投影到新的相关表或对象。 利用此方法，您可以在可以作为所有投影的源的整形程序技能中定义单个节点。

## <a name="table-projections"></a>表投影

我们建议在 Power BI 中使用表投影浏览数据，因为这可以更方便地导入。 另外，表投影允许更改表关系之间的基数。 

可将索引中的单个文档投影到多个表，并保留关系。 投影到多个表时，除非子节点是同一个组中其他表的源，否则整个形状将投影到每个表。

### <a name="defining-a-table-projection"></a>定义表投影

在技能集的 `knowledgeStore` 元素中定义表投影时，请先将扩充树中的某个节点映射到表源。 此节点通常是添加到技能列表的、用于生成需要投影到表的“整形程序”技能的输出。**** 选择投影的节点可以分片，以投影到多个表。 表定义是要投影的表列表。

每个表需要三个属性：

+ tableName： Azure 存储中表的名称。

+ generatedKeyName：唯一标识此行的键的列名。

+ 源：你从其扩充的根据的节点。 此节点通常是整形者的输出，但可以是任何技能的输出。

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

+ storageContainer：将在其中保存对象的 blob 容器
+ 源：扩充树的节点的路径，该节点是投影的根

## <a name="file-projection"></a>文件投影

文件投影类似于对象投影，只对`normalized_images`集合进行操作。 与对象投影类似，文件投影保存在 blob 容器中，其文件夹前缀为文档 ID 的 base64 编码值。 文件投影不能与对象投影共享同一个容器，需要将其投影到不同的容器。

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

投影具有一个与数据源中的源数据绑定的生命周期。 随着数据的更新和重新编制索引，将用根据的结果更新投影，确保预测最终与数据源中的数据保持一致。 投影继承为索引配置的删除策略。 删除索引器或搜索服务本身时不会删除投影。

## <a name="using-projections"></a>使用投影

运行索引器后，可以在通过投影指定的容器或表中读取投影的数据。

分析时，可以方便地在 Power BI 中浏览数据，只需将 Azure 表存储设置为数据源即可。 您可以使用中的关系轻松地创建一组针对您的数据的可视化效果。

或者，如果您需要在数据科学管道中使用大量数据，则可以将[blob 中的数据加载到 Pandas 数据帧](../machine-learning/team-data-science-process/explore-data-blob.md)中。

最后，如果需要从知识存储导出数据，可以使用 Azure 数据工厂提供的连接器来导出数据，然后将其载入所选的数据库。 

## <a name="next-steps"></a>后续步骤

接下来，请使用示例数据遵照说明创建第一个知识存储。

> [!div class="nextstepaction"]
> [在 REST 中创建知识库](knowledge-store-create-rest.md)。

有关涉及切片、内联造型和关系等高级投影概念的教程，请从[在知识存储中定义投影](knowledge-store-projections-examples.md)开始

> [!div class="nextstepaction"]
> [定义知识库中的投影](knowledge-store-projections-examples.md)
