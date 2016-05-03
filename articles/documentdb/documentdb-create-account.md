<properties
	pageTitle="创建 NoSQL 数据库帐户 — 免费试用版 | Microsoft Azure"
	description="了解如何使用在线数据库创建程序为 Azure DocumentDB（用于 JSON 的托管 NoSQL 文档数据库）创建数据库帐户。立即获取免费试用版。"
	keywords="免费试用版, 在线数据库创建程序, 创建数据库, 创建数据库, documentdb, azure, Microsoft azure"
	services="documentdb"
	documentationCenter=""
	authors="mimig1"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="documentdb"
	ms.date="02/23/2016"
	wacn.date=""/>

# 如何使用 Azure 门户创建 DocumentDB 数据库帐户

> [AZURE.SELECTOR]
- [Azure 门户](/documentation/articles/documentdb-create-account)
- [Azure CLI 和 ARM](/documentation/articles/documentdb-automation-resource-manager-cli)

若要使用 Microsoft Azure DocumentDB，你必须使用 Azure 门户、Azure Resource Manager 模板或 Azure 命令行界面 (CLI) 创建 DocumentDB 数据库帐户。本文说明如何使用 Azure 门户创建数据库帐户。若要使用 Azure Resource Manager 或 Azure CLI 创建帐户，请参阅[自动创建 DocumentDB 数据库帐户](/documentation/articles/documentdb-automation-resource-manager-cli)。

你是 DocumentDB 新手吗？ 观看 Scott Hanselman 制作的[这部](https://azure.microsoft.com/documentation/videos/create-documentdb-on-azure/)四分钟的视频，了解如何在在线门户中完成最常见的任务。

[AZURE.INCLUDE [documentdb-create-dbaccount](../includes/documentdb-create-dbaccount.md)]

## 后续步骤

现在你已经有了 DocumentDB 帐户，下一步是创建 DocumentDB 数据库。你可以使用下面其中一项来创建数据库：

- C# .NET 示例，位于 GitHub 上 [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples) 存储库的 [DatabaseManagement](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/DatabaseManagement) 项目中。
- Azure 门户，如[使用 Azure 门户创建 DocumentDB 数据库](/documentation/articles/documentdb-create-database)中所述。
- 综合教程：[.NET](/documentation/articles/documentdb-get-started)、[.NET MVC](documentdb-dotnet-application.md)、[Java](/documentation/articles/documentdb-java-application)、[Node.js](documentdb-nodejs-application.md) 或 [Python](/documentation/articles/documentdb-python-application)。
- [DocumentDB SDK](/documentation/articles/documentdb-sdk-dotnet)。DocumentDB 有 .NET、Java、Python、Node.js 和 JavaScript API SDK。


创建数据库后，你必须向数据库[添加一个或多个集合](/documentation/articles/documentdb-create-collection)，然后向集合[添加文档](/documentation/articles/documentdb-view-json-document-explorer)。

当集合中有文档后，你就可以利用门户中的[查询资源管理器](/documentation/articles/documentdb-query-collections-query-explorer)、[REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) 或某个 [SDK](/documentation/articles/documentdb-sdk-dotnet)，来针对文档使用 [DocumentDB SQL](/documentation/articles/documentdb-sql-query) [执行查询](/documentation/articles/documentdb-sql-query#executing-queries)。

若要详细了解 DocumentDB，请浏览以下资源：

-	[DocumentDB 学习路径](https://azure.microsoft.com/documentation/learning-paths/documentdb/)
-	[DocumentDB 资源模型和概念](/documentation/articles/documentdb-resources)

<!---HONumber=Mooncake_0425_2016-->