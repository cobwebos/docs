---
title: "如何开始使用表存储和 Visual Studio 连接服务 (ASP.NET Core) | Microsoft Docs"
description: "在使用 Visual Studio 连接服务连接到存储帐户后，如何开始在 Visual Studio 的 ASP.NET Core 项目中使用 Azure 表存储"
services: storage
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: c3c451d1-71ff-4222-a348-c41c98a02b85
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: kraigb
ms.openlocfilehash: 4693c637597346e5c72bef6ddca9369e67b740f3
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2018
---
# <a name="how-to-get-started-with-azure-table-storage-and-visual-studio-connected-services"></a>如何开始使用 Azure 表存储和 Visual Studio 连接服务

[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

本文介绍通过使用 Visual Studio 中的“连接服务”功能在 ASP.NET Core 项目中创建或引用 Azure 存储帐户之后，如何开始在 Visual Studio 中使用 Azure 表存储。 执行“连接服务”操作会安装相应的 NuGet 程序包，以访问项目中的 Azure 存储，并将存储帐户的连接字符串添加到项目配置文件中。 （有关 Azure 存储的常规信息，请参阅[存储文档](https://azure.microsoft.com/documentation/services/storage/)。）

Azure 表存储服务使用户可以存储大量结构化数据。 该服务是一个 NoSQL 数据存储，接受来自 Azure 云内部和外部的通过验证的呼叫。 Azure 表最适合存储结构化非关系型数据。 有关使用 Azure 表存储的更多常规信息，请参阅 [Get started with Azure Table storage using .NET](../storage/storage-dotnet-how-to-use-tables.md)（通过 .NET 开始使用 Azure 表存储）。

若要开始，首先在你的存储帐户中创建一个表。 然后本文将演示如何运用 C# 创建表，以及如何执行基本的表操作，如添加、修改、读取和删除表项。  代码使用适用于 .NET 的 Azure 存储客户端库。 有关 ASP.NET 的详细信息，请参阅 [ASP.NET](http://www.asp.net)。

某些 Azure 存储 API 为异步，而本文中的代码假定正在使用异步方法。 有关详细信息，请参阅[异步编程](https://docs.microsoft.com/dotnet/csharp/async)。

## <a name="access-tables-in-code"></a>使用代码访问表

要访问 ASP.NET Core 项目中的表，需要将下列事项包含在任何访问 Azure 表存储的 C# 源文件中。

1. 添加必要的 `using` 语句：

    ```cs
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Table;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Framework.Logging.LogLevel;
    ```

1. 获取表示存储帐户信息的 `CloudStorageAccount` 对象。 使用下面的代码获取存储连接字符串和 Azure 服务配置中的存储帐户信息：

    ```cs
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. 获取 `CloudTableClient` 对象，以引用存储帐户中的表对象：

    ```cs
    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. 获取 `CloudTable` 引用对象，以引用特定的表和实体：

    ```cs
    // Get a reference to a table named "peopleTable"
    CloudTable peopleTable = tableClient.GetTableReference("peopleTable");
    ```

## <a name="create-a-table-in-code"></a>使用代码创建表

若要创建 Azure 表，请调用 ``CreateIfNotExistsAsync()`：

```cs
// Create the CloudTable if it does not exist
await peopleTable.CreateIfNotExistsAsync();
```

## <a name="add-an-entity-to-a-table"></a>将实体添加到表

要将实体添加到表，请创建用于定义实体的属性的类。 以下代码将定义一个名为 `CustomerEntity` 的实体类，其使用客户的名字和姓氏分别作为行键和分区键。

```cs
public class CustomerEntity : TableEntity
{
    public CustomerEntity(string lastName, string firstName)
    {
        this.PartitionKey = lastName;
        this.RowKey = firstName;
    }

    public CustomerEntity() { }

    public string Email { get; set; }

    public string PhoneNumber { get; set; }
}
```

涉及这些实体的表操作将使用之前在[代码访问表](#access-tables-in-code)中创建的 `CloudTable` 对象。 `TableOperation` 对象表示将完成的操作。 以下代码示例演示如何创建 `CloudTable` 对象和 `CustomerEntity` 对象。 为准备此操作，会创建一个 `TableOperation` 以将客户实体插入该表中。 最后，通过调用 `CloudTable.ExecuteAsync` 执行此操作。

```cs
// Create a new customer entity.
CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
customer1.Email = "Walter@contoso.com";
customer1.PhoneNumber = "425-555-0101";

// Create the TableOperation that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer1);

// Execute the insert operation.
await peopleTable.ExecuteAsync(insertOperation);
```

## <a name="insert-a-batch-of-entities"></a>插入一批实体

可以通过单个写入操作将多个实体插入表中。 以下代码示例将创建两个实体对象（“Jeff Smith”和“Ben Smith”），使用 `Insert` 方法将它们添加到 `TableBatchOperation` 对象，并通过调用 `CloudTable.ExecuteBatchAsync` 来启动操作。

```cs
// Create the batch operation.
TableBatchOperation batchOperation = new TableBatchOperation();

// Create a customer entity and add it to the table.
CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
customer1.Email = "Jeff@contoso.com";
customer1.PhoneNumber = "425-555-0104";

// Create another customer entity and add it to the table.
CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
customer2.Email = "Ben@contoso.com";
customer2.PhoneNumber = "425-555-0102";

// Add both customer entities to the batch insert operation.
batchOperation.Insert(customer1);
batchOperation.Insert(customer2);

// Execute the batch operation.
await peopleTable.ExecuteBatchAsync(batchOperation);
```

## <a name="get-all-of-the-entities-in-a-partition"></a>获取分区中的所有实体

若要查询表以获取某个分区中的所有实体，请使用 `TableQuery` 对象。 以下代码示例指定了一个筛选器，以筛选分区键为“Smith”的实体。 此示例会将查询结果中每个实体的字段输出到控制台。

```cs
// Construct the query operation for all customer entities where PartitionKey="Smith".
TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

// Print the fields for each customer.
TableContinuationToken token = null;
do
{
    TableQuerySegment<CustomerEntity> resultSegment = await peopleTable.ExecuteQuerySegmentedAsync(query, token);
    token = resultSegment.ContinuationToken;

    foreach (CustomerEntity entity in resultSegment.Results)
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
    }
} while (token != null);
```

## <a name="get-a-single-entity"></a>获取单个实体

可以编写查询以获取单个特定实体。 以下代码使用 `TableOperation` 对象来指定名为“Ben Smith”的客户。 此方法只返回一个实体，而不是一个集合，并且 `TableResult.Result` 中返回的值为 `CustomerEntity` 对象。 在查询中指定分区键和行键是从 `Table` 服务中检索单个实体最快的方法。

```cs
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);

// Print the phone number of the result.
if (retrievedResult.Result != null)
   Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
else
   Console.WriteLine("The phone number could not be retrieved.");
```

## <a name="delete-an-entity"></a>删除实体

可以在找到实体后将其删除。 以下代码查找并删除名为“Ben Smith”的客户实体：

```cs
// Create a retrieve operation that expects a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the operation.
TableResult retrievedResult = peopleTable.Execute(retrieveOperation);

// Assign the result to a CustomerEntity object.
CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

// Create the Delete TableOperation and then execute it.
if (deleteEntity != null)
{
   TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

   // Execute the operation.
   await peopleTable.ExecuteAsync(deleteOperation);

   Console.WriteLine("Entity deleted.");
}

else
   Console.WriteLine("Couldn't delete the entity.");
```

## <a name="next-steps"></a>后续步骤
[!INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]
