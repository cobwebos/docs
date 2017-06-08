---
title: "如何在 Azure 搜索中为复杂数据类型建模 | Microsoft Docs"
description: "可以在 Azure 搜索索引中使用平展行集和集合数据类型对嵌套或分层数据结构建模。"
services: search
documentationcenter: 
author: LiamCa
manager: pablocas
editor: 
tags: complex data types; compound data types; aggregate data types
ms.assetid: e4bf86b4-497a-4179-b09f-c1b56c3c0bb2
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/01/2017
ms.author: liamca
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: d576fd7bb267ae7a100589413185b595e3b2be42
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017


---
# <a name="how-to-model-complex-data-types-in-azure-search"></a>如何在 Azure 搜索中为复杂数据类型建模
用于填充 Azure 搜索索引的外部数据集有时包括分层或嵌套子结构，此种结构无法整齐地分解为表格行集。 此类结构可能包括单个客户的多个位置和电话号码、单个 SKU 的多个颜色和大小、一本书籍的多位作者等等。 在建模术语中，可能会看到称为*复杂数据类型*、*复合数据类型*、*复合数据类型*或*聚合数据类型*，仅举几例。

Azure 搜索本身并不支持复杂数据类型，但经验证的解决方法包括以下两步骤过程：平展该结构，然后使用**集合**数据类型重构内部结构。 遵循本文中所述技术，可允许内容被搜索、查找、筛选和排序。

## <a name="example-of-a-complex-data-structure"></a>复杂数据结构示例
通常，相关数据驻留为一组 JSON 或 XML 文档，或者驻留为 NoSQL 存储（如 Azure Cosmos DB）中的项目。 在结构上，挑战来自于具有需要搜索和筛选的多个子项目。  开始说明解决方法，请将列出一组联系人的以下 JSON 文档用作示例：

~~~~~
[
  {
    "id": "1",
    "name": "John Smith",
    "company": "Adventureworks",
    "locations": [
      {
        "id": "1",
        "description": "Adventureworks Headquarters"
      },
      {
        "id": "2",
        "description": "Home Office"
      }
    ]
  }, 
  {
    "id": "2",
    "name": "Jen Campbell",
    "company": "Northwind",
    "locations": [
      {
        "id": "3",
        "description": "Northwind Headquarter"
      },
      {
        "id": "4",
        "description": "Home Office"
      }
    ]
}]
~~~~~

虽然名为“id”、“name”和“company”的字段可以轻松地一对一映射为 Azure 搜索索引中的字段，但“locations”字段包含一个位置数组，其中具有一组位置 ID 以及位置描述。 鉴于 Azure 搜索没有支持此结构的数据类型，我们需要使用其他方法以在 Azure 搜索中对此结构建模。 

> [!NOTE]
> 另外，此技术由 Kirk Evans 在[使用 Azure 搜索索引 DocumentDB](https://blogs.msdn.microsoft.com/kaevans/2015/03/09/indexing-documentdb-with-azure-seach/) 博客文章中进行了介绍，其中演示了一种称为“平展数据”的技术，由此得到名为 `locationsID` 和 `locationsDescription` 的字段（两者都是[集合](https://msdn.microsoft.com/library/azure/dn798938.aspx)或字符串数组）。   
> 
> 

## <a name="part-1-flatten-the-array-into-individual-fields"></a>第 1 部分：将数组平展为个别字段
若要创建适合此数据集的 Azure 搜索，请为嵌套子结构创建个别字段：`locationsID` 和 `locationsDescription`（数据类型为[集合](https://msdn.microsoft.com/library/azure/dn798938.aspx)或字符串数组）。 在这些字段中，将值“1”和“2”索引到 John Smith 的 `locationsID` 字段，将值“3”和“4”索引到 Jen Campbell 的 `locationsID` 字段。  

Azure 搜索中的数据将如下所示： 

![示例数据，2 行](./media/search-howto-complex-data-types/sample-data.png)

## <a name="part-2-add-a-collection-field-in-the-index-definition"></a>第 2 部分：在索引定义中添加集合字段
在索引架构中，字段定义可能与此示例类似。

~~~~
var index = new Index()
{
    Name = indexName,
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("name", DataType.String) { IsSearchable = true, IsFilterable = false, IsSortable = false, IsFacetable = false },
        new Field("company", DataType.String) { IsSearchable = true, IsFilterable = false, IsSortable = false, IsFacetable = false },
        new Field("locationsId", DataType.Collection(DataType.String)) { IsSearchable = true, IsFilterable = true, IsFacetable = true },
        new Field("locationsDescription", DataType.Collection(DataType.String)) { IsSearchable = true, IsFilterable = true, IsFacetable = true }
    }
};
~~~~

## <a name="validate-search-behaviors-and-optionally-extend-the-index"></a>验证搜索行为并选择性地扩展索引
假设已创建了索引并加载了数据，现在可以测试解决方案，以验证针对数据集的搜索查询执行。 每个 **collection** 字段应**可搜索**、**可筛选**和**可查找**。 应能够运行如下所示的查询：

* 查找在“Adventureworks Headquarters”工作的所有人员。
* 获取在“Home Office”工作的人数。  
* 在“Home Office”工作的这些人中，显示他们工作的其他办公室以及每个地点的人数。  

在你需要执行结合位置 ID 以及位置描述的查询时，此技术将崩溃。 例如：

* 查找在 Home Office 工作且位置 ID 为 4 的所有人员。  

如果还记得如下所示的原始内容：

~~~~
   {
        id: '4',
        description: 'Home Office'
   }
~~~~

不过，由于我们已将数据分成单独的字段，我们无从知道 Jen Campbell 的 Home Office 是否与 `locationsID 3` 或 `locationsID 4` 相关。  

若要处理这种情况，请在将所有数据合并为单个集合的索引中定义另一个字段。  对于我们的示例，我们将调用此字段 `locationsCombined`，并且我们将使用 `||` 分隔内容，虽说可以选择任何你认为是内容的一组独特字符的分隔符。 例如： 

![示例数据，带分隔符的 2 行](./media/search-howto-complex-data-types/sample-data-2.png)

通过使用此 `locationsCombined` 字段，我们现在可以适应更多查询，例如：

* 显示在“Home Office”工作、位置 ID 为“4”的人数。  
* 搜索在“Home Office”工作、位置 ID 为“4”的人员。 

## <a name="limitations"></a>限制
这种技术对于若干情形非常有用，但它不适用于每种情况。  例如：

1. 如果在复杂数据类型中没有一组静态字段，并且无法将所有可能类型映射到单个字段。 
2. 更新嵌套对象需要一些额外的工作，才能够准确确定需要在 Azure 搜索索引中更新的内容

## <a name="sample-code"></a>代码示例
可以看到有关如何将复杂 JSON 数据集索引到 Azure 搜索的示例，并对此 [GitHub 存储库](https://github.com/liamca/AzureSearchComplexTypes)中的此数据集执行大量查询。

## <a name="next-step"></a>后续步骤
在 Azure 搜索 UserVoice 页上[对复杂数据类型的本机支持进行投票](https://feedback.azure.com/forums/263029-azure-search)，并提供你希望我们考虑关于功能实现的任何其他输入。 还可以直接在 Twitter 通过 @liamca 与我联系。


