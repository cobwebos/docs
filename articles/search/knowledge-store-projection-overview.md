---
title: 投影概念
titleSuffix: Azure Cognitive Search
description: 将 AI 扩充索引编制管道中的扩充数据保存并整型到知识存储中，以便在除了全文搜索以外的方案中使用。
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 22db4f95bacd926208ac7edf3306cd136d81b00e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85565190"
---
# <a name="knowledge-store-projections-in-azure-cognitive-search"></a>Azure 认知搜索中的知识存储“投影”

Azure 认知搜索允许通过编制索引功能附带的内置认知技能和自定义技能来扩充内容。 扩充创建以前不存在的新信息：从图像中提取信息，从文本中检测情感、关键短语和实体等等。 扩充还向无差别文本中添加结构。 所有这些过程将产生使全文搜索更有效的文档。 在许多情况下，扩充的文档可用于除搜索以外的方案，例如知识挖掘。

投影（[知识存储](knowledge-store-concept-intro.md)的一个组件）是可以保存到物理存储的、用于实现知识挖掘的扩充文档的视图。 使用投影可将数据“投影”到符合需求的形状，并保持相应的关系，使 Power BI 等工具能够在不增大负载的情况下读取数据。

投影可以是表格式的，其中的数据存储在 Azure Blob 存储中的行与列中；也可以是存储在 Azure 表存储中的 JSON 对象。 可以在扩充数据时定义数据的多个投影。 希望以不同的方式为各种用例塑造相同的数据时，多个投影非常有用。

知识存储支持三种类型的投影：

+ **表**：对于最好是以行和列表示的数据，可以使用表投影在表存储中定义架构化的形状或投影。 只有有效的 JSON 对象可以投影为表，扩充的文档可以包含不是已命名 JSON 对象的节点，并在将这些对象投影时，使用整形程序技能或内联整形创建有效的 JSON 对象。

+ **对象**：需要数据和扩充内容的 JSON 表示形式时，可将对象投影保存为 Blob。 只有有效的 JSON 对象可以投影为对象，扩充的文档可以包含不是已命名 JSON 对象的节点，并在将这些对象投影时，使用整形程序技能或内联整形创建有效的 JSON 对象。

+ **文件**：当需要保存从文档中提取的图像时，文件投影允许你将规范化图像保存到 blob 存储中。

若要查看上下文中定义的投影，请执行[在 REST 中创建知识存储](knowledge-store-create-rest.md)中的每个步骤。

## <a name="projection-groups"></a>投影组

在某些情况下，你需要投影采用不同形状的扩充数据，以符合不同的目标。 使用知识存储可以定义多个投影组。 投影组具有以下重要的互斥性和相关性特征。

### <a name="mutual-exclusivity"></a>互斥性

投影到单个组的所有内容独立于投影到其他投影组的数据。
此独立性意味着，能够以不同的方式塑造相同的数据，不过需要在每个投影组中重复该操作。

### <a name="relatedness"></a>相关性

投影组现在允许你跨投影类型来将文档投影，同时跨投影类型保留关系。 在单个投影组中投影的所有内容跨投影类型保留数据中的关系。 在表中，关系基于生成的键，每个子节点保留对父节点的引用。 跨类型（表、对象和文件）时，当跨不同类型对单个节点进行投影时，关系将保留。 例如，假设有一个文档包含图像和文本。 你可以将文本投影到表或对象，将图像投影到文件，但文件中的表或对象必须具有包含文件 URL 的列/属性。

## <a name="input-shaping"></a>输入整形

获取采用适当形状或结构（表或对象）的数据对于有效利用数据而言至关重要。 根据访问和使用数据的方式塑造或结构化数据的功能，是在技能集中作为“整形程序”公开的关键功能。  

如果扩充树中存在与投影架构匹配的对象，则可以更轻松地定义投影。 使用更新的[整形程序技能](cognitive-search-skill-shaper.md)可以从扩充树的不同节点编写对象，并将其指定为新节点下的父级。 使用“整形程序”技能可以定义包含嵌套对象的复杂类型。

如果定义的新形状包含需要投影出的所有元素，则你现在可以使用此形状作为投影的源，或作为另一技能的输入。

## <a name="projection-slicing"></a>投影切片

定义投影组时，可将扩充树中的单个节点切片为多个相关的表或对象。 添加源路径为现有投影的子级的投影将导致子节点从父节点中分割出来，并投影到新的但相关的表或对象中。 利用此技术，你可以使用整形程序技能定义单个节点，该节点可以作为所有投影的源。

## <a name="table-projections"></a>表投影

我们建议在 Power BI 中使用表投影浏览数据，因为这可以更方便地导入。 此外，表投影允许更改表关系之间的基数。 

可将索引中的单个文档投影到多个表，并保留关系。 投影到多个表时，除非子节点是同一个组中其他表的源，否则整个形状将投影到每个表。

### <a name="defining-a-table-projection"></a>定义表投影

在技能集的 `knowledgeStore` 元素中定义表投影时，请先将扩充树中的某个节点映射到表源。 此节点通常是添加到技能列表的、用于生成需要投影到表的“整形程序”技能的输出。 选择投影的节点可以分片，以投影到多个表。 表定义是要投影的表列表。

每个表需要三个属性：

+ tableName：Azure 存储中的表名称。

+ generatedKeyName：用于唯一标识此行的键的列名。

+ source：扩充树中用作扩充来源的节点。 此节点通常是整形程序的输出，但也可能是任何技能的输出。

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

对象投影是可以从任何节点寻源的扩充树的 JSON 表示形式。 在许多情况下，可以使用用于创建表投影的同一个“整形程序”技能来生成对象投影。 

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

+ storageContainer：对象将保存到的 blob 容器
+ source：扩充树中充当投影根的节点的路径

## <a name="file-projection"></a>文件投影

文件投影类似于对象投影，只对 `normalized_images` 集合进行操作。 与对象投影类似，文件投影保存在 blob 容器中，其文件夹前缀为文档 ID 的 base64 编码值。 文件投影不能与对象投影共享同一个容器，需要将其投影到不同的容器。

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

投影具有一个与数据源中的源数据绑定的生命周期。 更新数据并重建其索引时，将使用扩充的结果更新投影，以确保投影最终与数据源中的数据保持一致。 投影继承你为索引配置的删除策略。 删除索引器或搜索服务本身时不会删除投影。

## <a name="using-projections"></a>使用投影

运行索引器后，可以在通过投影指定的容器或表中读取投影的数据。

分析时，可以方便地在 Power BI 中浏览数据，只需将 Azure 表存储设置为数据源即可。 可以利用数据内部的关系，轻松地基于数据创建一组可视化效果。

另外，如果你需要在数据科学管道中使用扩充的数据，则可以[将数据从 blob 加载到 Pandas 数据帧](../machine-learning/team-data-science-process/explore-data-blob.md)。

最后，如果需要从知识存储导出数据，可以使用 Azure 数据工厂提供的连接器来导出数据，然后将其载入所选的数据库。 

## <a name="next-steps"></a>后续步骤

接下来，请使用示例数据遵照说明创建第一个知识存储。

> [!div class="nextstepaction"]
> [在 REST 中创建知识存储](knowledge-store-create-rest.md)。

有关涵盖高级投影概念（如切片、内联整形和关系）的教程，请先参阅[在知识存储中定义投影](knowledge-store-projections-examples.md)

> [!div class="nextstepaction"]
> [在知识存储中定义投影](knowledge-store-projections-examples.md)
