<properties 
	pageTitle="教程：使用 Azure 门户创建包含复制活动的管道 | Microsoft Azure" 
	description="本教程使用 Azure 门户中的数据工厂编辑器创建包含复制活动的 Azure 数据工厂管道。" 
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
	ms.date="09/16/2016" 
	ms.author="spelluru"/>

# 教程：使用 Azure 门户创建包含复制活动的管道
> [AZURE.SELECTOR]
- [概述与先决条件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Azure 门户](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
- [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)
- [复制向导](data-factory-copy-data-wizard-tutorial.md)


本教程演示如何使用 Azure 门户创建和监视 Azure 数据工厂。数据工厂中的管道使用复制活动将数据从 Azure Blob 存储复制到 SQL 数据库。

下面是本教程中要执行的步骤：

步骤 | 说明
-----| -----------
[创建 Azure 数据工厂](#create-data-factory) | 本步骤创建名为 **ADFTutorialDataFactory** 的 Azure 数据工厂。  
[创建链接服务](#create-linked-services) | 本步骤创建两个链接服务：**AzureStorageLinkedService** 和 **AzureSqlLinkedService**。AzureStorageLinkedService 和 AzureSqlLinkedService 分别将 Azure 存储和 Azure SQL 数据库链接到 ADFTutorialDataFactory。管道的输入数据存在于 Azure Blob 存储的 Blob 容器中，其输出数据存储在 Azure SQL 数据库的表中。因此，将这两个数据存储作为链接服务添加到数据工厂。      
[创建输入和输出数据集](#create-datasets) | 在上一步骤中，创建了引用包含输入/输出数据的数据存储的链接服务。此步骤将定义两个数据工厂表：**EmpTableFromBlob** 和 **EmpSQLTable**，它们分别代表存储在数据存储中的输入/输出数据。为 EmpTableFromBlob 指定 Blob 容器，其中包含用于存储源数据的 Blob；为 EmpSQLTable 指定 SQL 表，用于存储输出数据。还可指定其他属性，例如结构、可用性和策略。 
[创建管道](#create-pipeline) | 本步骤在 ADFTutorialDataFactory 中创建名为 **ADFTutorialPipeline** 的管道。该管道拥有**复制活动**，可将输入数据从 Azure Blob 复制到输出 Azure SQL 表。复制活动在 Azure 数据工厂中执行数据移动。该活动由全球可用的服务提供支持，能以安全、可靠、可缩放的方式在各种数据存储之间复制数据。有关复制活动的详细信息，请参阅 [Data Movement Activities](data-factory-data-movement-activities.md)（数据移动活动）。 
[监视管道](#monitor-pipeline) | 本步骤使用 Azure 门户监视输入和输出表的切片。

> [AZURE.IMPORTANT] 
执行本教程前，请通读 [Tutorial Overview](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)（教程概述）一文并完成**先决条件**步骤。

## 创建数据工厂
本步骤使用 Azure 门户创建名为 **ADFTutorialDataFactory** 的 Azure 数据工厂。

1.	登录 [Azure 门户][azure-portal]后，在左下角单击“+ 新建”，在“创建”边栏选项卡中选择“数据分析”，然后在“数据分析”边栏选项卡中单击“数据工厂”。

	![“新建”->“数据工厂”][image-data-factory-new-datafactory-menu]

6. 在“新建数据工厂”边栏选项卡中：
	1. 输入 **ADFTutorialDataFactory** 作为**名称**。
	
  		![“新建数据工厂”边栏选项卡][image-data-factory-getstarted-new-data-factory-blade]
	2. 单击“资源组名称”并执行以下步骤：
		1. 单击“新建资源组”。
		2. 在“创建资源组”边栏选项卡中，输入 **ADFTutorialResourceGroup** 作为资源组的**名称**，然后单击“确定”。

			![创建资源组][image-data-factory-create-resource-group]

		本教程中的某些步骤假设使用 **ADFTutorialResourceGroup** 作为资源组名称。若要了解有关资源组的详细信息，请参阅 [Using resource groups to manage your Azure resources](../resource-group-overview.md)（使用资源组管理 Azure 资源）。
7. 请注意，在“新建数据工厂”边栏选项卡中，“添加到启动板”处于选中状态。
8. 在“新建数据工厂”边栏选项卡中单击“创建”。

	Azure 数据工厂的名称必须是全局唯一的。如果收到错误，请更改数据工厂的名称（例如改为 yournameADFTutorialDataFactory），然后重新尝试创建。有关数据工厂项目命名规则，请参阅 [Data Factory - Naming Rules](data-factory-naming-rules.md)（数据工厂 - 命名规则）主题。
	
		Data factory name “ADFTutorialDataFactory” is not available  
	 
	![数据工厂名称不可用][image-data-factory-name-not-available]
	
	> [AZURE.NOTE] 数据工厂名称可能在将来被注册为 DNS 名称，因此将变成公开可见。
	> 
	> 只有 Azure 订阅的参与者/管理员才可以创建数据工厂实例

9. 在创建过程中单击左侧的“通知”中心可以查看通知。如果“通知”边栏选项卡处于打开状态，请单击“X”将其关闭。
10. 完成创建后，将显示图中所示的“数据工厂”边栏选项卡。

    ![数据工厂主页][image-data-factory-get-stated-factory-home-page]

## 创建链接服务
链接服务将数据存储或计算服务链接到 Azure 数据工厂。数据存储可以是 Azure 存储、Azure SQL 数据库或本地 SQL Server 数据库。

本步骤创建两个链接服务：**AzureStorageLinkedService** 和 **AzureSqlLinkedService**。AzureStorageLinkedService 和 AzureSqlLinkedService 链接服务分别将 Azure 存储帐户和 Azure SQL 数据库链接到 **ADFTutorialDataFactory**。稍后要在本教程中创建管道，将数据从 AzureStorageLinkedService 中的 Blob 容器复制到 AzureSqlLinkedService 中的 SQL 表。

### 为 Azure 存储帐户创建链接服务
1.	在“数据工厂”边栏选项卡中，单击“编写和部署”磁贴启动数据工厂的“编辑器”。

	![“编写和部署”磁贴][image-author-deploy-tile]

	 
5. 在“编辑器”中，单击工具栏上的“新建数据存储”按钮，然后从下拉菜单中选择“Azure 存储”。在右窗格中，应会看到用于创建 Azure 存储链接服务的 JSON 模板。

	![编辑器中的“新建数据存储”按钮][image-editor-newdatastore-button]
    
6. 将 **accountname** 和 **accountkey** 替换为 Azure 存储帐户的帐户名与帐户密钥值。

	![编辑器中的 Blob 存储 JSON](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-blob-storage-json.png)
	
	有关 JSON 属性的详细信息，请参阅 [JSON Scripting Reference](http://go.microsoft.com/fwlink/?LinkId=516971)（JSON 脚本参考）。

6. 单击工具栏上的“部署”，部署 AzureStorageLinkedService。确认标题栏中显示了“已成功创建链接服务”消息。

	![编辑器中的 Blob 存储部署][image-editor-blob-storage-deploy]

### 为 Azure SQL 数据库创建链接服务
1. 在“数据工厂编辑器”中，单击工具栏上的“新建数据存储”按钮，然后从下拉菜单中选择“Azure SQL 数据库”。在右窗格中，应会看到用于创建 Azure SQL 链接服务的 JSON 模板。

	![编辑器中的 Azure SQL 设置][image-editor-azure-sql-settings]

2. 将 **servername**、**databasename**、**username@servername** 和 **password** 分别替换为 Azure SQL 服务器名称、数据库名称、用户帐户名和密码。
3. 单击工具栏上的“部署”，创建并部署 AzureSqlLinkedService。
   

## 创建数据集
上一步骤已创建 **AzureStorageLinkedService** 和 **AzureSqlLinkedService** 链接服务，用于将 Azure 存储帐户和 Azure SQL 数据库链接到数据工厂：**ADFTutorialDataFactory**。本步骤定义两个数据工厂表 - **EmpTableFromBlob** 和 **EmpSQLTable**，表示存储在 AzureStorageLinkedService 和 AzureSqlLinkedService 所引用数据存储中的输入/输出数据。为 EmpTableFromBlob 指定 Blob 容器，其中包含用于存储源数据的 Blob；为 EmpSQLTable 指定 SQL 表，用于存储输出数据。

### 创建输入数据集 
表是具有架构的矩形数据集。本步骤在 **AzureStorageLinkedService** 链接服务代表的 Azure 存储中创建指向 Blob 容器的 **EmpBlobTable** 表。

1. 在数据工厂的“编辑器”中，单击工具栏上的“新建数据集”按钮，然后在下拉菜单中单击“Blob 表”。
2. 将右窗格中的 JSON 替换为以下 JSON 代码片段：

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
		    "linkedServiceName": "AzureStorageLinkedService",
		    "typeProperties": {
		      "folderPath": "adftutorial/",
			  "fileName": "emp.txt",
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

		
     请注意以下几点：
	
	- 数据集 **type** 设置为 **AzureBlob**。
	- **linkedServiceName** 设置为 **AzureStorageLinkedService**。已在步骤 2 中创建此链接服务。
	- **folderPath** 设置为 **adftutorial** 容器。还可以指定文件夹中 Blob 的名称。由于未指定 Blob 的名称，容器中所有 Blob 的数据被视为输入数据。
	- 格式 **type** 设置为 **TextFormat**
	- 文本文件中有两个以逗号分隔 (**columnDelimiter**) 的字段 - **FirstName** 和 **LastName**
	- **availability** 设置为 **hourly**（**frequency** 设置为 **hour**，**interval** 设置为 **1**）。因此，数据工厂每小时在指定的 Blob 容器 (**adftutorial**) 的根文件夹中查找输入数据。
	

	如果没有指定**输入****表**的 **fileName**，则输入文件夹 (**folderPath**) 中的所有文件/Blob 都被视为输入。如果在 JSON 中指定 fileName，则只有指定的文件/Blob 被视为输入。
 
	如果未指定**输出表**的 **fileName**，**folderPath** 中生成的文件根据以下格式命名：Data.&lt;Guid&gt;.txt（例如：Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt）。

	若要根据 **SliceStart** 时间动态设置 **folderPath** 和 **fileName**，请使用 **partitionedBy** 属性。在以下示例中，folderPath 使用 SliceStart（所处理的切片的开始时间）中的年、月和日，fileName 使用 SliceStart 中的小时。例如，如果切片生成于 2014-10-20T08:00:00，folderName 将设置为 wikidatagateway/wikisampledataout/2014/10/20，fileName 设置为 08.csv。

	  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
        	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

	有关 JSON 属性的详细信息，请参阅 [JSON Scripting Reference](http://go.microsoft.com/fwlink/?LinkId=516971)（JSON 脚本参考）。

2. 单击工具栏上的“部署”，创建并部署 **EmpTableFromBlob** 表。确认编辑器标题栏中显示了“已成功创建表”消息。

### 创建输出数据集
本步骤的此部分创建名为 **EmpSQLTable** 的输出数据集。此数据集指向 Azure SQL 数据库中 **AzureSqlLinkedService** 所代表的 SQL 表。

1. 在数据工厂的“编辑器”中，单击工具栏上的“新建数据集”按钮，然后在下拉菜单中单击“Azure SQL 表”。
2. 将右窗格中的 JSON 替换为以下 JSON 代码片段：

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
		    "linkedServiceName": "AzureSqlLinkedService",
		    "typeProperties": {
		      "tableName": "emp"
		    },
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}

		
     请注意以下几点：
	
	* 数据集 **type** 设置为 **AzureSQLTable**。
	* **linkedServiceName** 设置为 **AzureSqlLinkedService**（已在步骤 2 中创建此链接服务）。
	* **tablename** 设置为 **emp**。
	* 数据库的 emp 表包含三列 – **ID**、**FirstName** 和 **LastName**。ID 是标识列，因此只需在此处指定 **FirstName** 和 **LastName**。
	* **availability** 设置为 **hourly**（**frequency** 设置为 **hour**，**interval** 设置为 **1**）。数据工厂服务每隔一小时在 Azure SQL 数据库的 **emp** 表中生成输出数据切片。


3. 单击工具栏上的“部署”，创建并部署 **EmpSQLTable** 表。


## 创建管道
本步骤将创建管道，其中包含使用 **EmpTableFromBlob** 作为输入和使用 **EmpSQLTable** 作为输出的**复制活动**。

1. 在数据工厂的“编辑器”中，单击工具栏上的“新建管道”按钮。如果没有看到此按钮，请在工具栏中单击 **...（省略号）**。或者，也可以在树视图中，右键单击“管道”，然后单击“新建管道”。

	![编辑器中的“新建管道”按钮][image-editor-newpipeline-button]
 
2. 将右窗格中的 JSON 替换为以下 JSON 代码片段：
		
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
		          "retry": 0,
		          "timeout": "01:00:00"
		        }
		      }
		    ],
		    "start": "2015-07-12T00:00:00Z",
		    "end": "2015-07-13T00:00:00Z"
		  }
		} 

	请注意以下几点：

	- 在 activities 节中，只有一个活动的 **type** 设置为 **CopyActivity**。
	- 活动的输入设置为 **EmpTableFromBlob**，活动的输出设置为 **EmpSQLTable**。
	- 在 **transformation** 节中，**BlobSource** 指定为源类型，**SqlSink** 指定为接收器类型。

	将 **start** 属性的值替换为当前日期，将 **end** 值替换为下一个日期。可以仅指定日期部分，跳过日期时间的时间部分。例如，“2015-02-03”等效于“2015-02-03T00:00:00Z”
	
	开始和结束日期时间必须采用 [ISO 格式](http://en.wikipedia.org/wiki/ISO_8601)。例如：2014-10-14T16:32:41Z。**结束**时间是可选的，但本教程使用该时间。
	
	如果未指定 **end** 属性的值，则以“**开始时间 + 48 小时**”计算。若要无限期运行管道，请指定 **9999-09-09** 作为 **end** 属性的值。
	
	在上述示例中，由于每小时生成一个数据切片，因此共有 24 个数据切片。
	
	有关 JSON 属性的详细信息，请参阅 [JSON Scripting Reference](http://go.microsoft.com/fwlink/?LinkId=516971)（JSON 脚本参考）。

4. 单击工具栏上的“部署”，创建并部署 **ADFTutorialPipeline**。确认显示了“已成功创建管道”消息。
5. 现在，请单击“X”关闭“编辑器”边栏选项卡。再次单击“X”关闭包含工具栏和树视图的“ADFTutorialDataFactory”边栏选项卡。如果看到“将丢弃未保存的编辑”消息，请单击“确定”。
6. 随即返回 **ADFTutorialDataFactory** 的“数据工厂”边栏选项卡。

**祝贺你！** 现已成功创建 Azure 数据工厂、链接服务、表和管道，并已计划好管道。
 
### 在图示视图中查看数据工厂 
1. 在“数据工厂”边栏选项卡中，单击“图示”。

	![数据工厂边栏选项卡 - 图示磁贴][image-datafactoryblade-diagramtile]

2. 应会看到如下所示的图形：

	![图示视图][image-data-factory-get-started-diagram-blade]

	可以将管道和表放大、缩小、恢复、缩放到适当比例和自动定位，以及显示沿袭信息（突出显示所选项的上游和下游项）。可以双击对象（输入/输出表或管道）查看其属性。
3. 在图示视图中右键单击“ADFTutorialPipeline”，然后单击“打开管道”。应会看到管道中的活动，以及活动的输入和输出数据集。在本教程中，管道只能包含一个活动（复制活动），其中 EmpTableBlob 为输入数据集，EmpSQLTable 为输出数据集。

	![打开管道](./media/data-factory-copy-activity-tutorial-using-azure-portal/DiagramView-OpenPipeline.png)

4. 单击左上角痕迹导航栏中的“数据工厂”，返回图示视图。图示视图显示所有管道。在本示例中，只创建了一个管道。
 

## 监视管道
本步骤使用 Azure 门户监视 Azure 数据工厂的运行情形。

1. 导航到 [Azure 门户（预览版）][azure-portal]（如果尚未打开）。
2. 如果 **ADFTutorialDataFactory** 的边栏选项卡未打开，请单击“启动板”上的“ADFTutorialDataFactory”将它打开。
3. 应会在此边栏选项卡上看到创建的表和管道的计数与名称。

	![包含名称的主页][image-data-factory-get-started-home-page-pipeline-tables]

4. 现在，请单击“数据集”磁贴。
5. 在“数据集”边栏选项卡中，单击“EmpTableFromBlob”。此数据集是 **ADFTutorialPipeline** 的输入数据集。

	![已选择 EmpTableFromBlob 的数据集][image-data-factory-get-started-datasets-emptable-selected]
5. 请注意，到目前为止，数据切片已生成并已“就绪”，因为 **emp.txt** 文件始终在 Blob 容器中：**adftutorial\\input**。确认底部的“最近失败的切片”部分中没有任何切片。

	“最近更新的切片”和“最近失败的切片”列表已根据“上次更新时间”排序。在以下情况下，切片的更新时间已更改。
    
	单击列表标题或 **...（省略号）**可以查看详细的切片列表。单击工具栏上的“筛选”可以筛选切片。
	
	若要按切片开始/结束时间查看数据切片，请单击“数据切片(依切片时间)”磁贴。

	![按切片时间为数据切片排序][DataSlicesBySliceTime]

6. 现在，请在“数据集”边栏选项卡中单击“EmpSQLTable”。此数据集是 **ADFTutorialPipeline** 的输出数据集。

	![数据集边栏选项卡][image-data-factory-get-started-datasets-blade]

6. 应会看到下图所示的“EmpSQLTable”边栏选项卡：

	![表边栏选项卡][image-data-factory-get-started-table-blade]
 
7. 可以看到，到目前为止已生成数据切片，并且其状态为“就绪”。底部的“问题切片”部分中未显示任何切片。
8. 单击 **...（省略号）**查看所有切片。

	![数据切片边栏选项卡][image-data-factory-get-started-dataslices-blade]

9. 在列表中单击任一数据切片可以看到“数据切片”边栏选项卡。

	![数据切片边栏选项卡][image-data-factory-get-started-dataslice-blade]
  
	如果切片状态不是“就绪”，可以在“未就绪的上游切片”列表中看到未就绪且阻碍当前切片运行的上游切片。

11. 在“数据切片”边栏选项卡的底部列表中，应会看到所有活动都在运行。单击“活动运行”查看“活动运行详细信息”边栏选项卡。

	![活动运行详细信息][image-data-factory-get-started-activity-run-details]

	
12. 单击“X”关闭所有边栏选项卡，直到返回 **ADFTutorialDataFactory** 的主页边栏选项卡。
14. （可选）在 **ADFTutorialDataFactory** 的主页上单击“管道”，单击“管道”边栏选项卡中的“ADFTutorialPipeline”，然后深入到输入表（“已使用”）或输出表（“已生成”）。
15. 启动 **SQL Server Management Studio**，连接到 Azure SQL 数据库，然后确认行是否已插入数据库的 **emp** 表中。

	![SQL 查询结果][image-data-factory-get-started-sql-query-results]


## 摘要 
本教程创建了一个 Azure 数据工厂，用于将数据从 Azure Blob 复制到 Azure SQL 数据库。已使用 Azure 门户创建了数据工厂、链接服务、数据集和管道。下面是本教程中执行的高级步骤：

1.	创建了 Azure **数据工厂**。
2.	创建了**链接服务**：
	1. 一个 **Azure 存储**链接服务，用于链接保存输入数据的 Azure 存储帐户。
	2. 一个 **Azure SQL** 链接服务，用于链接保存输出数据的 Azure SQL 数据库。
3.	创建了**数据集**，用于描述管道的输入和输出数据。
4.	创建了包含**复制活动**的**管道**，其中 **BlobSource** 为源，**SqlSink** 为接收器。


## 另请参阅
| 主题 | 说明 |
| :---- | :---- |
| [Data Movement Activities](data-factory-data-movement-activities.md)（数据移动活动） | 此文提供有关教程中所用复制活动的详细信息。 |
| [Scheduling and execution](data-factory-scheduling-and-execution.md)（计划和执行） | 此文介绍 Azure 数据工厂应用程序模型的计划和执行方面。 |
| [管道](data-factory-create-pipelines.md) | 帮助了解 Azure 数据工厂中的管道和活动 |
| [数据集](data-factory-create-datasets.md) | 此文帮助了解 Azure 数据工厂中的数据集。
| [Monitor and manage pipelines using Monitoring App](data-factory-monitor-manage-app.md)（使用监视应用来监视和管理管道） | 此文介绍如何使用监视和管理应用来监视、管理及调试管道。 

<!--Link references-->
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[msdn-activities]: https://msdn.microsoft.com/library/dn834988.aspx
[msdn-linkedservices]: https://msdn.microsoft.com/library/dn834986.aspx
[data-factory-naming-rules]: https://msdn.microsoft.com/library/azure/dn835027.aspx

[azure-portal]: https://portal.azure.com/
[download-azure-powershell]: http://azure.microsoft.com/documentation/articles/install-configure-powershell
[sql-management-studio]: http://azure.microsoft.com/documentation/articles/sql-database-manage-azure-ssms/#Step2
[sql-cmd-exe]: https://msdn.microsoft.com/library/azure/ee336280.aspx

[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[data-factory-create-storage]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account



[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

<!--Image references-->

[DataSlicesBySliceTime]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/DataSlicesBySliceTime.png

[image-data-factory-getstarted-new-data-factory-blade]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-new-data-factory.png

[image-data-factory-get-stated-factory-home-page]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-data-factory-home-page.png

[image-author-deploy-tile]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-author-deploy-tile.png

[image-editor-newdatastore-button]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-newdatastore-button.png

[image-editor-blob-storage-deploy]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-blob-storage-deploy.png

[image-editor-azure-sql-settings]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-azure-sql-settings.png

[image-editor-newpipeline-button]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-newpipeline-button.png

[image-datafactoryblade-diagramtile]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-datafactoryblade-diagramtile.png


[image-data-factory-get-started-diagram-blade]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-diagram-blade.png

[image-data-factory-get-started-home-page-pipeline-tables]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-datafactory-home-page-pipeline-tables.png

[image-data-factory-get-started-datasets-blade]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-datasets-blade.png

[image-data-factory-get-started-table-blade]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-table-blade.png

[image-data-factory-get-started-dataslices-blade]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-dataslices-blade.png

[image-data-factory-get-started-dataslice-blade]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-dataslice-blade.png

[image-data-factory-get-started-sql-query-results]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-sql-query-results.png

[image-data-factory-get-started-datasets-emptable-selected]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/DataSetsWithEmpTableFromBlobSelected.png

[image-data-factory-get-started-activity-run-details]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/ActivityRunDetails.png

[image-data-factory-create-resource-group]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/CreateNewResourceGroup.png


[image-data-factory-new-datafactory-menu]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/NewDataFactoryMenu.png


[image-data-factory-name-not-available]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-data-factory-not-available.png
 

<!---HONumber=AcomDC_0921_2016-->