---
title: 使用知识库中的预测（预览）
titleSuffix: Azure Cognitive Search
description: 保存并绘制 AI 扩充索引管道中的已扩充数据，以便在除全文搜索以外的其他方案中使用。
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 8e6c285e8917c4224e2007c565d5ac5447b20853
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72789997"
---
# <a name="working-with-projections-in-a-knowledge-store-in-azure-cognitive-search"></a>在 Azure 中使用知识库中的预测认知搜索

> [!Note]
> 知识存储目前为预览版，不适合在生产环境中使用。 [REST API 版本 2019-05-06-Preview](search-api-preview.md) 提供了此功能。 目前不支持 .NET SDK。
>

Azure 认知搜索通过内置认知技能和自定义技能作为索引的一部分，扩充内容。 根据将结构添加到文档，使搜索更有效。 在许多情况下，扩充的文档对于除搜索以外的方案非常有用，例如用于知识挖掘。

"知识库" 是 "[知识存储](knowledge-store-concept-intro.md)" 的一个组件，它是可保存到物理存储中以供知识库挖掘使用的丰富文档的视图。 使用投影，您可以将数据 "投影" 到满足您的需求的形状，同时保留关系，以便 Power BI 的工具可以读取数据而无需额外的精力。

投影可以是表格形式的，其中的数据存储在 Azure 表存储中的行和列中，或存储在 Azure Blob 存储中的 JSON 对象。 您可以在数据被充实时定义多个数据投影。 如果希望单个用例的数据形状不同，则多个投影会很有用。

知识存储支持三种类型的投影：

+ **表**：对于最能表示为行和列的数据，表投影允许在表存储中定义架构化形状或投影。

+ **对象**：如果需要数据和根据的 JSON 表示形式，则会将对象投影保存为 blob。

+ **文件**：当需要保存从文档中提取的图像时，文件投影允许您保存规范化的映像。

若要查看在上下文中定义的投影，请逐步了解[如何开始使用知识 store](knowledge-store-howto.md)。

## <a name="projection-groups"></a>投影组

在某些情况下，需要在不同的形状中投影您的数据，以满足不同的目标。 知识库允许您定义多组投影。 投影组具有独占性和关联性的以下重要特性。

### <a name="mutual-exclusivity"></a>相互独占性

投影到单个组中的所有内容都独立于投影到其他投影组中的数据。
这种独立性意味着，你可以让相同的数据形状不同，并且在每个投影组中重复。

### <a name="relatedness"></a>关联性

投影组现在允许你跨投影类型投影文档，同时保留跨投影类型的关系。 在单个投影组内投影的所有内容都将跨投影类型保留数据中的关系。 在表中，关系基于生成的键，每个子节点保留对父节点的引用。 跨类型（表、对象和文件），当跨不同类型投影单个节点时，将保留关系。 例如，假设有一个文档包含图像和文本。 您可以为表或对象创建文本，并将图像投影到其中的表或对象具有包含文件 URL 的属性的文件。

## <a name="input-shaping"></a>输入造型

以正确的形状或结构获取数据是有效使用的关键，就是表或对象。 基于你计划访问和使用数据的方式对数据进行形状或结构设计的功能是在技能组合中作为**整形**程序技能公开的一项关键功能。  

当在扩充树中具有与投影架构匹配的对象时，可以更轻松地定义投影。 更新的[整形程序技能](cognitive-search-skill-shaper.md)使你可以从扩充树的不同节点撰写对象，并将其作为新节点的父对象。 利用**整形**技能，你可以通过嵌套对象定义复杂类型。

如果定义了一个新的形状，其中包含需要投影的所有元素，则现在可以将此形状用作投影的源，或用作其他技能的输入。

## <a name="projection-slicing"></a>投影切片

定义投影组时，可以将扩充树中的单个节点切片为多个相关的表或对象。 添加源路径为现有投影的子节点的投影将导致子节点从父节点上切分并投影到新的相关表或对象。 利用此方法，您可以在可以作为所有投影的源的整形程序技能中定义单个节点。

## <a name="table-projections"></a>表投影

由于这使得导入更简单，因此建议通过 Power BI 来浏览数据。 另外，表投影允许更改表关系之间的基数。 

您可以将索引中的单个文档投影到多个表中，从而保留这些关系。 投影到多个表时，整个形状将投影到每个表中，除非子节点是同一组内另一个表的源。

### <a name="defining-a-table-projection"></a>定义表投影

在技能组合的 `knowledgeStore` 元素中定义表投影时，首先将扩充树上的节点映射到表源。 通常，此节点是添加到技能列表中的**整形**程序技能的输出，用于生成需要投影到表中的特定形状。 选择要投影到的节点可以切分到多个表中。 表定义是要投影的表的列表。

每个表都需要三个属性：

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

如本示例中所示，关键短语和实体将建模为不同的表，并且将包含返回给每行的父（MainTable）的引用。

下图是[有关如何开始使用知识库](knowledge-store-howto.md)的示例中的示例。 在案例有多个观点的情况下，每个观点通过标识其中包含的实体来得到充实，您可以按照此处所示对投影建模。

![表中的实体和关系](media/knowledge-store-projection-overview/TableRelationships.png "表投影中的建模关系")

## <a name="object-projections"></a>对象投影

对象投影是扩充树的 JSON 表示形式，可源自任何节点。 在许多情况下，创建表投影的相同**整形**程序技能可用于生成对象投影。 

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
              "storageContainer": "Reviews", 
              "format": "json", 
              "source": "/document/Review", 
              "key": "/document/Review/Id" 
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

+ storageContainer：将在其中保存对象的容器
+ 源：扩充树的节点的路径，该节点是投影的根
+ key：一个路径，表示要存储的对象的唯一键。 它将用于在容器中创建 blob 的名称。

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

您的投影具有与数据源中的源数据相关联的生命周期。 随着数据的更新和重新编制索引，将用根据的结果更新投影，确保预测最终与数据源中的数据保持一致。 投影继承为索引配置的删除策略。 删除索引器或搜索服务本身时不会删除投影。

## <a name="using-projections"></a>使用投影

在运行索引器后，您可以读取通过投影指定的容器或表中的投影数据。

对于分析，Power BI 中的浏览就像将 Azure 表存储设置为数据源一样简单。 您可以使用中的关系轻松地创建一组针对您的数据的可视化效果。

或者，如果您需要在数据科学管道中使用大量数据，则可以将[blob 中的数据加载到 Pandas 数据帧](../machine-learning/team-data-science-process/explore-data-blob.md)中。

最后，如果需要从知识库中导出数据，Azure 数据工厂会提供用于导出数据的连接器，并将数据存储在所选数据库中。 

## <a name="next-steps"></a>后续步骤

下一步，使用示例数据和说明创建第一个知识存储。

> [!div class="nextstepaction"]
> [如何创建知识库](knowledge-store-howto.md)。
