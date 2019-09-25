---
title: 使用知识存储（预览版）中的投影 - Azure 搜索
description: 保存并塑造 AI 索引管道中的扩充数据，以便在除搜索以外的方案中使用
manager: nitinme
author: vkurpad
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: vikurpad
ms.openlocfilehash: c5fb547b18bc4014f91341070f49c4af84c01005
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "71265186"
---
# <a name="working-with-projections-in-a-knowledge-store-in-azure-search"></a>在 Azure 搜索中使用知识存储中的投影

> [!Note]
> 知识存储目前以预览版提供，不适合在生产环境中使用。 [REST API 版本 2019-05-06-Preview](search-api-preview.md) 提供了此功能。 没有不提供任何 .NET SDK 支持。
>

Azure 搜索允许通过编制索引功能附带的 AI 认知技能和自定义技能来扩充内容。 扩充会将结构添加到文档，并提高搜索效率。 在许多情况下，扩充的文档可用于除搜索以外的方案，例如知识挖掘。

投影（[知识存储](knowledge-store-concept-intro.md)的一个组件）是可以保存到物理存储的、用于实现知识挖掘的扩充文档的视图。 使用投影可将数据“投影”到符合需求的形状，并保持相应的关系，使 Power BI 等工具能够在不增大负载的情况下读取数据。 

投影可以是表格式的，其中的数据存储在 Azure Blob 存储中的行与列中；也可以是存储在 Azure 表存储中的 JSON 对象。 可以在扩充数据时定义数据的多个投影。 希望以不同的方式为各种用例塑造相同的数据时，这种做法非常有效。 

知识存储支持两种类型的投影：

+ **表**：对于最好是以行和列表示的数据，可以使用表投影在表存储中定义架构化的形状或投影。 

+ **对象**：需要数据和扩充内容的 JSON 表示形式时，可将对象投影保存为 Blob。

若要查看在上下文中定义的投影，请逐步了解[如何开始使用知识 store](knowledge-store-howto.md)。

## <a name="projection-groups"></a>投影组

在某些情况下，你需要投影采用不同形状的扩充数据，以符合不同的目标。 使用知识存储可以定义多个投影组。 投影组具有以下重要的互斥性和相关性特征。

### <a name="mutually-exclusivity"></a>互斥性

投影到单个组的所有内容独立于投影到其他投影组的数据。 这意味着，能够以不同的方式塑造相同的数据，不过需要在每个投影组中重复该操作。 

在投影组中施加的一个约束是投影组的投影类型互斥性。 在单个组中只能定义表投影或对象投影。 如果同时需要表和对象，请为表定义一个投影组，并为对象定义另一个投影组。

### <a name="relatedness"></a>相关性

在单个投影组中投影的所有内容保留数据内部的关系。 关系基于生成的键，每个子节点保留对父节点的引用。 关系不会跨越投影组，在一个投影组中创建的表或对象，与其他投影组中生成的数据没有关系。

## <a name="input-shaping"></a>输入整形
获取采用适当形状或结构（表或对象）的数据对于有效利用数据而言至关重要。 根据访问和使用数据的方式塑造或结构化数据的功能，是在技能集中作为“整形程序”公开的关键功能。  

如果扩充树中存在与投影架构匹配的对象，则可以更轻松地定义投影。 使用更新的[整形程序技能](cognitive-search-skill-shaper.md)可以从扩充树的不同节点编写对象，并将其指定为新节点下的父级。 使用“整形程序”技能可以定义包含嵌套对象的复杂类型。

如果定义的新形状包含需要投影出的所有元素，则你现在可以使用此形状作为投影的源，或作为另一技能的输入。

## <a name="table-projections"></a>表投影

我们建议在 Power BI 中使用表投影浏览数据，因为这可以更方便地导入。 此外，表投影允许更改表关系之间的基数。 

可将索引中的单个文档投影到多个表，并保留关系。 投影到多个表时，除非子节点是同一个组中其他表的源，否则整个形状将投影到每个表。

### <a name="defining-a-table-projection"></a>定义表投影

在技能集的 `knowledgeStore` 元素中定义表投影时，请先将扩充树中的某个节点映射到表源。 此节点通常是添加到技能列表的、用于生成需要投影到表的“整形程序”技能的输出。 选择投影的节点可以分片，以投影到多个表。 表定义是要投影的表列表。 

#### <a name="projection-slicing"></a>投影切片
定义表投影组时，可以将扩充树中的单个节点切片为多个相关表。 添加源路径为现有表投影的子节点的表将导致子节点从父节点上切分并投影到新的相关表中。 这使您可以在可以作为所有表投影的源的整形程序技能中定义单个节点。

每个表需要三个属性：

+ tableName：Azure 存储中的表名称。

+ generatedKeyName：用于唯一标识此行的键的列名。

+ source：扩充树中用作扩充来源的节点。 这通常是整形程序的输出，但也可能是任何技能的输出。

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
          "objects": [
            
          ]
        }
      ]
    }
}
```
如此示例中所示，关键短语和实体已建模到不同的表中，将包含对每行的父级 (MainTable) 的反向引用。 

下图显示了对[如何开始使用知识存储](knowledge-store-howto.md)中的 Caselaw 练习的引用。 如果某个案例包含多种观点，并且每种观点已通过标识其中包含的实体进行扩充，则你可以按如下所示为投影建模。

![表中的实体和关系](media/knowledge-store-projection-overview/TableRelationships.png "为表投影中的关系建模")

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
              "storageContainer": "Reviews", 
              "format": "json", 
              "source": "/document/Review", 
              "key": "/document/Review/Id" 
            }
          ]
        }
      ]
    }
}
```

生成对象投影需要几个特定于对象的属性：

+ storageContainer：对象要保存到的容器
+ source：扩充树中充当投影根的节点的路径
+ key：表示要存储的对象的唯一键的路径。 它将用于在容器中创建 Blob 的名称。

## <a name="projection-lifecycle"></a>投影生命周期

投影具有一个与数据源中的源数据绑定的生命周期。 更新数据并重建其索引时，将使用扩充的结果更新投影，以确保投影最终与数据源中的数据保持一致。 投影继承你为索引配置的删除策略。 

## <a name="using-projections"></a>使用投影

运行索引器后，可以在通过投影指定的容器或表中读取投影的数据。 

分析时，可以方便地在 Power BI 中浏览数据，只需将 Azure 表存储设置为数据源即可。 可以利用数据内部的关系，十分轻松地基于数据创建一组可视化效果。

或者，如果您需要在数据科学管道中使用大量数据，则可以将[blob 中的数据加载到 Pandas 数据帧](../machine-learning/team-data-science-process/explore-data-blob.md)中。

最后，如果需要从知识存储导出数据，可以使用 Azure 数据工厂提供的连接器来导出数据，然后将其载入所选的数据库。 

## <a name="next-steps"></a>后续步骤

接下来，请使用示例数据遵照说明创建第一个知识存储。

> [!div class="nextstepaction"]
> [如何创建知识存储](knowledge-store-howto.md)。
