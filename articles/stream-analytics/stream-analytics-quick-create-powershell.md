---
title: 使用 Azure PowerShell 创建流分析作业
description: 本快速入门详细介绍了如何使用 Azure PowerShell 模块来部署并运行 Azure 流分析作业。
services: stream-analytics
author: SnehaGunda
ms.author: sngun
ms.date: 05/14/2018
ms.topic: quickstart
ms.service: stream-analytics
ms.custom: mvc
manager: kfile
ms.openlocfilehash: 2b5d8bfd6dbe36637a0c6873e941118e7ee71b80
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212426"
---
# <a name="quickstart-create-a-stream-analytics-job-by-using-azure-powershell"></a>快速入门：使用 Azure PowerShell 创建流分析作业

Azure PowerShell 模块用于通过 PowerShell cmdlet 或脚本创建和管理 Azure 资源。 本快速入门详细介绍了如何使用 Azure PowerShell 模块来部署并运行 Azure 流分析作业。 

示例作业从 Azure Blob 存储中的 Blob 读取流式处理数据。 在本快速入门中使用的输入数据文件包含的静态数据仅供说明之用。 在实际方案中，请将流式处理输入数据用于流分析作业。 接下来，作业使用流分析查询语言计算温度超过 100° 时的平均温度，对数据进行转换。 最后，作业将生成的输出事件写入到另一文件中。 

## <a name="before-you-begin"></a>开始之前

* 如果没有 Azure 订阅，请创建一个[免费帐户](https://azure.microsoft.com/free/)。  

* 本快速入门需要 Azure PowerShell 模块 3.6 版或更高版本。 运行 `Get-Module -ListAvailable AzureRM` 即可找到在本地计算机上安装的版本。 如果需要进行安装或升级，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)一文。 


## <a name="sign-in-to-azure"></a>登录 Azure

使用 `Connect-AzureRmAccount` 命令登录到 Azure 订阅，然后在弹出的浏览器中输入 Azure 凭据：

```powershell
# Log in to your Azure account
Connect-AzureRmAccount
```

登录后，如果有多个订阅，请运行以下 cmdlet，选择要用于本快速入门的订阅。 请确保将 <your subscription name> 替换为订阅的名称：  

```powershell
# List all available subscriptions.
Get-AzureRmSubscription

# Select the Azure subscription you want to use to create the resource group and resources.
Get-AzureRmSubscription -SubscriptionName "<your subscription name>" | Select-AzureRmSubscription
```

## <a name="create-a-resource-group"></a>创建资源组

使用 [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup) 创建 Azure 资源组。 资源组是在其中部署和管理 Azure 资源的逻辑容器。

```powershell
$resourceGroup = "StreamAnalyticsRG"
$location = "WestUS2"
New-AzureRmResourceGroup `
   -Name $resourceGroup `
   -Location $location 
```

## <a name="prepare-the-input-data"></a>对输入数据进行准备

在定义流分析作业之前，请对已配置为作业输入的数据进行准备。

1. 从 GitHub 下载[传感器示例数据](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Samples/GettingStarted/HelloWorldASA-InputStream.json)。 右键单击链接，然后选择“将链接另存为...”或“将目标另存为”。

2. 以下 PowerShell 代码块通过多项命令来准备作业所需的输入数据。 查看介绍代码的部分。 

   1. 使用 [New-AzureRmStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.storage/New-AzureRmStorageAccount) cmdlet 创建标准的常规用途存储帐户。  本示例创建一个名为 mystorageaccount 的存储帐户，该帐户默认启用本地冗余存储 (LRS) 和 Blob 加密。  

   2. 检索存储帐户上下文 `$storageAccount.Context`，该上下文定义要使用的存储帐户。 使用存储帐户时，请引用上下文而不是重复提供凭据。 

   3. 请使用 [New-AzureStorageContainer](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontainer) 创建存储容器，然后上传此前下载的[传感器示例数据](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/GettingStarted/HelloWorldASA-InputStream.json)。 

   4. 复制代码所输出的存储密钥，然后将该密钥粘贴到 JSON 文件中，以便稍后创建流式处理作业的输入和输出。

   ```powershell
   $storageAccountName = "mystorageaccount"
   $storageAccount = New-AzureRmStorageAccount `
     -ResourceGroupName $resourceGroup `
     -Name $storageAccountName `
     -Location $location `
     -SkuName Standard_LRS `
     -Kind Storage
   
   $ctx = $storageAccount.Context
   $containerName = "streamanalytics"
   
   New-AzureStorageContainer `
     -Name $containerName `
     -Context $ctx
   
   Set-AzureStorageBlobContent `
     -File "c:\HelloWorldASA-InputStream.json" `
     -Blob "input/HelloWorldASA-InputStream.json" `
     -Container $containerName `
     -Context $ctx  
   
   $storageAccountKey = (Get-AzureRmStorageAccountKey `
     -ResourceGroupName $resourceGroup `
     -Name $storageAccountName).Value[0]
   
   Write-Host "The <storage account key> placeholder needs to be replaced in your input and output json files with this key value:" 
   Write-Host $storageAccountKey -ForegroundColor Cyan
   ```

## <a name="create-a-stream-analytics-job"></a>创建流分析作业

请使用 [New-AzureRmStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsjob?view=azurermps-5.4.0) cmdlet 创建流分析作业。 此 cmdlet 使用作业名称、资源组名称和作业定义作为参数。 作业名称可以是用于标识作业的任何友好名称， 但只能包含字母数字字符、连字符和下划线，且其长度必须介于 3 到 63 个字符之间。 作业定义是一个 JSON 文件，其中包含创建作业所需的属性。 在本地计算机上创建名为 `JobDefinition.json` 的文件，并向其添加以下 JSON 数据：

```json
{    
   "location":"WestUS2",  
   "properties":{    
      "sku":{    
         "name":"standard"  
      },  
      "eventsOutOfOrderPolicy":"adjust",  
      "eventsOutOfOrderMaxDelayInSeconds":10,  
      "compatibilityLevel": 1.1
   }
}
```

接下来运行 `New-AzureRmStreamAnalyticsJob` cmdlet。 确保将 `jobDefinitionFile` 变量的值替换为在其中存储了作业定义 JSON 文件的路径。 

```powershell
$jobName = "MyStreamingJob"
$jobDefinitionFile = "C:\JobDefinition.json"
New-AzureRmStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -File $jobDefinitionFile `
  -Name $jobName `
  -Force 
```

## <a name="configure-input-to-the-job"></a>配置作业输入

使用 [New-AzureRmStreamAnalyticsInput](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsinput?view=azurermps-5.4.0) cmdlet 添加作业的输入。 此 cmdlet 使用作业名称、作业输入名称、资源组名称和作业输入定义作为参数。 作业输入定义是一个 JSON 文件，其中包含配置作业的输入所需的属性。 在此示例中，需将 Blob 存储创建为输入。 

在本地计算机上创建名为 `JobInputDefinition.json` 的文件，并向其添加以下 JSON 数据。 确保将 `accountKey` 的值替换为存储帐户的访问密钥，该密钥是存储在 $storageAccountKey 值中的值。 

```json
{
    "properties": {
        "type": "Stream",
        "datasource": {
            "type": "Microsoft.Storage/Blob",
            "properties": {
                "storageAccounts": [
                {
                   "accountName": "mystorageaccount",
                   "accountKey":"<storage account key>"
                }],
                "container": "streamanalytics",
                "pathPattern": "input/",
                "dateFormat": "yyyy/MM/dd",
                "timeFormat": "HH"
            }
        },
        "serialization": {
            "type": "Json",
            "properties": {
                "encoding": "UTF8"
            }
        }
    },
    "name": "MyBlobInput",
    "type": "Microsoft.StreamAnalytics/streamingjobs/inputs"
}
```

接下来运行 `New-AzureRmStreamAnalyticsInput` cmdlet，确保将 `jobDefinitionFile` 变量的值替换为在其中存储了作业输入定义 JSON 文件的路径。 

```powershell
$jobInputName = "MyBlobInput"
$jobInputDefinitionFile = "C:\JobInputDefinition.json"
New-AzureRmStreamAnalyticsInput `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobInputDefinitionFile `
  -Name $jobInputName 
```

## <a name="configure-output-to-the-job"></a>配置作业输出

使用 [New-AzureRmStreamAnalyticsOutput](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsoutput?view=azurermps-5.4.0) cmdlet 添加作业的输出。 此 cmdlet 使用作业名称、作业输出名称、资源组名称和作业输出定义作为参数。 作业输出定义是一个 JSON 文件，其中包含配置作业的输出所需的属性。 此示例使用 Blob 存储作为输出。 

在本地计算机上创建名为 `JobOutputDefinition.json` 的文件，并向其添加以下 JSON 数据。 确保将 `accountKey` 的值替换为存储帐户的访问密钥，该密钥是存储在 $storageAccountKey 值中的值。 

```json
{
    "properties": {
        "datasource": {
            "type": "Microsoft.Storage/Blob",
            "properties": {
                "storageAccounts": [
                    {
                      "accountName": "mystorageaccount",
                      "accountKey": "<storage account key>"
                    }],
                "container": "streamanalytics",
                "pathPattern": "output/",
                "dateFormat": "yyyy/MM/dd",
                "timeFormat": "HH"
            }
        },
        "serialization": {
            "type": "Json",
            "properties": {
                "encoding": "UTF8",
                "format": "LineSeparated"
            }
        }
    },
    "name": "MyBlobOutput",
    "type": "Microsoft.StreamAnalytics/streamingjobs/outputs"
}
```

接下来运行 `New-AzureRmStreamAnalyticsOutput` cmdlet。 确保将 `jobOutputDefinitionFile` 变量的值替换为在其中存储了作业输出定义 JSON 文件的路径。 

```powershell
$jobOutputName = "MyBlobOutput"
$jobOutputDefinitionFile = "C:\JobOutputDefinition.json"
New-AzureRmStreamAnalyticsOutput `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobOutputDefinitionFile `
  -Name $jobOutputName -Force 
```

## <a name="define-the-transformation-query"></a>定义转换查询

使用 [New-AzureRmStreamAnalyticsTransformation](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticstransformation?view=azurermps-5.4.0) cmdlet 添加作业的转换。 此 cmdlet 使用作业名称、作业转换名称、资源组名称和作业转换定义作为参数。 在本地计算机上创建名为 `JobTransformationDefinition.json` 的文件，并向其添加以下 JSON 数据。 此 JSON 文件包含一个查询参数，用于定义转换查询：

```json
{     
   "name":"MyTransformation",  
   "type":"Microsoft.StreamAnalytics/streamingjobs/transformations",  
   "properties":{    
      "streamingUnits":1,  
      "script":null,  
      "query":" SELECT System.Timestamp AS OutputTime, dspl AS SensorName, Avg(temp) AS AvgTemperature INTO MyBlobOutput FROM MyBlobInput TIMESTAMP BY time GROUP BY TumblingWindow(second,30),dspl HAVING Avg(temp)>100"  
   }  
}
```

接下来运行 `New-AzureRmStreamAnalyticsTransformation` cmdlet。 确保将 `jobTransformationDefinitionFile` 变量的值替换为在其中存储了作业转换定义 JSON 文件的路径。 

```powershell
$jobTransformationName = "MyJobTransformation"
$jobTransformationDefinitionFile = "C:\JobTransformationDefinition.json"
New-AzureRmStreamAnalyticsTransformation `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobTransformationDefinitionFile `
  -Name $jobTransformationName -Force
```

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>启动流分析作业并检查输出

请使用 [Start-AzureRmStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/start-azurermstreamanalyticsjob?view=azurermps-5.4.0) cmdlet 启动作业。 此 cmdlet 使用作业名称、资源组名称、输出启动模式和启动时间作为参数。 `OutputStartMode` 接受的值为 `JobStartTime`、`CustomTime` 或 `LastOutputEventTime`。 若要详细了解每个值是指什么，请参阅 PowerShell 文档中的[参数](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/start-azurermstreamanalyticsjob?view=azurermps-5.4.0)部分。 在此示例中，请将模式指定为 `CustomTime` 并提供一个值作为 `OutputStartTime`。 

对于时间值，请选择 `2018-01-01`。 之所以选择此起始日期，是因为它比示例数据中的事件时间戳要早。 以下 cmdlet 在运行以后会返回 `True` 作为输出（如果作业启动）。 在存储容器中，创建的输出文件夹包含已转换的数据。 

```powershell
Start-AzureRmStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode CustomTime `
  -OutputStartTime 2018-01-01T00:00:00Z 
```

## <a name="clean-up-resources"></a>清理资源

若不再需要资源组、流式处理作业以及所有相关资源，请将其删除。 删除作业可避免对作业使用的流单元进行计费。 如果计划在将来使用该作业，可以跳过删除它的操作，暂时只需停止该作业。 如果不打算继续使用该作业，请运行以下 cmdlet，删除本快速入门创建的所有资源：

```powershell
Remove-AzureRmResourceGroup `
  -Name $resourceGroup 
```

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何部署简单的流分析作业。 若要了解如何配置其他输入源并执行实时检测，请继续阅读以下文章：

> [!div class="nextstepaction"]
> [使用 Azure 流分析实时检测欺诈行为](stream-analytics-real-time-fraud-detection.md)
