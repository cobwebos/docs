---
title: "教程：使用 Azure PowerShell 创建移动数据的管道 | Microsoft 文档"
description: "本教程使用 Azure PowerShell 创建包含复制活动的 Azure 数据工厂管道。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 71087349-9365-4e95-9847-170658216ed8
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/02/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: fbf77e9848ce371fd8d02b83275eb553d950b0ff
ms.openlocfilehash: a95e65db804f1c6cc2927901216ee7a287a911ee


---
# <a name="tutorial-create-a-data-factory-pipeline-that-moves-data-by-using-azure-powershell"></a>教程：使用 Azure PowerShell 创建移动数据的数据工厂管道
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

本教程介绍如何使用 Azure PowerShell cmdlet 创建和监视 Azure 数据工厂实例。 学习本教程过程中在数据工厂中创建的管道使用复制活动将数据从 Azure Blob 复制到 Azure SQL 数据库。

“复制活动”功能在数据工厂中执行数据移动。 该活动由全球可用的服务提供支持，能以安全、可靠、可缩放的方式在各种数据存储区间复制数据。 有关复制活动的详细信息，请参阅[数据移动活动](data-factory-data-movement-activities.md)。   

> [!NOTE]
> 本文不会介绍所有数据工厂 cmdlet。 有关这些 cmdlet 的综合文档，请参阅[数据工厂 cmdlet 参考](/powershell/resourcemanager/azurerm.datafactories/v2.5.0/azurerm.datafactories)。
>
> 本教程中的数据管道将数据从源数据存储复制到目标数据存储。 该管道并不通过转换输入数据来生成输出数据。 有关如何使用 Azure 数据工厂来转换数据的教程，请参阅[教程：生成使用 Hadoop 群集来转换数据的管道](data-factory-build-your-first-pipeline.md)。

## <a name="prerequisites"></a>先决条件
- 请阅读 [教程概述和先决条件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) 了解教程概述，并完成 **先决条件** 步骤。
- 安装 Azure PowerShell 中的说明进行操作。 遵循[如何安装和配置 Azure PowerShell](../powershell-install-configure.md) 中的说明。

## <a name="in-this-tutorial"></a>本教程的内容
下表列出了要在本教程中执行的步骤。

| 步骤 | 说明 |
| --- | --- |
| [创建 Azure 数据工厂](#create-data-factory) |此步骤创建名为 **ADFTutorialDataFactoryPSH**的 Azure 数据工厂。 |
| [创建链接服务](#create-linked-services) |此步骤创建两个链接服务：**StorageLinkedService** 和 **AzureSqlLinkedService**。 StorageLinkedService 和 AzureSqlLinkedService 分别将 Azure 存储服务与 Azure SQL 数据库链接到 ADFTutorialDataFactoryPSH。 |
| [创建输入和输出数据集](#create-datasets) |此步骤定义两个数据集（**EmpTableFromBlob** 和 **EmpSQLTable**）。 在下一步骤中创建 ADFTutorialPipeline 时，这两个数据集用作**复制活动**的输入和输出表。 |
| [创建和运行管道](#create-pipeline) |此步骤在数据工厂 ADFTutorialDataFactoryPSH 中创建名为 **ADFTutorialPipeline** 的管道。 该管道使用复制活动将数据从 Azure Blob 复制到 Azure 数据库输出表。 |
| [监视数据集和管道](#monitor-pipeline) |此步骤使用 Azure PowerShell 监视数据集和管道。 |

## <a name="create-a-data-factory"></a>创建数据工厂
此步骤使用 Azure PowerShell 创建名为 **ADFTutorialDataFactoryPSH** 的 Azure 数据工厂。

1. 启动 **PowerShell**。 在本教程结束之前，请将 Azure PowerShell 保持打开状态。 如果将它关闭再重新打开，则需要再次运行下述命令。

   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 运行以下命令并输入用于登录 Azure 门户的用户名和密码：

           Login-AzureRmAccount   
   b. 运行以下命令查看此帐户的所有订阅：

           Get-AzureRmSubscription
   c. 运行以下命令选择要使用的订阅。 将 **&lt;NameOfAzureSubscription**&gt; 替换为 Azure 订阅的名称：

           Get-AzureRmSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzureRmContext
2. 运行以下命令，创建名为 **ADFTutorialResourceGroup** 的 Azure 资源组：

        New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

    本教程中的某些步骤假设使用名为 **ADFTutorialResourceGroup**的资源组。 如果使用不同的资源组，需使用该资源组取代本教程中的 ADFTutorialResourceGroup。
3. 运行 **New-AzureRmDataFactory** cmdlet，创建名为 **ADFTutorialDataFactoryPSH** 的数据工厂：  

        New-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH –Location "West US"

注意以下几点：

* Azure 数据工厂的名称必须全局唯一。 如果收到以下错误，请更改名称（例如改为 yournameADFTutorialDataFactoryPSH）。 执行本教程中的步骤时，请使用此名称取代 ADFTutorialFactoryPSH。 对于数据工厂项目，请参阅 [Data Factory - Naming Rules](data-factory-naming-rules.md)（数据工厂 - 命名规则）。

        Data factory name “ADFTutorialDataFactoryPSH” is not available
* 只有 Azure 订阅的参与者或管理员才可以创建数据工厂实例。
* 数据工厂名称可能在将来被注册为 DNS 名称，因此将公开可见。
* 可能会收到以下错误：“未注册订阅，无法使用命名空间 Microsoft.DataFactory”。 请执行以下操作之一，然后重试发布：

  * 在 Azure PowerShell 中运行以下命令，注册数据工厂提供程序。

          Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory

      运行以下命令，确认数据工厂提供程序是否已注册：

          Get-AzureRmResourceProvider
  * 使用 Azure 订阅登录到 [Azure 门户](https://portal.azure.com)。 在 Azure 门户中转到“数据工厂”边栏选项卡，或创建一个数据工厂。 此操作将自动注册提供程序。

## <a name="create-linked-services"></a>创建链接服务
链接服务将数据存储或计算服务链接到 Azure 数据工厂。 数据存储可以是 Azure 存储服务、Azure SQL 数据库，或者是包含数据工厂管道的输入数据或存储其输出数据的本地 SQL Server 数据库。 计算服务是处理输入数据并生成输出数据的服务。

本步骤创建两个链接服务：**StorageLinkedService** 和 **AzureSqlLinkedService**。 StorageLinkedService 和 AzureSqlLinkedService 分别将 Azure 存储帐户和 Azure SQL 数据库链接到数据工厂 **ADFTutorialDataFactoryPSH**。 稍后要在本教程中创建管道，将数据从 StorageLinkedService 中的 Blob 容器复制到 AzureSqlLinkedService 中的 SQL 表。

### <a name="create-a-linked-service-for-an-azure-storage-account"></a>为 Azure 存储帐户创建链接服务
1. 在 **C:\ADFGetStartedPSH** 文件夹中，创建包含以下内容的名为 **StorageLinkedService.json** 的 JSON 文件。 （创建 ADFGetStartedPSH 文件夹（如果不存在）。）

         {
               "name": "StorageLinkedService",
               "properties": {
                 "type": "AzureStorage",
                 "typeProperties": {
                       "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
                 }
               }
         }

   将 **accountname** 和 **accountkey** 分别替换为 Azure 存储帐户的名称和密钥。
2. 在 **Azure PowerShell** 中，切换到 **ADFGetStartedPSH** 文件夹。
3. 可以使用 **New-AzureRmDataFactoryLinkedService** cmdlet 创建链接服务。 此 cmdlet 以及本教程中使用的其他数据工厂 cmdlet 要求传递 **ResourceGroupName** 和 **DataFactoryName** 参数的值。 或者，可以使用 **Get-AzureRmDataFactory** 获取 DataFactory 对象并传递该对象，这样就不需要在每次运行 cmdlet 时输入 ResourceGroupName 和 DataFactoryName。 运行以下命令，将 **Get-AzureRmDataFactory** cmdlet 的输出分配给变量 **$df**：

    ```   
    $df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH
    ```

4. 现在，运行 **New-AzureRmDataFactoryLinkedService** cmdlet 创建链接服务 **StorageLinkedService**。

    ```
    New-AzureRmDataFactoryLinkedService $df -File .\StorageLinkedService.json
    ```

    如果尚未运行 **Get-AzureRmDataFactory** cmdlet 并将输出分配给 **$df** 变量，则必须指定 ResourceGroupName 和 DataFactoryName 参数的值，如下所示。   

    ```
    New-AzureRmDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactoryPSH -File .\StorageLinkedService.json
    ```

如果在教程的中途关闭 Azure PowerShell，则下次启动 Azure PowerShell 时必须运行 Get-AzureRmDataFactory cmdlet 才能完成教程。

### <a name="create-a-linked-service-for-an-azure-sql-database"></a>为 Azure SQL 数据库创建链接服务
1. 创建包含以下内容的名为 AzureSqlLinkedService.json 的 JSON 文件：

         {
             "name": "AzureSqlLinkedService",
             "properties": {
                 "type": "AzureSqlDatabase",
                 "typeProperties": {
                       "connectionString": "Server=tcp:<server>.database.windows.net,1433;Database=<databasename>;User ID=<user>@<server>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
                 }
               }
         }

   将 **servername**、**databasename**、**username@servername** 和 **password** 分别替换为 Azure SQL 服务器名称、数据库名称、用户帐户名和密码。
2. 运行以下命令创建链接服务：

    ```
    New-AzureRmDataFactoryLinkedService $df -File .\AzureSqlLinkedService.json
    ```

   确认为 SQL 数据库服务器启用了“允许访问 Azure 服务”设置。 若要验证并启用此设置，请执行以下步骤：

   1. 单击左侧的“浏览”中心，然后单击“SQL 服务器”。
   2. 选择你的服务器，然后单击“SQL 服务器”边栏选项卡上的“设置”。
   3. 在“设置”边栏选项卡中，单击“防火墙”。
   4. 在“防火墙设置”边栏选项卡中，单击“允许访问 Azure 服务”旁边的“打开”。
   5. 单击左侧的“活动”中心，切换到原先所在的“数据工厂”边栏选项卡。

## <a name="create-datasets"></a>创建数据集
在上一步骤中，已创建用于将 Azure 存储帐户和 Azure SQL 数据库链接到数据工厂的服务。 本步骤将创建数据集，这些数据集在下一步骤所创建的管道中表示复制活动的输入和输出数据。

表是一个矩形数据集， 也是目前唯一受支持的数据集类型。 本教程中的输入表引用 Azure 存储中的 Blob 容器。 输出表引用 Azure SQL 数据库中的 SQL 表。  

### <a name="prepare-azure-blob-storage-and-azure-sql-database-for-the-tutorial"></a>准备教程所需的 Azure Blob 存储和 Azure SQL 数据库
如果已完成[将数据从 Blob 存储复制到 SQL 数据库](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)中的教程，请跳过此步骤。

执行以下步骤，准备本教程所需的 Blob 存储和 SQL 数据库。

1. 在 **StorageLinkedService** 指向的 Blob 存储中，创建名为 **adftutorial** 的 Blob 容器。
2. 创建名为 **emp.txt** 的文本文件，将其作为 Blob 上载到 **adftutorial** 容器。
3. 在 **AzureSqlLinkedService** 指向的 SQL 数据库中，创建名为 **emp** 的表。

4. 启动记事本，粘贴以下文本，将文件命名为 **emp.txt**，然后将其保存到硬盘上的 **C:\ADFGetStartedPSH** 文件夹。

        John, Doe
        Jane, Doe
5. 使用 [Azure 存储资源管理器](https://azurestorageexplorer.codeplex.com/)等工具创建 **adftutorial** 容器，将 **emp.txt** 文件上载到该容器。

    ![Azure 存储资源管理器](media/data-factory-copy-activity-tutorial-using-powershell/getstarted-storage-explorer.png)
6. 使用以下 SQL 脚本在 SQL 数据库中创建 **emp** 表。  

        CREATE TABLE dbo.emp
        (
            ID int IDENTITY(1,1) NOT NULL,
            FirstName varchar(50),
            LastName varchar(50),
        )
        GO

        CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);

    如果已在计算机上安装 SQL Server 2014，请遵循 [Step 2: Connect to SQL Database of the Managing Azure SQL Database using SQL Server Management Studio](../sql-database/sql-database-manage-azure-ssms.md)（步骤 2：使用 SQL Server Management Studio 连接到管理 Azure SQL 数据库的 SQL 数据库）中的说明连接到 SQL 数据库服务器，然后运行 SQL 脚本。

    如果不允许客户端访问 SQL 数据库服务器，则需要将 SQL 数据库服务器的防火墙配置为允许从计算机（IP 地址）访问。 相关步骤请参阅[此文](../sql-database/sql-database-configure-firewall-settings.md)。

### <a name="create-an-input-dataset"></a>创建输入数据集
表是具有架构的矩形数据集。 本步骤将创建名为 **EmpBlobTable** 的表。 此表指向 Azure 存储中由 **StorageLinkedService** 链接服务表示的 Blob 容器。 此 Blob 容器 (**adftutorial**) 包含 **emp.txt** 文件中的输入数据。

1. 在 **C:\ADFGetStartedPSH** 文件夹中，创建包含以下内容的名为 **EmpBlobTable.json** 的 JSON 文件：

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
             "linkedServiceName": "StorageLinkedService",
             "typeProperties": {
               "fileName": "emp.txt",
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

   注意以下几点：

   * 数据集 **type** 设置为 **AzureBlob**。
   * **linkedServiceName** 设置为 **StorageLinkedService**。
   * **folderPath** 设置为 **adftutorial** 容器。
   * **fileName** 设置为 **emp.txt**。 如果未指定 Blob 的名称，容器中所有 Blob 的数据被视为输入数据。  
   * 格式 **type** 设置为 **TextFormat**。
   * 文本文件中包含两个以逗号字符 (**columnDelimiter**) 分隔的字段：**FirstName** 和 **LastName**。    
   * **availability** 设置为 **hourly**（**frequency** 设置为 **hour**，**interval** 设置为 **1**）。 因此，数据工厂每小时在 Blob 容器 (**adftutorial**) 的根文件夹中查找输入数据。

   如果未指定**输入表**的 **fileName**，则输入文件夹 (**folderPath**) 中的所有文件和 Blob 都被视为输入。 如果在 JSON 中指定 fileName，则只有指定的文件或 Blob 被视为输入。

   如果未指定**输出表**的 **fileName**，**folderPath** 中生成的文件根据以下格式命名：Data.<Guid\>.txt（例如：Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt）。

   若要根据 **SliceStart** 时间动态设置 **folderPath** 和 **fileName**，请使用 **partitionedBy** 属性。 在以下示例中，folderPath 使用 SliceStart（所处理的切片的开始时间）中的年、月和日，fileName 使用 SliceStart 中的小时。 例如，如果切片生成于 2016-10-20T08:00:00，folderName 将设置为 wikidatagateway/wikisampledataout/2016/10/20，fileName 设置为 08.csv。

         "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
         "fileName": "{Hour}.csv",
         "partitionedBy":
         [
             { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
             { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
             { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
             { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
         ],

   有关 JSON 属性的详细信息，请参阅 [JSON 脚本参考](data-factory-data-movement-activities.md)。
2. 运行以下命令创建数据工厂数据集。

    ```  
    New-AzureRmDataFactoryDataset $df -File .\EmpBlobTable.json
    ```

### <a name="create-an-output-dataset"></a>创建输出数据集
本步骤创建名为 **EmpSQLTable**的输出数据集。 此数据集指向 Azure SQL 数据库中 **AzureSqlLinkedService** 所代表的 SQL 表 (**emp**)。 管道将输入 Blob 中的数据复制到 **emp** 表。

1. 在 **C:\ADFGetStartedPSH** 文件夹中，创建包含以下内容的名为 **EmpSQLTable.json** 的 JSON 文件：

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

   注意以下几点：

   * 数据集 **type** 设置为 **AzureSqlTable**。
   * **linkedServiceName** 设置为 **AzureSqlLinkedService**。
   * **tablename** 设置为 **emp**。
   * 数据库中的 emp 表包含三列：**ID**、**FirstName** 和 **LastName**。 ID 是标识列，因此只需在此处指定 **FirstName** 和 **LastName**。
   * **availability** 设置为 **hourly**（**frequency** 设置为 **hour**，**interval** 设置为 **1**）。 数据工厂服务每隔一小时在 Azure SQL 数据库的 **emp** 表中生成输出数据切片。
2. 运行以下命令创建数据工厂数据集。

    ```   
    New-AzureRmDataFactoryDataset $df -File .\EmpSQLTable.json
    ```

## <a name="create-a-pipeline"></a>创建管道
本步骤创建包含**复制活动**的管道。 该管道使用 **EmpTableFromBlob** 作为输入，使用 **EmpSQLTable** 作为输出。

1. 在 **C:\ADFGetStartedPSH** 文件夹中，创建包含以下内容的名为 **ADFTutorialPipeline.json** 的 JSON 文件：

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
                     "type": "SqlSink"
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
             "start": "2016-08-09T00:00:00Z",
             "end": "2016-08-10T00:00:00Z",
             "isPaused": false
           }
         }

   注意以下几点：

   * 在 activities 节中，只有一个活动的 **type** 设置为 **Copy**。
   * 活动的输入设置为 **EmpTableFromBlob**，活动的输出设置为 **EmpSQLTable**。
   * 在 **transformation** 节中，**BlobSource** 指定为源类型，**SqlSink** 指定为接收器类型。

   将 **start** 属性的值替换为当前日期，将 **end** 属性的值替换为下一个日期。 开始和结束日期时间必须采用 [ISO 格式](http://en.wikipedia.org/wiki/ISO_8601)。 例如：2016-10-14T16:32:41Z。 教程使用了**结束**时间 (end)，但该属性是可选的。

   如果未指定 **end** 属性的值，则以“**开始时间 + 48 小时**”计算。 若要无限期运行管道，请指定 **9/9/9999** 作为 **end** 属性的值。

   在示例中，由于每小时生成一个数据切片，因此共有 24 个数据切片。

   有关 JSON 属性的更多详细信息，请参阅 [JSON 脚本参考](data-factory-data-movement-activities.md)。
2. 运行以下命令创建数据工厂表。

    ```   
    New-AzureRmDataFactoryPipeline $df -File .\ADFTutorialPipeline.json
    ```

祝贺你！ 现已成功创建 Azure 数据工厂、链接服务、表和管道。 此外，还计划好了管道。

## <a name="monitor-the-pipeline"></a>监视管道
本步骤使用 Azure PowerShell 监视 Azure 数据工厂的运行情况。

1. 运行 **Get-AzureRmDataFactory** 并将输出分配给变量 $df。

    ```  
    $df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH
    ```

2. 运行 **Get-AzureRmDataFactorySlice**，获取有关 **EmpSQLTable**（管道的输出表）所有切片的详细信息。  

    ```   
    Get-AzureRmDataFactorySlice $df -DatasetName EmpSQLTable -StartDateTime 2016-08-09T00:00:00
    ```

   将 **StartDateTime** 参数的 year、month、date 部分分别替换为当前的年份、月份、日期。 此设置应与管道 JSON 中的 **Start** 值匹配。

   应会看到 24 个切片，从当日 12 AM 到下一日 12 AM，每小时各有一个。

   **示例输出：**

    ```   
     ResourceGroupName : ADFTutorialResourceGroup
     DataFactoryName   : ADFTutorialDataFactoryPSH
     TableName         : EmpSQLTable
     Start             : 8/9/2016 12:00:00 AM
     End               : 8/9/2016 1:00:00 AM
     RetryCount        : 0
     Status            : Waiting
     LatencyStatus     :
     LongRetryCount    : 0
    ```
3. 运行 **Get-AzureRmDataFactoryRun**，获取**特定**切片的活动运行详细信息。 更改 **StartDateTime** 参数的值，使其与输出中切片的 **Start** 时间匹配。 **StartDateTime** 的值必须采用 [ISO 格式](http://en.wikipedia.org/wiki/ISO_8601)。

    ```  
    Get-AzureRmDataFactoryRun $df -DatasetName EmpSQLTable -StartDateTime 2016-08-09T00:00:00
    ```

   应会显示类似于以下示例的输出：

    ```   
     Id                  : 3404c187-c889-4f88-933b-2a2f5cd84e90_635614488000000000_635614524000000000_EmpSQLTable
     ResourceGroupName   : ADFTutorialResourceGroup
     DataFactoryName     : ADFTutorialDataFactoryPSH
     TableName           : EmpSQLTable
     ProcessingStartTime : 8/9/2016 11:03:28 PM
     ProcessingEndTime   : 8/9/2016 11:04:36 PM
     PercentComplete     : 100
     DataSliceStart      : 8/9/2016 10:00:00 PM
     DataSliceEnd        : 8/9/2016 11:00:00 PM
     Status              : Succeeded
     Timestamp           : 8/9/2016 11:03:28 PM
     RetryAttempt        : 0
     Properties          : {}
     ErrorMessage        :
     ActivityName        : CopyFromBlobToSQL
     PipelineName        : ADFTutorialPipeline
     Type                : Copy
    ```

有关数据工厂 cmdlet 的综合文档，请参阅[数据工厂 Cmdlet 参考][cmdlet-reference]。

## <a name="summary"></a>摘要
本教程创建了一个 Azure 数据工厂，用于将数据从 Azure Blob 复制到 Azure SQL 数据库。 已使用 Visual PowerShell 创建数据工厂、链接服务、数据集和管道。 下面是本教程中执行的高级步骤：  

1. 创建了 Azure **数据工厂**。
2. 创建了**链接服务**：

   a. 一个 **Azure 存储**链接服务，用于链接保存输入数据的 Azure 存储帐户。     
   b. 一个 **Azure SQL** 链接服务，用于链接保存输出数据的 SQL 数据库。
3. 创建了 **数据集** ，用于描述管道的输入和输出数据。
4. 创建了包含**复制活动**的**管道**，其中 **BlobSource** 为源，**SqlSink** 为接收器。

## <a name="see-also"></a>另请参阅
| 主题 | 说明 |
|:--- |:--- |
| [Data Factory Cmdlet Reference](/powershell/resourcemanager/azurerm.datafactories/v2.5.0/azurerm.datafactories) | 本部分介绍所有数据工厂 cmdlet |
| [管道](data-factory-create-pipelines.md) |帮助了解 Azure 数据工厂中的管道和活动 |
| [datasets](data-factory-create-datasets.md) |还有助于了解 Azure 数据工厂中的数据集。 |
| [计划和执行](data-factory-scheduling-and-execution.md) |此文介绍 Azure 数据工厂应用程序模型的计划和执行方面。 |

[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234.aspx
[old-cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234(v=azure.98).aspx
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[azure-portal]: http://portal.azure.com
[download-azure-powershell]: ../powershell-install-configure.md
[data-factory-introduction]: data-factory-introduction.md

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-copy-activity-tutorial-using-powershell/getstarted-storage-explorer.png

[sql-management-studio]: ../sql-database/sql-database-manage-azure-ssms.md



<!--HONumber=Feb17_HO1-->


