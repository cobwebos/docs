---
title: 教程：使用 REST API 创建包含复制活动的管道 | Microsoft Docs
description: 本教程使用 REST API 创建包含复制活动的 Azure 数据工厂管道。
services: data-factory
documentationcenter: ''
author: spelluru
manager: jhubbard
editor: monicar

ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/16/2016
ms.author: spelluru

---
# 教程：使用 REST API 创建包含复制活动的管道
> [!div class="op_single_selector"]
> * [概述与先决条件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Azure 门户](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)
> * [复制向导](data-factory-copy-data-wizard-tutorial.md)
> 
> 

本教程演示如何使用 REST API 创建和监视 Azure 数据工厂。数据工厂中的管道使用复制活动将数据从 Azure Blob 存储复制到 SQL 数据库。

> [!NOTE]
> 本文不会介绍所有数据工厂 REST API。有关数据工厂 cmdlet 的综合文档，请参阅 [Data Factory REST API Reference](https://msdn.microsoft.com/library/azure/dn906738.aspx)（数据工厂 REST API 参考）。
> 
> 

## 先决条件
* 通读[教程概述](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)，完成**先决条件**步骤。
* 在计算机上安装 [Curl](https://curl.haxx.se/dlwiz/)。配合使用 Curl 工具与 REST 命令来创建数据工厂。
* 遵循[此文](../resource-group-create-service-principal-portal.md)的说明：
  1. 在 Azure Active Directory 中创建名为 **ADFCopyTutorialApp** 的 Web 应用程序。
  2. 获取**客户端 ID** 和**机密密钥**。
  3. 获取**租户 ID**。
  4. 将 **ADFCopyTutorialApp** 应用程序分配给**数据工厂参与者**角色。
* 安装 [Azure PowerShell](../powershell-install-configure.md)。
* 启动 **PowerShell** 并运行以下命令。在本教程结束之前，请将 Azure PowerShell 保持打开状态。如果将它关闭再重新打开，则需要再次运行下述命令。
  
  1. 运行以下命令并输入用于登录 Azure 门户的用户名和密码。
     
          Login-AzureRmAccount   
  2. 运行以下命令查看此帐户的所有订阅。
     
          Get-AzureRmSubscription 
  3. 运行以下命令选择要使用的订阅。将 **&lt;NameOfAzureSubscription**&gt; 替换为 Azure 订阅的名称。
     
          Get-AzureRmSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzureRmContext
  4. 在 PowerShell 中运行以下命令，创建名为 **ADFTutorialResourceGroup** 的 Azure 资源组。
     
          New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
     
      如果资源组已存在，请指定是要更新它 (Y) 还是保留原样 (N)。
     
      本教程中的某些步骤假设使用名为 ADFTutorialResourceGroup 的资源组。如果使用不同的资源组，需使用该资源组的名称取代本教程中的 ADFTutorialResourceGroup。

## 创建 JSON 定义
在 curl.exe 所在的文件夹中创建以下 JSON 文件。

### datafactory.json
> [!IMPORTANT]
> 名称必须全局唯一，因此建议使用 ADFCopyTutorialDF 作为前缀/后缀，使其成为唯一名称。
> 
> 

    {  
        "name": "ADFCopyTutorialDF",  
        "location": "WestUS"
    }  

### azurestoragelinkedservice.json
> [!IMPORTANT]
> 将 **accountname** 和 **accountkey** 分别替换为 Azure 存储帐户的名称和密钥。若要了解如何获取存储访问密钥，请参阅 [View, copy and regenerate storage access keys](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)（查看、复制和重新生成存储访问密钥）。
> 
> 

    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        }
    }

### azuersqllinkedservice.json
> [!IMPORTANT]
> 将 **servername**、**databasename**、**username** 和 **password** 分别替换为 Azure SQL 服务器名称、SQL 数据库名称、用户帐户和帐户密码。
> 
> 

    {
        "name": "AzureSqlLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "description": "",
            "typeProperties": {
                "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30"
            }
        }
    }


### inputdataset.json
    {
      "name": "AzureBlobInput",
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

JSON 定义用于定义名为 **AzureBlobInput** 的数据集，表示管道中活动的输入数据。此外，它还指定要将输入数据放在 Blob 容器 **adftutorial** 中的 **emp.txt** 文件内。

 请注意以下几点：

* 数据集 **type** 设置为 **AzureBlob**。
* **linkedServiceName** 设置为 **AzureStorageLinkedService**。
* **folderPath** 设置为 **adftutorial** 容器和 **fileName** 设置为 **emp.txt**。
* 格式 **type** 设置为 **TextFormat**
* 文本文件中有两个以逗号分隔 (**columnDelimiter**) 的字段 - **FirstName** 和 **LastName**
* **availability** 设置为 **hourly**（frequency 设置为 hour，interval 设置为 1）。因此，数据工厂每小时在指定的 Blob 容器 (**adftutorial**) 的根文件夹中查找输入数据。

如果没有指定输入数据集的 **fileName**，则输入文件夹 (**folderPath**) 中的所有文件/Blob 都被视为输入。如果在 JSON 中指定 fileName，则只有指定的文件/Blob 被视为输入。

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


### outputdataset.json
    {
      "name": "AzureSqlOutput",
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


JSON 定义用于定义名为 **AzureSqlOutput** 的数据集，表示管道中活动的输出数据。此外，它还指定要将结果存放在 AzureSqlLinkedService 代表的数据库的 **emp** 表中。**availability** 节指定每小时生成输出数据集一次（frequency：hour；interval：1）。

请注意以下几点：

* 数据集 **type** 设置为 **AzureSQLTable**。
* **linkedServiceName** 设置为 **AzureSqlLinkedService**。
* **tablename** 设置为 **emp**。
* 数据库的 emp 表包含三列 – **ID**、**FirstName** 和 **LastName**。ID 是标识列，因此只需在此处指定 **FirstName** 和 **LastName**。
* **availability** 设置为 **hourly**（**frequency** 设置为 **hour**，**interval** 设置为 **1**）。数据工厂服务每隔一小时在 Azure SQL 数据库的 **emp** 表中生成输出数据切片。

### pipeline.json
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
                "name": "AzureBlobInput"
              }
            ],
            "outputs": [
              {
                "name": "AzureSqlOutput"
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
        "start": "2016-08-12T00:00:00Z",
        "end": "2016-08-13T00:00:00Z"
      }
    }


请注意以下几点：

* 在 activities 节中，只有一个活动的 **type** 设置为 **CopyActivity**。
* 活动的输入设置为 **AzureBlobInput**，活动的输出设置为 **AzureSqlOutput**。
* 在 **transformation** 节中，**BlobSource** 指定为源类型，**SqlSink** 指定为接收器类型。

将 **start** 属性的值替换为当前日期，将 **end** 值替换为下一个日期。可以仅指定日期部分，跳过日期时间的时间部分。例如，“2015-02-03”等效于“2015-02-03T00:00:00Z”

开始和结束日期时间必须采用 [ISO 格式](http://en.wikipedia.org/wiki/ISO_8601)。例如：2014-10-14T16:32:41Z。**结束**时间是可选的，但本教程使用该时间。

如果未指定 **end** 属性的值，则以“**开始时间 + 48 小时**”计算。若要无限期运行管道，请指定 **9999-09-09** 作为 **end** 属性的值。

在示例中，由于每小时生成一个数据切片，因此共有 24 个数据切片。

> [!NOTE]
> 有关上述示例中使用的 JSON 属性的详细信息，请参阅 [Anatomy of a Pipeline](data-factory-create-pipelines.md#anatomy-of-a-pipeline)（管道剖析）。
> 
> 

## 设置全局变量
在 Azure PowerShell 中将值替换为自己的值后，请执行以下命令：

> [!IMPORTANT]
> 有关获取客户端 ID、客户端机密、租户 ID 和订阅 ID 的说明，请参阅[先决条件](#prerequisites)部分。
> 
> 

    $client_id = "<client ID of application in AAD>"
    $client_secret = "<client key of application in AAD>"
    $tenant = "<Azure tenant ID>";
    $subscription_id="<Azure subscription ID>";

    $rg = "ADFTutorialResourceGroup"
    $adf = "ADFCopyTutorialDF"

## 在 AAD 中进行身份验证
运行以下命令，在 Azure Active Directory (AAD) 中进行身份验证。

    $cmd = { .\curl.exe -X POST https://login.microsoftonline.com/$tenant/oauth2/token  -F grant_type=client_credentials  -F resource=https://management.core.windows.net/ -F client_id=$client_id -F client_secret=$client_secret };
    $responseToken = Invoke-Command -scriptblock $cmd;
    $accessToken = (ConvertFrom-Json $responseToken).access_token;

    (ConvertFrom-Json $responseToken) 

## 创建数据工厂
本步骤创建名为 **ADFCopyTutorialDF** 的 Azure 数据工厂。数据工厂可以包含一个或多个数据管道。管道可以包含一个或多个活动。例如，复制活动可将数据从源复制到目标数据存储。HDInsight Hive 活动运行 Hive 脚本转换输入数据，以生成输出数据。运行以下命令创建数据工厂：

1. 将命令分配到名为 **cmd** 的变量。
   
    确认此处指定的名称 (ADFCopyTutorialDF) 与 **datafactory.json** 中指定的名称匹配。
   
        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@datafactory.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/ADFCopyTutorialDF?api-version=2015-10-01};
2. 使用 **Invoke-Command** 运行命令。
   
        $results = Invoke-Command -scriptblock $cmd;
3. 查看结果。如果已成功创建数据工厂，**结果**中会显示数据工厂的 JSON；否则，会显示错误消息。
   
        Write-Host $results

请注意以下几点：

* Azure 数据工厂的名称必须全局唯一。如果在结果中看到错误：“数据工厂名称 ‘ADFCopyTutorialDF’ 不可用”，请执行以下步骤：
  
  1. 在 **datafactory.json** 文件中更改名称（例如，yournameADFCopyTutorialDF）。
  2. 在分配 **$cmd** 变量值的第一个命令中，将 ADFCopyTutorialDF 替换为新名称，然后运行该命令。
  3. 运行以下两个命令来调用 REST API，创建数据工厂并列显操作结果。
     
     有关数据工厂项目命名规则，请参阅 [Data Factory - Naming Rules](data-factory-naming-rules.md)（数据工厂 - 命名规则）主题。
* 只有 Azure 订阅的参与者/管理员才可以创建数据工厂实例
* 数据工厂名称可能在将来被注册为 DNS 名称，因此将公开可见。
* 如果收到错误：“该订阅未注册为使用命名空间 Microsoft.DataFactory”，请执行下列操作之一，尝试再次发布：
  
  * 在 Azure PowerShell 中运行以下命令，注册数据工厂提供程序。
    
          Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
    
      可运行以下命令来确认数据工厂提供程序是否已注册。
    
          Get-AzureRmResourceProvider
  * 使用 Azure 订阅登录到 [Azure 门户](https://portal.azure.com)，然后导航到“数据工厂”边栏选项卡，或在 Azure 门户中创建数据工厂。此操作将自动注册提供程序。

在创建管道之前，需要创建一些数据工厂项。先创建链接服务，将源和目标数据存储链接到数据存储。然后，定义输入和输出数据集，表示链接数据存储中的数据。最后创建管道，其中包含使用这些数据集的活动。

## 创建链接服务
链接服务将数据存储或计算服务链接到 Azure 数据工厂。数据存储可以是 Azure 存储、Azure SQL 数据库，或者是包含数据工厂管道的输入数据或存储其输出数据的本地 SQL Server 数据库。计算服务是处理输入数据并生成输出数据的服务。

本步骤创建两个链接服务：**AzureStorageLinkedService** 和 **AzureSqlLinkedService**。AzureStorageLinkedService 和 AzureSqlLinkedService 链接服务分别将 Azure 存储帐户和 Azure SQL 数据库链接到数据工厂：**ADFCopyTutorialDF**。稍后要在本教程中创建管道，将数据从 AzuretStorageLinkedService 中的 Blob 容器复制到 AzureSqlLinkedService 中的 SQL 表。

### 创建 Azure 存储链接服务
在此步骤中，将 Azure 存储帐户链接到数据工厂。本教程使用 Azure 存储帐户来存储输入数据。

1. 将命令分配到名为 **cmd** 的变量。
   
        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@azurestoragelinkedservice.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureStorageLinkedService?api-version=2015-10-01};
2. 使用 **Invoke-Command** 运行命令。
   
        $results = Invoke-Command -scriptblock $cmd;
3. 查看结果。如果已成功创建链接服务，**结果**中会显示该链接服务的 JSON；否则，会显示错误消息。
   
        Write-Host $results

### 创建 Azure SQL 链接服务
在此步骤中，将 Azure SQL 数据库链接到数据工厂。本教程使用相同的 Azure SQL 数据库来存储输出数据。

1. 将命令分配到名为 **cmd** 的变量。
   
        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@azuresqllinkedservice.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureSqlLinkedService?api-version=2015-10-01};
2. 使用 **Invoke-Command** 运行命令。
   
        $results = Invoke-Command -scriptblock $cmd;
3. 查看结果。如果已成功创建链接服务，**结果**中会显示该链接服务的 JSON；否则，会显示错误消息。
   
        Write-Host $results

## 创建数据集
上一步骤已创建 **AzureStorageLinkedService** 和 **AzureSqlLinkedService** 链接服务，用于将 Azure 存储帐户和 Azure SQL 数据库链接到数据工厂：**ADFCopyTutorialDF**。此步骤将创建数据集，这些数据集在下一步骤所创建的管道中代表复制活动的输入和输出数据。

本教程中的输入数据集引用 Azure 存储中 AzureStorageLinkedService 指向的 Blob 容器。输出数据集引用 Azure SQL 数据库中 AzureSqlLinkedService 指向的 SQL 表。

### 准备教程所需的 Azure Blob 存储和 Azure SQL 数据库
执行以下步骤来准备本教程所需的 Azure Blob 存储和 Azure SQL 数据库。

* 在 **AzureStorageLinkedService** 指向的 Azure Blob 存储中，创建名为 **adftutorial** 的 Blob 容器。
* 创建文本文件 **emp.txt**，并将其作为 Blob 上载到 **adftutorial** 容器。
* 在 **AzureSqlLinkedService** 指向的 Azure SQL 数据库中，创建名为 **emp** 的表。

1. 启动记事本，粘贴以下文本，将文件命名为 **emp.txt**，然后将其保存到硬盘上的 **C:\\ADFGetStartedPSH** 文件夹。
   
        John, Doe
        Jane, Doe
2. 使用 [Azure 存储资源管理器](https://azurestorageexplorer.codeplex.com/)等工具创建 **adftutorial** 容器，将 **emp.txt** 文件上载到该容器。
   
    ![Azure 存储空间资源管理器](media/data-factory-copy-activity-tutorial-using-powershell/getstarted-storage-explorer.png)
3. 使用以下 SQL 脚本在 Azure SQL 数据库中创建 **emp** 表。

        CREATE TABLE dbo.emp 
        (
            ID int IDENTITY(1,1) NOT NULL,
            FirstName varchar(50),
            LastName varchar(50),
        )
        GO

        CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID); 

    如果计算机上已安装 SQL Server 2014：请遵循 [Step 2: Connect to SQL Database of the Managing Azure SQL Database using SQL Server Management Studio][sql-management-studio]（步骤 2：使用 SQL Server Management Studio 连接到管理 Azure SQL 数据库的 SQL 数据库）一文中的说明连接到 Azure SQL Server，然后运行 SQL 脚本。

    如果不允许客户端访问 Azure SQL Server，则需要将 Azure SQL Server 的防火墙配置为允许从计算机（IP 地址）访问。请参阅[此文](../sql-database/sql-database-configure-firewall-settings.md)中的步骤，为 Azure SQL Server 配置防火墙。

### 创建输入数据集
本步骤在 **AzureStorageLinkedService** 链接服务代表的 Azure 存储中，创建指向 Blob 容器的 **AzureBlobInput** 数据集。此 Blob 容器 (**adftutorial**) 包含 **emp.txt** 文件中的输入数据。

1. 将命令分配到名为 **cmd** 的变量。
   
        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@inputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureBlobInput?api-version=2015-10-01};
2. 使用 **Invoke-Command** 运行命令。
   
        $results = Invoke-Command -scriptblock $cmd;
3. 查看结果。如果已成功创建数据集，**结果**中会显示该数据集的 JSON；否则，会显示错误消息。
   
        Write-Host $results

### 创建输出数据集
本步骤创建名为 **AzureSqlOutput** 的输出表。此数据集指向 Azure SQL 数据库中 **AzureSqlLinkedService** 所代表的 SQL 表 (**emp**)。管道将输入 Blob 中的数据复制到 **emp** 表。

1. 将命令分配到名为 **cmd** 的变量。
   
        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@outputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureSqlOutput?api-version=2015-10-01};
2. 使用 **Invoke-Command** 运行命令。
   
        $results = Invoke-Command -scriptblock $cmd;
3. 查看结果。如果已成功创建数据集，**结果**中会显示该数据集的 JSON；否则，会显示错误消息。
   
        Write-Host $results 

## 创建管道
本步骤创建管道，其中包含使用 **AzureBlobInput** 作为输入和使用 **AzureSqlOutput** 作为输出的**复制活动**。

1. 将命令分配到名为 **cmd** 的变量。
   
        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@pipeline.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datapipelines/MyFirstPipeline?api-version=2015-10-01};
2. 使用 **Invoke-Command** 运行命令。
   
        $results = Invoke-Command -scriptblock $cmd;
3. 查看结果。如果已成功创建数据集，**结果**中会显示该数据集的 JSON；否则，会显示错误消息。
   
        Write-Host $results

**祝贺你！** 现已成功创建 Azure 数据工厂，其中包含可将数据从 Azure Blob 存储复制到 Azure SQL 数据库的管道。

## 监视管道
本步骤使用数据工厂 REST API 来监视管道生成的切片。

    $ds ="AzureSqlOutput"

    $cmd = {.\curl.exe -X GET -H "Authorization: Bearer $accessToken" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/$ds/slices?start=1970-01-01T00%3a00%3a00.0000000Z"&"end=2016-08-12T00%3a00%3a00.0000000Z"&"api-version=2015-10-01};

    $results2 = Invoke-Command -scriptblock $cmd;


    IF ((ConvertFrom-Json $results2).value -ne $NULL) {
        ConvertFrom-Json $results2 | Select-Object -Expand value | Format-Table
    } else {
            (convertFrom-Json $results2).RemoteException
    }

运行以下命令，直到切片进入“就绪”状态或“失败”状态。当切片进入“就绪”状态时，在 Azure SQL 数据库的 **emp** 表中检查输出数据。

对于每个切片，源文件中有两行数据已复制到 Azure SQL 数据库中的 emp 表。因此，成功处理所有切片（处于“就绪”状态）后，emp 表中有 24 条新记录。

## 摘要
本教程使用 REST API 创建了一个 Azure 数据工厂，用于将数据从 Azure Blob 复制到 Azure SQL 数据库。下面是本教程中执行的高级步骤：

1. 创建了 Azure **数据工厂**。
2. 创建了**链接服务**：
   1. 一个 Azure 存储链接服务，用于链接保存输入数据的 Azure 存储帐户。
   2. 一个 Azure SQL 链接服务，用于链接保存输出数据的 Azure SQL 数据库。
3. 创建了**数据集**，用于描述管道的输入和输出数据。
4. 创建了包含复制活动的**管道**，其中 BlobSource 为源，SqlSink 为接收器。

## 另请参阅
| 主题 | 说明 |
|:--- |:--- |
| [Data Movement Activities](data-factory-data-movement-activities.md)（数据移动活动） |此文提供有关教程中所用复制活动的详细信息。 |
| [Scheduling and execution](data-factory-scheduling-and-execution.md)（计划和执行） |此文介绍 Azure 数据工厂应用程序模型的计划和执行方面。 |
| [管道](data-factory-create-pipelines.md) |帮助了解 Azure 数据工厂中的管道和活动，以及如何利用它们为方案或业务构造端到端数据驱动工作流。 |
| [数据集](data-factory-create-datasets.md) |此文帮助了解 Azure 数据工厂中的数据集。 |
| [Monitor and manage pipelines using Monitoring App](data-factory-monitor-manage-app.md)（使用监视应用来监视和管理管道） |此文介绍如何使用监视和管理应用来监视、管理及调试管道。 |

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


<!---HONumber=AcomDC_0921_2016-->