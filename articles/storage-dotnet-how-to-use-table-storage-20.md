<properties linkid="dev-net-2-how-to-table-services" urlDisplayName="表服务 (2.0)" pageTitle="如何使用表存储 - Windows Azure 功能指南" metaKeywords="Azure 表入门   Azure nosql   Azure 大型结构化数据存储   Azure 表   Azure 表存储   Azure 表 .NET   Azure 表存储 .NET   Azure 表 C#   Azure 表存储 C#" description="了解如何使用表存储创建和删除表，以及在表中插入和查询实体。" metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="如何使用表存储服务"/>



# 如何使用表存储服务

<div class="dev-center-tutorial-selector">
<a href="/zh-cn/develop/net/how-to-guides/table-services-v17/" title="版本 1.7">版本 1.7</a>
<a href="/zh-cn/develop/net/how-to-guides/table-services/" title="版本 2.0" class="current">版本 2.0</a> 
</div>


本指南将演示如何使用 Windows Azure 表存储服务执行
常见方案。相关示例用 C\# 代码编写
并使用 Windows Azure Storage Client Library for .NET（2.0 版）。涉及的任务包括创建和删除表，
以及使用表实体。有关表的
更多信息，请参见[后续步骤][]一节。

## 目录

-   [什么是表服务][]
-   [概念][]
-   [创建 Windows Azure 存储帐户][]
-   [设置存储连接字符串][]
-   [如何以编程方式访问表存储][]
-   [如何创建表][]
-   [如何向表中添加实体][]
-   [如何插入一批实体][]
-   [如何检索分区中的所有实体][]
-   [如何检索分区中的一部分实体][]
-   [如何检索单个实体][]
-   [如何替换实体][]
-   [如何插入或替换实体][]
-   [如何查询实体属性子集][]
-   [如何删除实体][]
-   [如何删除表][]
-   [后续步骤][]

[WACOM.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

<h2><a name="create-account"></a><span class="short-header">创建帐户</span>创建 Windows Azure 存储帐户</h2>

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

<h2><a name="setup-connection-string"></a><span class="short-header">设置连接字符串</span>设置存储连接字符串</h2>

Windows Azure .NET 存储客户端库支持使用
存储连接字符串来配置终结点和用于访问存储服务
的凭据。您可以将存储连接字符串置于配置文件中，而不是在代码
中对其进行硬编码：

- 在使用 Windows Azure 云服务时，建议使用 Windows Azure 服务配置系统（*.csdef 和 *.cscfg 文件）存储连接字符串。
- 当使用 Windows Azure 网站、Windows Azure 虚拟机或构建准备在 Windows Azure 外部运行的 .NET 应用程序时，建议您使用 .NET 配置系统（如 web.config 或 app.config 文件）来存储连接字符串。

在上述两种情况下，您都可以使用 CloudConfigurationManager.GetSetting 方法检索连接字符串，本指南稍后部分将对此进行介绍。

### 使用云服务时配置连接字符串

该服务配置机制是 Windows Azure 云服务项目特有的，
它使您能够从 Windows Azure 管理门户动态更改配置设置，
而无需部署您的
应用程序。

在 Windows Azure 服务配置中配置
连接字符串：

1. 在 Visual Studio 解决方案资源管理器内 Windows Azure 部署
项目的“角色”文件夹中，右键单击 Web 角色或辅助角色，
然后单击“属性”。
    ![Blob5][Blob5]

2. 单击“设置”选项卡并按“添加设置”按钮。
    ![Blob6][Blob6]

    新的 Setting1 条目稍后将显示在设置网格中。

3. 在新的 Setting1 条目的“类型”下拉列表中，选择
    Connection String。
    ![Blob7][Blob7]

4. 单击 Setting1 条目最右侧的 ... 按钮。
    将打开“存储帐户连接字符串”对话框。

5. 选择是要定位到存储模拟器（在本地计算机上
模拟的 Windows Azure 存储），还是云中的
实际存储帐户。本指南中的代码使用其中
任一方式。如果您希望使用我们之前在 Windows Azure 中
创建的存储帐户存储 Blob 数据，请输入从本教程前面
的步骤中复制的“主访问密钥”值。
    ![Blob8][Blob8]

6. 将条目“名称”从 Setting1 更改为更友好的名称，
例如 StorageConnectionString。稍后将在本指南的代码中引用
此连接字符串。
    ![Blob9][Blob9]
	
### 使用 .NET 配置来配置连接字符串

如果您正在编写不是 Windows Azure 云服务的应用程序（请参见上一节），则建议您使用 .NET 配置系统（如 web.config 或 app.config）。这包括 Windows Azure 网站或 Windows Azure 虚拟机，以及设计为在 Windows Azure 外部运行的应用程序。您可以使用 <appSettings> 元素存储连接字符串，如下所示：

	<configuration>
  		<appSettings>
    		<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]" />
  		</appSettings>
	</configuration>

阅读[配置连接字符串][]，了解有关存储连接字符串的详细信息。
	
您现在即可准备执行本指南中的操作任务。

<h2> <a name="configure-access"> </a><span  class="short-header">以编程方式访问</span>如何以编程方式访问表存储</h2>

<h3>获得程序集</h3>
您可以使用 NuGet 获得 Microsoft.WindowsAzure.Storage.dll 程序集。在“解决方案资源管理器”中，右键单击您的项目并选择“管理 NuGet 包”。在线搜索“WindowsAzure.Storage”，然后单击“安装”以安装 Windows Azure 存储包和依赖项。

Microsoft.WindowsAzure.Storage.dll 还包含在 Windows Azure SDK for .NET 2.0 中，可从 <a href="http://www.windowsazure.com/zh-cn/develop/net/#">.NET 开发人员中心</a>下载该版本。程序集安装在 %Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\v2.0\ref\ 目录中。

<h3>命名空间声明</h3>
将以下代码命名空间声明添加到任何 C\# 文件的顶部，
您希望使用此类文件以编程方式访问 Windows Azure 存储：

    using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;

确保您引用 Microsoft.WindowsAzure.Storage.dll 程序集。

<h3>检索连接字符串</h3>
可以使用 CloudStorageAccount 类型来表示您的
存储帐户信息。如果您使用的是 Windows Azure 项目
模板并且/或者引用 Microsoft.WindowsAzure.CloudConfigurationManager 命名空间，则可以使用
 CloudConfigurationManager 类型从 Windows Azure 服务
配置中检索存储连接字符串和存储帐户
信息：

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

如果您要创建应用程序而不引用 Microsoft.WindowsAzure.CloudConfigurationManager，并且您的连接字符串位于前面显示的 web.config 或 app.config 中，那么您可以使用 ConfigurationManager 来检索连接字符串。您需要将对 System.Configuration.dll 的引用添加到您的项目中，并为其添加另一个命名空间声明：

	using System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

<h3>ODataLib 依赖项</h3>
.NET 存储客户端库中的 ODataLib 依赖项可通过在 NuGet （而非 WCF 数据服务）上获得的 ODataLib（5.0.2 版）包来解析。ODataLib 库可直接下载或者通过 NuGet 由代码项目引用。特定的 ODataLib 包为 [OData]、[Edm] 和 [Spatial]。

<h2><a name="create-table"></a><span class="short-header">创建表</span>如何创建表</h2>

利用 CloudTableClient 对象，您可以获得表和实体的引用
对象。以下代码将创建 CloudTableClient 对象并使用
它创建新表。本指南中的所有代码假定将构建的
应用程序是 Windows Azure 云服务项目，并且使用存储在 Windows Azure 应用程序
的服务配置中的存储连接字符串。

    // 从连接字符串检索存储帐户。
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // 创建表客户端。
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // 如果该表不存在，则创建它。
    CloudTable table = tableClient.GetTableReference("people");
    table.CreateIfNotExists();

<h2><a name="add-entity"></a><span class="short-header">向表中添加实体</span>如何向表中添加实体</h2>

实体将映射到 C\# 对象，这是
使用派生自 TableEntity 的自定义类映射的。若要将实体添加到表，请创建用于
定义实体的属性的类。以下代码定义
将客户的名字和姓氏分别
用作行键和分区键的实体类。实体的分区键和行键共同
唯一地标识表中的实体。查询分区键相同的实体的速度
快于查询分区键不同的实体的速度，
但使用不同的分区键可实现更高的并行操作
可伸缩性。对于应存储在表服务中的任何属性，该属性必须是
公开 get 和 set 的受支持类型的公共属性。
此外，您的实体类型*必须*公开不带参数的构造函数。

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

可使用您在“如何创建表”中创建的 CloudTable 对象执行涉及实体的表操作。要执行的操作由 TableOperation 对象
表示。以下代码示例演示如何创建 CloudTable 对象以及 CustomerEntity 对象。为准备此操作，会创建一个 TableOperation 以将客户实体插入该表中。最后，将通过调用 CloudTable.Execute 执行此操作。

    // 从连接字符串检索存储帐户。
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // 创建表客户端。
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
	
	// 创建表示“people”表的 CloudTable 对象。
	CloudTable table = tableClient.GetTableReference("people");

    // 创建新客户实体。
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // 创建插入客户实体的 TableOperation。
    TableOperation insertOperation = TableOperation.Insert(customer1);

    // 执行插入操作。
    table.Execute(insertOperation);

<h2><a name="insert-batch"></a><span class="short-header">插入一批实体</span>如何插入一批实体</h2>

您可以通过一次写入操作将一批实体
插入表中。批处理操作的一些其他
注意事项：

1. 您可以在同一批处理操作中执行更新、删除和插入操作。
2. 单个批处理操作最多可包含 100 个实体。
3. 单次批处理操作中的所有实体都必须具有
相同的分区键。
4. 可以作为批处理操作执行查询时，此操作必须是批处理中仅有的操作。

<!-- -->
以下代码示例创建两个实体对象，并使用 Insert 方法将其中
每个对象都添加到 TableBatchOperation 中。然后调用 CloudTable.Execute 以执行此操作。

    // 从连接字符串检索存储帐户。
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // 创建表客户端。
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    
	// 创建表示“people”表的 CloudTable 对象。
    CloudTable table = tableClient.GetTableReference("people");

    // 创建批处理操作。
    TableBatchOperation batchOperation = new TableBatchOperation();

    // 创建一个客户实体，并将其添加到表中。
	CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
	customer1.Email = "Jeff@contoso.com";
	customer1.PhoneNumber = "425-555-0104";
            
	// 创建另一个客户实体，并将其添加到表中。
	CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
	customer2.Email = "Ben@contoso.com";
	customer2.PhoneNumber = "425-555-0102";
            
	// 将两个客户实体添加到批处理插入操作中。
	batchOperation.Insert(customer1);
	batchOperation.Insert(customer2);

	// 执行批处理操作。
	table.ExecuteBatch(batchOperation);

<h2><a name="retrieve-all-entities"></a><span class="short-header">检索所有实体</span>如何检索分区中的所有实体</h2>

若要查询表以获取分区中的所有实体，请使用 TableQuery 对象。
以下代码示例指定了一个筛选器，以筛选分区键为
“Smith”的实体。此示例会将查询结果中每个实体的字段
打印到控制台。

    // 从连接字符串检索存储帐户。
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // 创建表客户端。
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // 创建表示“people”表的 CloudTable 对象。
    CloudTable table = tableClient.GetTableReference("people");

    // 构造所有客户实体的查询操作，其中 PartitionKey="Smith"。
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

    // 打印每个客户的字段。
    foreach (CustomerEntity entity in table.ExecuteQuery(query))
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

<h2><a name="retrieve-range-entities"></a><span class="short-header">检索一部分实体</span>如何检索分区中的一部分实体</h2>

如果不想查询分区中的所有实体，则可以通过结合使用
分区键筛选器与行键筛选器来指定一个范围。以下代码示例使用两个筛选器来获取分区“Smith”中的、
行键（名字）以字母“E”前面的字母开头的所有实体，
然后输出查询结果。

    // 从连接字符串检索存储帐户。
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // 创建表客户端。
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // 创建表示“people”表的 CloudTable 对象。
    CloudTable table = tableClient.GetTableReference("people");

	// 创建表查询。
    TableQuery<CustomerEntity> rangeQuery = new TableQuery<CustomerEntity>().Where(
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"),
            TableOperators.And,
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "E")));

    // 循环访问结果，显示实体的有关信息。
    foreach (CustomerEntity entity in table.ExecuteQuery(rangeQuery))
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

<h2><a name="retrieve-single-entity"></a><span class="short-header">检索单个实体</span>如何检索单个实体</h2>

您可以编写查询以检索单个
特定实体。以下代码
使用 TableOperation 来指定客户“Ben Smith”。
此方法仅返回一个实体，而不是一个集合，
并且 TableResult.Result 中的返回值是一个 CustomerEntity。
在查询中指定分区键和行键是从表服务中检索
单个实体的最快方法。

    // 从连接字符串检索存储帐户。
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // 创建表客户端。
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // 创建表示“people”表的 CloudTable 对象。
    CloudTable table = tableClient.GetTableReference("people");

    // 创建取客户实体的检索操作。
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // 执行检索操作。
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // 打印结果的电话号码。
	if (retrievedResult.Result != null)
	   Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
	else
	   Console.WriteLine("The phone number could not be retrieved.");

<h2><a name="replace-entity"></a><span class="short-header">替换实体</span>如何替换实体</h2>

若要更新实体，请从表服务中检索它，修改实体对象，
然后将更改保存回表服务。以下代码
将更改现有客户的电话号码。代替
调用 Insert，此代码使用
Replace。这将导致在服务器上完全替换该实体，
除非服务器上的该实体自检索到它以后发生更改，
在此情况下，该操作将失败。操作失败将防止您的应用程序
无意中覆盖应用程序的其他组件
在检索与更新之间所做的更改。正确处理此失败问题的方法是
再次检索实体，进行更改（如果仍有效），然后再次
执行 Replace 操作。下一节将为您演示
如何重写此行为。

    // 从连接字符串检索存储帐户。
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // 创建表客户端
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // 创建表示“people”表的 CloudTable 对象。
    CloudTable table = tableClient.GetTableReference("people");

    // 创建取客户实体的检索操作。
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // 执行操作。
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // 将结果赋给一个 CustomerEntity 对象。
    CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

    if (updateEntity != null)
	{
	   // Change the phone number.
	   updateEntity.PhoneNumber = "425-555-0105";

	   // Create the InsertOrReplace TableOperation
	   TableOperation updateOperation = TableOperation.Replace(updateEntity);

	   // Execute the operation.
	   table.Execute(updateOperation);

	   Console.WriteLine("Entity updated.");
	}

	else
	   Console.WriteLine("Entity could not be retrieved.");

<h2><a name="insert-or-replace-entity"></a><span class="short-header">插入或替换实体</span>如何插入或替换实体</h2>

如果该实体自从服务器中检索到它以后发生更改，
则 Replace 操作将失败。此外，您必须首先从服务器中
检索该实体，Replace 才能成功。
但是，有时您不知道服务器上是否存在该实体以及存储在其中的
当前值是否无关 - 更新操作会将其
全部覆盖。为此，您应使用InsertOrReplace
 操作。如果该实体不存在，此操作将插入它，如果存在，则替换它，
而不管上次更新是何时进行的。在以下
代码示例中，仍将检索 Ben Smith 的客户实体，但稍后会使用 InsertOrReplace 将其
保存回服务器。将覆盖
在检索与更新操作之间对实体进行的任何
更新。

    // 从连接字符串检索存储帐户。
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // 创建表客户端。
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // 创建表示“people”表的 CloudTable 对象。
    CloudTable table = tableClient.GetTableReference("people");

    // 创建取客户实体的检索操作。
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // 执行操作。
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // 将结果赋给一个 CustomerEntity 对象。
    CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

    if (updateEntity != null)
	{
	   // Change the phone number.
	   updateEntity.PhoneNumber = "425-555-1234";

	   // Create the InsertOrReplace TableOperation
	   TableOperation insertOrReplaceOperation = TableOperation.InsertOrReplace(updateEntity);

	   // Execute the operation.
	   table.Execute(insertOrReplaceOperation);

	   Console.WriteLine("Entity was updated.");
	}

	else
	   Console.WriteLine("Entity could not be retrieved.");

<h2><a name="query-entity-properties"></a><span class="short-header">查询属性子集</span>如何查询实体属性子集</h2>

表查询可以只检索实体中的少数几个属性而不是所有实体属性。此方法称为投影，可减少带宽并提高查询性能，尤其适用于大型实体。以下代码
中的查询只返回表中实体的电子邮件
地址。这可通过使用 DynamicTableEntity 和 EntityResolver 的
查询来实现。您可以在此[博客文章][]中了解有关投影的详细信息。请注意，本地存储仿真程序不支持投影，因此，此代码仅在使用表服务中的帐户时才能运行。

    // 从连接字符串检索存储帐户
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // 创建表客户端
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // 创建表示“people”表的 CloudTable。
    CloudTable table = tableClient.GetTableReference("people");

    // 定义查询，并且仅选择 Email 属性
    TableQuery<DynamicTableEntity> projectionQuery = new TableQuery<DynamicTableEntity>().Select(new string[] { "Email" });

    // 定义一个实体解析器以便在检索后使用实体。
    EntityResolver<string> resolver = (pk, rk, ts, props, etag) => props.ContainsKey("Email") ? props["Email"].StringValue : null;

    foreach (string projectedEmail in table.ExecuteQuery(projectionQuery, resolver, null, null))
    {
        Console.WriteLine(projectedEmail);
    }

<h2><a name="delete-entity"></a><span class="short-header">删除实体</span>如何删除实体</h2>

在检索实体之后，可使用更新实体的相同演示模式轻松
删除该实体。以下代码
检索并删除一个客户实体。

    // 从连接字符串检索存储帐户
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // 创建表客户端
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // 创建表示“people”表的 CloudTable。
    CloudTable table = tableClient.GetTableReference("people");

    // 创建要对客户实体执行的检索操作。
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // 执行操作。
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // 将结果赋给一个 CustomerEntity。
    CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

    // 创建 Delete TableOperation。
	if (deleteEntity != null)
	{
	   TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

	   // 执行操作。
	   table.Execute(deleteOperation);

	   Console.WriteLine("Entity deleted.");
	}

	else
	   Console.WriteLine("Could not retrieve the entity.");

<h2><a name="delete-table"></a><span class="short-header">删除表</span>如何删除表</h2>

最后，以下代码示例将从存储帐户中删除表。在删除
表之后的一段时间内无法重新
创建它。

    // 从连接字符串检索存储帐户。
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // 创建表客户端。
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // 创建表示“people”表的 CloudTable。
    CloudTable table = tableClient.GetTableReference("people");

    // 如果表存在，则删除它。
    table.DeleteIfExists();

<h2><a name="next-steps"></a><span class="short-header">后续步骤</span>后续步骤</h2>

现在，您已了解有关表存储的基本知识，单击下面的链接可了解如何
执行更复杂的存储任务。

<ul>
<li>查看 Blob 服务参考文档，了解有关可用 API 的完整详情：
  <ul>
    <li><a href="http://msdn.microsoft.com/zh-cn/library/windowsazure/wa_storage_api_ref_reference_home.aspx">.NET 存储客户端库参考</a>
    </li>
    <li><a href="http://msdn.microsoft.com/zh-cn/library/windowsazure/dd179355">REST API 参考</a></li>
  </ul>
</li>
<li>在以下位置了解有关可以使用 Windows Azure 存储执行的更高级任务：<a href="http://msdn.microsoft.com/zh-cn/library/windowsazure/gg433040.aspx">在 Windows Azure 中存储和访问数据</a>。</li>
<li>查看更多功能指南，以了解在 Windows Azure 中存储数据的其他方式。
  <ul>
    <li>使用 <a href="/zh-cn/develop/net/how-to-guides/blob-storage/">Blob 存储</a>存储非结构化数据。</li>
    <li>使用 <a href="/zh-cn/develop/net/how-to-guides/sql-database/">SQL Database</a> 存储关系数据。</li>
  </ul>
</li>
</ul>

  [后续步骤]: #next-steps
  [什么是表服务？]: #what-is
  [概念]: #concepts
  [创建 Windows Azure 存储帐户]: #create-account
  [在 Visual Studio 中创建 Windows Azure 项目]: #create-project
  [配置应用程序以访问存储]: #configure-access
  [设置存储连接字符串]: #setup-connection-string
  [如何以编程方式访问表存储]: #configure-access
  [如何创建表]: #create-table
  [如何向表中添加实体]: #add-entity
  [如何插入一批实体]: #insert-batch
  [如何检索分区中的所有实体]: #retrieve-all-entities
  [如何检索分区中的一部分实体]: #retrieve-range-entities
  [如何检索单个实体]: #retrieve-single-entity
  [如何替换实体]: #replace-entity
  [如何插入或替换实体]: #insert-or-replace-entity
  [如何查询实体属性子集]: #query-entity-properties
  [如何删除实体]: #delete-entity
  [如何删除表]: #delete-table
  [下载和安装 Windows Azure SDK for .NET]: /zh-cn/develop/net/
  [在 Visual Studio 中创建 Windows Azure 项目]: http://msdn.microsoft.com/zh-cn/library/windowsazure/ee405487.aspx
  
  [Blob5]: ./media/storage-dotnet-how-to-use-table-storage-20/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-table-storage-20/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-table-storage-20/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-table-storage-20/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-table-storage-20/blob9.png
  
  [博客文章]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
  [在 Windows Azure 中存储和访问数据]: http://msdn.microsoft.com/zh-cn/library/windowsazure/gg433040.aspx
  [Windows Azure 存储团队博客（可能为英文网页）]: http://blogs.msdn.com/b/windowsazurestorage/
  [配置连接字符串]: http://msdn.microsoft.com/zh-cn/library/windowsazure/ee758697.aspx
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2

