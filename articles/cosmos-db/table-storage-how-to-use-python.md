---
title: "如何配合使用 Python 和 Azure 表存储 | Microsoft Docs"
description: "使用 Azure 表存储（一种 NoSQL 数据存储）将结构化数据存储在云中。"
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
ms.date: 11/03/2017
ms.author: mimig
ms.openlocfilehash: a786f82d94a1a0039ed65a618670f872ffa3e3c2
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/18/2018
---
# <a name="how-to-use-azure-table-storage-with-python"></a>如何配合使用 Python 和 Azure 表存储

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-langsoon-tip-include](../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

本指南介绍如何使用[用于 Python 的 Microsoft Azure 存储 SDK](https://github.com/Azure/azure-storage-python) 在 Python 中执行常见 Azure 表存储方案。 涉及的情景包括创建和删除表、插入和查询实体。

对本教程中的方案进行了解时，可参考[用于 Python API 参考的 Azure 存储 SDK](https://azure-storage.readthedocs.io/en/latest/index.html)。

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="install-the-azure-storage-sdk-for-python"></a>安装用于 Python 的 Azure 存储 SDK

创建存储帐户后，接下来是安装[用于 Python 的 Microsoft Azure 存储 SDK](https://github.com/Azure/azure-storage-python)。 有关安装 SDK 的详细信息，请参阅 GitHub 上用于 Python 的存储 SDK 存储库中的 [README.rst](https://github.com/Azure/azure-storage-python/blob/master/README.rst) 文件。

## <a name="create-a-table"></a>创建表

若要使用 Python 中的 Azure 表服务，必须导入 [TableService][py_TableService] 模块。 由于要使用表实体，便还需要[实体][py_Entity]类。 将此代码添加至靠近 Python 文件顶部的位置以同时导入：

```python
from azure.storage.table import TableService, Entity
```

创建 [TableService][py_TableService] 对象，传入存储帐户名称和帐户密钥。 用帐户名称和密钥替换 `myaccount` 和 `mykey`，并调用 [create_table][py_create_table]，以在 Azure 存储中创建表。

```python
table_service = TableService(account_name='myaccount', account_key='mykey')

table_service.create_table('tasktable')
```

## <a name="add-an-entity-to-a-table"></a>将实体添加到表

要添加实体，请先创建一个表示实体的对象，然后将该对象传递给 [TableService][py_TableService].[insert_entity][py_insert_entity] 方法。 实体对象可以是字典或类型为[实体][py_Entity]的对象，同时定义实体的属性名称和值。 除包含用户为实体定义的任何其他属性外，每个实体还必须包含必需的 [PartitionKey 和 RowKey](#partitionkey-and-rowkey) 属性。

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
from azure.storage.table import TableBatch
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

将实体的 PartitionKey 和 RowKey 传递给[delete_entity][py_delete_entity]方法，以删除该实体。

```python
table_service.delete_entity('tasktable', 'tasksSeattle', '001')
```

## <a name="delete-a-table"></a>删除表

如果不再需要表或表中的所有实体，请调用 [delete_table][py_delete_table]方法，从 Azure 存储永久删除该表。

```python
table_service.delete_table('tasktable')
```

## <a name="next-steps"></a>后续步骤

* [用于 Python API 参考的 Azure 存储 SDK](https://azure-storage.readthedocs.io/en/latest/index.html)
* [用于 Python 的 Azure 存储 SDK](https://github.com/Azure/azure-storage-python)
* [Python 开发人员中心](https://azure.microsoft.com/develop/python/)
* [Microsoft Azure 存储资源管理器](../vs-azure-tools-storage-manage-with-storage-explorer.md)：一种跨平台的免费应用程序，用于在 Windows、MacOS 和 Linux 上对 Azure 存储数据进行可视化处理。

[py_commit_batch]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.commit_batch
[py_create_table]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.create_table
[py_delete_entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.delete_entity
[py_delete_table]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.delete_table
[py_Entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.models.html#azure.storage.table.models.Entity
[py_get_entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.get_entity
[py_insert_entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.insert_entity
[py_insert_or_replace_entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.insert_or_replace_entity
[py_merge_entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.merge_entity
[py_update_entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.update_entity
[py_TableService]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html
[py_TableBatch]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tablebatch.html#azure.storage.table.tablebatch.TableBatch