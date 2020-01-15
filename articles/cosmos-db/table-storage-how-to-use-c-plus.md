---
title: 通过 C++ 使用 Azure 表存储和 Azure Cosmos DB 表 API
description: 使用 Azure 表存储或 Azure Cosmos DB 表 API 将结构化数据存储在云中。
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: cpp
ms.topic: sample
ms.date: 10/07/2019
author: wmengmsft
ms.author: wmeng
ms.openlocfilehash: 5df344b3f9f3d2fc2ff6fa65667039c545b70841
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75441181"
---
# <a name="how-to-use-azure-table-storage-and-azure-cosmos-db-table-api-with-c"></a>如何通过 C++ 使用 Azure 表存储或 Azure Cosmos DB 表 API

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

本指南通过使用 Azure 表存储服务或 Azure Cosmos DB 表 API 来演示常见方案。 示例采用 C++ 编写，并使用了[适用于 C++ 的 Azure 存储客户端库](https://github.com/Azure/azure-storage-cpp/blob/master/README.md)。 本文涵盖以下方案：

* 创建和删除表
* 处理表实体

> [!NOTE]
> 本指南主要面向适用于 C++ 的 Azure 存储客户端库 1.0.0 版及更高版本。 推荐版本：存储客户端库 2.2.0（可通过使用 [NuGet](https://www.nuget.org/packages/wastorage) 或 [GitHub](https://github.com/Azure/azure-storage-cpp/) 获得）。
>

## <a name="create-accounts"></a>创建帐户

### <a name="create-an-azure-service-account"></a>创建 Azure 服务帐户

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>创建 Azure 存储帐户

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-table-api-account"></a>创建 Azure Cosmos DB 表 API 帐户

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-c-application"></a>创建 C++ 应用程序

在本指南中，你会使用 C++ 应用程序的存储功能。 为此，请安装适用于 C++ 的 Azure 存储客户端库。

若要安装适用于 C++ 的 Azure 存储客户端库，请使用以下方法：

* **Linux：** 按照[适用于 C++ 的 Azure 存储客户端库自述文件：在 Linux 上开始使用](https://github.com/Azure/azure-storage-cpp#getting-started-on-linux)页中提供的说明操作。
* **Windows：** 在 Windows 中，使用 [vcpkg](https://github.com/microsoft/vcpkg) 作为依赖项管理员。 按照[快速入门](https://github.com/microsoft/vcpkg#quick-start)初始化 vcpkg。 然后使用以下命令安装库：

```powershell
.\vcpkg.exe install azure-storage-cpp
```

可以在[自述](https://github.com/Azure/azure-storage-cpp#download--install)文件中找到有关如何生成源代码和导出到 Nuget 的指南。

### <a name="configure-access-to-the-table-client-library"></a>配置对表客户端库的访问权限

若要使用 Azure 存储 API 来访问表，则将以下 `include` 语句添加到 C++ 文件的顶部：

```cpp
#include <was/storage_account.h>
#include <was/table.h>
```

Azure 存储客户端或 Cosmos DB 客户端使用连接字符串来存储用于访问数据管理服务的终结点和凭据。 运行客户端应用程序时，必须提供相应格式的存储连接字符串或 Azure Cosmos DB 连接字符串。

### <a name="set-up-an-azure-storage-connection-string"></a>设置 Azure 存储连接字符串

此示例演示如何声明一个静态字段以保存 Azure 存储连接字符串：  

```cpp
// Define the Storage connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=<your_storage_account>;AccountKey=<your_storage_account_key>"));
```

使用 `<your_storage_account>` 的存储帐户名称。 对于 <your_storage_account_key>，请使用 [Azure 门户](https://portal.azure.com)中列出的存储帐户的访问密钥。 有关存储帐户和访问密钥的信息，请参阅[创建存储帐户](../storage/common/storage-create-storage-account.md)。

### <a name="set-up-an-azure-cosmos-db-connection-string"></a>设置 Azure Cosmos DB 连接字符串

此示例演示如何声明一个静态字段以保存 Azure Cosmos DB 连接字符串：

```cpp
// Define the Azure Cosmos DB connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=<your_cosmos_db_account>;AccountKey=<your_cosmos_db_account_key>;TableEndpoint=<your_cosmos_db_endpoint>"));
```

使用 `<your_cosmos_db_account>` 的 Azure Cosmos DB 帐户名称。 输入 `<your_cosmos_db_account_key>` 的主密钥。 对于 `<your_cosmos_db_endpoint>`，请输入 [Azure 门户](https://portal.azure.com)中列出的终结点。

若要在基于 Windows 的本地计算机中测试应用程序，可以使用随 [Azure SDK](https://azure.microsoft.com/downloads/) 一起安装的 Azure 存储模拟器。 存储模拟器是一种用于模拟本地开发计算机上提供的 Azure Blob、队列和表服务的实用程序。 以下示例演示如何声明一个静态字段以将连接字符串保存到本地存储模拟器：  

```cpp
// Define the connection string with Azure storage emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

若要启动 Azure 存储模拟器，请从 Windows 桌面选择“开始”按钮或 Windows 键  。 输入并运行 Microsoft Azure 存储模拟器  。 有关详细信息，请参阅[使用 Azure 存储模拟器进行开发和测试](../storage/common/storage-use-emulator.md)。

### <a name="retrieve-your-connection-string"></a>检索连接字符串

可使用 `cloud_storage_account` 类来表示存储帐户信息。 若要从存储连接字符串中检索存储帐户信息，则使用 `parse` 方法。

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

接下来，获取对 `cloud_table_client` 类的引用。 使用该类即可获取表存储服务中存储的表和实体的引用对象。 以下代码使用你在之前检索到的存储帐户对象来创建 `cloud_table_client` 对象：  

```cpp
// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();
```

## <a name="create-and-add-entities-to-a-table"></a>创建实体并将其添加到表

### <a name="create-a-table"></a>创建表

使用 `cloud_table_client` 对象，可以获得表和实体的引用对象。 下面的代码创建 `cloud_table_client` 对象并使用它来创建一个新表。

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);  

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Retrieve a reference to a table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create the table if it doesn't exist.
table.create_if_not_exists();  
```

### <a name="add-an-entity-to-a-table"></a>将实体添加到表

若要将实体添加到表，请创建新的 `table_entity` 对象并将其传递给 `table_operation::insert_entity`。 以下代码使用客户的名字作为行键，并使用姓氏作为分区键。 实体的分区键和行键共同唯一地标识表中的实体。 查询分区键相同的实体的速度可能快于查询分区键不同的实体的速度。 使用不同的分区键可提升并行操作可伸缩性。 有关详细信息，请参阅 [Microsoft Azure 存储性能和可伸缩性清单](../storage/common/storage-performance-checklist.md)。

下面的代码将创建 `table_entity` 的新实例，其中包含一些可存储的客户数据。 接下来，该代码调用 `table_operation::insert_entity` 来创建 `table_operation` 对象，以便将实体插入表中，并将新的表实体与之关联。 最后，该代码会在 `cloud_table` 对象上调用 `execute` 方法。 新的 `table_operation` 向表服务发送请求，以此将新的客户实体插入 `people` 表中。  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Retrieve a reference to a table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create the table if it doesn't exist.
table.create_if_not_exists();

// Create a new customer entity.
azure::storage::table_entity customer1(U("Harp"), U("Walter"));

azure::storage::table_entity::properties_type& properties = customer1.properties();
properties.reserve(2);
properties[U("Email")] = azure::storage::entity_property(U("Walter@contoso.com"));

properties[U("Phone")] = azure::storage::entity_property(U("425-555-0101"));

// Create the table operation that inserts the customer entity.
azure::storage::table_operation insert_operation = azure::storage::table_operation::insert_entity(customer1);

// Execute the insert operation.
azure::storage::table_result insert_result = table.execute(insert_operation);
```

### <a name="insert-a-batch-of-entities"></a>插入一批实体

可以通过一次写入操作将一批实体插入到表服务。 以下代码创建 `table_batch_operation` 对象，并向其中添加三个插入操作。 每个插入操作的添加方法如下：创建一个新的实体对象，设置它的值，然后在 `table_batch_operation` 对象上调用 `insert` 方法以将实体与新插入操作相关联。 然后，该代码会调用 `cloud_table.execute` 来运行操作。  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Define a batch operation.
azure::storage::table_batch_operation batch_operation;

// Create a customer entity and add it to the table.
azure::storage::table_entity customer1(U("Smith"), U("Jeff"));

azure::storage::table_entity::properties_type& properties1 = customer1.properties();
properties1.reserve(2);
properties1[U("Email")] = azure::storage::entity_property(U("Jeff@contoso.com"));
properties1[U("Phone")] = azure::storage::entity_property(U("425-555-0104"));

// Create another customer entity and add it to the table.
azure::storage::table_entity customer2(U("Smith"), U("Ben"));

azure::storage::table_entity::properties_type& properties2 = customer2.properties();
properties2.reserve(2);
properties2[U("Email")] = azure::storage::entity_property(U("Ben@contoso.com"));
properties2[U("Phone")] = azure::storage::entity_property(U("425-555-0102"));

// Create a third customer entity to add to the table.
azure::storage::table_entity customer3(U("Smith"), U("Denise"));

azure::storage::table_entity::properties_type& properties3 = customer3.properties();
properties3.reserve(2);
properties3[U("Email")] = azure::storage::entity_property(U("Denise@contoso.com"));
properties3[U("Phone")] = azure::storage::entity_property(U("425-555-0103"));

// Add customer entities to the batch insert operation.
batch_operation.insert_or_replace_entity(customer1);
batch_operation.insert_or_replace_entity(customer2);
batch_operation.insert_or_replace_entity(customer3);

// Execute the batch operation.
std::vector<azure::storage::table_result> results = table.execute_batch(batch_operation);
```

批处理操作的注意事项如下：

* 在单次批处理中，可以执行最多 100 个任意组合的 `insert`、`delete`、`merge`、`replace`、`insert-or-merge` 和 `insert-or-replace` 操作。  
* 批处理操作也可以包含检索操作，但前提是检索操作是批处理中仅有的操作。  
* 单次批处理操作中的所有实体都必须具有相同的分区键。  
* 批处理操作的数据负载限制为 4MB。  

## <a name="query-and-modify-entities"></a>查询和修改实体

### <a name="retrieve-all-entities-in-a-partition"></a>检索分区中的所有实体

若要查询表以获取某个分区中的所有实体，请使用 `table_query` 对象。 以下代码示例指定了一个筛选器，以筛选分区键为 `Smith` 的实体。 此示例会将查询结果中每个实体的字段输出到控制台。  

> [!NOTE]
> Azure Cosmos DB 中的 C++ 目前不支持这些方法。

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Construct the query operation for all customer entities where PartitionKey="Smith".
azure::storage::table_query query;

query.set_filter_string(azure::storage::table_query::generate_filter_condition(U("PartitionKey"), azure::storage::query_comparison_operator::equal, U("Smith")));

// Execute the query.
azure::storage::table_query_iterator it = table.execute_query(query);

// Print the fields for each customer.
azure::storage::table_query_iterator end_of_results;
for (; it != end_of_results; ++it)
{
    const azure::storage::table_entity::properties_type& properties = it->properties();

    std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key()
        << U(", Property1: ") << properties.at(U("Email")).string_value()
        << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
}  
```

此示例中的查询会返回与筛选条件匹配的所有实体。 如果有大型表并需要经常下载表实体，我们建议改为将数据存储在 Azure 存储 Blob 中。

### <a name="retrieve-a-range-of-entities-in-a-partition"></a>检索分区中的一部分实体

如果不想查询分区中的所有实体，则可以指定范围。 合并分区键筛选器和行键筛选器。 以下代码示例使用两个筛选器来获取 `Smith` 分区中的、行键（名字）以字母 `E` 前面的字母开头的所有实体，并打印查询结果。  

> [!NOTE]
> Azure Cosmos DB 中的 C++ 目前不支持这些方法。

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create the table query.
azure::storage::table_query query;

query.set_filter_string(azure::storage::table_query::combine_filter_conditions(
    azure::storage::table_query::generate_filter_condition(U("PartitionKey"),
    azure::storage::query_comparison_operator::equal, U("Smith")),
    azure::storage::query_logical_operator::op_and,
    azure::storage::table_query::generate_filter_condition(U("RowKey"), azure::storage::query_comparison_operator::less_than, U("E"))));

// Execute the query.
azure::storage::table_query_iterator it = table.execute_query(query);

// Loop through the results, displaying information about the entity.
azure::storage::table_query_iterator end_of_results;
for (; it != end_of_results; ++it)
{
    const azure::storage::table_entity::properties_type& properties = it->properties();

    std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key()
        << U(", Property1: ") << properties.at(U("Email")).string_value()
        << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
}  
```

### <a name="retrieve-a-single-entity"></a>检索单个实体

可以编写查询以检索单个特定实体。 以下代码使用 `table_operation::retrieve_entity` 来指定客户 `Jeff Smith`。 此方法只返回一个实体，而不是返回一个集合，并且返回的值在 `table_result` 中。 在查询中指定分区键和行键是从表服务中检索单个实体的最快方法。  

```cpp
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Retrieve the entity with partition key of "Smith" and row key of "Jeff".
azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

// Output the entity.
azure::storage::table_entity entity = retrieve_result.entity();
const azure::storage::table_entity::properties_type& properties = entity.properties();

std::wcout << U("PartitionKey: ") << entity.partition_key() << U(", RowKey: ") << entity.row_key()
    << U(", Property1: ") << properties.at(U("Email")).string_value()
    << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
```

### <a name="replace-an-entity"></a>替换实体

要替换实体，请从表服务中检索它，修改实体对象，然后将更改保存回表服务。 以下代码更改现有客户的电话号码和电子邮件地址。 此代码并不调用 `table_operation::insert_entity`，而是使用 `table_operation::replace_entity`。 这一方法会导致在服务器上完全替换该实体，除非服务器上的该实体自检索到它以后就发生更改。 如果已更改，则操作失败。 操作失败可防止应用程序覆盖另一个组件在检索与更新之间所做的更改。 正确处理此失败问题的方法是再次检索实体，进行更改（如果仍有效），然后再次执行 `table_operation::replace_entity` 操作。  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Replace an entity.
azure::storage::table_entity entity_to_replace(U("Smith"), U("Jeff"));
azure::storage::table_entity::properties_type& properties_to_replace = entity_to_replace.properties();
properties_to_replace.reserve(2);

// Specify a new phone number.
properties_to_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0106"));

// Specify a new email address.
properties_to_replace[U("Email")] = azure::storage::entity_property(U("JeffS@contoso.com"));

// Create an operation to replace the entity.
azure::storage::table_operation replace_operation = azure::storage::table_operation::replace_entity(entity_to_replace);

// Submit the operation to the Table service.
azure::storage::table_result replace_result = table.execute(replace_operation);
```

### <a name="insert-or-replace-an-entity"></a>插入或替换实体

如果该实体自从服务器中检索到它以后就发生更改，则 `table_operation::replace_entity` 操作会失败。 此外，你必须首先从服务器中检索该实体，`table_operation::replace_entity` 才能成功。 有时，你不知道实体是否存在于服务器上。 这与当前存储在其中的值无关，因为更新操作会将它们全部覆盖。 若要完成此结果，请使用 `table_operation::insert_or_replace_entity` 操作。 如果实体不存在，该操作会插入实体。 如果实体存在，该操作会替换实体。 在以下代码示例中，仍然会检索 `Jeff Smith` 的客户实体，但稍后会使用 `table_operation::insert_or_replace_entity` 将其保存回服务器。 将覆盖在检索与更新操作之间对实体进行的任何更新。  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Insert or replace an entity.
azure::storage::table_entity entity_to_insert_or_replace(U("Smith"), U("Jeff"));
azure::storage::table_entity::properties_type& properties_to_insert_or_replace = entity_to_insert_or_replace.properties();

properties_to_insert_or_replace.reserve(2);

// Specify a phone number.
properties_to_insert_or_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0107"));

// Specify an email address.
properties_to_insert_or_replace[U("Email")] = azure::storage::entity_property(U("Jeffsm@contoso.com"));

// Create an operation to insert or replace the entity.
azure::storage::table_operation insert_or_replace_operation = azure::storage::table_operation::insert_or_replace_entity(entity_to_insert_or_replace);

// Submit the operation to the Table service.
azure::storage::table_result insert_or_replace_result = table.execute(insert_or_replace_operation);
```

### <a name="query-a-subset-of-entity-properties"></a>查询一部分实体属性

对表的查询可以只检索实体中的少数几个属性。 以下代码中的查询使用 `table_query::set_select_columns` 方法，仅返回表中实体的电子邮件地址。  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Define the query, and select only the Email property.
azure::storage::table_query query;
std::vector<utility::string_t> columns;

columns.push_back(U("Email"));
query.set_select_columns(columns);

// Execute the query.
azure::storage::table_query_iterator it = table.execute_query(query);

// Display the results.
azure::storage::table_query_iterator end_of_results;
for (; it != end_of_results; ++it)
{
    std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key();

    const azure::storage::table_entity::properties_type& properties = it->properties();
    for (auto prop_it = properties.begin(); prop_it != properties.end(); ++prop_it)
    {
        std::wcout << ", " << prop_it->first << ": " << prop_it->second.str();
    }

    std::wcout << std::endl;
}
```

> [!NOTE]
> 查询实体的几个属性是比检索所有属性更高效的操作。
>

## <a name="delete-content"></a>删除内容

### <a name="delete-an-entity"></a>删除实体

检索到实体后可以将其删除。 检索到实体后，对要删除的实体调用 `table_operation::delete_entity`。 然后调用 `cloud_table.execute` 方法。 以下代码检索并删除分区键为 `Smith`、行键为 `Jeff` 的实体。

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

// Create an operation to delete the entity.
azure::storage::table_operation delete_operation = azure::storage::table_operation::delete_entity(retrieve_result.entity());

// Submit the delete operation to the Table service.
azure::storage::table_result delete_result = table.execute(delete_operation);  
```

### <a name="delete-a-table"></a>删除表

最后，以下代码示例将从存储帐户中删除表。 在删除表之后的一段时间内无法重新创建表。  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Delete the table if it exists
if (table.delete_table_if_exists())
{
    std::cout << "Table deleted!";
}
else
{
    std::cout << "Table didn't exist";
}
```

## <a name="troubleshooting"></a>故障排除

对于 Visual Studio Community 版本，如果项目因包含文件 storage_account.h 和 table.h 而显示生成错误，请删除 /permissive- 编译器开关    ：

1. 在“解决方案资源管理器”  中，右键单击项目，并选择“属性”  。
1. 在“属性页”  对话框中，依次展开“配置属性”  、“C/C++”  ，然后选择“语言”  。
1. 将“符合模式”  设置为“否”  。

## <a name="next-steps"></a>后续步骤

[Microsoft Azure 存储资源管理器](../vs-azure-tools-storage-manage-with-storage-explorer.md)是 Microsoft 免费提供的独立应用，适用于在 Windows、macOS 和 Linux 上以可视方式处理 Azure 存储数据。

请打开以下链接了解有关 Azure 存储和 Azure Cosmos DB 中的表 API 的详细信息：

* [表 API 简介](table-introduction.md)
* [使用 C++ 列出 Azure 存储资源](../storage/common/storage-c-plus-plus-enumeration.md)
* [适用于 C++ 的存储客户端库参考](https://azure.github.io/azure-storage-cpp)
* [Azure 存储文档](https://azure.microsoft.com/documentation/services/storage/)
