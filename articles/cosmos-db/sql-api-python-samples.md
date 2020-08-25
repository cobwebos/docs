---
title: Azure Cosmos DB 的 SQL API Python 示例
description: 在 GitHub 上查找用于 Azure Cosmos DB 中常见任务的 Python 示例，包括 CRUD 操作。
author: Rodrigossz
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: sample
ms.date: 08/11/2020
ms.author: rosouz
ms.custom: devx-track-python
ms.openlocfilehash: 34257281470f051f3d385aa729ca5a4f3591e61c
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2020
ms.locfileid: "88586244"
---
# <a name="azure-cosmos-db-python-examples"></a>Azure Cosmos DB Python 示例

> [!div class="op_single_selector"]
> * [.NET V2 SDK 示例](sql-api-dotnet-samples.md)
> * [.NET V3 SDK 示例](sql-api-dotnet-v3sdk-samples.md)
> * [Java V4 SDK 示例](sql-api-java-sdk-samples.md)
> * [Node.js 示例](sql-api-nodejs-samples.md)
> * [Python 示例](sql-api-python-samples.md)
> * [Azure 代码示例库](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)

[azure-documentdb-python](https://github.com/Azure/azure-documentdb-python) GitHub 存储库中包含对 Azure Cosmos DB 资源执行 CRUD 操作和其他常见操作的示例解决方案。 本文将提供：

* 每个 Python 示例项目文件中的任务链接。
* 指向相关的 API 参考内容的链接。

## <a name="prerequisites"></a>先决条件

- 一个 Cosmos DB 帐户。 选项包括：
    * 具有 Azure 有效订阅：
        * [创建 Azure 免费帐户](https://azure.microsoft.com/free)或使用现有订阅 
        * [Visual Studio 每月额度](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers)
        * [Azure Cosmos DB 免费层](https://docs.microsoft.com/azure/cosmos-db/optimize-dev-test#azure-cosmos-db-free-tier)
    * 不具有 Azure 有效订阅：
        * [免费试用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)，为期 30 天的测试环境。
        * [Azure Cosmos DB 模拟器](https://aka.ms/cosmosdb-emulator) 
- [Python 2.7 或 3.5.3+](https://www.python.org/downloads/)，以及 `PATH` 中的 `python` 可执行文件。
- [Visual Studio Code](https://code.visualstudio.com/)。
- [适用于 Visual Studio Code 的 Python 扩展](https://marketplace.visualstudio.com/items?itemName=ms-python.python#overview)。
- [Git](https://www.git-scm.com/downloads)。 
- [用于 Python 的 Azure Cosmos DB SQL API SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)

## <a name="database-examples"></a>数据库示例

[database_management.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py) Python 示例演示如何执行以下任务。 若要在运行以下示例之前了解 Azure Cosmos 数据库，请参阅[使用数据库、容器和项](databases-containers-items.md)概念性文章。

| 任务 | API 参考 |
| --- | --- |
| [创建数据库](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L48-L56) |CosmosClient.create_database|
| [按 ID 读取数据库](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L59-L67) |CosmosClient.get_database_client|
| [查询数据库](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L32-L67) |CosmosClient.query_databases|
| [列出帐户的数据库](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L70-L81) |CosmosClient.list_databases|
| [删除数据库](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L84-L93) |CosmosClient.delete_database|

## <a name="container-examples"></a>容器示例

[container_management.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py) Python 示例演示如何执行以下任务。 若要在运行以下示例之前了解 Azure Cosmos 集合，请参阅[使用数据库、容器和项](databases-containers-items.md)概念性文章。

| 任务 | API 参考 |
| --- | --- |
| [查询容器](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L51-L66) |database.query_containers |
| [创建容器](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L69-L163) |database.create_container |
| [列出数据库中的所有容器](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L206-L217) |database.list_containers |
| [按 ID 获取容器](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L195-L203) |database.get_container_client |
| [管理容器的预配吞吐量](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L166-L192) |container.read_offer、container.replace_throughput|
| [删除容器](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L220-L229) |database.delete_container |

## <a name="item-examples"></a>项示例

[item_management.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py) Python 示例演示如何执行以下任务。 若要在运行以下示例之前了解 Azure Cosmos 文档，请参阅[使用数据库、容器和项](databases-containers-items.md)概念性文章。

| 任务 | API 参考 |
| --- | --- |
| [在容器中创建项](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L26-L38) |container.create_item |
| [按 ID 读取项](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L41-L49) |container.read_item |
| [读取容器中的所有项](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L52-L63) |container.read_all_items |
| [按 ID 查询项](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L66-L78) |container.query_items |
| [替换项](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L81-L88) |container.replace_items |
| [更新插入项](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L91-L98) |container.upsert_item |
| [删除项](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L101-L106) |container.delete_item |
| [获取容器中项的更改源](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/change_feed_management.py) |container.query_items_change_feed |

## <a name="indexing-examples"></a>索引示例

[index_management.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py) Python 示例演示如何执行以下任务。 若要在运行以下示例之前了解 Azure Cosmos DB 中的索引，请参阅[索引策略](index-policy.md)、[索引类型](index-types.md)和[索引路径](index-paths.md)概念文章。

| 任务 | API 参考 |
| --- | --- |
| [将特定项排除在索引范围外](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L145-L201) | documents.IndexingDirective.Exclude|
| [对已编入索引的特定项使用手动索引](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L204-L263) | documents.IndexingDirective.Include |
| [将路径排除在索引范围外](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L266-L336) |定义要在 `IndexingPolicy` 属性中排除的路径 |
| [对字符串使用范围索引](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L401-L485) | 使用范围索引对字符串数据类型定义索引策略。 `'kind': documents.IndexKind.Range`, `'dataType': documents.DataType.String`|
| [执行索引转换](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L488-L544) |database.replace_container（使用更新的索引策略）|
| [当路径上仅存在哈希索引时使用扫描](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L339-L398) | 查询项时设置 `enable_scan_in_query=True` 和 `enable_cross_partition_query=True` |
