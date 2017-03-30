---
title: "教程：使用 Visual Studio 创建包含复制活动的管道 | Microsoft 文档"
description: "在本教程中，通过使用 Visual Studio 创建带有复制活动的 Azure 数据工厂管道。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 1751185b-ce0a-4ab2-a9c3-e37b4d149ca3
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/02/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 5e6ffbb8f1373f7170f87ad0e345a63cc20f08dd
ms.openlocfilehash: 39824fa66dee9f1bd57687e59ece97f4f4636b7d
ms.lasthandoff: 03/24/2017


---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-visual-studio"></a>教程：使用 Visual Studio 创建带有复制活动的管道
> [!div class="op_single_selector"]
> * [概述与先决条件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [复制向导](data-factory-copy-data-wizard-tutorial.md)
> * [Azure 门户](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager 模板](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)
> 
> 

本教程演示如何使用 Visual Studio 创建和监视 Azure 数据工厂。 数据工厂中的管道使用复制活动将数据从 Azure Blob 存储复制到 SQL 数据库。

> [!NOTE]
> 本教程中的数据管道将数据从源数据存储复制到目标数据存储。 该管道并不通过转换输入数据来生成输出数据。 有关如何使用 Azure 数据工厂来转换数据的教程，请参阅[教程：生成第一个使用 Hadoop 群集来转换数据的管道](data-factory-build-your-first-pipeline.md)。
> 
> 通过将一个活动的输出数据集设置为另一个活动的输入数据集，可链接两个活动（两个活动先后运行）。 有关详细信息，请参阅[数据工厂中的计划和执行情况](data-factory-scheduling-and-execution.md)。

下面是本教程中要执行的步骤：

1. 创建两个链接服务：**AzureStorageLinkedService1** 和 **AzureSqlinkedService1**。 
   
    AzureStorageLinkedService1 和 AzureSqlLinkedService1 分别将 Azure 存储和 Azure SQL 数据库链接到数据工厂： **ADFTutorialDataFactoryVS**。 管道的输入数据存在于 Azure Blob 存储的 Blob 容器中，其输出数据存储在 Azure SQL 数据库的表中。 因此，将这两个数据存储区作为链接服务添加到数据工厂。
2. 创建两个数据集：**InputDataset** 和 **OutputDataset**，分别代表存储在数据存储中的输入/输出数据。 
   
    对于 InputDataset，请指定 Blob 容器，其中的某个 Blob 包含源数据。 对于 OutputDataset，请指定用于存储输出数据的 SQL 表。 还可指定其他属性，例如结构、可用性和策略。
3. 在 ADFTutorialDataFactoryVS 中创建名为 **ADFTutorialPipeline** 的管道。 
   
    该管道拥有 **复制活动** ，可将输入数据从 Azure Blob 复制到输出 Azure SQL 表。 复制活动在 Azure 数据工厂中执行数据移动。 该活动由全球可用的服务提供支持，能以安全、可靠、可缩放的方式在各种数据存储区间复制数据。 有关复制活动的详细信息，请参阅 [数据移动活动](data-factory-data-movement-activities.md) 。 
4. 创建名为 **VSTutorialFactory** 的数据工厂。 部署数据工厂和所有数据工厂实体（链接服务、表和管道）。    

## <a name="prerequisites"></a>先决条件
1. 阅读 [教程概述](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) ，完成 **先决条件** 步骤。 
2. 只有 **Azure 订阅的管理员** 才能将数据工厂实体发布到 Azure 数据工厂。  
3. 必须在计算机上安装了以下软件： 
   * Visual Studio 2013 或 Visual Studio 2015
   * 下载用于 Visual Studio 2013 或 Visual Studio 2015 的 Azure SDK。 导航到 [Azure 下载页](https://azure.microsoft.com/downloads/)，在“.NET”部分中单击“VS 2013”或“VS 2015”。
   * 下载用于 Visual Studio 的最新 Azure 数据工厂插件：[VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) 或 [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005)。 还可通过执行以下步骤更新插件：在菜单上，依次单击“工具” -> “扩展和更新” -> “联机” -> “Visual Studio 库” -> “适用于 Visual Studio 的 Microsoft Azure 数据工厂工具” -> “更新”。

## <a name="create-visual-studio-project"></a>创建 Visual Studio 项目
1. 启动 **Visual Studio 2013**。 单击“文件”，指向“新建”并单击“项目”。 将显示“新建项目”  对话框。  
2. 在“新建项目”对话框中，选择“DataFactory”模板，然后单击“空数据工厂项目”。 如果没有看到“DataFactory”模板，请关闭 Visual Studio，安装用于 Visual Studio 2013 的 Azure SDK，然后重新打开 Visual Studio。  
   
    ![“新建项目”对话框](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-project-dialog.png)
3. 输入项目的**名称**、**位置**以及**解决方案**的名称，然后单击“确定”。
   
    ![解决方案资源管理器](./media/data-factory-copy-activity-tutorial-using-visual-studio/solution-explorer.png)    

## <a name="create-linked-services"></a>创建链接服务
链接服务将数据存储或计算服务链接到 Azure 数据工厂。 有关复制活动支持的所有源和接收器，请参阅[支持的数据存储](data-factory-data-movement-activities.md#supported-data-stores-and-formats)。 有关数据工厂支持的计算服务列表，请参阅[计算链接的服务](data-factory-compute-linked-services.md)。 本教程不使用任何计算服务。 

本步骤创建两个链接服务：**AzureStorageLinkedService1** 和 **AzureSqlLinkedService1**。 AzureStorageLinkedService1 和 AzureSqlLinkedService 链接服务分别将 Azure 存储帐户和 Azure SQL 数据库链接到数据工厂： **ADFTutorialDataFactory**。 

### <a name="create-the-azure-storage-linked-service"></a>创建 Azure 存储链接服务
1. 在解决方案资源管理器中，右键单击“链接服务”，指向“添加”，然后单击“新建项”。      
2. 在“添加新项”对话框中，从列表中选择“Azure 存储链接服务”，然后单击“添加”。 
   
    ![新建链接服务](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-linked-service-dialog.png)
3. 将 `<accountname>` 和 `<accountkey>`* 替换为你的 Azure 存储帐户及其密钥。 
   
    ![Azure 存储链接服务](./media/data-factory-copy-activity-tutorial-using-visual-studio/azure-storage-linked-service.png)
4. 保存 **AzureStorageLinkedService1.json** 文件。

> 有关 JSON 属性的详细信息，请参阅 [Move data from/to Azure Blob](data-factory-azure-blob-connector.md#azure-storage-linked-service)（将数据移出/移入 Azure Blob）。
> 
> 

### <a name="create-the-azure-sql-linked-service"></a>创建 Azure SQL 链接服务
1. 在“解决方案资源管理器”中，再次右键单击“链接服务”节点，指向“添加”，然后单击“新建项”。 
2. 这次选择“Azure SQL 链接服务”，然后单击“添加”。 
3. 在 **AzureSqlLinkedService1.json 文件**中，将 `<servername>`、`<databasename>`、`<username@servername>` 和 `<password>` 分别替换为你的 Azure SQL 服务器、数据库、用户帐户和密码。    
4. 保存 **AzureSqlLinkedService1.json** 文件。 

> [!NOTE]
> 有关 JSON 属性的详细信息，请参阅 [Move data from/to Azure SQL Database](data-factory-azure-sql-connector.md#linked-service-properties)（将数据移出/移入 Azure SQL 数据库）。
> 
> 

## <a name="create-datasets"></a>创建数据集
上一步骤创建了 **AzureStorageLinkedService1** 和 **AzureSqlLinkedService1** 链接服务，用于将 Azure 存储帐户和 Azure SQL 数据库链接到数据工厂：**ADFTutorialDataFactory**。 本步骤定义两个数据集 - **InputDataset** 和 **OutputDataset**，分别表示存储在 AzureStorageLinkedService1 和 AzureSqlLinkedService1 所引用数据存储中的输入/输出数据。 对于 InputDataset，请指定 Blob 容器，其中的某个 Blob 包含源数据。 对于 OutputDataset，请指定用于存储输出数据的 SQL 表。

### <a name="create-input-dataset"></a>创建输入数据集
本步骤在 **AzureStorageLinkedService1** 链接服务代表的 Azure 存储中，创建指向 Blob 容器的 **InputDataset** 数据集。 表是矩形数据集，是目前唯一受支持的数据集类型。 

1. 在“解决方案资源管理器”中，右键单击“表”，指向“添加”，然后单击“新建项”。
2. 在“添加新项”对话框中，选择“Azure Blob”，然后单击“添加”。   
3. 将 JSON 文本替换为以下文本并保存 **AzureBlobLocation1.json** 文件。 

  ```json   
  {
    "name": "InputDataset",
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
  ``` 
    注意以下几点： 
   
   * 数据集 **type** 设置为 **AzureBlob**。
   * **linkedServiceName** 设置为 **AzureStorageLinkedService**。 已在步骤 2 中创建此链接服务。
   * **folderPath** 设置为 **adftutorial** 容器。 也可以使用 **fileName** 属性指定文件夹中 Blob 的名称。 由于未指定 Blob 的名称，容器中所有 Blob 的数据被视为输入数据。  
   * 格式 **type** 设置为 **TextFormat**
   * 文本文件中有两个以逗号分隔 (**columnDelimiter**) 的字段 - **FirstName** 和 **LastName**    
   * **availability** 设置为 **hourly**（**frequency** 设置为 **hour**，**interval** 设置为 **1**）。 因此，数据工厂每小时在指定的 Blob 容器 (**adftutorial**) 的根文件夹中查找输入数据。 
   
   如果没有指定**输入**数据集的 **fileName**，则输入文件夹 (**folderPath**) 中的所有文件/Blob 都被视为输入。 如果在 JSON 中指定 fileName，则只有指定的文件/Blob 被视为输入。
   
   如果未指定**输出表**的 **fileName**，**folderPath** 中生成的文件根据以下格式命名：Data.&lt;Guid&gt;.txt（例如：Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt）。
   
   若要根据 **SliceStart** 时间动态设置 **folderPath** 和 **fileName**，请使用 **partitionedBy** 属性。 在以下示例中，folderPath 使用 SliceStart（所处理的切片的开始时间）中的年、月和日，fileName 使用 SliceStart 中的小时。 例如，如果切片生成于 2016-09-20T08:00:00，folderName 将设置为 wikidatagateway/wikisampledataout/2016/09/20，fileName 设置为 08.csv。 
  
    ```json   
    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy": 
    [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
    ```
            
> [!NOTE]
> 有关 JSON 属性的详细信息，请参阅 [Move data from/to Azure Blob](data-factory-azure-blob-connector.md#dataset-properties)（将数据移出/移入 Azure Blob）。
> 
> 

### <a name="create-output-dataset"></a>创建输出数据集
本步骤创建名为 **OutputDataset** 的输出数据集。 此数据集指向 Azure SQL 数据库中 **AzureSqlLinkedService1** 所代表的 SQL 表。 

1. 在“解决方案资源管理器”中，再次右键单击“表”，指向“添加”，然后单击“新建项”。
2. 在“添加新项”对话框中，选择“Azure SQL”，然后单击“添加”。 
3. 将 JSON 文本替换为以下 JSON 并保存 **AzureSqlTableLocation1.json** 文件。

    ```json
    {
     "name": "OutputDataset",
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
    ```
   
    注意以下几点： 
   
   * 数据集 **type** 设置为 **AzureSQLTable**。
   * **linkedServiceName** 设置为 **AzureSqlLinkedService**（已在步骤 2 中创建此链接服务）。
   * **tablename** 设置为 **emp**。
   * 数据库的 emp 表包含三列 – **ID**、**FirstName** 和 **LastName**。 ID 是标识列，因此只需在此处指定 **FirstName** 和 **LastName**。
   * **availability** 设置为 **hourly**（**frequency** 设置为 **hour**，**interval** 设置为 **1**）。  数据工厂服务每隔一小时在 Azure SQL 数据库的 **emp** 表中生成输出数据切片。

> [!NOTE]
> 有关 JSON 属性的详细信息，请参阅 [Move data from/to Azure SQL Database](data-factory-azure-sql-connector.md#linked-service-properties)（将数据移出/移入 Azure SQL 数据库）。
> 
> 

## <a name="create-pipeline"></a>创建管道
到目前为止已创建了输入/输出链接服务和链接表。 现将创建带有 **复制活动** 的管道，将数据从 Azure Blob 复制到 Azure SQL 数据库。 

1. 在“解决方案资源管理器”中，右键单击“管道”，指向“添加”，然后单击“新建项”。  
2. 在“添加新项”对话框中，选择“复制数据管道”，然后单击“添加”。 
3. 将 JSON 替换为以下 JSON 并保存 **CopyActivity1.json** 文件。

    ```json   
    {
     "name": "ADFTutorialPipeline",
     "properties": {
       "description": "Copy data from a blob to Azure SQL table",
       "activities": [
         {
           "name": "CopyFromBlobToSQL",
           "type": "Copy",
           "inputs": [
             {
               "name": "InputDataset"
             }
           ],
           "outputs": [
             {
               "name": "OutputDataset"
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
    ```   
   注意以下几点：
   
   * 在 activities 节中，只有一个活动的 **type** 设置为 **Copy**。
   * 活动的输入设置为 **InputDataset**，活动的输出设置为 **OutputDataset**。
   * 在 **typeProperties** 节中，**BlobSource** 指定为源类型，**SqlSink** 指定为接收器类型。
   
   将 **start** 属性的值替换为当前日期，将 **end** 值替换为下一个日期。 可以仅指定日期部分，跳过日期时间的时间部分。 例如，“2016-02-03”等效于“2016-02-03T00:00:00Z”
   
   开始和结束日期时间必须采用 [ISO 格式](http://en.wikipedia.org/wiki/ISO_8601)。 例如：2016-10-14T16:32:41Z。 **结束** 时间是可选的，但本教程使用该时间。 
   
   如果未指定 **end** 属性的值，则以“**开始时间 + 48 小时**”计算。 若要无限期运行管道，请指定 **9999-09-09** 作为 **end** 属性的值。
   
   在上述示例中，由于每小时生成一个数据切片，因此共有 24 个数据切片。

## <a name="publishdeploy-data-factory-entities"></a>发布/部署数据工厂实体
本步骤发布前面所创建的数据工厂实体（链接服务、数据集和管道）。 还可以指定要创建用来保存这些实体的新数据工厂的名称。  

1. 在“解决方案资源管理器”中，右键单击该项目，并单击“发布” 。 
2. 如果显示“登录到 Microsoft 帐户”对话框，请输入拥有 Azure 订阅的帐户凭据，然后单击“登录”。
3. 应该会看到以下对话框：
   
   ![“发布”对话框](./media/data-factory-copy-activity-tutorial-using-visual-studio/publish.png)
4. 在“配置数据工厂”页，请按以下步骤操作： 
   
   1. 选择“新建数据工厂”  选项。
   2. 输入 **VSTutorialFactory** 作为**名称**。  
      
      > [!IMPORTANT]
      > Azure 数据工厂的名称必须是全局唯一的。 如果发布时收到了有关数据工厂名称的错误，请更改数据工厂的名称（例如，yournameVSTutorialFactory），再次尝试发布。 有关数据工厂项目命名规则，请参阅 [Data Factory - Naming Rules](data-factory-naming-rules.md) （数据工厂 - 命名规则）主题。        
      > 
      > 
   3. 在“订阅”字段中选择你的 Azure 订阅。
      
      > [!IMPORTANT]
      > 如果未看到任何订阅，请确保使用属于订阅管理员或共同管理员的帐户登录。  
      > 
      > 
   4. 为要创建的数据工厂选择 **资源组** 。 
   5. 为数据工厂选择 **区域** 。 下拉列表中只显示数据工厂服务支持的区域。
   6. 单击“下一步”切换到“发布项”页。
      
       ![配置数据工厂页](media/data-factory-copy-activity-tutorial-using-visual-studio/configure-data-factory-page.png)   
5. 在“发布项”页上，确保已选择所有数据工厂实体，然后单击“下一步”切换到“摘要”页。
   
   ![发布项页](media/data-factory-copy-activity-tutorial-using-visual-studio/publish-items-page.png)     
6. 查看摘要，单击“下一步”，启动部署过程并查看“部署状态”。
   
   ![发布摘要页](media/data-factory-copy-activity-tutorial-using-visual-studio/publish-summary-page.png)
7. 在“部署状态”  页上，应看到部署过程的状态。 部署完成后，单击“完成”。
 
   ![部署状态页](media/data-factory-copy-activity-tutorial-using-visual-studio/deployment-status.png)

请注意以下几点： 

* 如果收到错误：“**该订阅未注册为使用命名空间 Microsoft.DataFactory**”，请执行下列操作之一，尝试再次发布： 
  
  * 在 Azure PowerShell 中运行以下命令，注册数据工厂提供程序。 

    ```PowerShell    
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```
    可运行以下命令来确认数据工厂提供程序是否已注册。 
    
    ```PowerShell
    Get-AzureRmResourceProvider
    ```
  * 使用 Azure 订阅登录到 [Azure 门户](https://portal.azure.com) ，然后导航到“数据工厂”边栏选项卡，或在 Azure 门户中创建数据工厂。 此操作将为你自动注册提供程序。
* 数据工厂名称可能在将来被注册为 DNS 名称，因此将变成公开可见。

> [!IMPORTANT]
> 只有 Azure 订阅的管理员/共同管理员可以创建数据工厂实例
> 
> 

## <a name="summary"></a>摘要
在本教程中，这就创建了 Azure 数据工厂，将数据从 Azure Blob 复制到 Azure SQL 数据库。 使用 Visual Studio 创建数据工厂、链接服务、数据集和管道。 下面是本教程中执行的高级步骤：  

1. 创建了 Azure **数据工厂**。
2. 创建 **链接服务**：
   1. **Azure 存储** 链接服务，链接存放输入数据的 Azure 存储帐户。     
   2. **Azure SQL** 链接服务，链接存放输出数据的 Azure SQL 数据库。 
3. 创建了 **数据集**，用于描述管道的输入和输出数据。
4. 创建了包含**复制活动**的**管道**，其中 **BlobSource** 为源，**SqlSink** 为接收器。 

## <a name="use-server-explorer-to-view-data-factories"></a>使用“服务器资源管理器”查看数据工厂
1. 在 **Visual Studio** 中，在菜单上单击“视图”，然后单击“服务器资源管理器”。
2. 在“服务器资源管理器”窗口中，依次展开“Azure”和“数据工厂”。 如果看到“登录到 Visual Studio”，请输入与 Azure 订阅关联的**帐户**，然后单击“继续”。 输入**密码**，然后单击“登录”。 Visual Studio 尝试获取有关订阅中所有 Azure 数据工厂的信息。 可在“数据工厂任务列表”窗口中查看此操作的状态。

    ![服务器资源管理器](./media/data-factory-copy-activity-tutorial-using-visual-studio/server-explorer.png)
3. 可右键单击数据工厂，并选择“将数据工厂导出到新项目”，创建基于现有数据工厂的 Visual Studio 项目。

    ![将数据工厂导出为 VS 项目](./media/data-factory-copy-activity-tutorial-using-visual-studio/export-data-factory-menu.png)  

## <a name="update-data-factory-tools-for-visual-studio"></a>更新适用于 Visual Studio 的数据工厂工具
若要更新适用于 Visual Studio 的 Azure 数据工厂工具，请执行以下步骤：

1. 在菜单中单击“工具”，然后选择“扩展和更新”。 
2. 在左窗格中选择“更新”，然后选择“Visual Studio 库”。
3. 选择“用于 Visual Studio 的 Azure 数据工厂工具”，然后单击“更新”。 如果未看到此项，说明你已有此工具的最新版本。 

有关如何使用 Azure 门户监视本教程中所创建管道和数据集的说明，请参阅 [监视数据集和管道](data-factory-copy-activity-tutorial-using-azure-portal.md#monitor-pipeline) 。

## <a name="see-also"></a>另请参阅
| 主题 | 说明 |
|:--- |:--- |
| [管道](data-factory-create-pipelines.md) |帮助你了解 Azure 数据工厂中的管道和活动 |
| [数据集](data-factory-create-datasets.md) |还有助于了解 Azure 数据工厂中的数据集。 |
| [计划和执行](data-factory-scheduling-and-execution.md) |本文介绍 Azure 数据工厂应用程序模型的计划方面和执行方面。 |
| [使用监视应用监视和管理管道](data-factory-monitor-manage-app.md) |本文介绍如何使用监视和管理应用来监视、管理和调试管道。 |


