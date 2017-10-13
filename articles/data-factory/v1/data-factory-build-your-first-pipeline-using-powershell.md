---
title: "构建第一个数据工厂 (PowerShell) | Microsoft Docs"
description: "本教程使用 Azure PowerShell 创建一个示例 Azure 数据工厂管道。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 22ec1236-ea86-4eb7-b903-0e79a58b90c7
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 07/10/2017
ms.author: spelluru
robots: noindex
ms.openlocfilehash: aee8e183a05ae3817e814d027cf268bee71324c3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-build-your-first-azure-data-factory-using-azure-powershell"></a>教程：使用 Azure PowerShell 构建第一个 Azure 数据工厂
> [!div class="op_single_selector"]
> * [概述与先决条件](data-factory-build-your-first-pipeline.md)
> * [Azure 门户](data-factory-build-your-first-pipeline-using-editor.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Resource Manager 模板](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)
>
>

本教程介绍如何使用 Azure PowerShell 创建第一个 Azure 数据工厂。 若要使用其他工具/SDK 来完成教程，请从下拉列表中选择一个选项。

本教程中的管道有一个活动：**HDInsight Hive 活动**。 该活动在 Azure HDInsight 群集上运行 Hive 脚本，通过转换输入数据来生成输出数据。 管道在指定的开始时间和结束时间范围内每月按计划运行一次。 

> [!NOTE]
> 本教程中的数据管道可以转换输入数据，以便生成输出数据。 它不是将数据从源数据存储复制到目标数据存储。 有关如何使用 Azure 数据工厂复制数据的教程，请参阅[教程：将数据从 Blob 存储复制到 SQL 数据库](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。
> 
> 一个管道可以有多个活动。 而且，可以通过将一个活动的输出数据集设置为另一个活动的输入数据集，链接两个活动（两个活动先后运行）。 有关详细信息，请参阅[在数据工厂中计划和执行](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)。

## <a name="prerequisites"></a>先决条件
* 阅读 [教程概述](data-factory-build-your-first-pipeline.md) ，完成 **先决条件** 步骤。
* 遵循 [How to install and configure Azure PowerShell](/powershell/azure/overview) （如何安装和配置 Azure PowerShell）一文中的说明，在计算机上安装最新版本的 Azure PowerShell。
* （可选）本文不会介绍所有数据工厂 cmdlet。 有关数据工厂 cmdlet 的综合文档，请参阅 [Data Factory Cmdlet Reference](/powershell/module/azurerm.datafactories) （数据工厂 cmdlet 参考）。

## <a name="create-data-factory"></a>创建数据工厂
本步骤使用 Azure PowerShell 创建名为 **FirstDataFactoryPSH**的 Azure 数据工厂。 数据工厂可以包含一个或多个数据管道。 管道可以包含一个或多个活动。 例如，将数据从源复制到目标数据存储的复制活动，以及运行 Hive 脚本来转换输入数据的 HDInsight Hive 活动。 首先，在此步骤中创建数据工厂。

1. 启动 Azure PowerShell 并运行以下命令。 在本教程结束之前，请将 Azure PowerShell 保持打开状态。 如果将它关闭再重新打开，则需要再次运行这些命令。
   * 运行以下命令并输入用于登录 Azure 门户的用户名和密码。
    ```PowerShell
    Login-AzureRmAccount
    ```    
   * 运行以下命令查看此帐户的所有订阅。
    ```PowerShell
    Get-AzureRmSubscription 
    ```
   * 运行以下命令选择要使用的订阅。 此订阅应与 Azure 门户中使用的订阅相同。
    ```PowerShell
    Get-AzureRmSubscription -SubscriptionName <SUBSCRIPTION NAME> | Set-AzureRmContext
    ```     
2. 运行以下命令，创建名为 **ADFTutorialResourceGroup** 的 Azure 资源组：
    
    ```PowerShell
    New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
    ```
    本教程中的某些步骤假设使用名为 ADFTutorialResourceGroup 的资源组。 如果使用不同的资源组，需使用该资源组取代本教程中的 ADFTutorialResourceGroup。
3. 运行 **New-AzureRmDataFactory** cmdlet，创建名为 **FirstDataFactoryPSH** 的数据工厂。

    ```PowerShell
    New-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH –Location "West US"
    ```
注意以下几点：

* Azure 数据工厂的名称必须全局唯一。 如果收到错误：“数据工厂名称 ‘FirstDataFactoryPSH’ 不可用”，请更改名称（例如 yournameFirstDataFactoryPSH）。 执行本教程中的步骤时，请使用此名称取代 ADFTutorialFactoryPSH。 有关数据工厂项目命名规则，请参阅 [Data Factory - Naming Rules](data-factory-naming-rules.md) （数据工厂 - 命名规则）主题。
* 只有 Azure 订阅的参与者/管理员才可以创建数据工厂实例
* 数据工厂名称可能在将来被注册为 DNS 名称，因此将公开可见。
* 如果收到错误：“该订阅未注册为使用命名空间 Microsoft.DataFactory”，请执行下列操作之一，尝试再次发布：

  * 在 Azure PowerShell 中运行以下命令，注册数据工厂提供程序。

    ```PowerShell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```
      可运行以下命令来确认数据工厂提供程序是否已注册：

    ```PowerShell
    Get-AzureRmResourceProvider
    ```
  * 使用 Azure 订阅登录到 [Azure 门户](https://portal.azure.com) ，并导航到“数据工厂”边栏选项卡，或在 Azure 门户中创建数据工厂。 此操作会自动注册提供程序。

在创建管道之前，需要创建一些数据工厂项。 首先创建链接服务用于将数据存储/计算机链接到数据存储，然后定义输入和输出数据集来表示链接的数据存储中的输入/输出数据，接下来创建管道，其中的某个活动使用这些数据集。

## <a name="create-linked-services"></a>创建链接服务
在此步骤中，将 Azure 存储帐户和按需 Azure HDInsight 群集链接到数据工厂。 Azure 存储帐户保留本示例中管道的输入和输出数据。 HDInsight 链接服务用于运行本示例中管道活动指定的 Hive 脚本。 识别方案中使用的数据存储/计算服务，创建链接的服务将这些服务链接到数据工厂。

### <a name="create-azure-storage-linked-service"></a>创建 Azure 存储链接服务
在此步骤中，将 Azure 存储帐户链接到数据工厂。 使用相同的 Azure 存储帐户来存储输入/输出数据和 HQL 脚本文件。

1. 在 C:\ADFGetStarted 文件夹中，创建包含以下内容的名为 StorageLinkedService.json 的 JSON 文件。 创建 ADFGetStarted 文件夹（如果不存在）。

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
    将**帐户名**替换为 Azure 存储帐户名，将**帐户密钥**替换为 Azure 存储帐户的访问密钥。 要了解如何获取存储访问密钥，请在[管理存储帐户](../../storage/common/storage-create-storage-account.md#manage-your-storage-account)中查看有关如何查看、复制和重新生成存储访问密钥的信息。
2. 在 Azure PowerShell 中，切换到 ADFGetStarted 文件夹。
3. 可以使用 **New-AzureRmDataFactoryLinkedService** cmdlet 创建链接服务。 此 cmdlet 以及本教程中使用的其他数据工厂 cmdlet 要求传递 *ResourceGroupName* 和 *DataFactoryName* 参数的值。 或者，可以使用 **Get-AzureRmDataFactory** 获取 **DataFactory** 对象并传递该对象，这样就不需要在每次运行 cmdlet 时键入 *ResourceGroupName* 和 *DataFactoryName*。 运行以下命令，将 **Get-AzureRmDataFactory** cmdlet 的输出分配给 **$df** 变量。

    ```PowerShell
    $df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH
    ```
4. 现在，运行 **New-AzureRmDataFactoryLinkedService** cmdlet 创建 **StorageLinkedService** 链接服务。

    ```PowerShell
    New-AzureRmDataFactoryLinkedService $df -File .\StorageLinkedService.json
    ```
    如果尚未运行 **Get-AzureRmDataFactory** cmdlet 并将输出分配给 **$df** 变量，则必须指定 *ResourceGroupName* 和 *DataFactoryName* 参数的值，如下所示。

    ```PowerShell
    New-AzureRmDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName FirstDataFactoryPSH -File .\StorageLinkedService.json
    ```
    如果在教程的中途关闭 Azure PowerShell，则下次启动 Azure PowerShell 时必须运行 **Get-AzureRmDataFactory** cmdlet 才能完成教程。

### <a name="create-azure-hdinsight-linked-service"></a>创建 Azure HDInsight 链接服务
在此步骤中，将按需 HDInsight 群集链接到数据工厂。 HDInsight 群集在运行时自动创建，在处理完成之后删除，并且会空闲指定的一段时间。 可以使用自己的 HDInsight 群集，而不使用按需 HDInsight 群集。 有关详细信息，请参阅 [Compute Linked Services](data-factory-compute-linked-services.md) （计算链接服务）。

1. 在 **C:\ADFGetStarted** 文件夹中，创建包含以下内容的名为 **HDInsightOnDemandLinkedService**.json 的 JSON 文件。

    ```json
    {
        "name": "HDInsightOnDemandLinkedService",
        "properties": {
            "type": "HDInsightOnDemand",
            "typeProperties": {
                "version": "3.5",
                "clusterSize": 1,
                "timeToLive": "00:05:00",
                "osType": "Linux",
                "linkedServiceName": "StorageLinkedService"
            }
        }
    }
    ```
    下表提供了代码片段中使用的 JSON 属性的描述：

   | 属性 | 说明 |
   |:--- |:--- |
   | ClusterSize |指定 HDInsight 群集的大小。 |
   | TimeToLive |指定 HDInsight 群集在被删除之前的空闲时间。 |
   | linkedServiceName |指定用于存储 HDInsight 生成的日志的存储帐户 |

    请注意以下几点：

   * 数据工厂使用 JSON 创建**基于 Linux** 的 HDInsight 群集。 有关详细信息，请参阅 [On-demand HDInsight Linked Service](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) （按需 HDInsight 链接服务）。
   * 可以使用 **自己的 HDInsight 群集** ，而不使用按需 HDInsight 群集。 有关详细信息，请参阅 [HDInsight Linked Service](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) （HDInsight 链接服务）。
   * HDInsight 群集在 JSON 中指定的 Blob 存储 (**linkedServiceName**).内创建**默认容器**。 HDInsight 不会在删除群集时删除此容器。 这是设计的行为。 使用按需 HDInsight 链接服务时，除非有现有的实时群集 (**timeToLive**)，否则每当需要处理切片时，都会创建 HDInsight 群集。 处理完成后会自动删除该群集。

       随着处理的切片越来越多，Azure Blob 存储中会出现大量的容器。 如果不需要使用它们对作业进行故障排除，则可能需要删除它们以降低存储成本。 这些容器的名称遵循模式：“adf**yourdatafactoryname**-**linkedservicename**-datetimestamp”。 使用 [Microsoft 存储资源管理器](http://storageexplorer.com/) 等工具删除 Azure Blob 存储中的容器。

     有关详细信息，请参阅 [On-demand HDInsight Linked Service](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) （按需 HDInsight 链接服务）。
2. 运行 **New-AzureRmDataFactoryLinkedService** cmdlet 创建名为 HDInsightOnDemandLinkedService 的链接服务。
    
    ```PowerShell
    New-AzureRmDataFactoryLinkedService $df -File .\HDInsightOnDemandLinkedService.json
    ```

## <a name="create-datasets"></a>创建数据集
此步骤创建数据集来代表 Hive 处理的输入和输出数据。 这些数据集引用前面在本教程中创建的 **StorageLinkedService** 。 链接服务指向 Azure 存储帐户，数据集指定用于保存输入和输出数据的存储中的容器、文件夹和文件名。

### <a name="create-input-dataset"></a>创建输入数据集
1. 在 **C:\ADFGetStarted** 文件夹中，创建包含以下内容的名为 **InputTable.json** 的 JSON 文件：

    ```json
    {
        "name": "AzureBlobInput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "fileName": "input.log",
                "folderPath": "adfgetstarted/inputdata",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Month",
                "interval": 1
            },
            "external": true,
            "policy": {}
        }
    }
    ```
    该 JSON 定义名为 **AzureBlobInput**的数据集，表示管道中活动的输入数据。 此外，它指定要将输入数据放在名为 **adfgetstarted** 的 Blob 容器及名为 **inputdata** 的文件夹中。

    下表提供了代码片段中使用的 JSON 属性的描述：

   | 属性 | 说明 |
   |:--- |:--- |
   | type |type 属性设置为 AzureBlob，因为数据位于 Azure Blob 存储中。 |
   | linkedServiceName |引用前面创建的 StorageLinkedService。 |
   | fileName |此属性是可选的。 如果省略此属性，将选择 folderPath 中的所有文件。 在这种情况下，只处理 input.log。 |
   | type |日志文件采用文本格式，因此这里使用 TextFormat。 |
   | columnDelimiter |日志中的列以逗号 (,) 分隔。 |
   | frequency/interval |frequency 设置为 Month，interval 为 1，表示每月获取输入切片。 |
   | external |如果输入数据不是由数据工厂服务生成的，此属性设置为 true。 |
2. 在 Azure PowerShell 中运行以下命令，创建数据工厂数据集：

    ```PowerShell
    New-AzureRmDataFactoryDataset $df -File .\InputTable.json
    ```

### <a name="create-output-dataset"></a>创建输出数据集
现在，创建输出数据集来表示 Azure Blob 存储中存储的输出数据。

1. 在 **C:\ADFGetStarted** 文件夹中，创建包含以下内容的名为 **OutputTable.json** 的 JSON 文件：

    ```json
    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "adfgetstarted/partitioneddata",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ","
          }
        },
        "availability": {
          "frequency": "Month",
          "interval": 1
        }
      }
    }
    ```
    该 JSON 定义名为 **AzureBlobOutput**的数据集，表示管道中活动的输出数据。 此外，它指定要将结果存储在名为 **adfgetstarted** 的 Blob 容器及名为 **partitioneddata** 的文件夹中。 **availability** 节指定每月生成输出数据集一次。
2. 在 Azure PowerShell 中运行以下命令，创建数据工厂数据集：

    ```PowerShell
    New-AzureRmDataFactoryDataset $df -File .\OutputTable.json
    ```

## <a name="create-pipeline"></a>创建管道
此步骤创建第一个具有 **HDInsightHive** 活动的管道。 每月获取输入切片（frequency：Month；interval：1），每月生成输出切片，活动的计划程序属性也设置为每月。 输出数据集的设置必须与活动计划程序匹配。 当前，输出数据集驱动计划，因此即使活动并未生成任何输出，也必须创建输出数据集。 如果活动没有任何输入，可以跳过创建输入数据集。 本部分结尾说明以下 JSON 中使用的属性。

1. 在 C:\ADFGetStarted 文件夹中，创建包含以下内容的名为 MyFirstPipelinePSH.json 的 JSON 文件：

   > [!IMPORTANT]
   > 在 JSON 中，将 **storageaccountname** 替换为存储帐户名。
   >
   >

    ```json
    {
        "name": "MyFirstPipeline",
        "properties": {
            "description": "My first Azure Data Factory pipeline",
            "activities": [
                {
                    "type": "HDInsightHive",
                    "typeProperties": {
                        "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                        "scriptLinkedService": "StorageLinkedService",
                        "defines": {
                            "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                            "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                        }
                    },
                    "inputs": [
                        {
                            "name": "AzureBlobInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOutput"
                        }
                    ],
                    "policy": {
                        "concurrency": 1,
                        "retry": 3
                    },
                    "scheduler": {
                        "frequency": "Month",
                        "interval": 1
                    },
                    "name": "RunSampleHiveActivity",
                    "linkedServiceName": "HDInsightOnDemandLinkedService"
                }
            ],
            "start": "2017-07-01T00:00:00Z",
            "end": "2017-07-02T00:00:00Z",
            "isPaused": false
        }
    }
    ```
    在 JSON 代码片段中创建一个管道，其中包括在 HDInsight 群集上使用 Hive 处理数据的单个活动。

    Hive 脚本文件 **partitionweblogs.hql** 存储在 Azure 存储帐户（由 scriptLinkedService 指定，名为 **StorageLinkedService**）中，以及 **adfgetstarted** 容器的 **script** 文件夹中。

    **defines** 节用于指定运行时设置，这些设置将作为 Hive 配置值（例如 ${hiveconf:inputtable}、${hiveconf:partitionedtable}）传递到 Hive 脚本。

    管道的 **start** 和 **end** 属性指定管道的活动期限。

    在活动 JSON 中，指定 Hive 脚本要在通过 **linkedServiceName** – **HDInsightOnDemandLinkedService** 指定的计算资源上运行。

   > [!NOTE]
   > 有关本示例中使用的 JSON 属性的详细信息，请参阅 [Pipelines and activities in Azure Data Factory](data-factory-create-pipelines.md)（Azure 数据工厂中的管道和活动）中的“Pipeline JSON”（管道 JSON）。

2. 确认在 Azure Blob 存储的 **adfgetstarted/inputdata** 文件夹中看到了**input.log** 文件，并运行以下命令部署管道。 由于 **start** 和 **end** 时间设置为过去的时间，**isPaused** 设置为 false，因此管道（管道中的活动）在部署后立即运行。

    ```PowerShell
    New-AzureRmDataFactoryPipeline $df -File .\MyFirstPipelinePSH.json
    ```
3. 恭喜，现已使用 Azure PowerShell 成功创建第一个管道！

## <a name="monitor-pipeline"></a>监视管道
本步骤使用 Azure PowerShell 监视 Azure 数据工厂的运行情况。

1. 运行 **Get-AzureRmDataFactory** 并将输出分配给 **$df** 变量。

    ```PowerShell
    $df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH
    ```
2. 运行 **Get-AzureRmDataFactorySlice**，获取有关 **EmpSQLTable**（管道的输出表）所有切片的详细信息。

    ```PowerShell
    Get-AzureRmDataFactorySlice $df -DatasetName AzureBlobOutput -StartDateTime 2017-07-01
    ```
    请注意，此处指定的 StartDateTime 与在管道 JSON 中指定的开始时间是相同的。 下面是示例输出：

    ```PowerShell
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : FirstDataFactoryPSH
    DatasetName       : AzureBlobOutput
    Start             : 7/1/2017 12:00:00 AM
    End               : 7/2/2017 12:00:00 AM
    RetryCount        : 0
    State             : InProgress
    SubState          :
    LatencyStatus     :
    LongRetryCount    : 0
    ```
3. 运行 **Get-AzureRmDataFactoryRun** ，获取特定切片的活动运行详细信息。

    ```PowerShell
    Get-AzureRmDataFactoryRun $df -DatasetName AzureBlobOutput -StartDateTime 2017-07-01
    ```

    下面是示例输出： 

    ```PowerShell
    Id                  : 0f6334f2-d56c-4d48-b427-d4f0fb4ef883_635268096000000000_635292288000000000_AzureBlobOutput
    ResourceGroupName   : ADFTutorialResourceGroup
    DataFactoryName     : FirstDataFactoryPSH
    DatasetName         : AzureBlobOutput
    ProcessingStartTime : 12/18/2015 4:50:33 AM
    ProcessingEndTime   : 12/31/9999 11:59:59 PM
    PercentComplete     : 0
    DataSliceStart      : 7/1/2017 12:00:00 AM
    DataSliceEnd        : 7/2/2017 12:00:00 AM
    Status              : AllocatingResources
    Timestamp           : 12/18/2015 4:50:33 AM
    RetryAttempt        : 0
    Properties          : {}
    ErrorMessage        :
    ActivityName        : RunSampleHiveActivity
    PipelineName        : MyFirstPipeline
    Type                : Script
    ```
    可以继续运行此 cmdlet，直到切片进入“就绪”状态或“失败”状态。 当切片处于“就绪”状态时，检查 Blob 存储中 **adfgetstarted** 容器内 **partitioneddata** 文件夹的输出数据。  创建按需 HDInsight 群集通常需要一段时间。

    ![输出数据](./media/data-factory-build-your-first-pipeline-using-powershell/three-ouptut-files.png)

> [!IMPORTANT]
> 创建按需 HDInsight 群集通常需要一段时间（大约 20 分钟）。 因此，预期管道需要花费 **大约 30 分钟** 来处理切片。
>
> 成功处理切片后，会删除输入文件。 因此，如果想要重新运行切片或重新学习本教程，请将输入文件 (input.log) 上传到 adfgetstarted 容器的 inputdata 文件夹中。
>
>

## <a name="summary"></a>摘要
本教程通过在 HDInsight hadoop 群集上运行 Hive 脚本，创建了一个 Azure 数据工厂来处理数据。 在 Azure 门户中使用数据工厂编辑器执行了以下步骤：

1. 创建了 Azure **数据工厂**。
2. 创建了两个 **链接服务**：
   1. **Azure 存储** 链接服务，用于将保存输入/输出文件的 Azure Blob 存储链接到数据工厂。
   2. **Azure HDInsight** 按需链接服务，用于将 HDInsight Hadoop 按需群集链接到数据工厂。 Azure 数据工厂实时创建 HDInsight Hadoop 群集来处理输入数据以及生成输出数据。
3. 创建了两个 **数据集**，描述管道中 HDInsight Hive 活动的输入和输出数据。
4. 创建了包含 **HDInsight Hive** 活动的**管道**。

## <a name="next-steps"></a>后续步骤
本文创建了可在按需 Azure HDInsight 群集上运行 Hive 脚本、包含转换活动（HDInsight 活动）的管道。 要了解如何使用复制活动将数据从 Azure Blob 复制到 Azure SQL，请参阅 [Tutorial: Copy data from an Azure Blob to Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)（教程：将数据从 Azure Blob 复制到 Azure SQL）。

## <a name="see-also"></a>另请参阅
| 主题 | 说明 |
|:--- |:--- |
| [Data Factory Cmdlet Reference](/powershell/module/azurerm.datafactories) |参阅有关数据工厂 cmdlet 的综合文档 |
| [管道](data-factory-create-pipelines.md) |帮助你了解 Azure 数据工厂中的管道和活动，以及如何利用它们为方案或业务构造端对端数据驱动工作流。 |
| [数据集](data-factory-create-datasets.md) |还有助于了解 Azure 数据工厂中的数据集。 |
| [计划和执行](data-factory-scheduling-and-execution.md) |本文介绍 Azure 数据工厂应用程序模型的计划方面和执行方面。 |
| [使用监视应用监视和管理管道](data-factory-monitor-manage-app.md) |本文介绍如何使用监视和管理应用来监视、管理和调试管道。 |
