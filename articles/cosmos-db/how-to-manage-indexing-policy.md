---
title: 了解如何在 Azure Cosmos DB 中管理数据库帐户
description: 了解如何在 Azure Cosmos DB 中管理数据库帐户
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/10/2018
ms.author: mjbrown
ms.openlocfilehash: c27cee4842c0e65e1737f100a215cff82a0fd439
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2019
ms.locfileid: "54033088"
---
# <a name="manage-indexing-in-azure-cosmos-db"></a>管理 Azure Cosmos DB 中的索引编制

在 Azure Cosmos DB 中，可以选择是否希望容器自动为所有项编制索引。 默认情况下，将会为 Azure Cosmos 容器中的所有项自动编制索引，但可以关闭自动索引功能。 关闭索引编制功能后，可以通过本身的链接或通过使用项 ID（例如文档 ID）进行查询的方式来访问项。可以显式请求系统提供结果，不需使用索引，方法是：在 REST API 中传入 **x-ms-documentdb-enable-scan** 标头，或者通过 .NET SDK 传入 **EnableScanInQuery** 请求选项。

关闭自动索引后，仍然可以选择性地向索引添加特定项。 与之相反，可以让自动索引处于打开状态，并选择排除特定的项。 需要查询项子集时，索引开/关配置非常有用。  

写入吞吐量和请求单位数与需要编制索引的值的数目成正比，该数目可以在索引编制策略中通过包含的集来指定。 如果很了解查询模式，则可显式选择路径的包括/排除子集，以便提高写入吞吐量。

## <a name="manage-indexing-using-azure-portal"></a>使用 Azure 门户管理索引编制

1. 登录到 [Azure 门户](https://portal.azure.com/)。

2. 创建新的 Azure Cosmos 帐户或选择现有的帐户。

3. 打开“数据资源管理器”窗格。

4. 选择一个现有的容器，将其展开并修改以下值：

   * 打开“规模和设置”窗口。
   * 将 **indexingMode** 从 *consistent* 更改为 *none*，或者包括/排除索引编制中的特定路径。
   * 单击“确定”以保存更改  。

   ![使用 Azure 门户管理索引编制](./media/how-to-manage-indexing/how-to-manage-indexing-portal.png)

## <a name="manage-indexing-using-azure-sdks"></a>使用 Azure SDK 管理索引编制

### <a id="dotnet"></a>.NET SDK

下面的示例演示了如何使用 [SQL API .NET SDK](sql-api-sdk-dotnet.md) 和 [RequestOptions.IndexingDirective](/dotnet/api/microsoft.azure.documents.client.requestoptions.indexingdirective) 属性来显式包括项。

```csharp
// To override the default behavior to exclude (or include) a document in indexing,
// use the RequestOptions.IndexingDirective property.
client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),
    new { id = "myDocumentId", isRegistered = true },
    new RequestOptions { IndexingDirective = IndexingDirective.Include });
```

## <a name="next-steps"></a>后续步骤

阅读以下文章中有关索引的详细信息：

* [索引概览](index-overview.md)
* [索引策略](index-policy.md)
* [索引类型](index-types.md)
* [索引路径](index-paths.md)
