---
title: 适用于 Azure Cosmos DB 的数据库迁移工具 | Microsoft Docs
description: 了解如何使用开源 Azure Cosmos DB 数据迁移工具从各种源将数据导入 Azure Cosmos DB 中，这些源包括 MongoDB、SQL Server、表存储、Amazon DynamoDB、CSV 和 JSON 文件。 将 CSV 转换为 JSON。
keywords: csv 到 json, 数据库迁移工具, 将 csv 转换为 json
services: cosmos-db
author: SnehaGunda
manager: kfile
editor: monicar
ms.service: cosmos-db
ms.devlang: na
ms.topic: tutorial
ms.date: 03/30/2018
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: 07c41bb02863cc32372722cbcbac4be2c5071860
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34611460"
---
# <a name="azure-cosmos-db-data-migration-tool"></a>Azure Cosmos DB：数据迁移工具

本教程说明如何使用可将数据从各种源导入 Azure Cosmos DB 集合和表的 Azure Cosmos DB 数据迁移工具。 可以从 JSON 文件、CSV 文件、SQL、MongoDB、Azure 表存储、Amazon DynamoDB 甚至 Azure Cosmos DB SQL API 集合导入数据，并可以将数据迁移到集合和表以便在 Azure Cosmos DB 中使用。 还可在从单个分区集合迁移到 SQL API 的多分区集合时使用数据迁移工具。

要对 Azure Cosmos DB 使用哪个 API？ 
* **[SQL API](documentdb-introduction.md)** - 可以使用数据迁移工具中提供的任何源选项导入数据。
* **[表 API](table-introduction.md)** - 可以使用数据迁移工具或 AzCopy 导入数据。 有关详细信息，请参阅[导入要在 Azure Cosmos DB 表 API 中使用的数据](table-import.md)。
* **[MongoDB API](mongodb-introduction.md)** - 数据迁移工具当前不支持将 Azure Cosmos DB MongoDB API 作为源或目标。 如果想要从 Azure Cosmos DB 的 MongoDB API 集合中迁入或迁出数据，请参阅 [Azure Cosmos DB：如何迁移 MongoDB API 的数据](mongodb-migrate.md)的相关说明。 仍可使用数据迁移工具将数据从 MongoDB 导出到 Azure Cosmos DB SQL API 集合中，以便使用 SQL API。 
* **[图形 API](graph-introduction.md)** - 目前，图形 API 帐户不支持将数据迁移工具用作导入工具。 

本教程涵盖以下任务：

> [!div class="checklist"]
> * 安装数据迁移工具
> * 从不同的数据源导入数据
> * 从 Azure Cosmos DB 导出到 JSON

## <a id="Prerequisites"></a>先决条件
在按照本文中的说明操作之前，请确保已安装下列项：

* [Microsoft .NET Framework 4.51](https://www.microsoft.com/download/developer-tools.aspx) 或更高版本。

* 增加吞吐量：数据迁移的持续时间取决于为单个集合或一组集合设置的吞吐量。 请确保对于较大的数据迁移增加吞吐量。 完成迁移后，减少吞吐量以节约成本。 有关在 Azure 门户中增加吞吐量的详细信息，请参阅 Azure Cosmos DB 中的性能级别和定价层。

## <a id="Overviewl"></a>概述
数据迁移工具是一种开源解决方案，可将数据从各种源导入 Azure Cosmos DB 中，这些源包括：

* JSON 文件
* MongoDB
* SQL Server
* CSV 文件
* Azure 表存储
* Amazon DynamoDB
* HBase
* Azure Cosmos DB 集合

虽然导入工具包括图形用户界面 (dtui.exe)，但是也可从命令行 (dt.exe) 中驱动。 实际上，有一个选项可以在通过用户界面设置导入后输出关联的命令。 可以转换表格源数据（例如 SQL Server 或 CSV 文件），以便可以在导入过程中创建层次结构关系（子文档）。 继续阅读，以了解有关源选项、用于从每个源导入的示例命令以及目标选项的详细信息，并查看导入结果。

## <a id="Install"></a>安装
迁移工具源代码可在 GitHub 上的[此存储库](https://github.com/azure/azure-documentdb-datamigrationtool)中获得。 可以在本地下载并编译解决方案，或者[下载一个预编译的库](https://cosmosdbportalstorage.blob.core.windows.net/datamigrationtool/2018.02.28-1.8.1/dt-1.8.1.zip)，然后运行以下任一项：

* **Dtui.exe**：该工具的图形界面版本
* **Dt.exe**：该工具的命令行版本

## <a name="select-data-source"></a>选择数据源

安装该工具之后，就可以导入数据。 要导入什么类型的数据？

* [JSON 文件](#JSON)
* [MongoDB](#MongoDB)
* [MongoDB 导出文件](#MongoDBExport)
* [SQL Server](#SQL)
* [CSV 文件](#CSV)
* [Azure 表存储](#AzureTableSource)
* [Amazon DynamoDB](#DynamoDBSource)
* [Blob](#BlobImport)
* [Azure Cosmos DB 集合](#SQLSource)
* [HBase](#HBaseSource)
* [Azure Cosmos DB 批量导入](#SQLBulkImport)
* [Azure Cosmos DB 顺序记录导入](#DocumentDSeqTarget)


## <a id="JSON"></a>导入 JSON 文件
借助 JSON 文件源导入程序选项，可以导入一个或多个只包含单个文档的 JSON 文件或者每个都包含一组 JSON 文档的 JSON 文件。 添加包含 JSON 文件的文件夹以供导入时，可以选择递归搜索子文件夹中的文件。

![JSON 文件源选项的屏幕截图 - 数据库迁移工具](./media/import-data/jsonsource.png)

下面是一些导入 JSON 文件的命令行示例：

    #Import a single JSON file
    dt.exe /s:JsonFile /s.Files:.\Sessions.json /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Sessions /t.CollectionThroughput:2500

    #Import a directory of JSON files
    dt.exe /s:JsonFile /s.Files:C:\TESessions\*.json /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Sessions /t.CollectionThroughput:2500

    #Import a directory (including sub-directories) of JSON files
    dt.exe /s:JsonFile /s.Files:C:\LastFMMusic\**\*.json /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Music /t.CollectionThroughput:2500

    #Import a directory (single), directory (recursive), and individual JSON files
    dt.exe /s:JsonFile /s.Files:C:\Tweets\*.*;C:\LargeDocs\**\*.*;C:\TESessions\Session48172.json;C:\TESessions\Session48173.json;C:\TESessions\Session48174.json;C:\TESessions\Session48175.json;C:\TESessions\Session48177.json /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:subs /t.CollectionThroughput:2500

    #Import a single JSON file and partition the data across 4 collections
    dt.exe /s:JsonFile /s.Files:D:\\CompanyData\\Companies.json /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:comp[1-4] /t.PartitionKey:name /t.CollectionThroughput:2500

## <a id="MongoDB"></a>从 MongoDB 导入

> [!IMPORTANT]
> 如果要导入到支持 MongoDB 的 Azure Cosmos DB 帐户，请按照这些[说明](mongodb-migrate.md)操作。
> 
> 

借助 MongoDB 源导入程序选项，可从单个 MongoDB 集合中导入，并且选择使用查询筛选文档和/或使用投影来修改文档结构。  

![MongoDB 源选项的屏幕截图](./media/import-data/mongodbsource.png)

连接字符串是标准 MongoDB 格式：

    mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database>

> [!NOTE]
> 使用验证命令来确保可以访问在连接字符串字段中指定的 MongoDB 实例。
> 
> 

输入将从其中导入数据的集合的名称。 可以选择为查询（例如 {pop: {$gt: 5000}}）和/或投影（例如 {loc:0}）指定或提供一个文件来筛选和形成要导入的数据。

下面是一些从 MongoDB 中导入的命令行示例：

    #Import all documents from a MongoDB collection
    dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:BulkZips /t.IdField:_id /t.CollectionThroughput:2500

    #Import documents from a MongoDB collection which match the query and exclude the loc field
    dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /s.Query:{pop:{$gt:50000}} /s.Projection:{loc:0} /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:BulkZipsTransform /t.IdField:_id/t.CollectionThroughput:2500

## <a id="MongoDBExport"></a>导入 MongoDB 导出文件

> [!IMPORTANT]
> 如果要导入到支持 MongoDB 的 Azure Cosmos DB 帐户，请按照这些[说明](mongodb-migrate.md)操作。
> 
> 

借助 MongoDB 导出 JSON 文件源导入程序选项，可以导入一个或多个通过 mongoexport 实用程序生成的 JSON 文件。  

![MongoDB 导出源选项的屏幕截图](./media/import-data/mongodbexportsource.png)

添加包含 MongoDB 导出 JSON 文件的文件夹以供导入时，可以选择递归搜索子文件夹中的文件。

下面是一个用于从 MongoDB 导出 JSON 文件中导入的命令行示例：

    dt.exe /s:MongoDBExport /s.Files:D:\mongoemployees.json /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:employees /t.IdField:_id /t.Dates:Epoch /t.CollectionThroughput:2500

## <a id="SQL"></a>从 SQL Server 导入
借助 SQL 源导入程序选项，可以从单个 SQL Server 数据库中导入，并且选择筛选要使用查询来导入的记录。 此外，可以通过指定嵌套分隔符（稍后进行详细介绍）来修改文档结构。  

![SQL 源选项的屏幕截图 - 数据库迁移工具](./media/import-data/sqlexportsource.png)

连接字符串的格式是标准 SQL 连接字符串格式。

> [!NOTE]
> 使用验证命令来确保可以访问在连接字符串字段中指定的 SQL Server 实例。
> 
> 

嵌套分隔符属性用于在导入过程中创建层次结构关系（子文档）。 请考虑下列 SQL 查询：

select CAST(BusinessEntityID AS varchar) as Id, Name, AddressType as [Address.AddressType], AddressLine1 as [Address.AddressLine1], City as [Address.Location.City], StateProvinceName as [Address.Location.StateProvinceName], PostalCode as [Address.PostalCode], CountryRegionName as [Address.CountryRegionName] from Sales.vStoreWithAddresses WHERE AddressType='Main Office'

它将返回以下（部分）结果：

![SQL 查询结果的屏幕截图](./media/import-data/sqlqueryresults.png)

请注意 Address.AddressType 和 Address.Location.StateProvinceName 等别名。 通过指定嵌套分隔符“.”，导入工具会在导入过程中创建 Address 和 Address.Location 子文档。 下面是 Azure Cosmos DB 中的生成文档的示例：

{ "id": "956", "Name": "Finer Sales and Service", "Address": { "AddressType": "Main Office", "AddressLine1": "#500-75 O'Connor Street", "Location": { "City": "Ottawa", "StateProvinceName": "Ontario" }, "PostalCode": "K4B 1S2", "CountryRegionName": "Canada" } }

下面是一些从 SQL Server 中导入的命令行示例：

    #Import records from SQL which match a query
    dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, * from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Stores /t.IdField:Id /t.CollectionThroughput:2500

    #Import records from sql which match a query and create hierarchical relationships
    dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, Name, AddressType as [Address.AddressType], AddressLine1 as [Address.AddressLine1], City as [Address.Location.City], StateProvinceName as [Address.Location.StateProvinceName], PostalCode as [Address.PostalCode], CountryRegionName as [Address.CountryRegionName] from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /s.NestingSeparator:. /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:StoresSub /t.IdField:Id /t.CollectionThroughput:2500

## <a id="CSV"></a>导入 CSV 文件并将 CSV 转换为 JSON
CSV 文件源导入程序选项可用于导入一个或多个 CSV 文件。 添加包含 CSV 文件的文件夹以供导入时，可以选择递归搜索子文件夹中的文件。

![CSV 源选项的屏幕截图 - CSV 到 JSON](media/import-data/csvsource.png)

与 SQL 源相似，嵌套分隔符属性可用于在导入过程中创建层次结构关系（子文档）。 请考虑以下 CSV 标头行和数据行︰

![CSV 示例记录的屏幕截图 - CSV 到 JSON](./media/import-data/csvsample.png)

请注意 DomainInfo.Domain_Name 和 RedirectInfo.Redirecting 等别名。 通过指定嵌套分隔符“.”，导入工具会在导入过程中创建 DomainInfo 和 RedirectInfo 子文档。 下面是 Azure Cosmos DB 中的生成文档的示例：

{ "DomainInfo": { "Domain_Name": "ACUS.GOV", "Domain_Name_Address": "http://www.ACUS.GOV" }, "Federal Agency": "Administrative Conference of the United States", "RedirectInfo": { "Redirecting": "0", "Redirect_Destination": "" }, "id": "9cc565c5-ebcd-1c03-ebd3-cc3e2ecd814d" }

导入工具会尝试针对 CSV 文件中不带引号的值推断类型信息（带引号的值始终作为字符串处理）。  按以下顺序标识类型︰数值、日期时间、布尔值。  

关于 CSV 导入，还需要注意两个事项︰

1. 默认情况下，不带引号的值总是会去掉制表符和空格，而带引号的值将保持原样。 通过使用“剪裁带引号的值”复选框或 /s.TrimQuoted 命令行选项，可以重写此行为。
2. 默认情况下，不带引号的 null 视为 null 值。 通过使用“将不带引号的 NULL 作为字符串处理”复选框或 /s.NoUnquotedNulls 命令行选项，可以重写此行为（即，将不带引号的 null 视为“null”字符串）。

下面是 CSV 导入的命令行示例：

    dt.exe /s:CsvFile /s.Files:.\Employees.csv /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Employees /t.IdField:EntityID /t.CollectionThroughput:2500

## <a id="AzureTableSource"></a>从 Azure 表存储导入
借助 Azure 表存储源导入程序选项，可以从单个 Azure 表存储表导入。 可以选择性地筛选要导入的表实体。 

从 Azure 表存储导入的数据可以输出到 Azure Cosmos DB 表和实体以用于表 API，或者输出到集合和文档以用于 SQL API。 不过，表 API 只能在命令行实用程序中用作目标，无法使用数据迁移工具用户界面导出到表 API。 有关详细信息，请参阅[导入要在 Azure Cosmos DB 表 API 中使用的数据](table-import.md)。 

![Azure 表存储源选项的屏幕截图](./media/import-data/azuretablesource.png)

Azure 表存储连接字符串的格式为：

    DefaultEndpointsProtocol=<protocol>;AccountName=<Account Name>;AccountKey=<Account Key>;

> [!NOTE]
> 使用验证命令来确保可以访问在连接字符串字段中指定的 Azure 表存储实例。
> 
> 

输入要从其中导入数据的 Azure 表的名称。 可以选择指定[筛选器](https://msdn.microsoft.com/library/azure/ff683669.aspx)。

Azure 表存储源导入程序选项具有下列附加选项︰

1. 包括内部字段
   1. 所有 - 包括所有内部字段（PartitionKey、RowKey 和 Timestamp）
   2. 无 - 排除所有内部字段
   3. RowKey - 仅包括 RowKey 字段
2. 选择列
   1. Azure 表存储筛选器不支持投影。 如果想要仅导入特定的 Azure 表实体属性，请将它们添加到“选择列”列表中。 将忽略其他所有实体属性。

下面是一个用于从 Azure 表存储导入的命令行示例：

    dt.exe /s:AzureTable /s.ConnectionString:"DefaultEndpointsProtocol=https;AccountName=<Account Name>;AccountKey=<Account Key>" /s.Table:metrics /s.InternalFields:All /s.Filter:"PartitionKey eq 'Partition1' and RowKey gt '00001'" /s.Projection:ObjectCount;ObjectSize  /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:metrics /t.CollectionThroughput:2500

## <a id="DynamoDBSource"></a>从 Amazon DynamoDB 导入
借助 Amazon DynamoDB 源导入程序选项，可以从单个 Amazon DynamoDB 表中导入，并且可以选择筛选要导入的实体。 提供多个模板，以便尽可能简化导入设置。

![Amazon DynamoDB 源选项的屏幕截图 - 数据库迁移工具](./media/import-data/dynamodbsource1.png)

![Amazon DynamoDB 源选项的屏幕截图 - 数据库迁移工具](./media/import-data/dynamodbsource2.png)

Amazon DynamoDB 连接字符串的格式为：

    ServiceURL=<Service Address>;AccessKey=<Access Key>;SecretKey=<Secret Key>;

> [!NOTE]
> 使用验证命令来确保可以访问在连接字符串字段中指定的 Amazon DynamoDB 实例。
> 
> 

下面是一个用于从 Amazon DynamoDB 导入的命令行示例：

    dt.exe /s:DynamoDB /s.ConnectionString:ServiceURL=https://dynamodb.us-east-1.amazonaws.com;AccessKey=<accessKey>;SecretKey=<secretKey> /s.Request:"{   """TableName""": """ProductCatalog""" }" /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<Azure Cosmos DB Endpoint>;AccountKey=<Azure Cosmos DB Key>;Database=<Azure Cosmos DB Database>;" /t.Collection:catalogCollection /t.CollectionThroughput:2500

## <a id="BlobImport"></a>从 Azure Blob 存储导入
借助 JSON 文件、MongoDB 导出文件和 CSV 文件源导入程序选项，可以从 Azure Blob 存储中导入一个或多个文件。 在指定 Blob 容器 URL 和帐户密钥后，提供正则表达式来选择要导入的文件。

![Blob 文件源选项的屏幕截图](./media/import-data/blobsource.png)

下面是一个用于从 Azure Blob 存储导入 JSON 文件的命令行示例：

    dt.exe /s:JsonFile /s.Files:"blobs://<account key>@account.blob.core.windows.net:443/importcontainer/.*" /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:doctest

## <a id="SQLSource"></a>从 SQL API 集合导入
借助 Azure Cosmos DB 源导入程序选项，可从一个或多个 Azure Cosmos DB 集合中导入数据，并且（可选）使用查询来筛选文档。  

![Azure Cosmos DB 源选项的屏幕截图](./media/import-data/documentdbsource.png)

Azure Cosmos DB 连接字符串的格式为：

    AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;

Azure Cosmos DB 帐户连接字符串可从 Azure 门户的“密钥”页中检索到（如[如何管理 Azure Cosmos DB 帐户](manage-account.md)中所述），但是需要将数据库的名称追加到连接字符串后面（格式如下）：

    Database=<CosmosDB Database>;

> [!NOTE]
> 使用“验证”命令来确保可以访问在连接字符串字段中指定的 Azure Cosmos DB 实例。
> 
> 

若要从单个 Azure Cosmos DB 集合导入，请输入要从其中导入数据的集合的名称。 若要从多个 Azure Cosmos DB 集合导入，请提供与一个或多个集合名称相匹配的正则表达式（例如 collection01 | collection02 | collection03）。 可以选择为查询指定或提供一个文件来筛选和形成要导入的数据。

> [!NOTE]
> 由于集合字段接受正则表达式，因此如果要从名称包含正则表达式字符的单个集合中导入，则必须相应地转义这些字符。
> 
> 

Azure Cosmos DB 源导入程序选项具有下列高级选项：

1. 包括内部字段：指定是否在导出中包括 Azure Cosmos DB 文档系统属性（例如 _rid、_ts）。
2. 失败重试次数：指定在发生暂时性故障（例如网络连接中断）时重试 Azure Cosmos DB 连接的次数。
3. 重试间隔：指定在发生暂时性故障（例如网络连接中断）时重试 Azure Cosmos DB 连接等待的时间间隔。
4. 连接模式：指定要用于 Azure Cosmos DB 的连接模式。 可用选项包括 DirectTcp、DirectHttps 和网关。 直接连接模式速度更快，而网关模式对于防火墙更加友好，因为它仅使用端口 443。

![Azure Cosmos DB 源高级选项的屏幕截图](./media/import-data/documentdbsourceoptions.png)

> [!TIP]
> 导入工具默认设置为 DirectTcp 连接模式。 如果遇到防火墙问题，请切换到网关连接模式，因为它只需要端口 443。
> 
> 

下面是一些从 Azure Cosmos DB 导入的命令行示例：

    #Migrate data from one Azure Cosmos DB collection to another Azure Cosmos DB collections
    dt.exe /s:CosmosDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:TEColl /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:TESessions /t.CollectionThroughput:2500

    #Migrate data from multiple Azure Cosmos DB collections to a single Azure Cosmos DB collection
    dt.exe /s:CosmosDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:comp1|comp2|comp3|comp4 /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:singleCollection /t.CollectionThroughput:2500

    #Export an Azure Cosmos DB collection to a JSON file
    dt.exe /s:CosmosDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:StoresSub /t:JsonFile /t.File:StoresExport.json /t.Overwrite /t.CollectionThroughput:2500

> [!TIP]
> Azure Cosmos DB 数据导入工具还支持从 [Azure Cosmos DB 模拟器](local-emulator.md)导入数据。 从本地模拟器导入数据时，请将终结点设置为 `https://localhost:<port>`。 
> 
> 

## <a id="HBaseSource"></a>从 HBase 导入
借助 HBase 源导入程序选项，可以从 HBase 表中导入数据并且选择筛选数据。 提供多个模板，以便尽可能简化导入设置。

![HBase 源选项的屏幕截图](./media/import-data/hbasesource1.png)

![HBase 源选项的屏幕截图](./media/import-data/hbasesource2.png)

HBase Stargate 连接字符串的格式为︰

    ServiceURL=<server-address>;Username=<username>;Password=<password>

> [!NOTE]
> 使用验证命令来确保可以访问在连接字符串字段中指定的 HBase 实例。
> 
> 

下面是一个用于从 HBase 导入的命令行示例：

    dt.exe /s:HBase /s.ConnectionString:ServiceURL=<server-address>;Username=<username>;Password=<password> /s.Table:Contacts /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:hbaseimport

## <a id="SQLBulkTarget"></a>导入到 SQL API（批量导入）
借助 Azure Cosmos DB 批量导入程序，可以使用 Azure Cosmos DB 存储过程从任何可用的源选项导入，以提高效率。 该工具支持导入到一个单分区的 Azure Cosmos DB 集合以及分片导入，从而可跨多个单分区 Azure Cosmos DB 集合对数据进行分区。 有关将数据分区的详细信息，请参阅 [Azure Cosmos DB 中的分区和扩展](partition-data.md)。 该工具将在目标集合中创建、执行然后删除存储过程。  

![Azure Cosmos DB 批量选项的屏幕截图](./media/import-data/documentdbbulk.png)

Azure Cosmos DB 连接字符串的格式为：

    AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;

Azure Cosmos DB 帐户连接字符串可从 Azure 门户的“密钥”页中检索到（如[如何管理 Azure Cosmos DB 帐户](manage-account.md)中所述），但是需要将数据库的名称追加到连接字符串后面（格式如下）：

    Database=<CosmosDB Database>;

> [!NOTE]
> 使用“验证”命令来确保可以访问在连接字符串字段中指定的 Azure Cosmos DB 实例。
> 
> 

若要导入到单个 DocumentDB 集合，请输入要将数据导入到的集合的名称，然后单击“添加”按钮。 若要导入到多个集合，请分别输入每个集合名称，或使用以下语法指定多个集合： collection_prefix [开始索引 - 结束索引]。 通过前述语法指定多个集合时，请注意以下指导原则：

1. 仅支持整数范围名称模式。 例如，指定 collection[0-3] 会创建以下集合：collection0、collection1、collection2 和 collection3。
2. 可以使用缩写的语法：collection[3] 创建步骤 1 中所述的同一组集合。
3. 可以提供多个替代。 例如，collection[0-1] [0-9] 会生成 20 个带前导零的集合名称（collection01、...02、...03）。

指定集合名称后，选择集合所需的吞吐量（400 RU 到 10,000 RU）。 为了获得最佳导入性能，请选择更高的吞吐量。 有关性能级别的详细信息，请参阅 [Azure Cosmos DB 中的性能级别](performance-levels.md)。

> [!NOTE]
> 性能吞吐量设置仅适用于集合创建。 如果指定的集合已存在，则不会修改其吞吐量。
> 
> 

导入到多个集合时，导入工具支持基于哈希的分片。 在此方案中，请指定要用作分区键的文档属性（如果分区键留空，文档将跨多个目标集合随机分片）。

在导入过程中，可以选择指定要将导入源中的哪个字段用作 Azure Cosmos DB 文档 ID 属性（如果文档不包含此属性，导入工具将会生成 GUID 作为 ID 属性值）。

导入过程中可以使用多个高级选项。 首先，虽然工具包含默认的批量导入存储过程 (BulkInsert.js)，但可以选择指定自己的导入存储过程︰

 ![Azure Cosmos DB 批量插入 sproc 选项的屏幕截图](./media/import-data/bulkinsertsp.png)

此外，在导入日期类型时（例如从 SQL Server 或 MongoDB 导入），可以选择三个导入选项之一：

 ![Azure Cosmos DB 日期时间导入选项的屏幕截图](./media/import-data/datetimeoptions.png)

* 字符串：保持字符串值
* Epoch：保持 Epoch 数字值
* 两者：保持字符串和 Epoch 数字值。 此选项创建一个子文档，例如："date_joined": { "Value": "2013-10-21T21:17:25.2410000Z", "Epoch": 1382390245 }

Azure Cosmos DB 批量导入程序具有下列高级附加选项：

1. 批大小︰工具默认将批大小设置为 50。  如果要导入的文档很大，请考虑减小批大小。 如果要导入的文档很小，请考虑增大批大小。
2. 最大脚本大小（字节）：工具默认设置为 512 KB 的最大脚本大小。
3. 禁用自动生成 ID︰如果要导入的每个文档都包含一个 ID 字段，则选择此选项可以提高性能。 不会导入缺少唯一 ID 字段的文档。
4. 更新现有文档︰工具将默认设置为不替换存在 ID 冲突的现有文档。 选择此选项可以覆盖具有匹配 ID 的现有文档。 此功能可用于更新现有文档的计划内数据迁移。
5. 失败重试次数：指定在发生暂时性故障（例如网络连接中断）时重试 Azure Cosmos DB 连接的次数。
6. 重试间隔：指定在发生暂时性故障（例如网络连接中断）时重试 Azure Cosmos DB 连接等待的时间间隔。
7. 连接模式：指定要用于 Azure Cosmos DB 的连接模式。 可用选项包括 DirectTcp、DirectHttps 和网关。 直接连接模式速度更快，而网关模式对于防火墙更加友好，因为它仅使用端口 443。

![Azure Cosmos DB 批量导入高级选项的屏幕截图](./media/import-data/docdbbulkoptions.png)

> [!TIP]
> 导入工具默认设置为 DirectTcp 连接模式。 如果遇到防火墙问题，请切换到网关连接模式，因为它只需要端口 443。
> 
> 

## <a id="SQLSeqTarget"></a>导入到 SQL API（顺序记录导入）
借助 Azure Cosmos DB 顺序记录导入程序，可以从任何可用的源选项中逐条导入记录。 如果要导入到已达到存储过程配额的现有集合中，可以选择此选项。 该工具支持导入到单个（单分区和多分区）Azure Cosmos DB 集合以及分片导入，从而可跨多个单分区和/或多分区 Azure Cosmos DB 集合对数据进行分区。 有关将数据分区的详细信息，请参阅 [Azure Cosmos DB 中的分区和扩展](partition-data.md)。

![Azure Cosmos DB 顺序记录导入选项的屏幕截图](./media/import-data/documentdbsequential.png)

Azure Cosmos DB 连接字符串的格式为：

    AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;

Azure Cosmos DB 帐户连接字符串可从 Azure 门户的“密钥”页中检索到（如[如何管理 Azure Cosmos DB 帐户](manage-account.md)中所述），但是需要将数据库的名称追加到连接字符串后面（格式如下）：

    Database=<Azure Cosmos DB Database>;

> [!NOTE]
> 使用“验证”命令来确保可以访问在连接字符串字段中指定的 Azure Cosmos DB 实例。
> 
> 

要导入到单个 DocumentDB 集合，请输入将向其中导入数据的集合的名称，然后单击“添加”按钮。 若要导入到多个集合，请分别输入每个集合名称，或使用以下语法指定多个集合： collection_prefix [开始索引 - 结束索引]。 通过前述语法指定多个集合时，请注意以下指导原则：

1. 仅支持整数范围名称模式。 例如，指定 collection[0-3] 会创建以下集合：collection0、collection1、collection2 和 collection3。
2. 可以使用缩写的语法：collection[3] 创建步骤 1 中所述的同一组集合。
3. 可以提供多个替代。 例如，collection[0-1] [0-9] 创建 20 个带前导零的集合名称（collection01、...02、...03）。

指定集合名称后，选择集合所需的吞吐量（400 RU 到 250,000 RU）。 为了获得最佳导入性能，请选择更高的吞吐量。 有关性能级别的详细信息，请参阅 [Azure Cosmos DB 中的性能级别](performance-levels.md)。 任何导入到吞吐量大于 10,000 RU 的集合的内容都需要分区键。 如果选择拥有超过 250,000 个 RU，则需要在门户中提交一个请求来增加帐户。

> [!NOTE]
> 吞吐量设置仅适用于集合或数据库创建。 如果指定的集合已存在，则不会修改其吞吐量。
> 
> 

导入到多个集合时，导入工具支持基于哈希的分片。 在此方案中，请指定要用作分区键的文档属性（如果分区键留空，文档将跨多个目标集合随机分片）。

在导入过程中，可以选择指定要将导入源中的哪个字段用作 Azure Cosmos DB 文档 ID 属性（如果文档不包含此属性，导入工具将会生成 GUID 作为 ID 属性值）。

导入过程中可以使用多个高级选项。 首先，在导入日期类型时（例如从 SQL Server 或 MongoDB 导入），可以选择三个导入选项之一：

 ![Azure Cosmos DB 日期时间导入选项的屏幕截图](./media/import-data/datetimeoptions.png)

* 字符串：保持字符串值
* Epoch：保持 Epoch 数字值
* 两者：保持字符串和 Epoch 数字值。 此选项创建一个子文档，例如："date_joined": { "Value": "2013-10-21T21:17:25.2410000Z", "Epoch": 1382390245 }

Azure Cosmos DB - 顺序记录导入程序具有下列高级附加选项：

1. 并行请求数：工具默认设置为两个并行请求。 如果要导入的文档很小，请考虑增加并行请求的数量。 如果此数字提高得过多，则导入可能会遇到限制。
2. 禁用自动生成 ID︰如果要导入的每个文档都包含一个 ID 字段，则选择此选项可以提高性能。 不会导入缺少唯一 ID 字段的文档。
3. 更新现有文档︰工具将默认设置为不替换存在 ID 冲突的现有文档。 选择此选项可以覆盖具有匹配 ID 的现有文档。 此功能可用于更新现有文档的计划内数据迁移。
4. 失败重试次数：指定在发生暂时性故障（例如网络连接中断）时重试 Azure Cosmos DB 连接的次数。
5. 重试间隔：指定在发生暂时性故障（例如网络连接中断）时重试 Azure Cosmos DB 连接等待的时间间隔。
6. 连接模式：指定要用于 Azure Cosmos DB 的连接模式。 可用选项包括 DirectTcp、DirectHttps 和网关。 直接连接模式速度更快，而网关模式对于防火墙更加友好，因为它仅使用端口 443。

![Azure Cosmos DB 顺序记录导入高级选项的屏幕截图](./media/import-data/documentdbsequentialoptions.png)

> [!TIP]
> 导入工具默认设置为 DirectTcp 连接模式。 如果遇到防火墙问题，请切换到网关连接模式，因为它只需要端口 443。
> 
> 

## <a id="IndexingPolicy"></a>指定索引策略
允许迁移工具在导入过程中创建 Azure Cosmos DB SQL API 集合时，可以指定集合的索引策略。 在 Azure Cosmos DB 批量导入和 Azure Cosmos DB 顺序记录选项的高级选项部分，导航到“索引策略”部分。

![Azure Cosmos DB 索引策略高级选项的屏幕截图](./media/import-data/indexingpolicy1.png)

使用索引策略高级选项，可以选择一个索引策略文件，手动输入索引策略，或从一组默认模板中选择（右键单击索引策略文本框）。

工具提供的策略模板包括︰

* 默认。 针对字符串执行等式查询并针对数值使用 ORDER BY、范围和等式查询时，此策略最佳。 与范围模板相比，此策略的索引存储开销较低。
* 范围。 此策略最适合对数字和字符串同时使用 ORDER BY、范围和等式查询的情况。 与默认或哈希模板相比，此策略的索引存储开销较高。

![Azure Cosmos DB 索引策略高级选项的屏幕截图](./media/import-data/indexingpolicy2.png)

> [!NOTE]
> 如果未指定索引策略，将应用默认策略。 有关索引策略的详细信息，请参阅 [Azure Cosmos DB 索引策略](indexing-policies.md)。
> 
> 

## <a name="export-to-json-file"></a>导出到 JSON 文件
使用 Azure Cosmos DB JSON 导出程序，可以将任何可用的源选项导出到包含一组 JSON 文档的 JSON 文件。 该工具可自行处理导出，你也可以选择查看生成的迁移命令并自己运行该命令。 生成的 JSON 文件可能存储在本地或 Azure Blob 存储中。

![Azure Cosmos DB JSON 本地文件导出选项的屏幕截图](./media/import-data/jsontarget.png)

![Azure Cosmos DB JSON Azure Blob 存储导出选项的屏幕截图](./media/import-data/jsontarget2.png)

可以根据需要选择整理生成的 JSON，这会增加生成文档的大小，同时提高内容的易读性。

    Standard JSON export
    [{"id":"Sample","Title":"About Paris","Language":{"Name":"English"},"Author":{"Name":"Don","Location":{"City":"Paris","Country":"France"}},"Content":"Don's document in Azure Cosmos DB is a valid JSON document as defined by the JSON spec.","PageViews":10000,"Topics":[{"Title":"History of Paris"},{"Title":"Places to see in Paris"}]}]

    Prettified JSON export
    [
     {
    "id": "Sample",
    "Title": "About Paris",
    "Language": {
      "Name": "English"
    },
    "Author": {
      "Name": "Don",
      "Location": {
        "City": "Paris",
        "Country": "France"
      }
    },
    "Content": "Don's document in Azure Cosmos DB is a valid JSON document as defined by the JSON spec.",
    "PageViews": 10000,
    "Topics": [
      {
        "Title": "History of Paris"
      },
      {
        "Title": "Places to see in Paris"
      }
    ]
    }]

## <a name="advanced-configuration"></a>高级配置
在高级配置屏幕中，指定要向其中写入错误的日志文件的位置。 本页适用的规则如下：

1. 如果未提供文件名，则会在结果页上返回所有错误。
2. 如果提供的文件名中不包含目录，则会在当前环境目录中创建（或覆盖）文件。
3. 如果选择一个现有文件，该文件将被覆盖，且不提供追加选项。

然后，选择是记录所有、关键还是无错误消息。 最后，确定更新屏幕上的传输消息及其进度的频率。

    ![Screenshot of Advanced configuration screen](./media/import-data/AdvancedConfiguration.png)

## <a name="confirm-import-settings-and-view-command-line"></a>确认导入设置并查看命令行
1. 在指定源信息、目标信息以及高级配置后，查看迁移摘要，并可选择查看/复制生成的迁移命令（复制命令对于自动执行导入操作非常有用）︰
   
    ![摘要屏幕的屏幕截图](./media/import-data/summary.png)
   
    ![摘要屏幕的屏幕截图](./media/import-data/summarycommand.png)
2. 对源和目标选项满意后，单击“导入”。 在导入过程中，已用时间、传输计数和失败信息（如果未在“高级”配置中提供文件名）将会更新。 完成后，可以导出结果（例如，用于处理所有导入失败结果）。
   
    ![Azure Cosmos DB JSON 导出选项的屏幕截图](./media/import-data/viewresults.png)
3. 还可以启动新的导入，并保留现有设置（例如连接字符串信息、源和目标选项等），或重置所有值。
   
    ![Azure Cosmos DB JSON 导出选项的屏幕截图](./media/import-data/newimport.png)

## <a name="next-steps"></a>后续步骤

在本教程中，我们已完成以下任务：

> [!div class="checklist"]
> * 已安装数据迁移工具
> * 已从不同的数据源导入数据
> * 已从 Azure Cosmos DB 导出到 JSON

现在可以继续学习下一教程并了解如何使用 Azure Cosmos DB 查询数据。 

> [!div class="nextstepaction"]
>[如何查询数据？](../cosmos-db/tutorial-query-sql-api.md)
