---
title: "教程：创建用于复制数据的 Azure 数据工厂管道（Azure 门户）| Microsoft Docs"
description: "本教程使用 Azure 门户创建带复制活动的 Azure 数据工厂管道，以便将数据从 Azure Blob 存储复制到 Azure SQL 数据库。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: d9317652-0170-4fd3-b9b2-37711272162b
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/01/2017
ms.author: spelluru
robots: noindex
ms.openlocfilehash: 1ff1206296103f1bc4710c857c648b100c37f17e
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/18/2017
---
# <a name="tutorial-use-azure-portal-to-create-a-data-factory-pipeline-to-copy-data"></a>教程：使用 Azure 门户创建用于复制数据的数据工厂管道 
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

> [!NOTE]
> 本文适用于数据工厂版本 1（正式版 (GA)）。 如果使用数据工厂服务版本 2（即预览版），请参阅[版本 2 中的复制活动教程文档](../quickstart-create-data-factory-dot-net.md)。 

本文介绍如何使用 [Azure 门户](https://portal.azure.com)创建数据工厂，以便通过管道将数据从 Azure Blob 存储复制到 Azure SQL 数据库。 如果不熟悉 Azure 数据工厂，请在学习本教程之前，先通读 [Azure 数据工厂简介](data-factory-introduction.md)一文。   

本教程会创建包含一个活动（复制活动）的管道。 复制活动可以将数据从支持的数据存储复制到支持的接收器数据存储。 如需可以用作源和接收器的数据存储的列表，请参阅[支持的数据存储](data-factory-data-movement-activities.md#supported-data-stores-and-formats)。 该活动由全球可用的服务提供支持，能以安全、可靠、可缩放的方式在各种数据存储区间复制数据。 有关复制活动的详细信息，请参阅[数据移动活动](data-factory-data-movement-activities.md)。

一个管道可以有多个活动。 而且，可以通过将一个活动的输出数据集设置为另一个活动的输入数据集，链接两个活动（两个活动先后运行）。 有关详细信息，请参阅[管道中的多个活动](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)。 

> [!NOTE] 
> 本教程中的数据管道将数据从源数据存储复制到目标数据存储。 有关如何使用 Azure 数据工厂来转换数据的教程，请参阅[教程：生成使用 Hadoop 群集来转换数据的管道](data-factory-build-your-first-pipeline.md)。

## <a name="prerequisites"></a>先决条件
执行本教程之前，请完成[教程先决条件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)一文中列出的先决条件步骤。

## <a name="steps"></a>步骤
下面是本教程中要执行的步骤：

1. 创建 Azure **数据工厂**。 本步骤创建名为 ADFTutorialDataFactory 的数据工厂。 
2. 在数据工厂中创建**链接服务**。 本步骤创建两类链接服务：Azure 存储和 Azure SQL 数据库。 
    
    AzureStorageLinkedService 链接将 Azure 存储帐户链接到数据工厂。 根据[先决条件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)创建了一个容器并将数据上传到该存储帐户。   

    AzureSqlLinkedService 将 Azure SQL 数据库链接到数据工厂。 从 Blob 存储复制的数据存储在该数据库中。 根据[先决条件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)在该数据库中创建了一个 SQL 表。   
3. 在数据工厂中创建输入和输出**数据集**。  
    
    Azure 存储链接服务指定一个连接字符串，数据工厂服务在运行时使用该字符串连接到 Azure 存储帐户。 输入 Blob 数据集指定容器以及包含输入数据的文件夹。  

    类似地，Azure SQL 数据库链接服务指定一个连接字符串，数据工厂服务在运行时使用该字符串连接到 Azure SQL 数据库。 输出 SQL 表数据集在数据库中指定一个表，数据将从 Blob 存储复制到该表中。
4. 在数据工厂中创建**管道**。 本步骤创建包含复制活动的管道。   
    
    复制活动将数据从 Azure Blob 存储中的 Blob 复制到 Azure SQL 数据库中的表。 可以通过管道中的复制活动，将数据从任何支持的源复制到任何支持的目标。 有关支持的数据存储的列表，请参阅[数据移动活动](data-factory-data-movement-activities.md#supported-data-stores-and-formats)一文。 
5. 监视管道。 本步骤使用 Azure 门户**监视**输入和输出数据集的切片。 

## <a name="create-data-factory"></a>创建数据工厂
> [!IMPORTANT]
> 完成[本教程的先决条件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)（如果尚未这样做）。   

数据工厂可以包含一个或多个数据管道。 管道可以包含一个或多个活动。 例如，将数据从源复制到目标数据存储的复制活动，以及运行 Hive 脚本来将输入数据转换为产品输出数据的 HDInsight Hive 活动。 首先，在此步骤中创建数据工厂。

1. 登录到 [Azure 门户](https://portal.azure.com/)后，在左侧菜单上单击“新建”，然后单击“数据 + 分析”，再单击“数据工厂”。 
   
   ![新建 -> DataFactory](./media/data-factory-copy-activity-tutorial-using-azure-portal/NewDataFactoryMenu.png)    
2. 在“新建数据工厂”  边栏选项卡中：
   
   1. 输入 **ADFTutorialDataFactory** 作为**名称**。 
      
         ![“新建数据工厂”边栏选项卡](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-new-data-factory.png)
      
       Azure 数据工厂的名称必须 **全局唯一**。 如果收到错误，请更改数据工厂的名称（例如改为 yournameADFTutorialDataFactory），并重新尝试创建。 有关数据工厂项目命名规则，请参阅 [Data Factory - Naming Rules](data-factory-naming-rules.md) （数据工厂 - 命名规则）主题。
      
           Data factory name “ADFTutorialDataFactory” is not available  
      
       ![数据工厂名称不可用](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-data-factory-not-available.png)
   2. 选择要在其中创建数据工厂的 Azure **订阅**。 
   3. 对于**资源组**，请执行以下步骤之一：
      
      - 选择“使用现有资源组”，并从下拉列表选择现有的资源组。 
      - 选择“新建”，并输入资源组的名称。   
         
          本教程中的一些步骤假定使用 **ADFTutorialResourceGroup** 作为资源组名称。 若要了解有关资源组的详细信息，请参阅 [使用资源组管理 Azure 资源](../../azure-resource-manager/resource-group-overview.md)。  
   4. 选择数据工厂的**位置**。 下拉列表中只显示数据工厂服务支持的区域。
   5. 选择“固定到仪表板”。     
   6. 单击“创建” 。
      
      > [!IMPORTANT]
      > 只有订阅/资源组级别的 [数据工厂参与者](../../active-directory/role-based-access-built-in-roles.md#data-factory-contributor) 角色成员才能创建数据工厂实例。
      > 
      > 数据工厂名称可能在将来被注册为 DNS 名称，因此将公开可见。                
      > 
      > 
3. 在仪表板上，会看到状态为“正在部署数据工厂”的以下磁贴。 

    ![“正在部署数据工厂”磁贴](media/data-factory-copy-activity-tutorial-using-azure-portal/deploying-data-factory.png)
4. 完成创建后，会显示图中所示的“数据工厂”边栏选项卡。
   
   ![数据工厂主页](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-data-factory-home-page.png)

## <a name="create-linked-services"></a>创建链接服务
可在数据工厂中创建链接服务，将数据存储和计算服务链接到数据工厂。 在本教程中，请不要使用任何计算服务，例如 Azure HDInsight 或 Azure Data Lake Analytics。 可以使用两个数据存储，其类型为 Azure 存储（源）和 Azure SQL 数据库（目标）。 

因此，请创建两个名为 AzureStorageLinkedService 和 AzureSqlLinkedService 的链接服务，其类型为 AzureStorage 和 AzureSqlDatabase。  

AzureStorageLinkedService 链接将 Azure 存储帐户链接到数据工厂。 已根据[先决条件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)，在此存储帐户中创建了一个容器并上传了数据。   

AzureSqlLinkedService 将 Azure SQL 数据库链接到数据工厂。 从 Blob 存储复制的数据存储在该数据库中。 根据[先决条件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)在该数据库中创建了 emp 表。  

### <a name="create-azure-storage-linked-service"></a>创建 Azure 存储链接服务
本步骤将 Azure 存储帐户链接到数据工厂。 在本部分中指定 Azure 存储帐户的名称和密钥。  

1. 在“数据工厂”边栏选项卡中，单击“编写和部署”磁贴。
   
   ![“编写和部署”磁贴](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-author-deploy-tile.png) 
2. 此时会看到“数据工厂编辑器”，如下图所示： 

    ![数据工厂编辑器](./media/data-factory-copy-activity-tutorial-using-azure-portal/data-factory-editor.png)
3. 在“编辑器”中，单击工具栏上的“新建数据存储”按钮，并从下拉菜单中选择“Azure 存储”。 在右窗格中，应会看到用于创建 Azure 存储链接服务的 JSON 模板。 
   
    ![编辑器中的“新建数据存储”按钮](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-newdatastore-button.png)    
3. 将 `<accountname>` 和 `<accountkey>` 替换为 Azure 存储帐户的帐户名与帐户密钥值。 
   
    ![编辑器中的 Blob 存储 JSON](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-blob-storage-json.png)    
4. 单击工具栏上的“部署”。 现在，树视图中应会显示已部署的 **AzureStorageLinkedService**。 
   
    ![编辑器中的 Blob 存储部署](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-blob-storage-deploy.png)

    有关链接服务定义中 JSON 属性的详细信息，请参阅 [Azure Blob 存储连接器](data-factory-azure-blob-connector.md#linked-service-properties)一文。

### <a name="create-a-linked-service-for-the-azure-sql-database"></a>为 Azure SQL 数据库创建链接服务
在此步骤中，将 Azure SQL 数据库链接到数据工厂。 在本部分中指定 Azure SQL 服务器名称、数据库名称、用户名和用户密码。 

1. 在“数据工厂编辑器”中，单击工具栏上的“新建数据存储”按钮，并从下拉菜单中选择“Azure SQL 数据库”。 在右窗格中，应会看到用于创建 Azure SQL 链接服务的 JSON 模板。
2. 将 `<servername>`、`<databasename>`、`<username>@<servername>` 和 `<password>` 分别替换为 Azure SQL 服务器名称、数据库名称、用户帐户名和密码。 
3. 单击工具栏上的“部署”，创建并部署 **AzureSqlLinkedService**。
4. 确认在树视图的“链接服务”下显示 **AzureSqlLinkedService**。  

    有关这些 JSON 属性的详细信息，请参阅 [Azure SQL 数据库连接器](data-factory-azure-sql-connector.md#linked-service-properties)。

## <a name="create-datasets"></a>创建数据集
在上一步骤中，已创建用于将 Azure 存储帐户和 Azure SQL 数据库链接到数据工厂的链接服务。 本步骤定义两个名为 InputDataset 和 OutputDataset 的数据集，表示存储在数据存储中的输入和输出数据，这些数据存储分别由 AzureStorageLinkedService 和 AzureSqlLinkedService 引用。

Azure 存储链接服务指定一个连接字符串，数据工厂服务在运行时使用该字符串连接到 Azure 存储帐户。 输入 Blob 数据集 (InputDataset) 指定容器以及包含输入数据的文件夹。  

类似地，Azure SQL 数据库链接服务指定一个连接字符串，数据工厂服务在运行时使用该字符串连接到 Azure SQL 数据库。 输出 SQL 表数据集 (OututDataset) 在数据库中指定一个表，数据将从 Blob 存储复制到该表中。 

### <a name="create-input-dataset"></a>创建输入数据集
本步骤在 AzureStorageLinkedService 链接服务代表的 Azure 存储中创建名为 InputDataset 的数据集，该数据集指向 Blob 容器 (adftutorial) 根文件夹中的 Blob 文件 (emp.txt)。 如果不指定 fileName 的值（或者跳过此步骤），则会将输入文件夹中的所有 Blob 复制到目标。 在本教程中，请为 fileName 指定一个值。 

1. 在数据工厂的“编辑器”中，单击命令栏上的“......更多”、“新建数据集”、“Azure Blob 存储”。 
   
    ![“新建数据集”菜单](./media/data-factory-copy-activity-tutorial-using-azure-portal/new-dataset-menu.png)
2. 将右窗格中的 JSON 替换为以下 JSON 代码片段： 
   
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
3. 单击工具栏上的“部署”，创建并部署 **InputDataset** 数据集。 确认树视图中显示了 **InputDataset**。

### <a name="create-output-dataset"></a>创建输出数据集
Azure SQL 数据库链接服务指定一个连接字符串，数据工厂服务在运行时使用该字符串连接到 Azure SQL 数据库。 在此步骤中创建的输出 SQL 表数据集 (OututDataset) 在数据库中指定一个表，数据将从 Blob 存储复制到该表中。

1. 在数据工厂的“编辑器”中，依次单击下拉菜单中的“......更多”、“新建数据集”、“Azure SQL”。 
2. 将右窗格中的 JSON 替换为以下 JSON 代码片段：

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
    ```     

    下表提供了代码片段中使用的 JSON 属性的描述：

    | 属性 | 说明 |
    |:--- |:--- |
    | type | type 属性设置为 **AzureSqlTable**，因为数据复制到 Azure SQL 数据库中的表。 |
    | linkedServiceName | 表示前面创建的 **AzureSqlLinkedService**。 |
    | tableName | 指定一个**表**，以便将数据复制到其中。 | 
    | frequency/interval | frequency 设置为 **Hour**，interval 设置为 **1**，表示输出切片在管道开始和结束时间范围内（而不是范围外）**每小时**生成一次。  |

    数据库的 emp 表包含三列 – **ID**、**FirstName** 和 **LastName**。 ID 是标识列，因此只需在此处指定 **FirstName** 和 **LastName**。

    有关这些 JSON 属性的详细信息，请参阅 [Azure SQL 连接器](data-factory-azure-sql-connector.md#dataset-properties)一文。
3. 单击工具栏上的“部署”，创建并部署 **OutputDataset** 数据集。 确认在树视图中的“数据集”下显示了 **OutputDataset**。 

## <a name="create-pipeline"></a>创建管道
本步骤创建管道，其中包含使用 **InputDataset** 作为输入和使用 **OutputDataset** 作为输出的**复制活动**。

目前，输出数据集驱动计划。 在本教程中，输出数据集配置为每小时生成一个切片。 管道的开始时间和结束时间相差一天，即 24 小时。 因此，管道会生成 24 个输出数据集切片。 

1. 在数据工厂的“编辑器”中，单击命令栏上的“...更多”，并单击“新建管道”。 或者，也可以在树视图中，右键单击“管道”，并单击“新建管道”。
2. 将右窗格中的 JSON 替换为以下 JSON 代码片段： 

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
              "retry": 0,
              "timeout": "01:00:00"
            }
          }
        ],
        "start": "2017-05-11T00:00:00Z",
        "end": "2017-05-12T00:00:00Z"
      }
    } 
    ```   
    
    注意以下几点：
   
    - 在 activities 节中，只有一个活动的 **type** 设置为 **Copy**。 有关复制活动的详细信息，请参阅[数据移动活动](data-factory-data-movement-activities.md)。 在数据工厂解决方案中，也可以使用[数据转换活动](data-factory-data-transformation-activities.md)。
    - 活动的输入设置为 **InputDataset**，活动的输出设置为 **OutputDataset**。 
    - 在 **typeProperties** 节中，**BlobSource** 指定为源类型，**SqlSink** 指定为接收器类型。 有关复制活动支持的数据存储（以源和接收器的形式存在）的完整列表，请参阅[支持的数据存储](data-factory-data-movement-activities.md#supported-data-stores-and-formats)。 若要了解如何使用特定的受支持的数据存储（以源/接收器的形式存在），请单击表中的链接。
    - 开始和结束日期时间必须采用 [ISO 格式](http://en.wikipedia.org/wiki/ISO_8601)。 例如：2016-10-14T16:32:41Z。 **结束** 时间是可选的，但本教程使用该时间。 如果未指定 **end** 属性的值，则以“**开始时间 + 48 小时**”计算。 若要无限期运行管道，请指定 **9999-09-09** 作为 **end** 属性的值。
     
    在上述示例中，由于每小时生成一个数据切片，因此共有 24 个数据切片。

    有关管道定义中 JSON 属性的说明，请参阅[创建管道](data-factory-create-pipelines.md)一文。 有关复制活动定义中 JSON 属性的说明，请参阅[数据移动活动](data-factory-data-movement-activities.md)一文。 有关 BlobSource 支持的 JSON 属性的说明，请参阅 [Azure Blob 连接器](data-factory-azure-blob-connector.md)一文。 有关 SqlSink 支持的 JSON 属性的说明，请参阅 [Azure SQL 数据库连接器](data-factory-azure-sql-connector.md)一文。
3. 单击工具栏上的“部署”，创建并部署 **ADFTutorialPipeline**。 确认树视图中显示了该管道。 
4. 现在，请单击“X”关闭“编辑器”边栏选项卡。再次单击“X”查看 **ADFTutorialDataFactory** 的“数据工厂”主页。

**祝贺你！** 现已成功创建 Azure 数据工厂，可以使用管道将数据从 Azure Blob 存储复制到 Azure SQL 数据库了。 


## <a name="monitor-pipeline"></a>监视管道
本步骤使用 Azure 门户监视 Azure 数据工厂的运行情形。    

### <a name="monitor-pipeline-using-monitor--manage-app"></a>使用“监视和管理”应用来监视管道
以下步骤演示了如何通过“监视和管理”应用程序监视数据工厂中的管道： 

1. 在数据工厂的主页上单击“监视和管理”磁贴。
   
    ![“监视和管理”磁贴](./media/data-factory-copy-activity-tutorial-using-azure-portal/monitor-manage-tile.png) 
2. 应该会在单独的选项卡中看到“监视和管理”应用程序。 

    > [!NOTE]
    > 如果 Web 浏览器停滞在“正在授权...”处，请执行以下操作之一：清除“阻止第三方 Cookie 和站点数据”复选框，或为 **login.microsoftonline.com** 创建一个例外，并尝试再次打开该应用。

    ![“监视和管理”应用](./media/data-factory-copy-activity-tutorial-using-azure-portal/monitor-and-manage-app.png)
3. 更改“开始时间”和“结束时间”以包含管道的开始时间 (2017-05-11) 和结束时间 (2017-05-12)，并单击“应用”。       
3. 此时会在中间窗格的列表中显示**活动窗口**，这些窗口与管道开始和结束时间范围内的每一小时相关联。 
4. 若要查看某个活动窗口的详细信息，请在“活动窗口”列表中选择该活动窗口。 
    ![活动窗口详细信息](./media/data-factory-copy-activity-tutorial-using-azure-portal/activity-window-details.png)

    在右侧的“活动窗口资源管理器”中，会看到到当前的 UTC 时间（晚上 8:12）为止的切片都已处理（以绿色表示）。 晚上 8-9 点、9 - 10 点、10 - 11 点、11 - 12 点的切片尚未处理。

    右窗格中的“尝试”部分提供了针对数据切片运行的活动的相关信息。 如果有错误，则会提供该错误的详细信息。 例如，如果输入文件夹或容器不存在，导致切片处理失败，则会显示一条错误消息，指出容器或文件夹不存在。

    ![活动运行尝试](./media/data-factory-copy-activity-tutorial-using-azure-portal/activity-run-attempts.png) 
4. 启动 **SQL Server Management Studio**，连接到 Azure SQL 数据库，并确认行是否已插入数据库的 **emp** 表中。
    
    ![SQL 查询结果](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-sql-query-results.png)

有关使用此应用程序的详细信息，请参阅 [Monitor and manage Azure Data Factory pipelines using Monitoring and Management App](data-factory-monitor-manage-app.md)（使用监视和管理应用程序来监视和管理 Azure 数据工厂管道）。

### <a name="monitor-pipeline-using-diagram-view"></a>使用图示视图监视管道
也可使用图示视图监视数据管道。  

1. 在“数据工厂”边栏选项卡中，单击“图示”。
   
    ![数据工厂边栏选项卡 - 图示磁贴](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-datafactoryblade-diagramtile.png)
2. 应会看到如下所示的图形： 
   
    ![图示视图](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-diagram-blade.png)  
5. 在图示视图中，双击“InputDataset”查看数据集的切片。  
   
    ![已选择 InputDataset 的数据集](./media/data-factory-copy-activity-tutorial-using-azure-portal/DataSetsWithInputDatasetFromBlobSelected.png)   
5. 单击“查看更多”链接，查看所有数据切片。 此时会看到管道开始和结束时间范围内的 24 个小时切片。 
   
    ![所有输入数据切片](./media/data-factory-copy-activity-tutorial-using-azure-portal/all-input-slices.png)  
   
    请注意，到当前的 UTC 时间为止的所有数据切片已“就绪”，因为 **emp.txt** 文件始终在 Blob 容器中：**adftutorial\input**。 将来时间的切片尚未处于就绪状态。 确认底部的“最近失败的切片”部分中没有任何切片。
6. 关闭多个边栏选项卡，直至看到图示视图；或者向左滚动，直至看到图示视图。 然后，双击“OutputDataset”。 
8. 单击 **OutputDataset** 的“表”边栏选项卡上的“查看更多”链接，查看所有切片。

    ![数据切片边栏选项卡](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-dataslices-blade.png) 
9. 请注意，到当前的 UTC 时间为止的所有切片会从“待执行”状态转为“正在进行” ==> “就绪”状态。 过去（当前时间之前）的切片默认按从新到旧的顺序处理。 例如，如果当前时间为晚上 8:12 (UTC)，则会先处理晚上 7 点 - 8 点的切片，然后处理晚上 6 点 - 7 点的切片。 晚上 8 点 - 9 点的切片默认在时间间隔的结束时间过后（即晚上 9 点以后）处理。  
10. 在列表中单击任一数据切片可以看到“数据切片”边栏选项卡。 与活动窗口关联的一片数据称为切片。 切片可以是一个或多个文件。  
    
     ![数据切片边栏选项卡](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-dataslice-blade.png)
    
     如果切片状态不是“就绪”，可以在“未就绪的上游切片”列表中看到未就绪且阻碍当前切片运行的上游切片。
11. 在“数据切片”边栏选项卡的底部列表中，应会看到所有活动都在运行。 单击“活动运行”查看“活动运行详细信息”边栏选项卡。 
    
    ![活动运行详细信息](./media/data-factory-copy-activity-tutorial-using-azure-portal/ActivityRunDetails.png)

    在此边栏选项卡中，可以看到复制操作耗用的时间、具体的吞吐量、读取和写入数据的字节数、运行开始时间、运行结束时间等。  
12. 单击“X”关闭所有边栏选项卡，直到返回 **ADFTutorialDataFactory** 的主页边栏选项卡。
13. （可选）单击“数据集”磁贴或“管道”磁贴即可显示在前述步骤中见过的边栏选项卡。 
14. 启动 **SQL Server Management Studio**，连接到 Azure SQL 数据库，并确认行是否已插入数据库的 **emp** 表中。
    
    ![SQL 查询结果](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-sql-query-results.png)


## <a name="summary"></a>摘要
本教程创建了一个 Azure 数据工厂，用于将数据从 Azure Blob 复制到 Azure SQL 数据库。 已使用 Azure 门户创建了数据工厂、链接服务、数据集和管道。 下面是本教程中执行的高级步骤：  

1. 创建了 Azure **数据工厂**。
2. 创建 **链接服务**：
   1. **Azure 存储** 链接服务，链接存放输入数据的 Azure 存储帐户。     
   2. 一个 **Azure SQL** 链接服务，用于链接保存输出数据的 Azure SQL 数据库。 
3. 创建了 **数据集** ，用于描述管道的输入和输出数据。
4. 创建了包含**复制活动**的**管道**，其中 **BlobSource** 为源，**SqlSink** 为接收器。  

## <a name="next-steps"></a>后续步骤
在本教程中，在复制操作中使用了 Azure Blob 存储作为源数据存储，使用了 Azure SQL 数据库作为目标数据存储。 下表列出了复制活动支持的充当源和目标的数据存储： 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

若要了解如何通过数据存储复制数据，请单击表中数据存储的链接。