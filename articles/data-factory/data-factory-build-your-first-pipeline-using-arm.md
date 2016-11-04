---
title: 构建第一个数据工厂（Resource Manager 模板）| Microsoft Docs
description: 本教程使用 Azure Resource Manager 模板创建一个示例 Azure 数据工厂管道。
services: data-factory
documentationcenter: ''
author: spelluru
manager: jhubbard
editor: monicar

ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 08/01/2016
ms.author: spelluru

---
# 教程：使用 Azure Resource Manager 模板构建第一个 Azure 数据工厂
> [!div class="op_single_selector"]
> * [概述与先决条件](data-factory-build-your-first-pipeline.md)
> * [Azure 门户](data-factory-build-your-first-pipeline-using-editor.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Resource Manager 模板](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)
> 
> 

本教程介绍如何使用 Azure Resource Manager 模板创建第一个 Azure 数据工厂。

## 先决条件
* 阅读[教程概述](data-factory-build-your-first-pipeline.md)，完成**先决条件**步骤。
* 遵循 [How to install and configure Azure PowerShell](../powershell-install-configure.md)（如何安装和配置 Azure PowerShell）一文中的说明，在计算机上安装最新版本的 Azure PowerShell。
* 若要了解 Azure Resource Manager 模板，请参阅 [Authoring Azure Resource Manager Templates](../resource-group-authoring-templates.md)（创作 Azure Resource Manager 模板）。

## 创建 Resource Manager 模板
在本部分中创建以下数据工厂实体：

1. 名为 **TutorialDataFactoryARM** 的**数据工厂**。数据工厂可以包含一个或多个数据管道。管道可以包含一个或多个活动。例如，将数据从源复制到目标数据存储的复制活动，以及运行 Hive 脚本来转换输入数据的 HDInsight Hive 活动。
2. 两个**链接服务**：**StorageLinkedService** 和 **HDInsightOnDemandLinkedService**。这些链接服务将 Azure 存储帐户和按需 Azure HDInsight 群集链接到数据工厂。Azure 存储帐户保留本示例中管道的输入和输出数据。HDInsight 链接服务用于运行本示例中管道活动指定的 Hive 脚本。识别方案中使用的数据存储/计算服务，创建链接的服务将这些服务链接到数据工厂。
3. 两个（输入/输出）**数据集**：**AzureBlobInput** 和 **AzureBlobOutput**。这些数据集代表 Hive 处理的输入和输出数据。这些数据集引用前面在本教程中创建的 **StorageLinkedService**。链接服务指向 Azure 存储帐户，数据集指定用于保存输入和输出数据的存储中的容器、文件夹和文件名。

单击“使用数据工厂编辑器”选项卡，切换到详细说明此模板中所用 JSON 属性的文章。

在 **C:\\ADFGetStarted** 文件夹中，创建包含以下内容的名为 **ADFTutorialARM.json** 的 JSON 文件：

> [!IMPORTANT]
> 更改 **storageAccountName** 和 **storageAccountKey** 变量的值。同时更改 **dataFactoryName**，因为名称必须唯一。
> 
> 

    {
        "contentVersion": "1.0.0.0",
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {
        },
        "variables": {
            "dataFactoryName":  "TutorialDataFactoryARM",
            "storageAccountName":  "<AZURE STORAGE ACCOUNT NAME>" ,
            "storageAccountKey":  "<AZURE STORAGE ACCOUNT KEY>",
            "apiVersion": "2015-10-01",
            "storageLinkedServiceName": "AzureStorageLinkedService",
            "hdInsightOnDemandLinkedServiceName": "HDInsightOnDemandLinkedService",
            "blobInputDataset": "AzureBlobInput",
            "blobOutputDataset": "AzureBlobOutput",
            "singleQuote": "'"
        },
        "resources": [
            {
                "name": "[variables('dataFactoryName')]",
                "apiVersion": "[variables('apiVersion')]",
                "type": "Microsoft.DataFactory/datafactories",
                "location": "westus",
                "resources": [
                    {
                        "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]" ],
                        "type": "linkedservices",
                        "name": "[variables('storageLinkedServiceName')]",
                        "apiVersion": "[variables('apiVersion')]",
                        "properties": {
                            "type": "AzureStorage",
                            "typeProperties": {
                                "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',variables('storageAccountName'),';AccountKey=',variables('storageAccountKey'))]"
                            }
                        }
                    },
                    {
                        "dependsOn": [
                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedservices/', variables('storageLinkedServiceName'))]"
                        ],
                        "type": "linkedservices",
                        "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
                        "apiVersion": "[variables('apiVersion')]",
                        "properties": {
                            "type": "HDInsightOnDemand",
                            "typeProperties": {
                                "clusterSize": 4,
                                "version":  "3.2",
                                "timeToLive": "00:05:00",
                                "osType": "windows",
                                "linkedServiceName": "[variables('storageLinkedServiceName')]",
                            }
                        }
                    },
                    {
                        "dependsOn": [
                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]"
                        ],
                        "type": "datasets",
                        "name": "[variables('blobInputDataset')]",
                        "apiVersion": "[variables('apiVersion')]",
                            "properties": {
                                "type": "AzureBlob",
                                "linkedServiceName": "[variables('storageLinkedServiceName')]",
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
                        },
                    {
                        "dependsOn": [
                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]"
                        ],
                        "type": "datasets",
                        "name": "[variables('blobOutputDataset')]",
                        "apiVersion": "[variables('apiVersion')]",
                            "properties": {
                                "published": false,
                                "type": "AzureBlob",
                                "linkedServiceName": "[variables('storageLinkedServiceName')]",
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
                        },
                        {
                            "dependsOn": [
                                "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
                                "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]",
                                "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('hdInsightOnDemandLinkedServiceName'))]",
                                "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/datasets/', variables('blobInputDataset'))]",
                                "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/datasets/', variables('blobOutputDataset'))]"
                            ],
                            "type": "datapipelines",
                            "name": "[variables('dataFactoryName')]",
                            "apiVersion": "[variables('apiVersion')]",
                            "properties": {
                                "description": "My first Azure Data Factory pipeline",
                                "activities": [
                                    {
                                        "type": "HDInsightHive",
                                        "typeProperties": {
                                            "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                                            "scriptLinkedService": "[variables('storageLinkedServiceName')]",
                                            "defines": {
                                                "inputtable": "[concat('wasb://adfgetstarted@', variables('storageAccountName'), '.blob.core.windows.net/inputdata')]",
                                                "partitionedtable": "[concat('wasb://adfgetstarted@', variables('storageAccountName'), '.blob.core.windows.net/partitioneddata')]"
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
                                "start": "2016-04-01T00:00:00Z",
                                "end": "2016-04-02T00:00:00Z",
                                "isPaused": false
                            }
                        }
                ]
            }
        ]
    }

单击“使用数据工厂编辑器”选项卡，切换到详细说明此模板中所用 JSON 属性的文章。

注意以下事项：

* 数据工厂使用上述 JSON 创建**基于 Windows 的** HDInsight 群集。也可以让它创建**基于 Linux 的** HDInsight 群集。有关详细信息，请参阅 [On-demand HDInsight Linked Service](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)（按需 HDInsight 链接服务）。
* 可以使用**自己的 HDInsight 群集**，而不使用按需 HDInsight 群集。有关详细信息，请参阅 [HDInsight Linked Service](data-factory-compute-linked-services.md#azure-hdinsight-linked-service)（HDInsight 链接服务）。
* HDInsight 群集在 JSON 中指定的 Blob 存储 (**linkedServiceName**).内创建**默认容器**。HDInsight 不会在删除群集时删除此容器。这是设计的行为。使用按需 HDInsight 链接服务时，除非有现有的实时群集 (**timeToLive**)，否则每当需要处理切片时会创建 HDInsight 群集；并在处理完成后删除该群集。
  
    随着处理的切片越来越多，Azure Blob 存储中会出现大量的容器。如果不需要使用它们对作业进行故障排除，则可能需要删除它们以降低存储成本。这些容器的名称遵循模式：“adf**yourdatafactoryname**-**linkedservicename**-datetimestamp”。使用 [Microsoft 存储资源管理器](http://storageexplorer.com/)等工具删除 Azure Blob 存储中的容器。

有关详细信息，请参阅 [On-demand HDInsight Linked Service](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)（按需 HDInsight 链接服务）。

> [!NOTE]
> 可以在 [Github](https://github.com/Azure/azure-quickstart-templates/blob/master/101-data-factory-blob-to-sql/azuredeploy.json) 上找到用于创建 Azure 数据工厂的其他 Resource Manager 模板示例。
> 
> 

## 创建数据工厂
1. 启动 **Azure PowerShell** 并运行以下命令：
   * 运行 `Login-AzureRmAccount` 并输入用于登录 Azure 门户的用户名和密码。
   * 运行 `Get-AzureRmSubscription` 查看此帐户的所有订阅。
   * 运行 `Get-AzureRmSubscription -SubscriptionName <SUBSCRIPTION NAME> | Set-AzureRmContext` 选择要使用的订阅。此订阅应与 Azure 门户中使用的订阅相同。
2. 运行以下命令，使用步骤 1 中创建的 Resource Manager 模板来部署数据工厂实体。
   
        New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFTutorialARM.json

## 监视管道
1. 登录到 [Azure 门户](https://portal.azure.com/)后，单击“浏览”，然后选择“数据工厂”。![“浏览”->“数据工厂”](./media/data-factory-build-your-first-pipeline-using-arm/BrowseDataFactories.png)
2. 在“数据工厂”边栏选项卡中，单击创建的数据工厂 (**TutorialFactoryARM**)。
3. 在数据工厂的“数据工厂”边栏选项卡中，单击“图示”。![图示磁贴](./media/data-factory-build-your-first-pipeline-using-arm/DiagramTile.png)
4. 在“图示视图”中，可以看到管道的概述，以及本教程中使用的数据集。
   
   ![图示视图](./media/data-factory-build-your-first-pipeline-using-arm/DiagramView.png)
5. 在“图示视图”中，双击数据集 **AzureBlobOutput**。此时将显示当前正在处理的切片。
   
    ![数据集](./media/data-factory-build-your-first-pipeline-using-arm/AzureBlobOutput.png)
6. 处理完成后，可以看到切片处于“就绪”状态。创建按需 HDInsight 群集通常需要一段时间（大约 20 分钟）。因此，预期管道需要花费**大约 30 分钟**来处理切片。
   
    ![数据集](./media/data-factory-build-your-first-pipeline-using-arm/SliceReady.png)
7. 当切片处于“就绪”状态时，检查 Blob 存储中 **adfgetstarted** 容器内 **partitioneddata** 文件夹的输出数据。

有关如何使用 Azure 门户边栏选项卡监视本教程中所创建管道和数据集的说明，请参阅 [Monitor datasets and pipeline](data-factory-monitor-manage-pipelines.md)（监视数据集和管道）。

也可以使用监视和管理应用来监视数据管道。有关使用该应用程序的详细信息，请参阅 [Monitor and manage Azure Data Factory pipelines using Monitoring App](data-factory-monitor-manage-app.md)（使用监视应用来监视和管理 Azure 数据工厂管道）。

> [!IMPORTANT]
> 成功处理切片后，将会删除输入文件。因此，如果想要重新运行切片或重新学习本教程，请将输入文件 (input.log) 上载到 adfgetstarted 容器的 inputdata 文件夹中。
> 
> 

## 用于创建网关的 Resource Manager 模板
下面是用于在后端创建逻辑网关的示例 Resource Manager 模板。在本地计算机或 Azure IaaS VM 上安装网关，然后使用密钥向数据工厂服务注册该网关。有关详细信息，请参阅 [Move data between on-premises and cloud](data-factory-move-data-between-onprem-and-cloud.md)（在本地与云之间移动数据）。

    {
        "contentVersion": "1.0.0.0",
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {
        },
        "variables": {
            "dataFactoryName":  "GatewayUsingArmDF",
            "apiVersion": "2015-10-01",
            "singleQuote": "'"
        },
        "resources": [
            {
                "name": "[variables('dataFactoryName')]",
                "apiVersion": "[variables('apiVersion')]",
                "type": "Microsoft.DataFactory/datafactories",
                "location": "eastus",
                "resources": [
                    {
                        "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]" ],
                        "type": "gateways",
                        "apiVersion": "[variables('apiVersion')]",
                        "name": "GatewayUsingARM",
                        "properties": {
                            "description": "my gateway"
                        }
                    }            
                ]
            }
        ]
    }

此模板使用名为 GatewayUsingARM 的网关创建名为 GatewayUsingArmDF 的数据工厂。

## 另请参阅
| 主题 | 说明 |
|:--- |:--- |
| [Data Transformation Activities](data-factory-data-transformation-activities.md)（数据转换活动） |此文提供 Azure 数据工厂支持的数据转换活动列表（例如本教程中使用的 HDInsight Hive 转换）。 |
| [Scheduling and execution](data-factory-scheduling-and-execution.md)（计划和执行） |此文介绍 Azure 数据工厂应用程序模型的计划和执行方面。 |
| [管道](data-factory-create-pipelines.md) |帮助了解 Azure 数据工厂中的管道和活动，以及如何利用它们为方案或业务构造端到端数据驱动工作流。 |
| [数据集](data-factory-create-datasets.md) |此文帮助了解 Azure 数据工厂中的数据集。 |
| [Monitor and manage pipelines using Monitoring App](data-factory-monitor-manage-app.md)（使用监视应用来监视和管理管道） |此文介绍如何使用监视和管理应用来监视、管理及调试管道。 |

<!---HONumber=AcomDC_0921_2016-->