---
title: 如何通过 Ruby 使用 Azure 表存储或 Azure Cosmos DB 表 API | Microsoft Docs
description: 使用 Azure 表存储或 Azure Cosmos DB 表 API 将结构化数据存储在云中。
services: cosmos-db
author: SnehaGunda
manager: kfile
editor: ''
ms.service: cosmos-db
ms.component: cosmosdb-table
ms.devlang: ruby
ms.topic: sample
ms.date: 04/05/2018
ms.author: sngun
ms.openlocfilehash: d1583001550f5f272f4070006a4a6ac3be000de6
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34798264"
---
# <a name="how-to-use-azure-table-storage-and-the-azure-cosmos-db-table-api-with-ruby"></a>如何通过 Ruby 使用 Azure 表存储或 Azure Cosmos DB 表 API
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

## <a name="overview"></a>概述
本指南介绍如何使用 Azure 表服务和 Azure Cosmos DB 表 API 执行常见方案。 示例是采用 Ruby 编写的，并使用了[用于 Ruby 的 Azure 存储表客户端库](https://github.com/azure/azure-storage-ruby/tree/master/table)。 涉及的方案包括创建和删除表、在表中插入和查询条目。

## <a name="create-an-azure-service-account"></a>创建 Azure 服务帐户
[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>创建 Azure 存储帐户
[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-account"></a>创建 Azure Cosmos DB 帐户
[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="add-access-to-storage-or-azure-cosmos-db"></a>添加对存储或 Azure Cosmos DB 的访问权限
要使用 Azure 存储或 Azure Cosmos DB，必须下载和使用 Ruby azure 包，其中包括一组便于与表 REST 服务进行通信的库。

### <a name="use-rubygems-to-obtain-the-package"></a>使用 RubyGems 获取该程序包
1. 使用命令行接口，例如 **PowerShell** (Windows)、**Terminal** (Mac) 或 **Bash** (Unix)。
2. 在命令窗口中键入“gem install azure-storage-blob”以安装 gem 和依赖项。

### <a name="import-the-package"></a>导入包
使用常用的文本编辑器将以下内容添加到要在其中使用存储的 Ruby 文件的顶部：

```ruby
require "azure/storage/table"
```

## <a name="add-an-azure-storage-connection"></a>添加 Azure 存储连接
Azure 存储模块读取环境变量 AZURE_STORAGE_ACCOUNT 和 AZURE_STORAGE_ACCESS_KEY 以获取连接到 Azure 存储器帐户所需的信息。 如果未设置这些环境变量，则在使用 Azure::Storage::Table::TableService 之前必须通过以下代码指定帐户信息：

```ruby
Azure.config.storage_account_name = "<your Azure Storage account>"
Azure.config.storage_access_key = "<your Azure Storage access key>"
```

从 Azure 门户中的经典或 Resource Manager 存储帐户中获取这些值：

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 导航到要使用的存储帐户。
3. 在右侧的“设置”边栏选项卡中，单击“访问密钥”。
4. 在出现的“访问密钥”边栏选项卡中，将看到访问密钥 1 和访问密钥 2。 可以使用其中任意一个。
5. 单击复制图标以将键复制到剪贴板。

## <a name="add-an-azure-cosmos-db-connection"></a>添加 Azure Cosmos DB 连接
要连接到 Azure Cosmos DB，请从 Azure 门户中复制主连接字符串，并使用复制的连接字符串创建 Client 对象：  创建 TableService 对象时，可以传递 Client 对象：

```ruby
common_client = Azure::Storage::Common::Client.create(storage_account_name:'myaccount', storage_access_key:'mykey', storage_table_host:'mycosmosdb_endpoint')
table_client = Azure::Storage::Table::TableService.new(client: common_client)
```

## <a name="create-a-table"></a>创建表
使用 Azure::Storage::Table::TableService 对象可以对表和实体进行操作。 要创建表，请使用 create_table() 方法。 以下示例将创建表或输出存在的错误。

```ruby
azure_table_service = Azure::Storage::Table::TableService.new
begin
    azure_table_service.create_table("testtable")
rescue
    puts $!
end
```

## <a name="add-an-entity-to-a-table"></a>向表中添加条目
要添加实体，应首先创建一个定义了实体属性的哈希对象。 请注意，必须为每个实体指定 **PartitionKey** 和 **RowKey**。 这些值是实体的唯一标识符，并且查询它们比查询其他属性快很多。 Azure 存储使用 **PartitionKey** 将表的实体自动分发到多个存储节点。 具有相同 **PartitionKey** 的实体存储在同一个节点上。 **RowKey** 是实体在其所属分区内的唯一 ID。

```ruby
entity = { "content" => "test entity",
    :PartitionKey => "test-partition-key", :RowKey => "1" }
azure_table_service.insert_entity("testtable", entity)
```

## <a name="update-an-entity"></a>更新实体
可使用多种方法来更新现有实体：

* **update_entity()：** 通过替换来更新现有实体。
* **merge_entity()：** 通过将新属性值合并到现有实体来更新现有实体。
* **insert_or_merge_entity()：** 通过替换来更新现有实体。 如果不存在实体，将插入一个新实体。
* **insert_or_replace_entity()：** 通过将新属性值合并到现有实体来更新现有实体。 如果不存在实体，将插入一个新实体。

以下示例演示使用 update_entity() 更新实体：

```ruby
entity = { "content" => "test entity with updated content",
    :PartitionKey => "test-partition-key", :RowKey => "1" }
azure_table_service.update_entity("testtable", entity)
```

对于 update_entity() 和 merge_entity()，如果要更新的实体不存在，更新操作会失败。 因此，如果想要存储某个实体而不考虑它是否已存在，则应改用 insert_or_replace_entity() 或 insert_or_merge_entity()。

## <a name="work-with-groups-of-entities"></a>使用实体组
有时，有必要批量同时提交多项操作以确保通过服务器进行原子处理。 要完成此操作，首先要创建一个批处理对象，然后对 TableService 使用 execute_batch() 方法。 下面的示例演示在一个批次中提交 RowKey 为 2 和 3 的两个实体。 请注意，这仅适用于具有相同 PartitionKey 的实体。

```ruby
azure_table_service = Azure::TableService.new
batch = Azure::Storage::Table::Batch.new("testtable",
    "test-partition-key") do
    insert "2", { "content" => "new content 2" }
    insert "3", { "content" => "new content 3" }
end
results = azure_table_service.execute_batch(batch)
```

## <a name="query-for-an-entity"></a>查询实体
要在表中查询实体，请通过传递表名称、PartitionKey 和 RowKey 来使用 get_entity() 方法。

```ruby
result = azure_table_service.get_entity("testtable", "test-partition-key",
    "1")
```

## <a name="query-a-set-of-entities"></a>查询实体集
要查询表中的实体集，请创建一个查询哈希对象并使用 query_entities() 方法。 以下示例演示如何获取具有相同 **PartitionKey** 的所有实体：

```ruby
query = { :filter => "PartitionKey eq 'test-partition-key'" }
result, token = azure_table_service.query_entities("testtable", query)
```

> [!NOTE]
> 如果结果集太大，一个查询无法全部返回，则返回一个继续标记，可以使用该标记检索后续页面。
>
>

## <a name="query-a-subset-of-entity-properties"></a>查询一部分实体属性
对表的查询可以只检索实体中的少数几个属性。 此方法称为“投影”，可减少带宽并提高查询性能，尤其适用于大型实体。 请使用 select 子句并传递希望显示给客户端的属性的名称。

```ruby
query = { :filter => "PartitionKey eq 'test-partition-key'",
    :select => ["content"] }
result, token = azure_table_service.query_entities("testtable", query)
```

## <a name="delete-an-entity"></a>删除实体
要删除实体，请使用 delete_entity() 方法。 传入包含该实体的表的名称、实体的 PartitionKey 和 RowKey。

```ruby
azure_table_service.delete_entity("testtable", "test-partition-key", "1")
```

## <a name="delete-a-table"></a>删除表
要删除表，请使用 delete_table() 方法并传入要删除的表的名称。

```ruby
azure_table_service.delete_table("testtable")
```

## <a name="next-steps"></a>后续步骤

* [Microsoft Azure 存储资源管理器](../vs-azure-tools-storage-manage-with-storage-explorer.md)是 Microsoft 免费提供的独立应用，适用于在 Windows、macOS 和 Linux 上以可视方式处理 Azure 存储数据。
* [Ruby 开发人员中心](https://azure.microsoft.com/develop/ruby/)
* [适用于 Ruby 的 Microsoft Azure 存储表客户端库](https://github.com/azure/azure-storage-ruby/tree/master/table) 

