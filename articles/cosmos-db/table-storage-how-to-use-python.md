---
title: 通过 Python 开始使用 Azure 表存储 | Microsoft Docs
description: 使用 Azure 表存储（一种 NoSQL 数据存储）将结构化数据存储在云中。
services: cosmos-db
documentationcenter: python
author: mimig1
manager: jhubbard
editor: tysonn
ms.assetid: 7ddb9f3e-4e6d-4103-96e6-f0351d69a17b
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/08/2018
ms.author: mimig
ms.openlocfilehash: 455479c9eb77093dd5611263fe5bdcf699b9d026
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2018
---
# <a name="get-started-with-azure-table-storage-using-python"></a>通过 Python 开始使用 Azure 表存储

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

Azure 表存储是一项用于在云中存储结构化 NoSQL 数据的服务，通过无架构设计提供键/属性存储。 因为表存储无架构，因此可以很容易地随着应用程序需求的发展使数据适应存储。 对于许多类型的应用程序来说，访问表存储数据速度快且经济高效，在数据量相似的情况下，其成本通常比传统 SQL 要低。

可以使用表存储来存储灵活的数据集，例如 Web 应用程序的用户数据、通讯簿、设备信息，或者服务需要的其他类型的元数据。 可以在表中存储任意数量的实体，并且一个存储帐户可以包含任意数量的表，直至达到存储帐户的容量极限。

### <a name="about-this-tutorial"></a>关于本教程
本教程介绍如何在常见的 Azure 表存储方案中使用[适用于 Python 的 Azure Cosmos DB 表 SDK](https://pypi.python.org/pypi/azure-cosmosdb-table/)。 该 SDK 的名称表示它适合与 Azure Cosmos DB 配合使用，但其实该 SDK 既适合与 Azure Cosmos DB 配合使用，也适合与 Azure 表存储配合使用，只不过每个服务具有唯一的终结点。 本文使用 Python 示例探索这些方案，以演示如何：
* 创建和删除表
* 插入和查询实体
* 修改实体

浏览本教程中的方案时，可能需要参考[用于 Python API 的 Azure Cosmos DB SDK 参考](https://azure.github.io/azure-cosmosdb-python/)。

## <a name="prerequisites"></a>先决条件

若要成功完成本教程，需要具备以下先决条件：

- [Python](https://www.python.org/downloads/) 2.7、3.3、3.4、3.5 或 3.6
- [用于 Python 的 Azure Cosmos DB 表 SDK](https://pypi.python.org/pypi/azure-cosmosdb-table/)。 此 SDK 同时与 Azure 表存储和 Azure Cosmos DB 表 API 连接。
- [Azure 存储帐户](https://docs.microsoft.com/en-us/azure/storage/common/storage-create-storage-account#create-a-storage-account)或 [Azure Cosmos DB 帐户](https://azure.microsoft.com/en-us/try/cosmosdb/)

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

## <a name="create-an-azure-service-account"></a>创建 Azure 服务帐户

可以通过 Azure 表存储或 Azure Cosmos DB 使用表。 可以阅读[表产品](table-introduction.md#table-offerings)来详细了解服务之间的差别。 需要为所要使用的服务创建一个帐户。 

### <a name="create-an-azure-storage-account"></a>创建 Azure 存储帐户
创建第一个 Azure 存储帐户的最简单方法是使用 [Azure 门户](https://portal.azure.com)。 若要了解更多信息，请参阅 [创建存储帐户](../storage/common/storage-create-storage-account.md#create-a-storage-account)。

也可以使用 [Azure PowerShell](../storage/common/storage-powershell-guide-full.md) 或 [Azure CLI](../storage/common/storage-azure-cli.md) 创建 Azure 存储帐户。

如果暂时不想创建存储帐户，也可以使用 Azure 存储模拟器在本地环境中运行和测试代码。 有关详细信息，请参阅 [使用 Azure 存储模拟器进行开发和测试](../storage/common/storage-use-emulator.md)。

### <a name="create-an-azure-cosmos-db-table-api-account"></a>创建 Azure Cosmos DB 表 API 帐户

有关创建 Azure Cosmos DB 表 API 帐户的说明，请参阅[创建表 API 帐户](create-table-dotnet.md#create-a-database-account)。

## <a name="install-the-azure-cosmos-db-table-sdk-for-python"></a>安装用于 Python 的 Azure Cosmos DB 表 SDK

创建存储帐户后，下一步是安装[用于 Python 的 Microsoft Azure Cosmos DB 表 SDK](https://pypi.python.org/pypi/azure-cosmosdb-table/)。 有关安装该 SDK 的详细信息，请参阅 GitHub 上用于 Python 的 Cosmos DB 表 SDK 存储库中的 [README.rst](https://github.com/Azure/azure-cosmosdb-python/blob/master/azure-cosmosdb-table/README.rst) 文件。

## <a name="import-the-tableservice-and-entity-classes"></a>导入 TableService 和 Entity 类

若要通过 Python 使用 Azure 表服务中的实体，请使用 [TableService](https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html) 和 [Entity][py_Entity] 类。 将此代码添加至靠近 Python 文件顶部的位置以同时导入：

```python
from azure.cosmosdb.table.tableservice import TableService
from azure.cosmosdb.table.models import Entity
```

## <a name="connect-to-azure-table-service"></a>连接到 Azure 表服务

若要连接到 Azure 存储表服务，请创建 [TableService](https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html) 对象，并传入存储帐户名称和帐户密钥。 将 `myaccount` 和 `mykey` 替换为自己的帐户名和密钥。

```python
table_service = TableService(account_name='myaccount', account_key='mykey')
```

## <a name="connect-to-azure-cosmos-db"></a>连接到 Azure Cosmos DB

若要连接到 Azure Cosmos DB，请从 Azure 门户中复制主连接字符串，并使用复制的连接字符串创建 [TableService](https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html) 对象：

```python
table_service = TableService(connection_string='DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;TableEndpoint=myendpoint;)
```

## <a name="create-a-table"></a>创建表

调用 [create_table][py_create_table] 创建表。

```python
table_service.create_table('tasktable')
```

## <a name="add-an-entity-to-a-table"></a>将实体添加到表

若要添加实体，请先创建一个表示实体的对象，然后将该对象传递给 [TableService.insert_entity 方法][py_TableService]。 实体对象可以是字典或类型为[实体][py_Entity]的对象，同时定义实体的属性名称和值。 除包含用户为实体定义的任何其他属性外，每个实体还必须包含必需的 [PartitionKey 和 RowKey](#partitionkey-and-rowkey) 属性。

此示例创建表示实体的字典对象，然后将其传递给 [insert_entity][py_insert_entity]方法，以将其添加到表中：

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001', 'description' : 'Take out the trash', 'priority' : 200}
table_service.insert_entity('tasktable', task)
```

此示例创建[实体][py_Entity]对象，然后将其传递给[insert_entity][py_insert_entity]方法，以将其添加到表中：

```python
task = Entity()
task.PartitionKey = 'tasksSeattle'
task.RowKey = '002'
task.description = 'Wash the car'
task.priority = 100
table_service.insert_entity('tasktable', task)
```

### <a name="partitionkey-and-rowkey"></a>PartitionKey 和 RowKey

必须为每个实体同时指定 PartitionKey 和 RowKey 属性。 这些是实体的唯一标识符，它们一起形成实体的主密钥。 相比查询任何其他实体属性，使用这些值进行查询速度更快，因为只有这些属性编制了索引。

表服务使用 PartitionKey 在存储节点之间智能分布表实体。 具有相同 PartitionKey 的实体存储在同一节点上。 RowKey 是实体在其所属分区内的唯一 ID。

## <a name="update-an-entity"></a>更新实体

若要更新一个实体的所有属性值，请调用 [update_entity][py_update_entity] 方法。 此示例演示如何使用更新版本替换现有实体：

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001', 'description' : 'Take out the garbage', 'priority' : 250}
table_service.update_entity('tasktable', task)
```

如果要更新的实体尚不存在，更新操作会失败。 如果要存储实体（无论其存在与否），请使用 [insert_or_replace_entity][py_insert_or_replace_entity]。 在下面的示例中，第一次调用将替换现有实体。 第二次调用将插入新实体，因为表中不存在具有指定的 PartitionKey 和 RowKey 的实体。

```python
# Replace the entity created earlier
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001', 'description' : 'Take out the garbage again', 'priority' : 250}
table_service.insert_or_replace_entity('tasktable', task)

# Insert a new entity
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '003', 'description' : 'Buy detergent', 'priority' : 300}
table_service.insert_or_replace_entity('tasktable', task)
```

> [!TIP]
> [update_entity][py_update_entity] 方法将替换现有实体的所有属性和值，还可使用它从现有实体删除属性。 可使用 [merge_entity][py_merge_entity] 方法更新具有新的或修改过的属性值的现有实体，而无需完全替换该实体。

## <a name="modify-multiple-entities"></a>修改多个实体

若要确保表服务自动处理请求，可批量提交多个操作。 首先，使用 [TableBatch][py_TableBatch]类，将多个操作添加到单个批次。 然后，调用 [TableService][py_TableService].[commit_batch][py_commit_batch]，以在一个原子操作中提交这些操作。 要批量修改的所有实体必须位于同一分区。

此示例将两个实体一起添加到批处理：

```python
from azure.cosmosdb.table.tablebatch import TableBatch
batch = TableBatch()
task004 = {'PartitionKey': 'tasksSeattle', 'RowKey': '004', 'description' : 'Go grocery shopping', 'priority' : 400}
task005 = {'PartitionKey': 'tasksSeattle', 'RowKey': '005', 'description' : 'Clean the bathroom', 'priority' : 100}
batch.insert_entity(task004)
batch.insert_entity(task005)
table_service.commit_batch('tasktable', batch)
```

还可以通过上下文管理器语法来使用批处理：

```python
task006 = {'PartitionKey': 'tasksSeattle', 'RowKey': '006', 'description' : 'Go grocery shopping', 'priority' : 400}
task007 = {'PartitionKey': 'tasksSeattle', 'RowKey': '007', 'description' : 'Clean the bathroom', 'priority' : 100}

with table_service.batch('tasktable') as batch:
    batch.insert_entity(task006)
    batch.insert_entity(task007)
```

## <a name="query-for-an-entity"></a>查询实体

要查询表中的实体，请将其 PartitionKey 和 RowKey 传递给 [TableService][py_TableService].[get_entity][py_get_entity] 方法。

```python
task = table_service.get_entity('tasktable', 'tasksSeattle', '001')
print(task.description)
print(task.priority)
```

## <a name="query-a-set-of-entities"></a>查询实体集

可通过向筛选字符串提供“筛选”参数来查询一组实体。 此示例通过向 PartitionKey 应用筛选器来查找 Seattle 中的所有任务。

```python
tasks = table_service.query_entities('tasktable', filter="PartitionKey eq 'tasksSeattle'")
for task in tasks:
    print(task.description)
    print(task.priority)
```

## <a name="query-a-subset-of-entity-properties"></a>查询一部分实体属性

还可在查询中限制为每个实体返回的属性。 此方法称为“投影”，可减少带宽并提高查询性能，尤其适用于大型实体或结果集。 使用 select 参数并传递希望返回给客户端的属性的名称。

以下代码中的查询只返回表中实体的说明。

> [!NOTE]
> 下面的代码段仅对 Azure 存储有效。 但不受存储模拟器支持。

```python
tasks = table_service.query_entities('tasktable', filter="PartitionKey eq 'tasksSeattle'", select='description')
for task in tasks:
    print(task.description)
```

## <a name="delete-an-entity"></a>删除实体

将实体的 **PartitionKey** 和 **RowKey** 传递给 [delete_entity][py_delete_entity] 方法，以删除该实体。

```python
table_service.delete_entity('tasktable', 'tasksSeattle', '001')
```

## <a name="delete-a-table"></a>删除表

如果不再需要表或表中的所有实体，请调用 [delete_table][py_delete_table]方法，从 Azure 存储永久删除该表。

```python
table_service.delete_table('tasktable')
```

## <a name="next-steps"></a>后续步骤

* [常见问题解答 - 使用表 API 进行开发](https://docs.microsoft.com/en-us/azure/cosmos-db/faq#develop-with-the-table-api)
* [用于 Python API 的 Azure Cosmos DB SDK 参考](https://azure.github.io/azure-cosmosdb-python/)
* [Python 开发人员中心](https://azure.microsoft.com/develop/python/)
* [Microsoft Azure 存储资源管理器](../vs-azure-tools-storage-manage-with-storage-explorer.md)：一种跨平台的免费应用程序，用于在 Windows、MacOS 和 Linux 上对 Azure 存储数据进行可视化处理。
* [在 Visual Studio (Windows) 中使用 Python](https://docs.microsoft.com/en-us/visualstudio/python/overview-of-python-tools-for-visual-studio)

[py_commit_batch]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_create_table]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_delete_entity]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_delete_table]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_Entity]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.models.html
[py_get_entity]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_insert_entity]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_insert_or_replace_entity]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_TableService]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_TableBatch]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tablebatch.html
[py_merge_entity]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_update_entity]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
