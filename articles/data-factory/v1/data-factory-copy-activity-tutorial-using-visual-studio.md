---
title: 教程：使用 Visual Studio 创建包含复制活动的管道 | Microsoft 文档
description: 在本教程中，通过使用 Visual Studio 创建带有复制活动的 Azure 数据工厂管道。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 1751185b-ce0a-4ab2-a9c3-e37b4d149ca3
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: c25bd85c362e7055109e0a2a178a417ef110899a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-visual-studio"></a>教程：使用 Visual Studio 创建带有复制活动的管道
> [!div class="op_single_selector"]
> * [概述与先决条件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [复制向导](data-factory-copy-data-wizard-tutorial.md)
> * [Azure 门户](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure 资源管理器模板](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)
> 
> 

> [!NOTE]
> 本文适用于数据工厂版本 1（正式版 (GA)）。 如果使用数据工厂服务版本 2（即预览版），请参阅[版本 2 中的复制活动教程文档](../quickstart-create-data-factory-dot-net.md)。 

本文介绍如何使用 Microsoft Visual Studio 创建数据工厂，以便通过管道将数据从 Azure Blob 存储复制到 Azure SQL 数据库。 如果不熟悉 Azure 数据工厂，请在学习本教程之前，先通读 [Azure 数据工厂简介](data-factory-introduction.md)一文。   

本教程会创建包含一个活动（复制活动）的管道。 复制活动可以将数据从支持的数据存储复制到支持的接收器数据存储。 如需可以用作源和接收器的数据存储的列表，请参阅[支持的数据存储](data-factory-data-movement-activities.md#supported-data-stores-and-formats)。 该活动由全球可用的服务提供支持，能以安全、可靠、可缩放的方式在各种数据存储区间复制数据。 有关复制活动的详细信息，请参阅[数据移动活动](data-factory-data-movement-activities.md)。

一个管道可以有多个活动。 而且，可以通过将一个活动的输出数据集设置为另一个活动的输入数据集，链接两个活动（两个活动先后运行）。 有关详细信息，请参阅[管道中的多个活动](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)。

> [!NOTE] 
> 本教程中的数据管道将数据从源数据存储复制到目标数据存储。 有关如何使用 Azure 数据工厂来转换数据的教程，请参阅[教程：生成使用 Hadoop 群集来转换数据的管道](data-factory-build-your-first-pipeline.md)。

## <a name="prerequisites"></a>先决条件
1. 阅读 [教程概述](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) ，完成 **先决条件** 步骤。       
2. 只有订阅/资源组级别的 [数据工厂参与者](../../active-directory/role-based-access-built-in-roles.md#data-factory-contributor) 角色成员才能创建数据工厂实例。
3. 必须在计算机上安装了以下软件： 
   * Visual Studio 2013 或 Visual Studio 2015
   * 下载用于 Visual Studio 2013 或 Visual Studio 2015 的 Azure SDK。 导航到 [Azure 下载页](https://azure.microsoft.com/downloads/)，在“.NET”部分中单击“VS 2013”或“VS 2015”。
   * 下载用于 Visual Studio 的最新 Azure 数据工厂插件：[VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) 或 [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005)。 还可通过执行以下步骤更新插件：在菜单上，依次单击“工具” -> “扩展和更新” -> “联机” -> “Visual Studio 库” -> “适用于 Visual Studio 的 Microsoft Azure 数据工厂工具” -> “更新”。

## <a name="steps"></a>步骤
下面是本教程中要执行的步骤：

1. 在数据工厂中创建**链接服务**。 本步骤创建两类链接服务：Azure 存储和 Azure SQL 数据库。 
    
    AzureStorageLinkedService 链接将 Azure 存储帐户链接到数据工厂。 根据[先决条件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)创建了一个容器并将数据上传到该存储帐户。   

    AzureSqlLinkedService 将 Azure SQL 数据库链接到数据工厂。 从 Blob 存储复制的数据存储在该数据库中。 根据[先决条件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)在该数据库中创建了一个 SQL 表。     
2. 在数据工厂中创建输入和输出**数据集**。  
    
    Azure 存储链接服务指定一个连接字符串，数据工厂服务在运行时使用该字符串连接到 Azure 存储帐户。 输入 Blob 数据集指定容器以及包含输入数据的文件夹。  

    类似地，Azure SQL 数据库链接服务指定一个连接字符串，数据工厂服务在运行时使用该字符串连接到 Azure SQL 数据库。 输出 SQL 表数据集在数据库中指定一个表，数据将从 Blob 存储复制到该表中。
3. 在数据工厂中创建**管道**。 本步骤创建包含复制活动的管道。   
    
    复制活动将数据从 Azure Blob 存储中的 Blob 复制到 Azure SQL 数据库中的表。 可以通过管道中的复制活动，将数据从任何支持的源复制到任何支持的目标。 有关支持的数据存储的列表，请参阅[数据移动活动](data-factory-data-movement-activities.md#supported-data-stores-and-formats)一文。 
4. 在部署数据工厂实体（链接服务、数据集/表、管道）时创建 Azure **数据工厂**。 

## <a name="create-visual-studio-project"></a>创建 Visual Studio 项目
1. 启动 **Visual Studio 2015**。 单击“文件”，指向“新建”并单击“项目”。 将显示“新建项目”对话框。  
2. 在“新建项目”对话框中，选择“DataFactory”模板，并单击“空数据工厂项目”。  
   
    ![“新建项目”对话框](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-project-dialog.png)
3. 指定项目名称、解决方案位置以及解决方案名称，并单击“确定”。
   
    ![解决方案资源管理器](./media/data-factory-copy-activity-tutorial-using-visual-studio/solution-explorer.png)    

## <a name="create-linked-services"></a>创建链接服务
可在数据工厂中创建链接服务，将数据存储和计算服务链接到数据工厂。 在本教程中，请不要使用任何计算服务，例如 Azure HDInsight 或 Azure Data Lake Analytics。 可以使用两个数据存储，其类型为 Azure 存储（源）和 Azure SQL 数据库（目标）。 

因此，创建两类链接服务：AzureStorage 和 AzureSqlDatabase。  

Azure 存储链接服务将 Azure 存储帐户链接到数据工厂。 已根据[先决条件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)，在此存储帐户中创建了一个容器并上传了数据。   

Azure SQL 链接服务可将 Azure SQL 数据库链接到数据工厂。 从 Blob 存储复制的数据存储在该数据库中。 根据[先决条件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)在该数据库中创建了 emp 表。

链接服务将数据存储区或计算服务链接到 Azure 数据工厂。 有关复制活动支持的所有源和接收器，请参阅[支持的数据存储](data-factory-data-movement-activities.md#supported-data-stores-and-formats)。 有关数据工厂支持的计算服务列表，请参阅[计算链接的服务](data-factory-compute-linked-services.md)。 本教程不使用任何计算服务。 

### <a name="create-the-azure-storage-linked-service"></a>创建 Azure 存储链接服务
1. 在“解决方案资源管理器”中，右键单击“链接服务”，指向“添加”，并单击“新建项”。      
2. 在“添加新项”对话框中，从列表中选择“Azure 存储链接服务”，并单击“添加”。 
   
    ![新建链接服务](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-linked-service-dialog.png)
3. 将 `<accountname>` 和 `<accountkey>`* 替换为 Azure 存储帐户及其密钥。 
   
    ![Azure 存储链接服务](./media/data-factory-copy-activity-tutorial-using-visual-studio/azure-storage-linked-service.png)
4. 保存 **AzureStorageLinkedService1.json** 文件。

    有关链接服务定义中 JSON 属性的详细信息，请参阅 [Azure Blob 存储连接器](data-factory-azure-blob-connector.md#linked-service-properties)一文。

### <a name="create-the-azure-sql-linked-service"></a>创建 Azure SQL 链接服务
1. 在解决方案资源管理器中，再次右键单击“链接服务”节点，指向“添加”，并单击“新建项”。 
2. 这次选择“Azure SQL 链接服务”，并单击“添加”。 
3. 在 **AzureSqlLinkedService1.json 文件**中，将 `<servername>`、`<databasename>`、`<username@servername>` 和 `<password>` 分别替换为 Azure SQL 服务器、数据库、用户帐户和密码。    
4. 保存 **AzureSqlLinkedService1.json** 文件。 
    
    有关这些 JSON 属性的详细信息，请参阅 [Azure SQL 数据库连接器](data-factory-azure-sql-connector.md#linked-service-properties)。


## <a name="create-datasets"></a>创建数据集
在上一步骤中，已创建用于将 Azure 存储帐户和 Azure SQL 数据库链接到数据工厂的链接服务。 本步骤定义两个名为 InputDataset 和 OutputDataset 的数据集，表示存储在数据存储中的输入和输出数据，这些数据存储分别由 AzureStorageLinkedService1 和 AzureSqlLinkedService1 引用。

Azure 存储链接服务指定一个连接字符串，数据工厂服务在运行时使用该字符串连接到 Azure 存储帐户。 输入 Blob 数据集 (InputDataset) 指定容器以及包含输入数据的文件夹。  

类似地，Azure SQL 数据库链接服务指定一个连接字符串，数据工厂服务在运行时使用该字符串连接到 Azure SQL 数据库。 输出 SQL 表数据集 (OututDataset) 在数据库中指定一个表，数据将从 Blob 存储复制到该表中。 

### <a name="create-input-dataset"></a>创建输入数据集
本步骤在 AzureStorageLinkedService1 链接服务代表的 Azure 存储中创建名为 InputDataset 的数据集，该数据集指向 Blob 容器 (adftutorial) 根文件夹中的 Blob 文件 (emp.txt)。 如果不指定 fileName 的值（或者跳过此步骤），则会将输入文件夹中的所有 Blob 复制到目标。 在本教程中，请为 fileName 指定一个值。 

在这里，使用的术语是“表”而不是“数据集”。 表是矩形数据集，是目前唯一受支持的数据集类型。 

1. 在“解决方案资源管理器”中，右键单击“表”，指向“添加”，并单击“新建项”。
2. 在“添加新项”对话框中，选择“Azure Blob”，并单击“添加”。   
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
    下表提供了代码片段中使用的 JSON 属性的描述：

    | 属性 | 说明 |
    |:--- |:--- |
    | type | type 属性设置为 **AzureBlob**，因为数据驻留在 Azure Blob 存储中。 |
    | linkedServiceName | 表示前面创建的 **AzureStorageLinkedService**。 |
    | folderPath | 指定 Blob **容器**以及包含输入 Blob 的**文件夹**。 在本教程中，adftutorial 是 Blob 容器，文件夹是根文件夹。 | 
    | fileName | 此属性是可选的。 如果省略此属性，将选取 folderPath 中的所有文件。 在本教程中，为 fileName 指定了 **emp.txt**，因此仅选取该文件进行处理。 |
    | 格式 -> 类型 |输入文件为文本格式，因此我们使用 **TextFormat**。 |
    | columnDelimiter | 输入文件中的列以**逗号字符 (`,`)** 分隔。 |
    | frequency/interval | frequency 设置为 **Hour**，interval 设置为 **1**，表示**每小时**获取一次输入切片。 换言之，数据工厂服务每小时在指定的 Blob 容器 (**adftutorial**) 的根文件夹中查找输入数据。 它会查找管道开始和结束时间范围内的数据，而不是范围外的数据。  |
    | external | 如果数据不是由该管道生成的，此属性设置为 **true**。 本教程中的输入数据位于 emp.txt 文件中，此文件不是由该管道生成的，因此将此属性设置为 true。 |

    有关这些 JSON 属性的详细信息，请参阅 [Azure Blob 连接器](data-factory-azure-blob-connector.md#dataset-properties)一文。   

### <a name="create-output-dataset"></a>创建输出数据集
本步骤创建名为 **OutputDataset** 的输出数据集。 此数据集指向 Azure SQL 数据库中 **AzureSqlLinkedService1** 所代表的 SQL 表。 

1. 在“解决方案资源管理器”中，再次右键单击“表”，指向“添加”，并单击“新建项”。
2. 在“添加新项”对话框中，选择“Azure SQL”，并单击“添加”。 
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
    下表提供了代码片段中使用的 JSON 属性的描述：

    | 属性 | 说明 |
    |:--- |:--- |
    | type | type 属性设置为 **AzureSqlTable**，因为数据复制到 Azure SQL 数据库中的表。 |
    | linkedServiceName | 表示前面创建的 **AzureSqlLinkedService**。 |
    | tableName | 指定一个表，以便将数据复制到其中。 | 
    | frequency/interval | frequency 设置为 **Hour**，interval 设置为 **1**，表示输出切片在管道开始和结束时间范围内（而不是范围外）**每小时**生成一次。  |

    数据库的 emp 表包含三列 – **ID**、**FirstName** 和 **LastName**。 ID 是标识列，因此只需在此处指定 **FirstName** 和 **LastName**。

    有关这些 JSON 属性的详细信息，请参阅 [Azure SQL 连接器](data-factory-azure-sql-connector.md#dataset-properties)一文。

## <a name="create-pipeline"></a>创建管道
本步骤创建管道，其中包含使用 InputDataset 作为输入和使用 OutputDataset 作为输出的复制活动。

目前，输出数据集驱动计划。 在本教程中，输出数据集配置为每小时生成一个切片。 管道的开始时间和结束时间相差一天，即 24 小时。 因此，管道会生成 24 个输出数据集切片。 

1. 在“解决方案资源管理器”中，右键单击“管道”，指向“添加”，并单击“新建项”。  
2. 在“添加新项”对话框中，选择“复制数据管道”，并单击“添加”。 
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
       "start": "2017-05-11T00:00:00Z",
       "end": "2017-05-12T00:00:00Z",
       "isPaused": false
     }
    }
    ```   
    - 在 activities 节中，只有一个活动的 **type** 设置为 **Copy**。 有关复制活动的详细信息，请参阅[数据移动活动](data-factory-data-movement-activities.md)。 在数据工厂解决方案中，也可以使用[数据转换活动](data-factory-data-transformation-activities.md)。
    - 活动的输入设置为 **InputDataset**，活动的输出设置为 **OutputDataset**。 
    - 在 **typeProperties** 节中，**BlobSource** 指定为源类型，**SqlSink** 指定为接收器类型。 有关复制活动支持的数据存储（以源和接收器的形式存在）的完整列表，请参阅[支持的数据存储](data-factory-data-movement-activities.md#supported-data-stores-and-formats)。 若要了解如何使用特定的受支持的数据存储（以源/接收器的形式存在），请单击表中的链接。  
     
    将 **start** 属性的值替换为当前日期，将 **end** 值替换为下一个日期。 可以仅指定日期部分，跳过日期时间的时间部分。 例如，“2016-02-03”等效于“2016-02-03T00:00:00Z”
     
    开始和结束日期时间必须采用 [ISO 格式](http://en.wikipedia.org/wiki/ISO_8601)。 例如：2016-10-14T16:32:41Z。 **结束** 时间是可选的，但本教程使用该时间。 
     
    如果未指定 **end** 属性的值，则以“**开始时间 + 48 小时**”计算。 若要无限期运行管道，请指定 **9999-09-09** 作为 **end** 属性的值。
     
    在上述示例中，由于每小时生成一个数据切片，因此共有 24 个数据切片。

    有关管道定义中 JSON 属性的说明，请参阅[创建管道](data-factory-create-pipelines.md)一文。 有关复制活动定义中 JSON 属性的说明，请参阅[数据移动活动](data-factory-data-movement-activities.md)一文。 有关 BlobSource 支持的 JSON 属性的说明，请参阅 [Azure Blob 连接器](data-factory-azure-blob-connector.md)一文。 有关 SqlSink 支持的 JSON 属性的说明，请参阅 [Azure SQL 数据库连接器](data-factory-azure-sql-connector.md)一文。

## <a name="publishdeploy-data-factory-entities"></a>发布/部署数据工厂实体
本步骤发布前面所创建的数据工厂实体（链接服务、数据集和管道）。 还可以指定要创建用来保存这些实体的新数据工厂的名称。  

1. 在“解决方案资源管理器”中，右键单击该项目，并单击“发布” 。 
2. 如果显示“登录到 Microsoft 帐户”对话框，请输入拥有 Azure 订阅的帐户凭据，并单击“登录”。
3. 应该会看到以下对话框：
   
   ![“发布”对话框](./media/data-factory-copy-activity-tutorial-using-visual-studio/publish.png)
4. 在“配置数据工厂”页，请按以下步骤操作： 
   
   1. 选择“新建数据工厂”  选项。
   2. 输入 **VSTutorialFactory** 作为**名称**。  
      
      > [!IMPORTANT]
      > Azure 数据工厂的名称必须全局唯一。 如果发布时收到了有关数据工厂名称的错误，请更改数据工厂的名称（例如，yournameVSTutorialFactory），再次尝试发布。 有关数据工厂项目命名规则，请参阅 [Data Factory - Naming Rules](data-factory-naming-rules.md) （数据工厂 - 命名规则）主题。        
      > 
      > 
   3. 在“订阅”字段中选择 Azure 订阅。
      
      > [!IMPORTANT]
      > 如果未看到任何订阅，请确保使用属于订阅管理员或共同管理员的帐户登录。  
      > 
      > 
   4. 为要创建的数据工厂选择 **资源组** 。 
   5. 为数据工厂选择 **区域** 。 下拉列表中只显示数据工厂服务支持的区域。
   6. 单击“下一步”切换到“发布项”页。
      
       ![配置数据工厂页](media/data-factory-copy-activity-tutorial-using-visual-studio/configure-data-factory-page.png)   
5. 在“发布项”页上，确保已选择所有数据工厂实体，并单击“下一步”切换到“摘要”页。
   
   ![发布项页](media/data-factory-copy-activity-tutorial-using-visual-studio/publish-items-page.png)     
6. 查看摘要，单击“下一步”，启动部署过程并查看“部署状态”。
   
   ![发布摘要页](media/data-factory-copy-activity-tutorial-using-visual-studio/publish-summary-page.png)
7. 在“部署状态”  页上，应看到部署过程的状态。 部署完成后，单击“完成”。
 
   ![部署状态页](media/data-factory-copy-activity-tutorial-using-visual-studio/deployment-status.png)

请注意以下几点： 

* 如果收到错误：“该订阅未注册为使用命名空间 Microsoft.DataFactory”，请执行下列操作之一，尝试再次发布： 
  
  * 在 Azure PowerShell 中运行以下命令，注册数据工厂提供程序。 

    ```PowerShell    
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```
    可运行以下命令来确认数据工厂提供程序是否已注册。 
    
    ```PowerShell
    Get-AzureRmResourceProvider
    ```
  * 使用 Azure 订阅登录到 [Azure 门户](https://portal.azure.com) ，并导航到“数据工厂”边栏选项卡，或在 Azure 门户中创建数据工厂。 此操作会自动注册提供程序。
* 数据工厂名称可能在将来被注册为 DNS 名称，因此将变成公开可见。

> [!IMPORTANT]
> 只有 Azure 订阅的管理员/共同管理员可以创建数据工厂实例

## <a name="monitor-pipeline"></a>监视管道
导航到数据工厂的主页：

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在左侧菜单上单击“更多服务”，并单击“数据工厂”。

    ![浏览数据工厂](media/data-factory-copy-activity-tutorial-using-visual-studio/browse-data-factories.png)
3. 开始键入数据工厂的名称。

    ![数据工厂的名称](media/data-factory-copy-activity-tutorial-using-visual-studio/enter-data-factory-name.png) 
4. 单击结果列表中的数据工厂，查看数据工厂的主页。

    ![数据工厂主页](media/data-factory-copy-activity-tutorial-using-visual-studio/data-factory-home-page.png)
5. 按照[监视数据集和管道](data-factory-copy-activity-tutorial-using-azure-portal.md#monitor-pipeline)中的说明，监视在本教程中创建的管道和数据集。 目前，Visual Studio 不支持对数据工厂管道进行监视。 

## <a name="summary"></a>摘要
本教程创建了一个 Azure 数据工厂，用于将数据从 Azure Blob 复制到 Azure SQL 数据库。 使用 Visual Studio 创建数据工厂、链接服务、数据集和管道。 下面是本教程中执行的高级步骤：  

1. 创建了 Azure **数据工厂**。
2. 创建 **链接服务**：
   1. **Azure 存储** 链接服务，链接存放输入数据的 Azure 存储帐户。     
   2. **Azure SQL** 链接服务，链接存放输出数据的 Azure SQL 数据库。 
3. 创建了 **数据集**，用于描述管道的输入和输出数据。
4. 创建了包含**复制活动**的**管道**，其中 **BlobSource** 为源，**SqlSink** 为接收器。 

若要了解如何使用 Azure HDInsight 群集通过 HDInsight Hive 活动来转换数据，请参阅[教程：使用 Hadoop 群集生成第一个转换数据的管道](data-factory-build-your-first-pipeline.md)。

通过将一个活动的输出数据集设置为另一个活动的输入数据集，可链接两个活动（两个活动先后运行）。 有关详细信息，请参阅[数据工厂中的计划和执行情况](data-factory-scheduling-and-execution.md)。 

## <a name="view-all-data-factories-in-server-explorer"></a>在服务器资源管理器中查看所有数据工厂
本部分介绍如何在 Visual Studio 中使用服务器资源管理器，以便查看 Azure 订阅中的所有数据工厂，并根据现有的数据工厂创建 Visual Studio 项目。 

1. 在 **Visual Studio** 中，在菜单上单击“视图”，并单击“服务器资源管理器”。
2. 在“服务器资源管理器”窗口中，依次展开“Azure”和“数据工厂”。 如果看到“登录到 Visual Studio”，请输入与 Azure 订阅关联的**帐户**，并单击“继续”。 输入**密码**，并单击“登录”。 Visual Studio 尝试获取有关订阅中所有 Azure 数据工厂的信息。 可在“数据工厂任务列表”窗口中查看此操作的状态。

    ![服务器资源管理器](./media/data-factory-copy-activity-tutorial-using-visual-studio/server-explorer.png)

## <a name="create-a-visual-studio-project-for-an-existing-data-factory"></a>为现有的数据工厂创建 Visual Studio 项目

- 在服务器资源管理器中右键单击数据工厂，并选择“将数据工厂导出到新项目”，创建基于现有数据工厂的 Visual Studio 项目。

    ![将数据工厂导出为 VS 项目](./media/data-factory-copy-activity-tutorial-using-visual-studio/export-data-factory-menu.png)  

## <a name="update-data-factory-tools-for-visual-studio"></a>更新适用于 Visual Studio 的数据工厂工具
若要更新适用于 Visual Studio 的 Azure 数据工厂工具，请执行以下步骤：

1. 在菜单中单击“工具”，并选择“扩展和更新”。 
2. 在左窗格中选择“更新”，并选择“Visual Studio 库”。
3. 选择“用于 Visual Studio 的 Azure 数据工厂工具”，并单击“更新”。 如果未看到此项，说明已有此工具的最新版本。 

## <a name="use-configuration-files"></a>使用配置文件
可以在 Visual Studio 中使用配置文件，以不同的方式为每个环境配置链接服务/表/管道的属性。

针对 Azure 存储链接服务，请考虑以下 JSON 定义。 根据部署数据工厂实体的环境（开发/测试/生产），为 accountname 和 accountkey 指定具有不同值的 **connectionString** 。 可以针对每个环境使用不同的配置文件来实现此行为。

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "description": "",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

### <a name="add-a-configuration-file"></a>添加配置文件
执行以下步骤，为每个环境添加配置文件：   

1. 在 Visual Studio 解决方案中右键单击数据工厂项目，指向“添加”，并单击“添加项”。
2. 在左侧的已安装模板列表中选择“配置”，选择“配置文件”，输入配置文件的**名称**，并单击“添加”。

    ![添加配置文件](./media/data-factory-build-your-first-pipeline-using-vs/add-config-file.png)
3. 使用以下格式添加配置参数及其值：

    ```json
    {
        "$schema": "http://datafactories.schema.management.azure.com/vsschemas/V1/Microsoft.DataFactory.Config.json",
        "AzureStorageLinkedService1": [
            {
                "name": "$.properties.typeProperties.connectionString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        ],
        "AzureSqlLinkedService1": [
            {
                "name": "$.properties.typeProperties.connectionString",
                "value":  "Server=tcp:<Azure SQL server name>.database.windows.net,1433;Database=<Azure SQL datbase>;User ID=<Username>;Password=<Password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        ]
    }
    ```

    此示例配置 Azure 存储链接服务和 Azure SQL 链接服务的 connectionString 属性。 请注意，指定名称的语法是 [JsonPath](http://goessner.net/articles/JsonPath/)。   

    如果 JSON 具有属性，该属性将包含以下代码所示的值数组：  

    ```json
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
    ```

    配置如以下配置文件中所示的属性（使用从零开始的索引）：

    ```json
    {
        "name": "$.properties.structure[0].name",
        "value": "FirstName"
    }
    {
        "name": "$.properties.structure[0].type",
        "value": "String"
    }
    {
        "name": "$.properties.structure[1].name",
        "value": "LastName"
    }
    {
        "name": "$.properties.structure[1].type",
        "value": "String"
    }
    ```

### <a name="property-names-with-spaces"></a>包含空格的属性名称
如果属性名称包含空格，请按以下示例中所示使用方括号（数据库服务器名称）：

```json
 {
     "name": "$.properties.activities[1].typeProperties.webServiceParameters.['Database server name']",
     "value": "MyAsqlServer.database.windows.net"
 }
```

### <a name="deploy-solution-using-a-configuration"></a>使用配置部署解决方案
在 VS 中发布 Azure 数据工厂实体时，可以指定要用于该发布操作的配置。

使用配置文件在 Azure 数据工厂项目中发布实体：   

1. 右键单击数据工厂项目，并单击“发布”查看“发布项”对话框。
2. 选择现有的数据工厂，或者在“配置数据工厂”页上指定用于创建数据工厂的值，并单击“下一步”。   
3. 在“发布项”页上，可以看到一个下拉列表，其中包含“选择部署配置”字段的可用配置。

    ![选择配置文件](./media/data-factory-build-your-first-pipeline-using-vs/select-config-file.png)
4. 选择要使用的**配置文件**，并单击“下一步”。
5. 确认在“摘要”页上显示了 JSON 文件的名称，并单击“下一步”。
6. 部署操作完成后，请单击“完成”。

如果进行部署，则在将实体部署到 Azure 数据工厂服务之前，请使用配置文件中的值设置 JSON 文件中的属性。   

## <a name="use-azure-key-vault"></a>使用 Azure 密钥保管库
不建议将敏感数据（例如连接字符串）提交到代码存储库，这样做违反安全策略。 请参阅 GitHub 上的 [ADF Secure Publish](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ADFSecurePublish) 示例，了解在发布数据工厂实体时，如何将敏感信息存储在 Azure Key Vault 中并进行使用。 使用适用于 Visual Studio 的 Secure Publish 扩展，可以将机密存储在 Key Vault 中，仅在链接的服务/部署配置中指定这些机密的引用。 向 Azure 发布数据工厂实体时，会对这些引用进行解析。 然后即可将这些文件提交到源存储库，不会公开任何机密。


## <a name="next-steps"></a>后续步骤
在本教程中，在复制操作中使用了 Azure Blob 存储作为源数据存储，使用了 Azure SQL 数据库作为目标数据存储。 下表列出了复制活动支持的充当源和目标的数据存储： 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

若要了解如何通过数据存储复制数据，请单击表中数据存储的链接。