---
title: 使用 Azure PowerShell 创建流分析作业
description: 本快速入门详细介绍了如何使用 Azure PowerShell 模块来部署并运行 Azure 流分析作业。
services: stream-analytics
author: SnehaGunda
ms.author: sngun
ms.date: 03/16/2018
ms.topic: quickstart
ms.service: stream-analytics
ms.custom: mvc
manager: kfile
ms.openlocfilehash: 0be8cee9e6c7874282f4e8f43f75fa7f2490c14e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="quickstart-create-a-stream-analytics-job-by-using-azure-powershell"></a>快速入门：使用 Azure PowerShell 创建流分析作业

Azure PowerShell 模块用于通过 PowerShell cmdlet 或脚本创建和管理 Azure 资源。 本快速入门详细介绍了如何使用 Azure PowerShell 模块来部署并运行 Azure 流分析作业。

## <a name="before-you-begin"></a>开始之前

* 如果没有 Azure 订阅，请创建一个[免费帐户](https://azure.microsoft.com/free/)。  

* 本快速入门需要 Azure PowerShell 模块 3.6 版或更高版本。 运行 `Get-Module -ListAvailable AzureRM` 即可找到在本地计算机上安装的版本。 如果需要进行安装或升级，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)一文。 本文中的方案介绍如何从 Blob 存储读取数据，如何对数据进行转换，然后如何将其写回到同一 Blob 存储中的另一容器。

## <a name="sign-in-to-azure"></a>登录 Azure

使用 `Connect-AzureRmAccount` 命令登录到 Azure 订阅，然后在弹出的浏览器中输入 Azure 凭据。 登录后，如果有多个订阅，请运行以下 cmdlet，选择要用于本快速入门的订阅。 请确保将 <your subscription> 替换为订阅的名称：  

```powershell
# Log in to your Azure account
Connect-AzureRmAccount

# List all available subscriptions.
Get-AzureRmSubscription

# Select the Azure subscription you want to use to create the resource group and resources.
Get-AzureRmSubscription -SubscriptionName "<your subscription name>" | Select-AzureRmSubscription
```

## <a name="create-a-resource-group"></a>创建资源组

使用 [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup) 创建 Azure 资源组。 资源组是在其中部署和管理 Azure 资源的逻辑容器。

```powershell
$resourceGroup = "ASAPSRG"
$location = "WestUS2"
New-AzureRMResourceGroup `
  -Name $resourceGroup `
  -Location $location 
```

## <a name="prepare-the-input-data"></a>对输入数据进行准备

在定义流分析作业之前，应该对已配置为作业输入的数据进行准备。 请运行以下步骤，对作业所需的输入数据进行准备： 

1. 从 GitHub 下载[传感器示例数据](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/GettingStarted/HelloWorldASA-InputStream.json)。  

2. 使用 [New-AzureRmStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.storage/New-AzureRmStorageAccount) cmdlet 创建具有 LRS 复制功能的标准常规用途存储帐户  

3. 检索用于定义要使用的存储帐户的存储帐户上下文。 使用存储帐户时，请引用上下文而不是重复提供凭据。 本示例创建一个名为 mystorageaccount 的存储帐户，该帐户默认启用本地冗余存储 (LRS) 和 Blob 加密。  

4. 接下来，请使用 [New-AzureStorageContainer](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontainer) 创建容器，将权限设置为“blob”，允许对文件进行公共访问，然后上传此前下载的[传感器示例数据](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/GettingStarted/HelloWorldASA-InputStream.json)。 

可以通过运行以下 PowerShell 脚本来实现这些步骤：

```powershell
$storageAccountName = "mystorageaccount"
$storageAccount = New-AzureRmStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context
$containerName = "myinputcontainer"

New-AzureStorageContainer `
  -Name $containerName `
  -Context $ctx `
  -Permission blob

Set-AzureStorageBlobContent `
  -File "C:\HelloWorldASA-InputStream.json" `
  -Container $containerName `
  -Context $ctx  

$storageAccountKey = (Get-AzureRmStorageAccountKey `
  -ResourceGroupName $resourceGroup `
  -Name $storageAccountName).Value[0]
```

## <a name="create-a-stream-analytics-job"></a>创建流分析作业

请使用 [New-AzureRMStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsjob?view=azurermps-5.4.0) cmdlet 创建流分析作业。 此 cmdlet 使用作业名称、资源组名称和作业定义作为参数。 作业名称可以是任何用于标识作业的友好名称。流分析作业名称只能包含字母数字字符、连字符和下划线，其长度必须介于 3 到 63 个字符之间。 作业定义是一个 JSON 文件，其中包含创建作业所需的属性。 在本地计算机上创建名为“JobDefinition.json”的文件，并向其添加以下 JSON 数据：

```json
{    
   "location":"Central US",  
   "properties":{    
      "sku":{    
         "name":"standard"  
      },  
      "eventsOutOfOrderPolicy":"drop",  
      "eventsOutOfOrderMaxDelayInSeconds":10,  
      "compatibilityLevel": 1.1
}
}
```

接下来运行 New-AzureRMStreamAnalyticsJob cmdlet，确保将 jobDefinitionFile 变量的值替换为在其中存储了作业定义 JSON 文件的路径。 

```powershell
$jobName = "MyStreamingJob"
$jobDefinitionFile = "C:\JobDefinition.json"
New-AzureRMStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  –File $jobDefinitionFile `
  –Name $jobName -Force 
```

## <a name="configure-input-to-the-job"></a>配置作业输入

使用 [New-AzureRMStreamAnalyticsInput](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsinput?view=azurermps-5.4.0) cmdlet 添加作业的输入。 此 cmdlet 使用作业名称、作业输入名称、资源组名称和作业输入定义作为参数。 作业输入定义是一个 JSON 文件，其中包含配置作业的输入（在此示例中，将创建 Blob 存储作为输入）所需的属性。 在本地计算机上创建名为“JobInputDefinition.json”的文件，并向其添加以下 JSON 数据，确保将 **accountKey** 的值替换为存储帐户的访问密钥，该密钥是存储在 $storageAccountKey 值中的值。 

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
                   "accountKey":"<Your storage account key>"
                }],
                "container": "myinputcontainer",
                "pathPattern": "",
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

接下来运行 New-AzureRMStreamAnalyticsInput cmdlet，确保将 jobDefinitionFile 变量的值替换为在其中存储了作业输入定义 JSON 文件的路径。 

```powershell
$jobInputName = "MyBlobInput"
$jobInputDefinitionFile = "C:\JobInputDefinition.json"
New-AzureRMStreamAnalyticsInput `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobInputDefinitionFile `
  -Name $jobInputName 
```

## <a name="configure-output-to-the-job"></a>配置作业输出

使用 [New-AzureRmStreamAnalyticsOutput](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsoutput?view=azurermps-5.4.0) cmdlet 添加作业的输出。 此 cmdlet 使用作业名称、作业输出名称、资源组名称和作业输出定义作为参数。 作业输出定义是一个 JSON 文件，其中包含配置作业的输出（在此示例中，将创建 Blob 存储作为输出）所需的属性。 在本地计算机上创建名为“JobOutputDefinition.json”的文件，并向其添加以下 JSON 数据，确保将 **accountKey** 的值替换为存储帐户的访问密钥，该密钥是存储在 $storageAccountKey 值中的值。 

```json
{
    "properties": {
        "datasource": {
            "type": "Microsoft.Storage/Blob",
            "properties": {
                "storageAccounts": [
                    {
                      "accountName": "mystorageaccount",
                      "accountKey": "<Your storage account key>"
                    }],
                "container": "myoutputcontainer",
                "pathPattern": "",
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

接下来运行 New-AzureRMStreamAnalyticsOutput cmdlet，确保将 jobOutputDefinitionFile 变量的值替换为在其中存储了作业输出定义 JSON 文件的路径。 

```powershell
$jobOutputName = "MyBlobOutput"
$jobOutputDefinitionFile = "C:\JobOutputDefinition.json"
New-AzureRMStreamAnalyticsOutput `
  -ResourceGroupName $resourceGroup `
  –JobName $jobName `
  –File $jobOutputDefinitionFile `
  –Name $jobOutputName -Force 
```

## <a name="define-the-transformation-query"></a>定义转换查询

使用 [New-AzureRmStreamAnalyticsTransformation](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticstransformation?view=azurermps-5.4.0) cmdlet 添加作业的转换。 此 cmdlet 使用作业名称、作业转换名称、资源组名称和作业转换定义作为参数。 在本地计算机上创建名为“JobTransformationDefinition.json”的文件，并向其添加以下 JSON 数据。 此 JSON 文件包含一个查询参数，用于定义转换查询：

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

接下来运行 New-AzureRMStreamAnalyticsTransformation cmdlet，确保将 jobTransformationDefinitionFile 变量的值替换为在其中存储了作业转换定义 JSON 文件的路径。 

```powershell
$jobTransformationName = "MyJobTransformation"
$jobTransformationDefinitionFile = "C:\JobTransformationDefinition.json"
New-AzureRMStreamAnalyticsTransformation `
  -ResourceGroupName $resourceGroup `
  –JobName $jobName `
  –File $jobTransformationDefinitionFile `
  –Name $jobTransformationName -Force
```

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>启动流分析作业并检查输出

请使用 [Start-AzureRMStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/start-azurermstreamanalyticsjob?view=azurermps-5.4.0) cmdlet 启动作业。 此 cmdlet 使用作业名称、资源组名称、输出启动模式和启动时间作为参数。 OutputStartMode 接受三个值：JobStartTime、CustomTime 或 LastOutputEventTime。若要了解每个值是指什么，请参阅 PowerShell 文档中的[参数](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/start-azurermstreamanalyticsjob?view=azurermps-5.4.0)部分。 在此示例中，可以将模式指定为 CustomTime 并提供一个值作为 OutputStartTime。 

对于时间值，请选择将文件上传到 Blob 存储之前的一天，因为进行文件上传的这个时间早于当前时间。 以下 cmdlet 在运行以后会返回“True”作为输出（如果作业启动）。 将会使用转换的数据在存储帐户中创建名为“myoutputcontainer”的容器。 

```powershell
Start-AzureRMStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode CustomTime `
  -OutputStartTime 2018-03-11T14:45:12Z 
```

## <a name="clean-up-resources"></a>清理资源

若不再需要资源组、流式处理作业以及所有相关资源，请将其删除。 删除作业可避免对作业使用的流单元进行计费。 如果计划在将来使用该作业，可以先停止它，等到以后需要时再重启它。 如果不打算继续使用该作业，请运行以下 cmdlet，删除本快速入门创建的所有资源：

```powershell
Remove-AzureRmResourceGroup `
  -Name $resourceGroup 
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已部署了简单的流分析作业。若要了解如何配置其他输入源并执行实时检测，请继续阅读以下文章：

> [!div class="nextstepaction"]
> [使用 Azure 流分析实时检测欺诈行为](stream-analytics-real-time-fraud-detection.md)
