<properties title="Getting Started with Azure Storage" pageTitle="Azure 存储入门" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="vs-getting-started" ms.devlang="na" ms.topic="article" ms.date="10/10/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
> - [入门](/documentation/articles/vs-storage-aspnet5-getting-started-tables/)
> - [发生了什么情况](/documentation/articles/vs-storage-aspnet5-what-happened/)

## Azure 存储入门（ASP.NET vNext 项目）

> [AZURE.SELECTOR]
> - [Blob](/documentation/articles/vs-storage-aspnet5-getting-started-blobs/)
> - [队列](/documentation/articles/vs-storage-aspnet5-getting-started-queues/)
> - [表](/documentation/articles/vs-storage-aspnet5-getting-started-tables/)

Azure 表存储服务使用户可以存储大量结构化数据。该服务是一个 NoSQL 数据存储，接受来自 Azure 云内部和外部的通过验证的呼叫。Azure 表最适合存储结构化非关系型数据。有关详细信息，请参阅[如何通过 .NET 使用表存储](http://azure.microsoft.com/zh-cn/documentation/articles/storage-dotnet-how-to-use-tables/#create-table "How to use Table Storage from .NET")。

若要以编程方式访问 ASP.NET 5 项目中的表，你需要添加以下项（如果尚未存在）。

1. 在您希望以编程方式访问 Azure 存储的任何 C# 文件中，将以下代码命名空间声明添加到文件的顶部。

		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Table;
		using Microsoft.Framework.ConfigurationModel;
		using System.Threading.Tasks;

2. 使用以下代码获取配置设置。

		Configuration = new Configuration()
                .AddJsonFile("config.json")
                .AddEnvironmentVariables();

#####获取存储连接字符串
在你可以使用表执行任何操作之前，你需要先获取将存放表的存储帐户的连接字符串。你可以使用 **CloudStorageAccount** 类型来表示你的存储帐户信息。如果你使用的是 ASP.NET vNext 项目，则可以调用 Configuration 对象的 get 方法从 Azure 服务配置获取存储连接字符串和存储帐户信息，如以下代码所示。

**注意：** 在 ASP.NET 5 中执行调出 Azure 存储空间的 API 是异步的。有关详细信息，请参阅[使用 Async 和 Await 进行异步编程](http://msdn.microsoft.com/library/hh191443.aspx)。下面的代码假定正在使用异步编程方法。

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      config.Get("MicrosoftAzureStorage:<storageAccountName>_AzureStorageConnectionString"));

#####创建表
利用 **CloudTableClient** 对象，您可以获得表和实体的引用对象。以下代码将创建 **CloudTableClient** 对象并使用它创建新表。该代码将尝试引用名为"people"的表。如果找不到该名称的表，它将创建一个。

**注意：**本指南中的所有代码假定将构建的应用程序是 Azure 云服务项目，并且使用存储在 Azure 应用程序的服务配置中的存储连接字符串。另外，请在接下来的部分中，在代码的前面使用全部此代码。

	// Create the table client.
	CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
	
	// Create the table if it doesn't exist.
	CloudTable table = tableClient.GetTableReference("people");
	await table.CreateIfNotExistsAsync();

#####将实体添加到表
若要将实体添加到表，请创建用于定义实体的属性的类。以下代码定义了将客户的名字和姓氏分别用作行键和分区键的名为 **CustomerEntity** 的实体类。

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

将使用你之前在"创建表"中创建的 **CloudTable** 对象完成涉及实体的表操作。**TableOperation** 对象表示将完成的操作。以下代码示例演示如何创建 **CloudTable** 对象以及 **CustomerEntity** 对象。为准备此操作，会创建一个 **TableOperation** 以将客户实体插入该表中。最后，将通过调用 CloudTable.ExecuteAsync 执行此操作。

	// Create a new customer entity.
	CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
	customer1.Email = "Walter@contoso.com";
	customer1.PhoneNumber = "425-555-0101";
	
	// Create the TableOperation that inserts the customer entity.
	TableOperation insertOperation = TableOperation.Insert(customer1);
	
	// Execute the insert operation.
	await table.ExecuteAsync(insertOperation);

#####插入一批实体
你可以通过单个写入操作将多个实体插入表中。以下代码示例将创建两个实体对象（"Jeff Smith"和"Ben Smith"），使用 Insert 方法将它们添加到 **TableBatchOperation** 对象，然后通过调用 CloudTable.ExecuteBatchAsync 启动操作。

	// Create the CloudTable object that represents the "people" table.
	CloudTable table = tableClient.GetTableReference("people");
	
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
	await table.ExecuteBatchAsync(batchOperation);

#####获取分区中的所有实体
若要查询表以获取分区中的所有实体，请使用 **TableQuery** 对象。以下代码示例指定了一个筛选器，以筛选分区键为"Smith"的实体。此示例会将查询结果中每个实体的字段输出到控制台。

	// Construct the query operation for all customer entities where PartitionKey="Smith".
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

    // Print the fields for each customer.
    TableContinuationToken token = null;
    do
    {
    	TableQuerySegment<CustomerEntity> resultSegment = await table.ExecuteQuerySegmentedAsync(query, token);
		token = resultSegment.ContinuationToken;

		foreach (CustomerEntity entity in resultSegment.Results)
    	{
    		Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
    		entity.Email, entity.PhoneNumber);
        }
    } while (token != null);

    return View();


#####获取单个实体
你可以编写查询以获取单个特定实体。以下代码使用 **TableOperation** 对象来指定名为"Ben Smith"的客户。此方法仅返回一个实体，而不是一个集合，并且 TableResult.Result 中的返回值是一个 **CustomerEntity** 对象。在查询中同时指定分区键和行键是从**表**服务中检索单个实体的最快方法。

	// Create the CloudTable object that represents the "people" table.
	CloudTable table = tableClient.GetTableReference("people");
	
	// Create a retrieve operation that takes a customer entity.
	TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
	
	// Execute the retrieve operation.
	TableResult retrievedResult = await table.ExecuteAsync(retrieveOperation);
	
	// Print the phone number of the result.
	if (retrievedResult.Result != null)
	   Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
	else
	   Console.WriteLine("The phone number could not be retrieved.");

#####删除实体
你可以在找到实体后将其删除。以下代码将查找名为"Ben Smith"的客户实体，如果找到，会将其删除。

	// Create the CloudTable that represents the "people" table.
	CloudTable table = tableClient.GetTableReference("people");
	
	// Create a retrieve operation that expects a customer entity.
	TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
	
	// Execute the operation.
	TableResult retrievedResult = table.Execute(retrieveOperation);
	
	// Assign the result to a CustomerEntity object.
	CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;
	
	// Create the Delete TableOperation and then execute it.
	if (deleteEntity != null)
	{
	   TableOperation deleteOperation = TableOperation.Delete(deleteEntity);
	
	   // Execute the operation.
	   await table.ExecuteAsync(deleteOperation);
	
	   Console.WriteLine("Entity deleted.");
	}
	
	else
	   Console.WriteLine("Couldn't delete the entity.");

[了解有关 Azure 存储空间的详细信息](http://azure.microsoft.com/documentation/services/storage/)
另请参阅[在服务器资源管理器中浏览存储资源](http://msdn.microsoft.com/zh-cn/library/azure/ff683677.aspx)以及 [ASP.NET 5](http://www.asp.net/vnext)。
