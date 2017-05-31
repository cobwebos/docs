---
redirect_url: https://azure.microsoft.com/services/cosmos-db/
ROBOTS: NOINDEX, NOFOLLOW
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: a6b0cd10c13622bf0d5af5d5871457204de37eb1
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017



---
# <a name="how-to-create-a-database-for-azure-cosmos-db-using-the-azure-portal"></a>如何使用 Azure 门户创建 Azure Cosmos DB 数据库
若要使用 Microsoft Azure Cosmos DB，必须拥有 [Azure Cosmos DB 帐户](documentdb-create-account.md)、数据库、集合和文档。 现在，在 Azure 门户中创建集合的同时，会创建 Azure Cosmos DB 数据库。 

若要使用 Azure 门户创建 Azure Cosmos DB 数据库和集合，请参阅[如何使用 Azure 门户创建 Azure Cosmos DB 集合](documentdb-create-collection.md)。

## <a name="other-ways-to-create-an-azure-cosmos-db-database"></a>创建 Azure Cosmos DB 数据库的其他方法
不一定要使用门户来创建数据库，也可以使用 [DocumentDB SDK](documentdb-sdk-dotnet.md) 或 [REST API](https://msdn.microsoft.com/library/mt489072.aspx) 来创建数据库。 有关使用 .NET SDK 处理数据库的信息，请参阅 [.NET 数据库示例](documentdb-dotnet-samples.md#database-examples)。 有关使用 Node.js SDK 处理数据库的信息，请参阅 [Node.js 数据库示例](documentdb-nodejs-samples.md#database-examples)。 

## <a name="next-steps"></a>后续步骤
创建数据库和集合后，可以使用门户中的文档资源管理器来[添加 JSON 文档](documentdb-view-json-document-explorer.md)，使用 DocumentDB 数据迁移工具向集合[导入文档](documentdb-import-data.md)，或使用某个 [DocumentDB SDK](documentdb-sdk-dotnet.md) 来执行 CRUD 操作。 DocumentDB 有 .NET、Java、Python、Node.js 和 JavaScript API SDK。 有关说明如何创建、移除、更新和删除文档的 .NET 代码示例，请参阅 [.NET 文档示例](documentdb-dotnet-samples.md#document-examples)。 有关使用 Node.js SDK 处理文档的信息，请参阅 [Node.js 文档示例](documentdb-nodejs-samples.md#document-examples)。 

当集合中有文档后，就可以利用门户中的[查询资源管理器](documentdb-query-collections-query-explorer.md)、[REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) 或某个 [SDK](documentdb-sdk-dotnet.md)来针对文档使用 [DocumentDB SQL](documentdb-sql-query.md) [执行查询](documentdb-sql-query.md#ExecutingSqlQueries)。 


