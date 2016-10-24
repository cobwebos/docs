<properties
	pageTitle="构建第一个数据工厂 (REST) | Microsoft Azure"
	description="本教程使用数据工厂 REST API 创建一个示例 Azure 数据工厂管道。"
	services="data-factory"
	documentationCenter=""
	authors="spelluru"
	manager="jhubbard"
	editor="monicar"
/>

<tags
	ms.service="data-factory"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="08/16/2016"
	ms.author="spelluru"/>

# 教程：使用数据工厂 REST API 构建第一个 Azure 数据工厂
> [AZURE.SELECTOR]
- [概述与先决条件](data-factory-build-your-first-pipeline.md)
- [Azure 门户](data-factory-build-your-first-pipeline-using-editor.md)
- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Resource Manager 模板](data-factory-build-your-first-pipeline-using-arm.md)
- [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)

本教程介绍如何使用数据工厂 REST API 创建第一个 Azure 数据工厂。

## 先决条件
- 阅读[教程概述](data-factory-build-your-first-pipeline.md)，完成**先决条件**步骤。
- 在计算机上安装 [Curl](https://curl.haxx.se/dlwiz/)。配合使用 CURL 工具与 REST 命令来创建数据工厂。
- 遵循[此文](../resource-group-create-service-principal-portal.md)的说明：
	1. 在 Azure Active Directory 中创建名为 **ADFGetStartedApp** 的 Web 应用程序。
	2. 获取**客户端 ID** 和**机密密钥**。
	3. 获取**租户 ID**。
	4. 将 **ADFGetStartedApp** 应用程序分配给**数据工厂参与者**角色。
- 安装 [Azure PowerShell](../powershell-install-configure.md)。
- 启动 **PowerShell** 并运行以下命令。在本教程结束之前，请将 Azure PowerShell 保持打开状态。如果将它关闭再重新打开，则需要再次运行下述命令。
	1. 运行 **Login-AzureRmAccount** 并输入用于登录 Azure 门户的用户名和密码。
	2. 运行 **Get-AzureRmSubscription**，查看此帐户的所有订阅。
	3. 运行 **Get-AzureRmSubscription -SubscriptionName NameOfAzureSubscription| Set-AzureRmContext** to select the subscription that you want to work with. Replace **NameOfAzureSubscription** with the name of your Azure subscription. 
3. 在 PowerShell 中运行以下命令，创建名为 **ADFTutorialResourceGroup** 的 Azure 资源组：

		New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

	本教程中的某些步骤假设使用名为 ADFTutorialResourceGroup 的资源组。如果使用不同的资源组，需使用该资源组的名称取代本教程中的 ADFTutorialResourceGroup。

## 创建 JSON 定义
在 curl.exe 所在的文件夹中创建以下 JSON 文件。

### datafactory.json 
> [AZURE.IMPORTANT] 名称必须全局唯一，因此建议使用 ADFCopyTutorialDF 作为前缀/后缀，使其成为唯一名称。

	{  
	    "name": "FirstDataFactoryREST",  
	    "location": "WestUS"
	}  

### azurestoragelinkedservice.json
> [AZURE.IMPORTANT] 将 **accountname** 和 **accountkey** 分别替换为 Azure 存储帐户的名称和密钥。若要了解如何获取存储访问密钥，请参阅 [View, copy and regenerate storage access keys](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)（查看、复制和重新生成存储访问密钥）。

	{
	    "name": "AzureStorageLinkedService",
	    "properties": {
	        "type": "AzureStorage",
	        "typeProperties": {
	            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	        }
	    }
	}


### hdinsightondemandlinkedservice.json

	{
		"name": "HDInsightOnDemandLinkedService",
		"properties": {
			"type": "HDInsightOnDemand",
			"typeProperties": {
				"version": "3.2",
				"clusterSize": 1,
				"timeToLive": "00:30:00",
				"linkedServiceName": "AzureStorageLinkedService"
			}
		}
	}

下表提供了代码片段中使用的 JSON 属性的描述：

| 属性 | 说明 |
| :------- | :---------- |
| 版本 | 指定所创建的 HDInsight 版本为 3.2。 | 
| ClusterSize | HDInsight 群集的大小。 | 
| TimeToLive | 指定 HDInsight 群集在被删除之前的空闲时间。 |
| linkedServiceName | 指定用于存储 HDInsight 生成的日志的存储帐户 |

请注意以下几点：

- 数据工厂使用上述 JSON 创建**基于 Windows 的** HDInsight 群集。也可以让它创建**基于 Linux 的** HDInsight 群集。有关详细信息，请参阅 [On-demand HDInsight Linked Service](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)（按需 HDInsight 链接服务）。
- 可以使用**自己的 HDInsight 群集**，而不使用按需 HDInsight 群集。有关详细信息，请参阅 [HDInsight Linked Service](data-factory-compute-linked-services.md#azure-hdinsight-linked-service)（HDInsight 链接服务）。
- HDInsight 群集在 JSON 中指定的 Blob 存储 (**linkedServiceName**).内创建**默认容器**。HDInsight 不会在删除群集时删除此容器。这是设计的行为。使用按需 HDInsight 链接服务时，除非有现有的实时群集 (**timeToLive**)，否则每当处理切片时会创建 HDInsight 群集；并在处理完成后删除该群集。

	随着处理的切片越来越多，Azure Blob 存储中会出现大量的容器。如果不需要使用它们对作业进行故障排除，则可能需要删除它们以降低存储成本。这些容器的名称遵循模式：“adf**yourdatafactoryname**-**linkedservicename**-datetimestamp”。使用 [Microsoft 存储资源管理器](http://storageexplorer.com/)等工具删除 Azure Blob 存储中的容器。

有关详细信息，请参阅 [On-demand HDInsight Linked Service](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)（按需 HDInsight 链接服务）。

### inputdataset.json

	{
		"name": "AzureBlobInput",
		"properties": {
			"type": "AzureBlob",
			"linkedServiceName": "AzureStorageLinkedService",
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


该 JSON 定义名为 **AzureBlobInput** 的数据集，表示管道中活动的输入数据。此外，它指定要将输入数据放在名为 **adfgetstarted** 的 Blob 容器及名为 **inputdata** 的文件夹中

下表提供了代码片段中使用的 JSON 属性的描述：

| 属性 | 说明 |
| :------- | :---------- |
| type | type 属性设置为 AzureBlob，因为数据位于 Azure Blob 存储中。 |  
| linkedServiceName | 引用前面创建的 StorageLinkedService。 |
| fileName | 此属性是可选的。如果省略此属性，将选择 folderPath 中的所有文件。在这种情况下，只处理 input.log。 |
| type | 日志文件采用文本格式，因此这里使用 TextFormat。 | 
| columnDelimiter | 日志文件中的列以逗号 (,) 分隔 |
| frequency/interval | frequency 设置为 Month，interval 为 1，表示每月获取输入切片。 | 
| external | 如果输入数据不是由数据工厂服务生成的，此属性设置为 true。 | 

### outputdataset.json

	{
		"name": "AzureBlobOutput",
		"properties": {
			"type": "AzureBlob",
			"linkedServiceName": "AzureStorageLinkedService",
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

该 JSON 定义名为 **AzureBlobOutput** 的数据集，表示管道中活动的输出数据。此外，它指定要将结果存储在名为 **adfgetstarted** 的 Blob 容器及名为 **partitioneddata** 的文件夹中。**availability** 节指定每月生成输出数据集一次。

### pipeline.json
> [AZURE.IMPORTANT] 将 **storageaccountname** 替换为 Azure 存储帐户的名称。


	{
		"name": "MyFirstPipeline",
		"properties": {
			"description": "My first Azure Data Factory pipeline",
			"activities": [{
				"type": "HDInsightHive",
				"typeProperties": {
					"scriptPath": "adfgetstarted/script/partitionweblogs.hql",
					"scriptLinkedService": "AzureStorageLinkedService",
					"defines": {
						"inputtable": "wasb://adfgetstarted@<stroageaccountname>.blob.core.windows.net/inputdata",
						"partitionedtable": "wasb://adfgetstarted@<stroageaccountname>t.blob.core.windows.net/partitioneddata"
					}
				},
				"inputs": [{
					"name": "AzureBlobInput"
				}],
				"outputs": [{
					"name": "AzureBlobOutput"
				}],
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
			}],
			"start": "2016-07-10T00:00:00Z",
			"end": "2016-07-11T00:00:00Z",
			"isPaused": false
		}
	}

在 JSON 代码片段中创建一个管道，其中包括在 HDInsight 群集上使用 Hive 处理数据的单个活动。

Hive 脚本文件 **partitionweblogs.hql** 存储在 Azure 存储帐户（由 scriptLinkedService 指定，名为 **StorageLinkedService**）中，以及 **adfgetstarted** 容器的 **script** 文件夹中。

**defines** 节指定运行时设置，这些设置将作为 Hive 配置值（例如 ${hiveconf:inputtable}、${hiveconf:partitionedtable}）传递到 Hive 脚本。

管道的 **start** 和 **end** 属性指定管道的活动期限。

在活动 JSON 中，指定 Hive 脚本要在通过 **linkedServiceName** – **HDInsightOnDemandLinkedService** 指定的计算资源上运行。

> [AZURE.NOTE] 有关上述示例中使用的 JSON 属性的详细信息，请参阅 [Anatomy of a Pipeline](data-factory-create-pipelines.md#anatomy-of-a-pipeline)（管道剖析）。

## 设置全局变量

在 Azure PowerShell 中将值替换为自己的值后，请执行以下命令：

> [AZURE.IMPORTANT] 有关获取客户端 ID、客户端机密、租户 ID 和订阅 ID 的说明，请参阅[先决条件](#prerequisites)部分。

	$client_id = "<client ID of application in AAD>"
	$client_secret = "<client key of application in AAD>"
	$tenant = "<Azure tenant ID>";
	$subscription_id="<Azure subscription ID>";

	$rg = "ADFTutorialResourceGroup"
	$adf = "FirstDataFactoryREST"



## 在 AAD 中进行身份验证

	$cmd = { .\curl.exe -X POST https://login.microsoftonline.com/$tenant/oauth2/token  -F grant_type=client_credentials  -F resource=https://management.core.windows.net/ -F client_id=$client_id -F client_secret=$client_secret };
	$responseToken = Invoke-Command -scriptblock $cmd;
	$accessToken = (ConvertFrom-Json $responseToken).access_token;
	
	(ConvertFrom-Json $responseToken) 



## 创建数据工厂

本步骤创建名为 **FirstDataFactoryREST** 的 Azure 数据工厂。数据工厂可以包含一个或多个数据管道。管道可以包含一个或多个活动。例如，将数据从源复制到目标数据存储的复制活动，以及运行 Hive 脚本来转换数据的 HDInsight Hive 活动。运行以下命令创建数据工厂：

1. 将命令分配到名为 **cmd** 的变量。

	确认此处指定的名称 (ADFCopyTutorialDF) 与 **datafactory.json** 中指定的名称匹配。

		$cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@datafactory.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/FirstDataFactoryREST?api-version=2015-10-01};
2. 使用 **Invoke-Command** 运行命令。

		$results = Invoke-Command -scriptblock $cmd;
3. 查看结果。如果已成功创建数据工厂，**结果**中会显示数据工厂的 JSON；否则，会显示错误消息。

		Write-Host $results

请注意以下几点：
 
- Azure 数据工厂的名称必须全局唯一。如果在结果中看到错误：“数据工厂名称 ‘FirstDataFactoryREST’ 不可用”，请执行以下步骤：
	1. 在 **datafactory.json** 文件中更改名称（例如，yournameFirstDataFactoryREST）。有关数据工厂项目命名规则，请参阅 [Data Factory - Naming Rules](data-factory-naming-rules.md)（数据工厂 - 命名规则）主题。
	2. 在分配 **$cmd** 变量值的第一个命令中，将 FirstDataFactoryREST 替换为新名称，然后运行该命令。
	3. 运行以下两个命令来调用 REST API，创建数据工厂并列显操作结果。
- 只有 Azure 订阅的参与者/管理员才可以创建数据工厂实例
- 数据工厂名称可能在将来被注册为 DNS 名称，因此将公开可见。
- 如果收到错误：“该订阅未注册为使用命名空间 Microsoft.DataFactory”，请执行下列操作之一，尝试再次发布：

	- 在 Azure PowerShell 中运行以下命令，注册数据工厂提供程序。
		
			Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
	
		可运行以下命令来确认数据工厂提供程序是否已注册：
	
			Get-AzureRmResourceProvider
	- 使用 Azure 订阅登录到 [Azure 门户](https://portal.azure.com)，然后导航到“数据工厂”边栏选项卡，或在 Azure 门户中创建数据工厂。此操作将自动注册提供程序。

在创建管道之前，需要创建一些数据工厂项。首先创建链接服务用于将数据存储/计算机链接到数据存储，定义输入和输出数据集来表示链接的数据存储中的数据。

## 创建链接服务 
在此步骤中，将 Azure 存储帐户和按需 Azure HDInsight 群集链接到数据工厂。Azure 存储帐户保留本示例中管道的输入和输出数据。HDInsight 链接服务用于运行本示例中管道活动指定的 Hive 脚本。

### 创建 Azure 存储链接服务
在此步骤中，将 Azure 存储帐户链接到数据工厂。本教程使用相同的 Azure 存储帐户来存储输入/输出数据和 HQL 脚本文件。

1. 将命令分配到名为 **cmd** 的变量。

		$cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@azurestoragelinkedservice.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureStorageLinkedService?api-version=2015-10-01};
2. 使用 **Invoke-Command** 运行命令。
 
		$results = Invoke-Command -scriptblock $cmd;
3. 查看结果。如果已成功创建链接服务，**结果**中会显示该链接服务的 JSON；否则，会显示错误消息。
  
		Write-Host $results

### 创建 Azure HDInsight 链接服务
在此步骤中，将按需 HDInsight 群集链接到数据工厂。HDInsight 群集在运行时自动创建，在处理完成之后删除，并且会空闲指定的一段时间。可以使用自己的 HDInsight 群集，而不使用按需 HDInsight 群集。有关详细信息，请参阅 [Compute Linked Services](data-factory-compute-linked-services.md)（计算链接服务）。

1. 将命令分配到名为 **cmd** 的变量。
 
		$cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@hdinsightondemandlinkedservice.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/hdinsightondemandlinkedservice?api-version=2015-10-01};
2. 使用 **Invoke-Command** 运行命令。

		$results = Invoke-Command -scriptblock $cmd;
3. 查看结果。如果已成功创建链接服务，**结果**中会显示该链接服务的 JSON；否则，会显示错误消息。

		Write-Host $results

## 创建数据集
此步骤创建数据集来代表 Hive 处理的输入和输出数据。这些数据集引用前面在本教程中创建的 **StorageLinkedService**。链接服务指向 Azure 存储帐户，数据集指定用于保存输入和输出数据的存储中的容器、文件夹和文件名。

### 创建输入数据集
本步骤创建输入数据集来表示 Azure Blob 存储中存储的输入数据。

1. 将命令分配到名为 **cmd** 的变量。

		$cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@inputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureBlobInput?api-version=2015-10-01};
2. 使用 **Invoke-Command** 运行命令。

		$results = Invoke-Command -scriptblock $cmd;
3. 查看结果。如果已成功创建数据集，**结果**中会显示该数据集的 JSON；否则，会显示错误消息。
  
		Write-Host $results
### 创建输出数据集
本步骤创建输出数据集来表示 Azure Blob 存储中存储的输出数据。

1. 将命令分配到名为 **cmd** 的变量。
 
		$cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@outputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureBlobOutput?api-version=2015-10-01};
2. 使用 **Invoke-Command** 运行命令。

		$results = Invoke-Command -scriptblock $cmd;
3. 查看结果。如果已成功创建数据集，**结果**中会显示该数据集的 JSON；否则，会显示错误消息。
  
		Write-Host $results 

## 创建管道
此步骤创建第一个具有 **HDInsightHive** 活动的管道。每月获取输入切片（frequency：Month；interval：1），每月生成输出切片，活动的计划程序属性也设置为每月。输出数据集的设置必须与活动计划程序匹配。当前，输出数据集驱动计划，因此即使活动并未生成任何输出，也必须创建输出数据集。如果活动没有任何输入，可以跳过创建输入数据集。

确认在 Azure Blob 存储的 **adfgetstarted/inputdata** 文件夹中看到了**input.log** 文件，然后运行以下命令部署管道。由于 **start** 和 **end** 时间设置为过去的时间，**isPaused** 设置为 false，因此管道（管道中的活动）在部署后立即运行。

1. 将命令分配到名为 **cmd** 的变量。
 
		$cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@pipeline.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datapipelines/MyFirstPipeline?api-version=2015-10-01};
2. 使用 **Invoke-Command** 运行命令。

		$results = Invoke-Command -scriptblock $cmd;
3. 查看结果。如果已成功创建数据集，**结果**中会显示该数据集的 JSON；否则，会显示错误消息。

		Write-Host $results
5. 恭喜，现已使用 Azure PowerShell 成功创建第一个管道！

## 监视管道
本步骤使用数据工厂 REST API 来监视管道生成的切片。

	$ds ="AzureBlobOutput"

	$cmd = {.\curl.exe -X GET -H "Authorization: Bearer $accessToken" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/$ds/slices?start=1970-01-01T00%3a00%3a00.0000000Z"&"end=2016-08-12T00%3a00%3a00.0000000Z"&"api-version=2015-10-01};

	$results2 = Invoke-Command -scriptblock $cmd;

	IF ((ConvertFrom-Json $results2).value -ne $NULL) {
	    ConvertFrom-Json $results2 | Select-Object -Expand value | Format-Table
	} else {
    	    (convertFrom-Json $results2).RemoteException
	}


> [AZURE.IMPORTANT] 
创建按需 HDInsight 群集通常需要一段时间（大约 20 分钟）。因此，预期管道需要花费**大约 30 分钟**来处理切片。

运行 Invoke-Command 和下一条命令，直到切片进入“就绪”或“失败”状态。当切片处于“就绪”状态时，检查 Blob 存储中 **adfgetstarted** 容器内 **partitioneddata** 文件夹的输出数据。创建按需 HDInsight 群集通常需要一段时间。

![输出数据](./media/data-factory-build-your-first-pipeline-using-rest-api/three-ouptut-files.png)

> [AZURE.IMPORTANT] 成功处理切片后，将会删除输入文件。因此，如果想要重新运行切片或重新学习本教程，请将输入文件 (input.log) 上载到 adfgetstarted 容器的 inputdata 文件夹中。

可以使用 Azure 门户监视切片和排除任何问题。有关详细信息，请参阅 [Monitor pipelines using Azure portal](data-factory-build-your-first-pipeline-using-editor.md##monitor-pipeline)（使用 Azure 门户监视管道）。

## 摘要 
本教程通过在 HDInsight hadoop 群集上运行 Hive 脚本，创建了一个 Azure 数据工厂来处理数据。在 Azure 门户中使用数据工厂编辑器执行了以下步骤：

1.	创建了 Azure **数据工厂**。
2.	创建了两个**链接服务**：
	1.	**Azure 存储**链接服务，用于将保存输入/输出文件的 Azure Blob 存储链接到数据工厂。
	2.	**Azure HDInsight** 按需链接服务，用于将 HDInsight Hadoop 按需群集链接到数据工厂。Azure 数据工厂实时创建 HDInsight Hadoop 群集来处理输入数据以及生成输出数据。
3.	创建了两个**数据集**，描述管道中 HDInsight Hive 活动的输入和输出数据。
4.	创建了具有 **HDInsight Hive** 活动的**管道**。

## 后续步骤
本文创建了可在按需 Azure HDInsight 群集上运行 Hive 脚本、包含转换活动（HDInsight 活动）的管道。若要了解如何使用复制活动将数据从 Azure Blob 复制到 Azure SQL，请参阅 [Tutorial: Copy data from an Azure Blob to Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)（教程：将数据从 Azure Blob 复制到 Azure SQL）。

## 另请参阅
| 主题 | 说明 |
| :---- | :---- |
| [Data Factory REST API Reference](https://msdn.microsoft.com/library/azure/dn906738.aspx)（数据工厂 REST API 参考） | 参阅有关数据工厂 cmdlet 的综合文档 |
| [Data Transformation Activities](data-factory-data-transformation-activities.md)（数据转换活动） | 此文提供 Azure 数据工厂支持的数据转换活动列表（例如本教程中使用的 HDInsight Hive 转换）。 |
| [计划和执行](data-factory-scheduling-and-execution.md) | 此文介绍 Azure 数据工厂应用程序模型的计划和执行方面。 |
| [管道](data-factory-create-pipelines.md) | 帮助了解 Azure 数据工厂中的管道和活动，以及如何利用它们为方案或业务构造端到端数据驱动工作流。 |
| [数据集](data-factory-create-datasets.md) | 此文帮助了解 Azure 数据工厂中的数据集。
| [Monitor and Manage Pipelines using Azure portal blades](data-factory-monitor-manage-pipelines.md)（使用 Azure 门户边栏选项卡监视和管理管道） | 此文介绍如何使用 Azure 门户边栏选项卡监视、管理和调试管道。 |
| [Monitor and manage pipelines using Monitoring App](data-factory-monitor-manage-app.md)（使用监视应用来监视和管理管道） | 此文介绍如何使用监视和管理应用来监视、管理及调试管道。 

<!---HONumber=AcomDC_0921_2016-->