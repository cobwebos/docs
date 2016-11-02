<properties 
	pageTitle="教程：使用 Visual Studio 创建带有复制活动的管道 | Microsoft Azure" 
	description="在本教程中，通过使用 Visual Studio 创建带有复制活动的 Azure 数据工厂管道。" 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="08/01/2016" 
	ms.author="spelluru"/>

# 教程：使用 Visual Studio 创建带有复制活动的管道
> [AZURE.SELECTOR]
- [概述与先决条件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Azure 门户](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
- [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)
- [复制向导](data-factory-copy-data-wizard-tutorial.md)

本教程演示如何使用 Visual Studio 创建和监视 Azure 数据工厂。数据工厂中的管道使用复制活动将数据从 Azure Blob 存储复制到 SQL 数据库。

下面是本教程中要执行的步骤：

1. 创建两个链接服务：**AzureStorageLinkedService1** 和 **AzureSqlinkedService1**。AzureStorageLinkedService1 和 AzureSqlLinkedService1 分别将 Azure 存储和 Azure SQL 数据库链接到数据工厂：**ADFTutorialDataFactoryVS**。管道的输入数据存在于 Azure Blob 存储的 Blob 容器中，其输出数据存储在 Azure SQL 数据库的表中。因此，将这两个数据存储区作为链接服务添加到数据工厂。
2. 创建两个数据工厂表：**EmpTableFromBlob** 和 **EmpSQLTable**，用来表示存储在数据存储区中的输入/输出数据。对于 EmpTableFromBlob，指定的 Blob 容器包含带有源数据的 Blob。对于 EmpSQLTable，指定的 SQL 表存储输出数据。还可指定其他属性，例如结构、可用性和策略。
3. 在 ADFTutorialDataFactoryVS 中创建名为 **ADFTutorialPipeline** 的管道。该管道拥有**复制活动**，可将输入数据从 Azure Blob 复制到输出 Azure SQL 表。复制活动在 Azure 数据工厂中执行数据移动。该活动由全球可用的服务提供支持，能以安全、可靠、可缩放的方式在各种数据存储区间复制数据。有关复制活动的详细信息，请参阅[数据移动活动](data-factory-data-movement-activities.md)文章。
4. 创建数据工厂，部署链接的服务、表和管道。

## 先决条件

1. 阅读[教程概述](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)，完成**先决条件**步骤。
2. 只有 **Azure 订阅的管理员**才能将数据工厂实体发布到 Azure 数据工厂。
3. 必须在计算机上安装了以下软件：
	- Visual Studio 2013 或 Visual Studio 2015
	- 下载用于 Visual Studio 2013 或 Visual Studio 2015 的 Azure SDK。导航到 [Azure 下载页](https://azure.microsoft.com/downloads/)，在 **.NET** 部分中单击 **VS 2013** 或 **VS 2015**。
	- 下载适用于 Visual Studio 的最新 Azure 数据工厂插件：[VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) 或 [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005)。如果使用了 Visual Studio 2013，则还可通过执行以下步骤更新插件：在菜单上，依次单击“工具”->“扩展和更新”->“联机”->“Visual Studio 库”->“适用于 Visual Studio 的 Microsoft Azure 数据工厂工具”->“更新”。
 


## 创建 Visual Studio 项目 
1. 启动 **Visual Studio 2013**。单击“文件”，指向“新建”并单击“项目”。将显示“新建项目”对话框。
2. 在“新建项目”对话框中，选择“DataFactory”模板，然后单击“空数据工厂项目”。如果没有看到“DataFactory”模板，请关闭 Visual Studio，安装用于 Visual Studio 2013 的 Azure SDK，然后重新打开 Visual Studio。

	![“新建项目”对话框](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-project-dialog.png)

3. 输入项目的**名称**、**位置**以及**解决方案**的名称，单击“确定”。

	![解决方案资源管理器](./media/data-factory-copy-activity-tutorial-using-visual-studio/solution-explorer.png)

## 创建链接服务
链接服务将数据存储区或计算服务链接到 Azure 数据工厂。数据存储区可以是 Azure 存储、Azure SQL 数据库或本地 SQL Server 数据库。

在本步中，创建两个链接服务：**AzureStorageLinkedService1** 和 **AzureSqlLinkedService1**。AzureStorageLinkedService1 和 AzureSqlLinkedService 链接服务分别将 Azure 存储帐户和 Azure SQL 数据库链接到数据工厂：**ADFTutorialDataFactory**。

### 创建 Azure 存储链接服务

4. 在“解决方案资源管理器”中，右键单击“链接服务”，指向“添加”，单击“新建项”。
5. 在“添加新项”对话框中，从列表中选择“Azure 存储链接服务”，然后单击“添加”。

	![新建链接服务](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-linked-service-dialog.png)
 
3. 将 **accountname** 和 **accountkey** 替换为你的 Azure 存储帐户及其密钥。

	![Azure 存储链接服务](./media/data-factory-copy-activity-tutorial-using-visual-studio/azure-storage-linked-service.png)

4. 保存 **AzureStorageLinkedService1.json** 文件。

### 创建 Azure SQL 链接服务

5. 在“解决方案资源管理器”中，再次右键单击“链接服务”节点，指向“添加”，单击“新建项”。
6. 这次选择“Azure SQL 链接服务”，然后单击“添加”。
7. 在 **AzureSqlLinkedService1.json 文件**中，将 **servername**、**databasename**、**username@servername** 和 **password** 分别替换为你的 Azure SQL 服务器、数据库、用户帐户和密码。
8.  保存 **AzureSqlLinkedService1.json** 文件。


## 创建数据集
在上一步中，创建了 **AzureStorageLinkedService1** 和 **AzureSqlLinkedService1** 链接服务，将 Azure 存储帐户和 Azure SQL 数据库链接到数据工厂：**ADFTutorialDataFactory**。在本步中，将定义两个数据工厂表 - **EmpTableFromBlob** 和 **EmpSQLTable**，表示存储在 AzureStorageLinkedService1 和 AzureSqlLinkedService1 所引用数据存储区中的输入/输出数据。对于 EmpTableFromBlob，指定的 Blob 容器包含带有源数据的 Blob。对于 EmpSQLTable，指定的 SQL 表存储输出数据。

### 创建输入数据集

9. 在“解决方案资源管理器”中，右键单击“表”，指向“添加”，单击“新建项”。
10. 在“添加新项”对话框中，选择“Azure Blob”，然后单击“添加”。
10. 将 JSON 文本替换为以下文本并保存 **AzureBlobLocation1.json** 文件。

		{
		  "name": "EmpTableFromBlob",
		  "properties": {
		    "structure": [
		      {
		        "name": "FirstName",
		        "type": "String"
		      },
		      {
		        "name": "LastName",
		        "type": "String"
		      }
		    ],
		    "type": "AzureBlob",
		    "linkedServiceName": "AzureStorageLinkedService1",
		    "typeProperties": {
		      "folderPath": "adftutorial/",
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "external": true,
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}

### 创建输出数据集

11. 在“解决方案资源管理器”中，再次右键单击“表”，指向“添加”，单击“新建项”。
12. 在“添加新项”对话框中，选择“Azure SQL”，然后单击“添加”。
13. 将 JSON 文本替换为以下 JSON 并保存 **AzureSqlTableLocation1.json** 文件。

		{
		  "name": "EmpSQLTable",
		  "properties": {
		    "structure": [
		      {
		        "name": "FirstName",
		        "type": "String"
		      },
		      {
		        "name": "LastName",
		        "type": "String"
		      }
		    ],
		    "type": "AzureSqlTable",
		    "linkedServiceName": "AzureSqlLinkedService1",
		    "typeProperties": {
		      "tableName": "emp"
		    },
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}

## 创建管道 
到目前为止已创建了输入/输出链接服务和链接表。现将创建带有**复制活动**的管道，将数据从 Azure Blob 复制到 Azure SQL 数据库。


1. 在“解决方案资源管理器”中，右键单击“管道”，指向“添加”，单击“新建项”。
15. 在“添加新项”对话框中，选择“复制数据管道”，然后单击“添加”。
16. 将 JSON 替换为以下 JSON 并保存 **CopyActivity1.json** 文件。
			
		{
		  "name": "ADFTutorialPipeline",
		  "properties": {
		    "description": "Copy data from a blob to Azure SQL table",
		    "activities": [
		      {
		        "name": "CopyFromBlobToSQL",
		        "description": "Push Regional Effectiveness Campaign data to Azure SQL database",
		        "type": "Copy",
		        "inputs": [
		          {
		            "name": "EmpTableFromBlob"
		          }
		        ],
		        "outputs": [
		          {
		            "name": "EmpSQLTable"
		          }
		        ],
		        "typeProperties": {
		          "source": {
		            "type": "BlobSource"
		          },
		          "sink": {
		            "type": "SqlSink",
		            "writeBatchSize": 10000,
		            "writeBatchTimeout": "60:00:00"
		          }
		        },
		        "Policy": {
		          "concurrency": 1,
		          "executionPriorityOrder": "NewestFirst",
		          "style": "StartOfInterval",
		          "retry": 0,
		          "timeout": "01:00:00"
		        }
		      }
		    ],
		    "start": "2015-07-12T00:00:00Z",
		    "end": "2015-07-13T00:00:00Z",
		    "isPaused": false
		  }
		}

## 发布/部署数据工厂实体
  
18. 在“解决方案资源管理器”中，右键单击该项目，并单击“发布”。
19. 如果显示“登录到 Microsoft 帐户”对话框，请输入拥有 Azure 订阅的帐户凭据，然后单击“登录”。
20. 应该会看到以下对话框：

	![“发布”对话框](./media/data-factory-copy-activity-tutorial-using-visual-studio/publish.png)

21. 在“配置数据工厂”页，请按以下步骤操作：
	1. 选择“新建数据工厂”选项。
	2. 输入 **VSTutorialFactory** 作为“名称”。
	
		> [AZURE.NOTE]  
		Azure 数据工厂的名称必须是全局唯一的。如果发布时收到了有关数据工厂名称的错误，请更改数据工厂的名称（例如，yournameVSTutorialFactory），再次尝试发布。有关数据工厂项目命名规则的信息，请参阅[数据工厂 - 命名规则](data-factory-naming-rules.md)主题。
		
	3. 为“订阅”字段选择正确的订阅。
	4. 为要创建的数据工厂选择**资源组**。
	5. 为数据工厂选择**区域**。
	6. 单击“下一步”切换到“发布项”页。
23. 在“发布项”页上，确保已选择所有数据工厂实体，然后单击“下一步”切换到“摘要”页。
24. 查看摘要，单击“下一步”，启动部署过程并查看**部署状态**。
25. 在“部署状态”页上，应看到部署过程的状态。部署完成后，单击“完成”。

注意以下几点：

- 如果收到错误：“**该订阅未注册为使用命名空间 Microsoft.DataFactory**”，请执行下列操作之一，尝试再次发布：

	- 在 Azure PowerShell 中，运行以下命令，注册数据工厂提供程序。
		
			Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
	
		可通过运行以下命令来确认数据工厂提供程序是否已注册。
	
			Get-AzureRmResourceProvider
	- 使用 Azure 订阅登录到 [Azure 门户](https://portal.azure.com)，然后导航到“数据工厂”边栏选项卡，或在 Azure 门户中创建数据工厂。此操作将为你自动注册提供程序。
- 	数据工厂名称可能在将来被注册为 DNS 名称，因此将变成公开可见。
- 	若要创建数据工厂实例，你需要是 Azure 订阅的参与者/管理员

## 摘要
在本教程中，这就创建了 Azure 数据工厂，将数据从 Azure Blob 复制到 Azure SQL 数据库。使用 Visual Studio 创建数据工厂、链接服务、数据集和管道。下面是本教程中执行的高级步骤：

1.	创建 Azure **数据工厂**。
2.	创建**链接服务**：
	1. **Azure 存储**链接服务，链接存放输入数据的 Azure 存储帐户。
	2. **Azure SQL** 链接服务，链接存放输出数据的 Azure SQL 数据库。
3.	创建**数据集**，描述管道的输入和输出数据。
4.	创建带有**复制活动**的**管道**，其中 **BlobSource** 作为源，**SqlSink** 作为接收器。


## 使用“服务器资源管理器”查看数据工厂

1. 在 **Visual Studio** 中，在菜单上单击“视图”，然后单击“服务器资源管理器”。
2. 在“服务器资源管理器”窗口中，依次展开“Azure”和“数据工厂”。如果看到“登录到 Visual Studio”，则输入与 Azure 订阅相关的**帐户**，然后单击“继续”。键入**密码**，然后单击“登录”。Visual Studio 尝试获取有关订阅中所有 Azure 数据工厂的信息。可在“数据工厂任务列表”窗口中查看此操作的状态。![服务器资源管理器](./media/data-factory-copy-activity-tutorial-using-visual-studio/server-explorer.png)
3. 可右键单击数据工厂，并选择“将数据工厂导出到新项目”，创建基于现有数据工厂的 Visual Studio 项目。![将数据工厂导出为 VS 项目](./media/data-factory-copy-activity-tutorial-using-visual-studio/export-data-factory-menu.png)

## 更新适用于 Visual Studio 的数据工厂工具
若要更新适用于 Visual Studio 的 Azure 数据工厂工具，请执行以下步骤：

1. 在菜单上单击“工具”，选择“扩展和更新”。
2. 在左窗格中选择“更新”，再选择“Visual Studio 库”。
4. 选择“适用于 Visual Studio 的 Azure 数据工厂工具”，然后单击“更新”。如果未看到此项，说明你已有此工具的最新版本。

有关如何使用 Azure 门户监视本教程中所创建管道和数据集的说明，请参阅[监视数据集和管道](data-factory-copy-activity-tutorial-using-azure-portal.md#monitor-pipeline)。

## 另请参阅
| 主题 | 说明 |
| :---- | :---- |
| [数据移动活动](data-factory-data-movement-activities.md) | 本文提供有关教程中所用复制活动的详细信息。 |
| [计划和执行](data-factory-scheduling-and-execution.md) | 本文介绍 Azure 数据工厂应用程序模型的计划方面和执行方面。 |
| [管道](data-factory-create-pipelines.md) | 帮助你了解 Azure 数据工厂中的管道和活动 |
| [数据集](data-factory-create-datasets.md) | 还有助于了解 Azure 数据工厂中的数据集。
| [使用监视应用监视和管理管道](data-factory-monitor-manage-app.md) | 本文介绍如何使用监视和管理应用来监视、管理和调试管道。 

<!---HONumber=AcomDC_0921_2016-->