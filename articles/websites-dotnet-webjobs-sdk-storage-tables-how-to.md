<properties pageTitle="如何通过 WebJobs SDK 使用 Azure 表存储" metaKeywords="WebJobs SDK Azure table storage .NET C#" description="如何通过 WebJobs SDK 使用 Azure 表存储创建表，将实体添加到表，并读取现有表。" metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="web-sites,storage" documentationCenter=".NET" title="How to use Azure table storage with the WebJobs SDK" authors="tdykstra" manager="wpickett" editor="jimbe" />

# 如何通过 WebJobs SDK 使用 Azure 表存储

本指南提供了 C# 代码示例，用于演示如何使用 [WebJobs SDK](../websites-dotnet-webjobs-sdk/) 版本 1.x 读取和写入 Azure 存储表。

本指南假定你知道[如何在 Visual Studio 中创建 Web 作业项目，其中包含指向存储帐户](../websites-dotnet-webjobs-sdk-get-started/)的连接字符串。
		
一些代码段显示了[手动调用](../websites-dotnet-webjobs-sdk-storage-queues-how-to/#manual)（即，不是通过使用触发器属性之一调用）的函数中使用的 `Table` 属性。 

## 目录

-   [如何向表中添加实体](#ingress)
-   [实时监视](#monitor)
-   [如何从表中读取多个实体](#multiple)
-   [如何从表中读取单个实体](#readone)
-   [如何直接使用 .NET 存储 API 处理表](#readone)
-   [队列操作指南文章涵盖的相关主题](#queues)
-   [后续步骤](#nextsteps)

## <a id="ingress"></a> 如何向表中添加实体

若要将实体添加到表中，请使用带 `ICollector<T>` 或 `IAsyncCollector<T>` 参数的 `Table` 属性，其中， `T` specifies the schema of the entities you want to add. The attribute constructor takes a string parameter that specifies the name of the table. 

The following code sample adds `Person` 实体到名为 *Ingress* 的表。

		[NoAutomaticTrigger]
		public static void IngressDemo(
		    [Table("Ingress")] ICollector<Person> tableBinding)
		{
		    for (int i = 0; i < 100000; i++)
		    {
		        tableBinding.Add(
		            new Person() { 
		                PartitionKey = "Test", 
		                RowKey = i.ToString(), 
		                Name = "Name" }
		            );
		    }
		}

通常使用类型与 `ICollector` 派生自 `TableEntity` 或实现 `ITableEntity`，但它并不是必需的。以下 `Person` 类之一适用于前面 `Ingress` 方法中所示的代码。

		public class Person : TableEntity
		{
		    public string Name { get; set; }
		}

		public class Person
		{
		    public string PartitionKey { get; set; }
		    public string RowKey { get; set; }
		    public string Name { get; set; }
		}

如果你想要直接使用 Azure 存储 API，则可以向方法签名添加一个 `CloudStorageAccount` 参数。

## <a id="monitor"></a> 实时监视

因为数据入口函数通常处理大量数据，WebJobs SDK 仪表板提供了实时监视的数据。"调用日志"部分告诉你函数是否仍在运行。

![Ingress function running](./media/websites-dotnet-webjobs-sdk-storage-tables-how-to/ingressrunning.png)

"调用详细信息"页在运行时报告函数的进度（写入的实体数），并且让你中止它。 

![Ingress function running](./media/websites-dotnet-webjobs-sdk-storage-tables-how-to/ingressprogress.png)

该函数完成时，"调用详细信息"页将报告写入的行数。

![Ingress function finished](./media/websites-dotnet-webjobs-sdk-storage-tables-how-to/ingresssuccess.png)

## <a id="multiple"></a> 如何从表中读取多个实体

若要读取表，请使用带 `IQueryable<T>` 参数的 `Table` 属性，其中，类型 `T` derives from `TableEntity` 或实现 `ITableEntity`。

以下代码示例读取并记录 `Ingress` 表中的所有行：
 
		public static void ReadTable(
		    [Table("Ingress")] IQueryable<Person> tableBinding,
		    TextWriter logger)
		{
		    var query = from p in tableBinding select p;
		    foreach (Person person in query)
		    {
		        logger.WriteLine("PK:{0}, RK:{1}, Name:{2}", 
		            person.PartitionKey, person.RowKey, person.Name);
		    }
		}

### <a id="readone"></a> 如何从表中读取单个实体

有一个 `Table` 属性构造函数，你可以在想要绑定到单个表实体时指定的分区键和行键的两个附加参数的构造函数。

下面的代码示例基于队列消息中收到的分区键和行键值读取 `Person` 实体的表行：  

		public static void ReadTableEntity(
		    [QueueTrigger("inputqueue")] Person personInQueue,
		    [Table("persontable","{PartitionKey}", "{RowKey}")] Person personInTable,
		    TextWriter logger)
		{
		    if (personInTable == null)
		    {
		        logger.WriteLine("Person not found: PK:{0}, RK:{1}",
		                personInQueue.PartitionKey, personInQueue.RowKey);
		    }
		    else
		    {
		        logger.WriteLine("Person found: PK:{0}, RK:{1}, Name:{2}",
		                personInTable.PartitionKey, personInTable.RowKey, personInTable.Name);
		    }
		}


此示例中的 `Person` 类不一定实现 `ITableEntity`。

## <a id="storageapi"></a> 如何直接使用 .NET 存储 API 处理表

你还可以使用包含 `CloudTable` 对象的 `Table` 属性来更灵活地处理表。

下面的代码示例使用 `CloudTable` 对象将单个实体添加到 *Ingress* 表。 
 
		public static void UseStorageAPI(
		    [Table("Ingress")] CloudTable tableBinding,
		    TextWriter logger)
		{
		    var person = new Person()
		        {
		            PartitionKey = "Test",
		            RowKey = "100",
		            Name = "Name"
		        };
		    TableOperation insertOperation = TableOperation.Insert(person);
		    tableBinding.Execute(insertOperation);
		}

有关如何使用 `CloudTable` 对象的详细信息，请参阅[如何通过 .NET 使用表存储](../storage-dotnet-how-to-use-tables/)。 

## <a id="queues"></a>队列操作指南文章涵盖的相关主题

有关如何处理队列消息触发的表处理，或者不特定于表处理的 WebJobs SDK 方案的信息，请参阅[如何通过 WebJobs SDK 使用 Azure 队列存储](../websites-dotnet-webjobs-sdk-storage-queues-how-to/)。 

该文章涵盖的主题包括：

* 异步函数
* 多个实例
* 正常关闭
* 在函数正文中使用 WebJobs SDK 属性
* 在代码中设置 SDK 连接字符串。
* 在代码中设置 WebJobs SDK 构造函数参数的值
* 手动触发函数
* 写入日志

## <a id="nextsteps"></a> 后续步骤

本指南提供的代码示例演示了如何处理常见方案以操作 Azure 表。有关如何使用 Azure Web 作业和 WebJobs SDK 的详细信息，请参阅[有关 Azure Web 作业的推荐资源](/zh-cn/documentation/articles/websites-webjobs-resources/)。
