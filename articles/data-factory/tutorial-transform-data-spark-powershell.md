---
title: "在 Azure 数据工厂中使用 Spark 转换数据 | Microsoft Docs"
description: "本教程提供有关在 Azure 数据工厂中使用 Spark 活动转换数据的分步说明。"
services: data-factory
documentationcenter: 
author: shengcmsft
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/22/2018
ms.author: shengc
ms.openlocfilehash: af5e19b212fdebe5220e49eeaa6ec9fc56b1da1c
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2018
---
# <a name="transform-data-in-the-cloud-by-using-spark-activity-in-azure-data-factory"></a>在 Azure 数据工厂中使用 Spark 活动转换云中的数据
本教程使用 Azure PowerShell 创建一个数据工厂管道，该管道可以使用 Spark 活动和按需 HDInsight 链接服务转换数据。 在本教程中执行以下步骤：

> [!div class="checklist"]
> * 创建数据工厂。 
> * 创作并部署链接服务。
> * 创作并部署管道。 
> * 启动管道运行。
> * 监视管道运行。

> [!NOTE]
> 本文适用于目前处于预览版的数据工厂版本 2。 如果使用数据工厂服务版本 1（即正式版 (GA)），请参阅[数据工厂版本 1 文档](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。

如果你还没有 Azure 订阅，可以在开始前创建一个[免费](https://azure.microsoft.com/free/)帐户。

## <a name="prerequisites"></a>先决条件
* **Azure 存储帐户**。 创建 Python 脚本和输入文件，并将其上传到 Azure 存储。 Spark 程序的输出存储在此存储帐户中。 按需 Spark 群集使用相同的存储帐户作为其主存储。  
* **Azure PowerShell**。 遵循[如何安装和配置 Azure PowerShell](/powershell/azure/install-azurerm-ps) 中的说明。


### <a name="upload-python-script-to-your-blob-storage-account"></a>将 Python 脚本上传到 Blob 存储帐户
1. 创建包含以下内容的名为 **WordCount_Spark.py** 的 Python 文件： 

    ```python
    import sys
    from operator import add
    
    from pyspark.sql import SparkSession
    
    def main():
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
            
        lines = spark.read.text("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/inputfiles/minecraftstory.txt").rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' ')) \
            .map(lambda x: (x, 1)) \
            .reduceByKey(add)
        counts.saveAsTextFile("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/outputfiles/wordcount")
        
        spark.stop()
    
    if __name__ == "__main__":
        main()
    ```
2. 将 **&lt;storageAccountName&gt;** 替换为 Azure 存储帐户的名称。 然后保存文件。 
3. 在 Azure Blob 存储中，创建名为 **adftutorial** 的容器（如果尚不存在）。 
4. 创建名为 **spark** 的文件夹。
5. 在 **spark** 文件夹中创建名为 **script** 的子文件夹。 
6. 将 **WordCount_Spark.py** 文件上传到 **script** 子文件夹。 


### <a name="upload-the-input-file"></a>上传输入文件
1. 创建包含一些文本的名为 **minecraftstory.txt** 的文件。 Spark 程序会统计此文本中的单词数量。 
2. 在 `spark` 文件夹中创建名为 `inputfiles` 的子文件夹。 
3. 将 `minecraftstory.txt` 上传到 `inputfiles` 子文件夹。 

## <a name="author-linked-services"></a>创作链接服务
在本部分中创作两个链接服务： 
    
- 一个 Azure 存储链接服务，用于将 Azure 存储帐户链接到数据工厂。 按需 HDInsight 群集使用此存储。 此存储还包含要执行的 Spark 脚本。 
- 一个按需 HDInsight 链接服务。 Azure 数据工厂自动创建 HDInsight 群集，运行 Spark 程序，然后在 HDInsight 群集空闲预配置的时间后将其删除。 

### <a name="azure-storage-linked-service"></a>Azure 存储链接服务
使用偏好的编辑器创建一个 JSON 文件，复制 Azure 存储链接服务的以下 JSON 定义，并将该文件另存为 **MyStorageLinkedService.json**。  

```json
{
    "name": "MyStorageLinkedService",
    "properties": {
      "type": "AzureStorage",
      "typeProperties": {
        "connectionString": {
          "value": "DefaultEndpointsProtocol=https;AccountName=<storageAccountName>;AccountKey=<storageAccountKey>",
          "type": "SecureString"
        }
      }
    }
}
```
使用 Azure 存储帐户的名称和密钥更新 &lt;storageAccountName&gt; 和 &lt;storageAccountKey&gt;。 


### <a name="on-demand-hdinsight-linked-service"></a>按需 HDInsight 链接服务
使用偏好的编辑器创建一个 JSON 文件，复制 Azure HDInsight 链接服务的以下 JSON 定义，并将该文件另存为 **MyOnDemandSparkLinkedService.json**。  

```json
{
    "name": "MyOnDemandSparkLinkedService",
    "properties": {
      "type": "HDInsightOnDemand",
      "typeProperties": {
        "clusterSize": 2,
        "clusterType": "spark",
        "timeToLive": "00:15:00",
        "hostSubscriptionId": "<subscriptionID> ",
        "servicePrincipalId": "<servicePrincipalID>",
        "servicePrincipalKey": {
          "value": "<servicePrincipalKey>",
          "type": "SecureString"
        },
        "tenant": "<tenant ID>",
        "clusterResourceGroup": "<resourceGroupofHDICluster>",
        "version": "3.6",
        "osType": "Linux",
        "clusterNamePrefix":"ADFSparkSample",
        "linkedServiceName": {
          "referenceName": "MyStorageLinkedService",
          "type": "LinkedServiceReference"
        }
      }
    }
}
```
更新链接服务定义中以下属性的值： 

- **hostSubscriptionId**。 将 &lt;subscriptionID&gt; 替换为 Azure 订阅的 ID。 按需 HDInsight 群集在此 Azure 订阅中创建。 
- **tenant**。 将 &lt;tenantID&gt; 替换为 Azure 租户的 ID。 
- **servicePrincipalId**、**servicePrincipalKey**。 将 &lt;servicePrincipalID&gt; 和 &lt;servicePrincipalKey&gt; 分别替换为 Azure Active Directory 中服务主体的 ID 和密钥。 此服务主体需是订阅“参与者”角色的成员，或创建群集的资源组的成员。 有关详细信息，请参阅[创建 Azure Active Directory 应用程序和服务主体](../azure-resource-manager/resource-group-create-service-principal-portal.md)。 
- **clusterResourceGroup**。 将 &lt;resourceGroupOfHDICluster&gt; 替换为需要在其中创建资源组的 HDInsight 群集的名称。 

> [!NOTE]
> Azure HDInsight 会限制可在其支持的每个 Azure 区域中使用的核心总数。 对于按需 HDInsight 链接服务，将在 Azure 存储用作其主存储的同一位置创建 HDInsight 群集。 请确保有足够的核心配额，以便能够成功创建群集。 有关详细信息，请参阅[使用 Hadoop、Spark、Kafka 等在 HDInsight 中设置群集](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)。 


## <a name="author-a-pipeline"></a>创作管道 
本步骤创建包含 Spark 活动的新管道。 该活动使用**单词计数**示例。 从此位置下载内容（如果尚未这样做）。

在偏好的编辑器中创建一个 JSON 文件，复制管道定义的以下 JSON 定义，并将该文件另存为 **MySparkOnDemandPipeline.json**。 

```json
{
  "name": "MySparkOnDemandPipeline",
  "properties": {
    "activities": [
      {
        "name": "MySparkActivity",
        "type": "HDInsightSpark",
        "linkedServiceName": {
            "referenceName": "MyOnDemandSparkLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
          "rootPath": "adftutorial/spark",
          "entryFilePath": "script/WordCount_Spark.py",
          "getDebugInfo": "Failure",
          "sparkJobLinkedService": {
            "referenceName": "MyStorageLinkedService",
            "type": "LinkedServiceReference"
          }
        }
      }
    ]
  }
}
```

注意以下几点： 

- rootPath 指向 adftutorial 容器的 spark 文件夹。 
- entryFilePath 指向 spark 文件夹的 script 子文件夹中的 WordCount_Spark.py 文件。 


## <a name="create-a-data-factory"></a>创建数据工厂 
已在 JSON 文件中创作链接服务和管道定义。 现在，让我们创建一个数据工厂，并使用 PowerShell cmdlet 部署链接服务和管道 JSON 文件。 逐条运行以下 PowerShell 命令： 

1. 逐个设置变量。

    **资源组名称**
    ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup" 
    ```

    **数据工厂名称。必须全局唯一** 
    ```powershell
    $dataFactoryName = "MyDataFactory09102017"
    ```
    
    **管道名称**
    ```powershell
    $pipelineName = "MySparkOnDemandPipeline" # Name of the pipeline
    ```
2. 启动 **PowerShell**。 在完成本快速入门之前，请将 Azure PowerShell 保持打开状态。 如果将它关闭再重新打开，则需要再次运行下述命令。 目前，数据工厂 V2 仅允许在“美国东部”、“美国东部 2”和“西欧”区域中创建数据工厂。 数据工厂使用的数据存储（Azure 存储、Azure SQL 数据库，等等）和计算资源（HDInsight 等）可以位于其他区域中。

    运行以下命令并输入用于登录 Azure 门户的用户名和密码：
        
    ```powershell
    Login-AzureRmAccount
    ```        
    运行以下命令查看此帐户的所有订阅：

    ```powershell
    Get-AzureRmSubscription
    ```
    运行以下命令选择要使用的订阅。 请将 **SubscriptionId** 替换为自己的 Azure 订阅的 ID：

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"    
    ```  
3. 创建资源组：ADFTutorialResourceGroup。 

    ```powershell
    New-AzureRmResourceGroup -Name $resourceGroupName -Location "East Us" 
    ```
4. 创建数据工厂。 

    ```powershell
     $df = Set-AzureRmDataFactoryV2 -Location EastUS -Name $dataFactoryName -ResourceGroupName $resourceGroupName
    ```

    执行以下命令查看输出： 

    ```powershell
    $df
    ```
5. 切换到在其中创建了 JSON 文件的文件夹，并运行以下命令部署 Azure 存储链接服务： 
       
    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "MyStorageLinkedService" -File "MyStorageLinkedService.json"
    ```
6. 运行以下命令部署按需 Spark 链接服务： 
       
    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "MyOnDemandSparkLinkedService" -File "MyOnDemandSparkLinkedService.json"
    ```
7. 运行以下命令部署管道： 
       
    ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name $pipelineName -File "MySparkOnDemandPipeline.json"
    ```
    
## <a name="start-and-monitor-a-pipeline-run"></a>启动并监视管道运行  

1. 启动管道运行。 该命令还会捕获管道运行 ID 用于将来的监视。

    ```powershell
    $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineName  
    ```
2. 运行以下脚本来持续检查管道运行状态，直到运行完成为止。

    ```powershell
    while ($True) {
        $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    
        if(!$result) {
            Write-Host "Waiting for pipeline to start..." -foregroundcolor "Yellow"
        }
        elseif (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
            Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
        }
        else {
            Write-Host "Pipeline '"$pipelineName"' run finished. Result:" -foregroundcolor "Yellow"
            $result
            break
        }
        ($result | Format-List | Out-String)
        Start-Sleep -Seconds 15
    }

    Write-Host "Activity `Output` section:" -foregroundcolor "Yellow"
    $result.Output -join "`r`n"

    Write-Host "Activity `Error` section:" -foregroundcolor "Yellow"
    $result.Error -join "`r`n" 
    ```  
3. 下面是示例运行的输出： 

    ```
    Pipeline run status: In Progress
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : 
    ActivityName      : MySparkActivity
    PipelineRunId     : 94e71d08-a6fa-4191-b7d1-cf8c71cb4794
    PipelineName      : MySparkOnDemandPipeline
    Input             : {rootPath, entryFilePath, getDebugInfo, sparkJobLinkedService}
    Output            : 
    LinkedServiceName : 
    ActivityRunStart  : 9/20/2017 6:33:47 AM
    ActivityRunEnd    : 
    DurationInMs      : 
    Status            : InProgress
    Error             :
    …
    
    Pipeline ' MySparkOnDemandPipeline' run finished. Result:
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : MyDataFactory09102017
    ActivityName      : MySparkActivity
    PipelineRunId     : 94e71d08-a6fa-4191-b7d1-cf8c71cb4794
    PipelineName      : MySparkOnDemandPipeline
    Input             : {rootPath, entryFilePath, getDebugInfo, sparkJobLinkedService}
    Output            : {clusterInUse, jobId, ExecutionProgress, effectiveIntegrationRuntime}
    LinkedServiceName : 
    ActivityRunStart  : 9/20/2017 6:33:47 AM
    ActivityRunEnd    : 9/20/2017 6:46:30 AM
    DurationInMs      : 763466
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    Activity Output section:
    "clusterInUse": "https://ADFSparkSamplexxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.azurehdinsight.net/"
    "jobId": "0"
    "ExecutionProgress": "Succeeded"
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
    Activity Error section:
    "errorCode": ""
    "message": ""
    "failureType": ""
    "target": "MySparkActivity"
    ```
4. 确认是否在 adftutorial 容器的 `spark` 文件夹中创建了包含 spark 程序的输出的、名为 `outputfiles` 的文件夹。 


## <a name="next-steps"></a>后续步骤
此示例中的管道将数据从 Azure Blob 存储中的一个位置复制到另一个位置。 你已了解如何： 

> [!div class="checklist"]
> * 创建数据工厂。 
> * 创作并部署链接服务。
> * 创作并部署管道。 
> * 启动管道运行。
> * 监视管道运行。

继续学习下一篇教程，了解如何通过运行 Azure HDInsight 群集上的 Hive 脚本，转换虚拟网络中的数据。 

> [!div class="nextstepaction"]
> [教程：在 Azure 虚拟网络中使用 Hive 转换数据](tutorial-transform-data-hive-virtual-network.md)。





