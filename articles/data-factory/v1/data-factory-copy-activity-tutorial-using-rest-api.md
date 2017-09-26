---
title: "教程：使用 REST API 创建 Azure 数据工厂管道 | Microsoft Docs"
description: "本教程使用 REST API 创建带复制活动的 Azure 数据工厂管道，以便将数据从 Azure Blob 存储复制到 Azure SQL 数据库。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 1704cdf8-30ad-49bc-a71c-4057e26e7350
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/10/2017
ms.author: spelluru
robots: noindex
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: a573bb9880b70ea994fe622226987563f18e1a9f
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---
# <a name="tutorial-use-rest-api-to-create-an-azure-data-factory-pipeline-to-copy-data"></a>教程：使用 REST API 创建用于复制数据的 Azure 数据工厂管道 
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

本文介绍如何使用 REST API 创建数据工厂，以便通过管道将数据从 Azure Blob 存储复制到 Azure SQL 数据库。 如果不熟悉 Azure 数据工厂，请在学习本教程之前，先通读 [Azure 数据工厂简介](data-factory-introduction.md)一文。   

本教程会创建包含一个活动（复制活动）的管道。 复制活动可以将数据从支持的数据存储复制到支持的接收器数据存储。 如需可以用作源和接收器的数据存储的列表，请参阅[支持的数据存储](data-factory-data-movement-activities.md#supported-data-stores-and-formats)。 该活动由全球可用的服务提供支持，能以安全、可靠、可缩放的方式在各种数据存储区间复制数据。 有关复制活动的详细信息，请参阅[数据移动活动](data-factory-data-movement-activities.md)。

一个管道可以有多个活动。 而且，可以通过将一个活动的输出数据集设置为另一个活动的输入数据集，链接两个活动（两个活动先后运行）。 有关详细信息，请参阅[管道中的多个活动](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)。

> [!NOTE]
> 本文不会介绍所有数据工厂 REST API。 有关数据工厂 cmdlet 的综合文档，请参阅 [Data Factory REST API Reference](/rest/api/datafactory/) （数据工厂 REST API 参考）。
>  
> 本教程中的数据管道将数据从源数据存储复制到目标数据存储。 有关如何使用 Azure 数据工厂来转换数据的教程，请参阅[教程：生成使用 Hadoop 群集来转换数据的管道](data-factory-build-your-first-pipeline.md)。

## <a name="prerequisites"></a>先决条件
* 通读 [教程概述](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) ，完成 **先决条件** 步骤。
* 在计算机上安装 [Curl](https://curl.haxx.se/dlwiz/) 。 配合使用 Curl 工具与 REST 命令来创建数据工厂。 
* 遵循 [此文](../../azure-resource-manager/resource-group-create-service-principal-portal.md) 的说明： 
  1. 在 Azure Active Directory 中创建名为 **ADFCopyTutorialApp** 的 Web 应用程序。
  2. 获取**客户端 ID** 和**机密密钥**。 
  3. 获取 **租户 ID**。 
  4. 将 **ADFCopyTutorialApp** 应用程序分配给**数据工厂参与者**角色。  
* 安装 [Azure PowerShell](/powershell/azure/overview)。  
* 启动 **PowerShell** 并执行以下步骤。 在本教程结束之前，请将 Azure PowerShell 保持打开状态。 如果将它关闭再重新打开，则需要再次运行下述命令。
  
  1. 运行以下命令并输入用于登录 Azure 门户的用户名和密码：
    
    ```PowerShell 
    Login-AzureRmAccount
    ```   
  2. 运行以下命令查看此帐户的所有订阅：

    ```PowerShell     
    Get-AzureRmSubscription
    ``` 
  3. 运行以下命令选择要使用的订阅。 将 **&lt;NameOfAzureSubscription**&gt; 替换为 Azure 订阅的名称。 
     
    ```PowerShell
    Get-AzureRmSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzureRmContext
    ```
  4. 在 PowerShell 中运行以下命令，创建名为 **ADFTutorialResourceGroup** 的 Azure 资源组：  

    ```PowerShell     
      New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
    ```
     
      如果资源组已存在，请指定是要更新它 (Y) 还是保留原样 (N)。 
     
      本教程中的某些步骤假设使用名为 ADFTutorialResourceGroup 的资源组。 如果使用不同的资源组，需使用该资源组的名称取代本教程中的 ADFTutorialResourceGroup。

## <a name="create-json-definitions"></a>创建 JSON 定义
在 curl.exe 所在的文件夹中创建以下 JSON 文件。 

### <a name="datafactoryjson"></a>datafactory.json
> [!IMPORTANT]
> 名称必须全局唯一，因此建议使用 ADFCopyTutorialDF 作为前缀/后缀，使其成为唯一名称。 
> 
> 

```JSON
{  
    "name": "ADFCopyTutorialDF",  
    "location": "WestUS"
}  
```

### <a name="azurestoragelinkedservicejson"></a>azurestoragelinkedservice.json
> [!IMPORTANT]
> 将 **accountname** 和 **accountkey** 分别替换为 Azure 存储帐户的名称和密钥。 若要了解如何获取存储访问密钥，请参阅 [View, copy and regenerate storage access keys](../../storage/common/storage-create-storage-account.md#manage-your-storage-access-keys)（查看、复制和重新生成存储访问密钥）。

```JSON
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

有关 JSON 属性的详细信息，请参阅 [Azure 存储链接服务](data-factory-azure-blob-connector.md#azure-storage-linked-service)。

### <a name="azuersqllinkedservicejson"></a>azuersqllinkedservice.json
> [!IMPORTANT]
> 将 **servername**、**databasename**、**username** 和 **password** 分别替换为 Azure SQL 服务器名称、SQL 数据库名称、用户帐户和帐户密码。  
> 
>

```JSON
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
```

有关 JSON 属性的详细信息，请参阅 [Azure SQL 链接服务](data-factory-azure-sql-connector.md#linked-service-properties)。

### <a name="inputdatasetjson"></a>inputdataset.json

```JSON
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

### <a name="outputdatasetjson"></a>outputdataset.json

```JSON
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

### <a name="pipelinejson"></a>pipeline.json

```JSON
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
    "start": "2017-05-11T00:00:00Z",
    "end": "2017-05-12T00:00:00Z"
  }
}
```

请注意以下几点：

- 在 activities 节中，只有一个活动的 **type** 设置为 **Copy**。 有关复制活动的详细信息，请参阅[数据移动活动](data-factory-data-movement-activities.md)。 在数据工厂解决方案中，也可以使用[数据转换活动](data-factory-data-transformation-activities.md)。
- 活动的输入设置为 **AzureBlobInput**，活动的输出设置为 **AzureSqlOutput**。 
- 在 **typeProperties** 节中，**BlobSource** 指定为源类型，**SqlSink** 指定为接收器类型。 有关复制活动支持的数据存储（以源和接收器的形式存在）的完整列表，请参阅[支持的数据存储](data-factory-data-movement-activities.md#supported-data-stores-and-formats)。 若要了解如何使用特定的受支持的数据存储（以源/接收器的形式存在），请单击表中的链接。  
 
将 **start** 属性的值替换为当前日期，将 **end** 值替换为下一个日期。 可以仅指定日期部分，跳过日期时间的时间部分。 例如，“2017-02-03”等效于“2017-02-03T00:00:00Z”
 
开始和结束日期时间必须采用 [ISO 格式](http://en.wikipedia.org/wiki/ISO_8601)。 例如：2016-10-14T16:32:41Z。 **结束** 时间是可选的，但本教程使用该时间。 
 
如果未指定 **end** 属性的值，则以“**开始时间 + 48 小时**”计算。 若要无限期运行管道，请指定 **9999-09-09** 作为 **end** 属性的值。
 
在上述示例中，由于每小时生成一个数据切片，因此共有 24 个数据切片。

有关管道定义中 JSON 属性的说明，请参阅[创建管道](data-factory-create-pipelines.md)一文。 有关复制活动定义中 JSON 属性的说明，请参阅[数据移动活动](data-factory-data-movement-activities.md)一文。 有关 BlobSource 支持的 JSON 属性的说明，请参阅 [Azure Blob 连接器](data-factory-azure-blob-connector.md)一文。 有关 SqlSink 支持的 JSON 属性的说明，请参阅 [Azure SQL 数据库连接器](data-factory-azure-sql-connector.md)一文。

## <a name="set-global-variables"></a>设置全局变量
在 Azure PowerShell 中将值替换为自己的值后，请执行以下命令：

> [!IMPORTANT]
> 有关获取客户端 ID、客户端机密、租户 ID 和订阅 ID 的说明，请参阅 [先决条件](#prerequisites) 部分。   
> 
> 

```JSON
$client_id = "<client ID of application in AAD>"
$client_secret = "<client key of application in AAD>"
$tenant = "<Azure tenant ID>";
$subscription_id="<Azure subscription ID>";

$rg = "ADFTutorialResourceGroup"
```

在更新要使用的数据工厂的名称后，运行以下命令： 

```
$adf = "ADFCopyTutorialDF"
```

## <a name="authenticate-with-aad"></a>在 AAD 中进行身份验证
运行以下命令，在 Azure Active Directory (AAD) 中进行身份验证： 

```PowerShell
$cmd = { .\curl.exe -X POST https://login.microsoftonline.com/$tenant/oauth2/token  -F grant_type=client_credentials  -F resource=https://management.core.windows.net/ -F client_id=$client_id -F client_secret=$client_secret };
$responseToken = Invoke-Command -scriptblock $cmd;
$accessToken = (ConvertFrom-Json $responseToken).access_token;

(ConvertFrom-Json $responseToken) 
```

## <a name="create-data-factory"></a>创建数据工厂
本步骤创建名为 **ADFCopyTutorialDF**的 Azure 数据工厂。 数据工厂可以包含一个或多个数据管道。 管道可以包含一个或多个活动。 例如，复制活动可将数据从源复制到目标数据存储。 HDInsight Hive 活动运行 Hive 脚本以将输入数据转换为产品输出数据。 运行以下命令创建数据工厂： 

1. 将命令分配到名为 **cmd**的变量。 
   
    > [!IMPORTANT]
    > 确认此处指定的名称 (ADFCopyTutorialDF) 与 **datafactory.json**中指定的名称匹配。 
   
    ```PowerShell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@datafactory.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/ADFCopyTutorialDF0411?api-version=2015-10-01};
    ```
2. 使用 **Invoke-Command**运行命令。
   
    ```PowerShell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. 查看结果。 如果已成功创建数据工厂， **结果**中会显示数据工厂的 JSON；否则，会显示错误消息。  
   
    ```
    Write-Host $results
    ```

请注意以下几点：

* Azure 数据工厂的名称必须全局唯一。 如果在结果中看到错误：“数据工厂名称 ‘ADFCopyTutorialDF’ 不可用”，请执行以下步骤：   
  
  1. 在 **datafactory.json** 文件中更改名称（例如，yournameADFCopyTutorialDF）。
  2. 在分配 **$cmd** 变量值的第一个命令中，将 ADFCopyTutorialDF 替换为新名称，然后运行该命令。 
  3. 运行以下两个命令来调用 REST API，创建数据工厂并列显操作结果。 
     
     有关数据工厂项目命名规则，请参阅 [Data Factory - Naming Rules](data-factory-naming-rules.md) （数据工厂 - 命名规则）主题。
* 只有 Azure 订阅的参与者/管理员才可以创建数据工厂实例
* 数据工厂名称可能在将来被注册为 DNS 名称，因此将公开可见。
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

在创建管道之前，需要创建一些数据工厂项。 先创建链接服务，将源和目标数据存储链接到数据存储。 然后，定义输入和输出数据集，表示链接数据存储中的数据。 最后创建管道，其中包含使用这些数据集的活动。

## <a name="create-linked-services"></a>创建链接服务
可在数据工厂中创建链接服务，将数据存储和计算服务链接到数据工厂。 在本教程中，请不要使用任何计算服务，例如 Azure HDInsight 或 Azure Data Lake Analytics。 可以使用两个数据存储，其类型为 Azure 存储（源）和 Azure SQL 数据库（目标）。 因此，请创建两个名为 AzureStorageLinkedService 和 AzureSqlLinkedService 的链接服务，其类型为 AzureStorage 和 AzureSqlDatabase。  

AzureStorageLinkedService 链接将 Azure 存储帐户链接到数据工厂。 已根据[先决条件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)，在此存储帐户中创建了一个容器并上传了数据。   

AzureSqlLinkedService 将 Azure SQL 数据库链接到数据工厂。 从 Blob 存储复制的数据存储在该数据库中。 根据[先决条件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)在该数据库中创建了 emp 表。  

### <a name="create-azure-storage-linked-service"></a>创建 Azure 存储链接服务
本步骤将 Azure 存储帐户链接到数据工厂。 在本部分中指定 Azure 存储帐户的名称和密钥。 有关用于定义 Azure 存储链接服务的 JSON 属性的详细信息。请参阅 [Azure Storage linked service](data-factory-azure-blob-connector.md#azure-storage-linked-service)（Azure 存储链接服务）。  

1. 将命令分配到名为 **cmd**的变量。 

    ```PowerShell   
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@azurestoragelinkedservice.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureStorageLinkedService?api-version=2015-10-01};
    ```
2. 使用 **Invoke-Command**运行命令。

    ```PowerShell   
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. 查看结果。 如果已成功创建链接服务， **结果**中会显示该链接服务的 JSON；否则，会显示错误消息。

    ```PowerShell   
    Write-Host $results
    ```

### <a name="create-azure-sql-linked-service"></a>创建 Azure SQL 链接服务
在此步骤中，将 Azure SQL 数据库链接到数据工厂。 在本部分中指定 Azure SQL 服务器名称、数据库名称、用户名和用户密码。 有关用于定义 Azure SQL 链接服务的 JSON 属性的详细信息。请参阅 [Azure SQL linked service](data-factory-azure-sql-connector.md#linked-service-properties)（Azure SQL 链接服务）。

1. 将命令分配到名为 **cmd**的变量。 
   
    ```PowerShell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@azuresqllinkedservice.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureSqlLinkedService?api-version=2015-10-01};
    ```
2. 使用 **Invoke-Command**运行命令。
   
    ```PowerShell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. 查看结果。 如果已成功创建链接服务， **结果**中会显示该链接服务的 JSON；否则，会显示错误消息。
   
    ```PowerShell
    Write-Host $results
    ```

## <a name="create-datasets"></a>创建数据集
在上一步骤中，已创建用于将 Azure 存储帐户和 Azure SQL 数据库链接到数据工厂的链接服务。 本步骤定义两个名为 AzureBlobInput 和 AzureSqlOutput 的数据集，表示存储在数据存储中的输入和输出数据，这些数据存储分别由 AzureStorageLinkedService 和 AzureSqlLinkedService 引用。

Azure 存储链接服务指定一个连接字符串，数据工厂服务在运行时使用该字符串连接到 Azure 存储帐户。 输入 Blob 数据集 (AzureBlobInput) 指定容器以及包含输入数据的文件夹。  

类似地，Azure SQL 数据库链接服务指定一个连接字符串，数据工厂服务在运行时使用该字符串连接到 Azure SQL 数据库。 输出 SQL 表数据集 (OututDataset) 在数据库中指定一个表，数据将从 Blob 存储复制到该表中。 

### <a name="create-input-dataset"></a>创建输入数据集
本步骤在 AzureStorageLinkedService 链接服务代表的 Azure 存储中创建名为 AzureBlobInput 的数据集，该数据集指向 Blob 容器 (adftutorial) 根文件夹中的 Blob 文件 (emp.txt)。 如果不指定 fileName 的值（或者跳过此步骤），则会将输入文件夹中的所有 Blob 复制到目标。 在本教程中，请为 fileName 指定一个值。 

1. 将命令分配到名为 **cmd**的变量。 

    ```PowerSHell   
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@inputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureBlobInput?api-version=2015-10-01};
    ```
2. 使用 **Invoke-Command**运行命令。
   
    ```PowerShell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. 查看结果。 如果已成功创建数据集， **结果**中会显示该数据集的 JSON；否则，会显示错误消息。
   
    ```PowerShell
    Write-Host $results
    ```

### <a name="create-output-dataset"></a>创建输出数据集
Azure SQL 数据库链接服务指定一个连接字符串，数据工厂服务在运行时使用该字符串连接到 Azure SQL 数据库。 在此步骤中创建的输出 SQL 表数据集 (OututDataset) 在数据库中指定一个表，数据将从 Blob 存储复制到该表中。

1. 将命令分配到名为 **cmd**的变量。

    ```PowerShell   
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@outputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureSqlOutput?api-version=2015-10-01};
    ```
2. 使用 **Invoke-Command**运行命令。
    
    ```PowerShell   
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. 查看结果。 如果已成功创建数据集， **结果**中会显示该数据集的 JSON；否则，会显示错误消息。
   
    ```PowerShell
    Write-Host $results
    ``` 

## <a name="create-pipeline"></a>创建管道
本步骤创建管道，其中包含使用 **AzureBlobInput** 作为输入、使用 **AzureSqlOutput** 作为输出的**复制活动**。

目前，输出数据集驱动计划。 在本教程中，输出数据集配置为每小时生成一个切片。 管道的开始时间和结束时间相差一天，即 24 小时。 因此，管道会生成 24 个输出数据集切片。 

1. 将命令分配到名为 **cmd**的变量。

    ```PowerShell   
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@pipeline.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datapipelines/MyFirstPipeline?api-version=2015-10-01};
    ```
2. 使用 **Invoke-Command**运行命令。

    ```PowerShell   
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. 查看结果。 如果已成功创建数据集， **结果**中会显示该数据集的 JSON；否则，会显示错误消息。  

    ```PowerShell   
    Write-Host $results
    ```

**祝贺你！** 现已成功创建 Azure 数据工厂，其中包含可将数据从 Azure Blob 存储复制到 Azure SQL 数据库的管道。

## <a name="monitor-pipeline"></a>监视管道
本步骤使用数据工厂 REST API 来监视管道生成的切片。

```PowerShell
$ds ="AzureSqlOutput"
```

> [!IMPORTANT] 
> 请确保在以下命令中指定的开始和结束时间与管道的开始和结束时间相符。 

```PowerShell
$cmd = {.\curl.exe -X GET -H "Authorization: Bearer $accessToken" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/$ds/slices?start=2017-05-11T00%3a00%3a00.0000000Z"&"end=2017-05-12T00%3a00%3a00.0000000Z"&"api-version=2015-10-01};
```

```PowerShell
$results2 = Invoke-Command -scriptblock $cmd;
```

```PowerShell
IF ((ConvertFrom-Json $results2).value -ne $NULL) {
    ConvertFrom-Json $results2 | Select-Object -Expand value | Format-Table
} else {
        (convertFrom-Json $results2).RemoteException
}
```

运行 Invoke-Command 和下一条命令，直到切片进入“就绪”或“失败”状态。 **emp** 表中检查输出数据。 

对于每个切片，源文件中有两行数据已复制到 Azure SQL 数据库中的 emp 表。 因此，成功处理所有切片（处于“就绪”状态）后，emp 表中有 24 条新记录。 

## <a name="summary"></a>摘要
本教程使用 REST API 创建了一个 Azure 数据工厂，用于将数据从 Azure Blob 复制到 Azure SQL 数据库。 下面是本教程中执行的高级步骤：  

1. 创建了 Azure **数据工厂**。
2. 创建了 **链接服务**：
   1. 一个 Azure 存储链接服务，用于链接保存输入数据的 Azure 存储帐户。     
   2. 一个 Azure SQL 链接服务，用于链接保存输出数据的 Azure SQL 数据库。 
3. 创建了 **数据集**，用于描述管道的输入和输出数据。
4. 创建了包含复制活动的 **管道** ，其中 BlobSource 为源，SqlSink 为接收器。 

## <a name="next-steps"></a>后续步骤
在本教程中，在复制操作中使用了 Azure Blob 存储作为源数据存储，使用了 Azure SQL 数据库作为目标数据存储。 下表列出了复制活动支持的充当源和目标的数据存储： 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

若要了解如何通过数据存储复制数据，请单击表中数据存储的链接。

