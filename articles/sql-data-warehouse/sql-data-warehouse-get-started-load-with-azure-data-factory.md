<properties
	pageTitle="使用 Azure 数据工厂加载数据 | Microsoft Azure"
	description="了解如何使用 Azure 数据工厂加载数据"
	services="sql-data-warehouse"
	documentationCenter="NA"
	authors="lodipalm"
	manager="barbkess"
	editor=""
	tags="azure-sql-data-warehouse"/>
<tags
   ms.service="sql-data-warehouse"
   ms.date="11/19/2015"
   wacn.date=""/>

# 使用 Azure 数据工厂加载数据

> [AZURE.SELECTOR]
- [Data Factory](sql-data-warehouse-get-started-load-with-azure-data-factory.md)
- [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)
- [BCP](sql-data-warehouse-load-with-bcp.md)

 此教程说明如何在 Azure 数据工厂中创建管道，以将数据从 Azure 存储 Blob 移到 SQL 数据仓库。使用以下步骤可以：

+ 在 Azure 存储 Blob 中设置示例数据。
+ 将资源连接到 Azure 数据工厂。
+ 创建管道用于将数据从存储 Blob 移到 SQL 数据仓库。

>[AZURE.VIDEO loading-azure-sql-data-warehouse-with-azure-data-factory]

## 资源

若要完成本教程，你需要以下资源：

   + **Azure 存储 Blob**：Azure 存储 Blob 将是管道的数据源。你可以使用现有的 Blob 或[预配新的 Blob](../storage/storage-create-storage-account/)。

   + **SQL 数据仓库**：在本教程中，你要将数据移到 SQL 数据仓库。如果你尚未设置实例，可在[此处](sql-data-warehouse-get-started-provision.md)了解如何设置。此外，还需要配合我们的 AdventureWorks DW 数据集设置实例。如果你未预配数据仓库与示例数据，可以[手动加载](sql-data-warehouse-get-started-manually-load-samples.md)。

   + **Azure 数据工厂**：Azure 数据工厂将完成实际加载。如需有关如何设置 Azure 数据工厂或创建管道的详细信息，可以参阅[此文](../data-factory/data-factory-build-your-first-pipeline-using-editor/)。

所有资源片段就绪后，你可以开始准备数据，并创建 Azure 数据工厂管道。

## 示例数据

除了管道的不同片段外，我们还需要一些示例数据，用于练习如何在 Azure 数据工厂中加载数据。

1. 首先，请[下载示例数据](https://migrhoststorage.blob.core.chinacloudapi.cn/adfsample/FactInternetSales.csv)。此数据配合已在示例数据中的示例数据（提供另一个三年期的销售数据）一起使用。

2. 下载数据后，可以在 AZCopy 中运行以下脚本，以将数据移到 Blob 存储：

        AzCopy /Source:<Sample Data Location>  /Dest:https://<storage account>.blob.core.chinacloudapi.cn/<container name> /DestKey:<storage key> /Pattern:FactInternetSales.csv

	有关如何安装和使用 AZCopy 的信息，请参阅 [AZCopy 文档](../storage/storage-use-azcopy/)。

获取适当的数据后，我们可以转到数据工厂来创建管道，以将数据从存储帐户移到 SQL 数据仓库。

## 使用 Azure 数据工厂

设置所有资源片段后，我们可以导航到 Azure 预览门户中的 Azure 数据工厂实例，开始设置管道。为此，可以转到 [Azure 门户](https://manage.windowsazure.cn)，并从左侧菜单中选择你的数据工厂。

在这里，可以通过以下三个步骤来设置 Azure 数据工厂管道，以将数据传送到数据仓库：链接服务、定义数据集，以及创建管道。

### 创建链接的服务

第一个步骤是将 Azure 存储帐户和 SQL 数据仓库链接到 数据工厂。

1. 首先，请通过单击数据工厂的“链接的服务”部分，然后单击“新建数据存储”，开始执行注册程序。 接下来，选择要在其下注册 Azure 存储空间的名称，选择 Azure 存储空间作为类型，然后输入你的帐户名和帐户密钥。

2. 若要注册 SQL 数据仓库，需要导航到“创作和部署”部分，然后依次选择“新建数据存储”和“Azure SQL 数据仓库”。然后，需要填写以下模板：

		{
		    "name": "<Linked Service Name>",
		    "properties": {
		        "description": "",
		        "type": "AzureSqlDW",
		        "typeProperties": {
		            "connectionString": "Data Source=tcp:<server name>.database.chinacloudapi.cn,1433;Initial Catalog=<server name>;Integrated Security=False;User ID=<user>@<servername>;Password=<password>;Connect Timeout=30;Encrypt=True"
		        }
		    }
		}

### 注册数据集

创建链接的服务后，我们需要定义数据集，即，定义要从你的存储转移到数据仓库的数据的结构。你可以阅读有关创建过程的信息

1. 若要开始此过程，请导航到数据工厂的“创作和部署”部分。

2. 依次单击“新建数据集”和“Azure Blob 存储”，以将你的存储链接到你的数据工厂。可以使用以下脚本，在 Azure Blob 存储中定义数据：

		{
			"name": "<Dataset Name>",
			"properties": {
				"type": "AzureBlob",
				"linkedServiceName": "<linked storage name>",
				"typeProperties": {
					"folderPath": "<containter name>",
					"fileName": "FactInternetSales.csv",
					"format": {
					"type": "TextFormat",
					"columnDelimiter": ",",
					"rowDelimiter": "\n"
					}
				},
				"external": true,
				"availability": {
					"frequency": "Hour",
					"interval": 1
				},
				"policy": {
				"externalData": {
					"retryInterval": "00:01:00",
					"retryTimeout": "00:10:00",
					"maximumRetry": 3
					}
				}
			}
		}



3. 现在，还要为 SQL 数据仓库定义我们的数据集。我们以相同的方式开始，即依次单击“新建数据集”和“Azure SQL 数据仓库”。

		{
		  "name": "<dataset name>",
		  "properties": {
		    "type": "AzureSqlDWTable",
		    "linkedServiceName": "<linked data warehouse name>",
		    "typeProperties": {
		      "tableName": "FactInternetSales"
		    },
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}

		{
		  "name": "DWDataset",
		  "properties": {
			"type": "AzureSqlDWTable",
			"linkedServiceName": "AzureSqlDWLinkedService",
			"typeProperties": {
			  "tableName": "FactInternetSales"
			},
			"availability": {
			  "frequency": "Hour",
			  "interval": 1
			}
		  }
		}

### 设置管道

最后，我们将在 Azure 数据工厂中设置并运行管道。此操作将完成实际的数据移动。可以在[此处](../data-factory/data-factory-azure-sql-data-warehouse-connector/)找到可以使用 SQL 数据仓库和 Azure 数据工厂完成的操作的完整视图。

在“创作和部署”部分中，依次单击“更多命令”和“新建管道”。创建管道后，可以使用以下代码将数据传送到数据仓库：

	{
	"name": "<Pipeline Name>",
	"properties": {
		"description": "<Description>",
		"activities": [
			{
				"type": "Copy",
				"typeProperties": {
					"source": {
						"type": "BlobSource",
						"skipHeaderLineCount": 1
					},
					"sink": {
						"type": "SqlDWSink",
						"writeBatchSize": 0,
						"writeBatchTimeout": "00:00:10"
					}
				},
				"inputs": [
					{
						"name": "<Storage Dataset>"
					}
				],
				"outputs": [
					{
						"name": "<Data Warehouse Dataset>"
					}
				],
				"policy": {
					"timeout": "01:00:00",
					"concurrency": 1
				},
				"scheduler": {
					"frequency": "Hour",
					"interval": 1
				},
				"name": "Sample Copy",
				"description": "Copy Activity"
			}
		],
		"start": "<Date YYYY-MM-DD>",
		"end": "<Date YYYY-MM-DD>",
		"isPaused": false
	}
	}
	

<!---HONumber=Mooncake_1207_2015-->