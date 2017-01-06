---
title: "构建第一个数据工厂（Resource Manager 模板）| Microsoft 文档"
description: "本教程使用 Azure Resource Manager 模板创建一个示例 Azure 数据工厂管道。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: eb9e70b9-a13a-4a27-8256-2759496be470
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 10/12/2016
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 9990e3ae2a978f0024c17f445ea07eae8aef5197
ms.openlocfilehash: 3f36162dd0826270a7e2a69e7d940ba5516ecef0


---
# <a name="tutorial-build-your-first-azure-data-factory-using-azure-resource-manager-template"></a>教程：使用 Azure Resource Manager 模板构建第一个 Azure 数据工厂
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

## <a name="prerequisites"></a>先决条件
* 阅读 [教程概述](data-factory-build-your-first-pipeline.md) ，完成 **先决条件** 步骤。
* 遵循 [How to install and configure Azure PowerShell](/powershell/azureps-cmdlets-docs) （如何安装和配置 Azure PowerShell）一文中的说明，在计算机上安装最新版本的 Azure PowerShell。
* 若要了解 Azure Resource Manager 模板，请参阅 [Authoring Azure Resource Manager Templates](../azure-resource-manager/resource-group-authoring-templates.md) （创作 Azure Resource Manager 模板）。 

## <a name="in-this-tutorial"></a>本教程的内容
| 实体 | 说明 |
| --- | --- |
| Azure 存储链接服务 |将 Azure 存储帐户链接到数据工厂。 Azure 存储帐户保留本示例中管道的输入和输出数据。 |
| HDInsight 按需链接服务 |将按需 HDInsight 群集链接到数据工厂。 系统会自动创建群集来处理数据，完成处理后会删除群集。 |
| Azure Blob 输入数据集 |引用 Azure 存储链接服务。 链接服务引用 Azure 存储帐户，Azure Blob 数据集指定用于保存输入数据的存储中的容器、文件夹和文件名。 |
| Azure Blob 输出数据集 |引用 Azure 存储链接服务。 链接服务引用 Azure 存储帐户，Azure Blob 数据集指定用于保存输出数据的存储中的容器、文件夹和文件名。 |
| 数据管道 |管道包含一个使用输入数据集并生成输出数据集的、HDInsightHive 类型的活动。 |

数据工厂可以包含一个或多个数据管道。 管道可以包含一个或多个活动。 有两种类型的活动：[数据移动活动](data-factory-data-movement-activities.md)和[数据转换活动](data-factory-data-transformation-activities.md)。 本教程将创建包含一个活动（复制活动）的管道。

以下部分提供了用于定义数据工厂实体的完整 Resource Manager 模板，以便你可以快速完成整个教程并测试模板。 若要了解每个数据工厂实体的定义方式，请参阅[模板中的数据工厂实体](#data-factory-entities-in-the-template)部分。

## <a name="data-factory-json-template"></a>数据工厂 JSON 模板
用于定义数据工厂的顶级 Resource Manager 模板是： 

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": { ...
    },
    "variables": { ...
    },
    "resources": [
        {
            "name": "[parameters('dataFactoryName')]",
            "apiVersion": "[variables('apiVersion')]",
            "type": "Microsoft.DataFactory/datafactories",
            "location": "westus",
            "resources": [
                { ... },
                { ... },
                { ... },
                { ... }
            ]
        }
    ]
}
```
在 **C:\ADFGetStarted** 文件夹中，创建包含以下内容的名为 **ADFTutorialARM.json** 的 JSON 文件：

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": {
        "storageAccountName": { "type": "string", "metadata": { "description": "Name of the Azure storage account that contains the input/output data." } },
          "storageAccountKey": { "type": "securestring", "metadata": { "description": "Key for the Azure storage account." } },
          "blobContainer": { "type": "string", "metadata": { "description": "Name of the blob container in the Azure Storage account." } },
          "inputBlobFolder": { "type": "string", "metadata": { "description": "The folder in the blob container that has the input file." } },
          "inputBlobName": { "type": "string", "metadata": { "description": "Name of the input file/blob." } },
          "outputBlobFolder": { "type": "string", "metadata": { "description": "The folder in the blob container that will hold the transformed data." } },
          "hiveScriptFolder": { "type": "string", "metadata": { "description": "The folder in the blob container that contains the Hive query file." } },
          "hiveScriptFile": { "type": "string", "metadata": { "description": "Name of the hive query (HQL) file." } }
    },
    "variables": {
          "dataFactoryName": "[concat('HiveTransformDF', uniqueString(resourceGroup().id))]",
          "azureStorageLinkedServiceName": "AzureStorageLinkedService",
          "hdInsightOnDemandLinkedServiceName": "HDInsightOnDemandLinkedService",
          "blobInputDatasetName": "AzureBlobInput",
          "blobOutputDatasetName": "AzureBlobOutput",
          "pipelineName": "HiveTransformPipeline"
    },
    "resources": [
      {
        "name": "[variables('dataFactoryName')]",
        "apiVersion": "2015-10-01",
        "type": "Microsoft.DataFactory/datafactories",
        "location": "West US",
        "resources": [
          {
            "type": "linkedservices",
            "name": "[variables('azureStorageLinkedServiceName')]",
            "dependsOn": [
                  "[variables('dataFactoryName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
                  "type": "AzureStorage",
                  "description": "Azure Storage linked service",
                  "typeProperties": {
                    "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
                  }
            }
          },
          {
            "type": "linkedservices",
            "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
            "dependsOn": [
                  "[variables('dataFactoryName')]",
                  "[variables('azureStorageLinkedServiceName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
                  "type": "HDInsightOnDemand",
                  "typeProperties": {
                    "clusterSize": 1,
                    "version": "3.2",
                    "timeToLive": "00:05:00",
                    "osType": "windows",
                    "linkedServiceName": "[variables('azureStorageLinkedServiceName')]"
                  }
            }
          },
          {
            "type": "datasets",
            "name": "[variables('blobInputDatasetName')]",
            "dependsOn": [
                  "[variables('dataFactoryName')]",
                  "[variables('azureStorageLinkedServiceName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
                  "type": "AzureBlob",
                  "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
                  "typeProperties": {
                    "fileName": "[parameters('inputBlobName')]",
                    "folderPath": "[concat(parameters('blobContainer'), '/', parameters('inputBlobFolder'))]",
                    "format": {
                          "type": "TextFormat",
                          "columnDelimiter": ","
                    }
                  },
                  "availability": {
                    "frequency": "Month",
                    "interval": 1
                  },
                  "external": true
            }
          },
          {
            "type": "datasets",
            "name": "[variables('blobOutputDatasetName')]",
            "dependsOn": [
                  "[variables('dataFactoryName')]",
                  "[variables('azureStorageLinkedServiceName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
                  "type": "AzureBlob",
                  "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
                  "typeProperties": {
                    "folderPath": "[concat(parameters('blobContainer'), '/', parameters('outputBlobFolder'))]",
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
            "type": "datapipelines",
            "name": "[variables('pipelineName')]",
            "dependsOn": [
                  "[variables('dataFactoryName')]",
                  "[variables('azureStorageLinkedServiceName')]",
                  "[variables('hdInsightOnDemandLinkedServiceName')]",
                  "[variables('blobInputDatasetName')]",
                  "[variables('blobOutputDatasetName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
                  "description": "Pipeline that transforms data using Hive script.",
                  "activities": [
                {
                      "type": "HDInsightHive",
                      "typeProperties": {
                        "scriptPath": "[concat(parameters('blobContainer'), '/', parameters('hiveScriptFolder'), '/', parameters('hiveScriptFile'))]",
                        "scriptLinkedService": "[variables('azureStorageLinkedServiceName')]",
                        "defines": {
                              "inputtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('inputBlobFolder'))]",
                              "partitionedtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('outputBlobFolder'))]"
                        }
                      },
                      "inputs": [
                        {
                              "name": "[variables('blobInputDatasetName')]"
                        }
                      ],
                      "outputs": [
                        {
                              "name": "[variables('blobOutputDatasetName')]"
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
                      "linkedServiceName": "[variables('hdInsightOnDemandLinkedServiceName')]"
                }
                  ],
                  "start": "2016-10-01T00:00:00Z",
                  "end": "2016-10-02T00:00:00Z",
                  "isPaused": false
              }
          }
        ]
      }
    ]
}
```

> [!NOTE]
> 可以在 [Tutorial: Create a pipeline with Copy Activity using an Azure Resource Manager template](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)（教程：使用 Azure Resource Manager 模板创建包含复制活动的管道）中找到用于创建 Azure 数据工厂的另一个 Resource Manager 模板示例。  
> 
> 

## <a name="parameters-json"></a>参数 JSON
创建名为 **ADFTutorialARM-Parameters.json**、包含 Azure Resource Manager 模板参数的 JSON 文件。  

> [!IMPORTANT]
> 为此参数文件中的 **storageAccountName** 和 **storageAccountKey** 参数指定 Azure 存储帐户的名称和密钥。 
> 
> 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountName": {
            "value": "<Name of your Azure Storage account>"
        },
        "storageAccountKey": {
            "value": "<Key of your Azure Storage account>"
        },
        "blobContainer": {
            "value": "adfgetstarted"
        },
        "inputBlobFolder": {
            "value": "inputdata"
        },
        "inputBlobName": {
            "value": "input.log"
        },
        "outputBlobFolder": {
            "value": "partitioneddata"
        },
        "hiveScriptFolder": {
              "value": "script"
        },
        "hiveScriptFile": {
              "value": "partitionweblogs.hql"
        }
    }
}
```

> [!IMPORTANT]
> 在同一个数据工厂 JSON 模板中，可为开发、测试和生产环境使用不同的参数 JSON 文件。 使用 Power Shell 脚本可在这些环境中自动部署数据工厂实体。 
> 
> 

## <a name="create-data-factory"></a>创建数据工厂
1. 启动 **Azure PowerShell** 并运行以下命令： 
   * 运行以下命令并输入用于登录 Azure 门户的用户名和密码。
    ```PowerShell
    Login-AzureRmAccount
    ```  
   * 运行以下命令查看此帐户的所有订阅。
    ```PowerShell
    Get-AzureRmSubscription
    ``` 
   * 运行以下命令选择要使用的订阅。 此订阅应与 Azure 门户中使用的订阅相同。
    ```
    Get-AzureRmSubscription -SubscriptionName <SUBSCRIPTION NAME> | Set-AzureRmContext
    ```   
2. 运行以下命令，使用步骤 1 中创建的 Resource Manager 模板来部署数据工厂实体。 

    ```PowerShell
    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFTutorialARM.json -TemplateParameterFile C:\ADFGetStarted\ADFTutorialARM-Parameters.json
    ```

## <a name="monitor-pipeline"></a>监视管道
1. 登录到 [Azure 门户](https://portal.azure.com/)后，单击“浏览”，然后选择“数据工厂”。
     ![“浏览”->“数据工厂”](./media/data-factory-build-your-first-pipeline-using-arm/BrowseDataFactories.png)
2. 在“数据工厂”边栏选项卡中，单击创建的数据工厂 (**TutorialFactoryARM**)。    
3. 在数据工厂的“数据工厂”边栏选项卡中，单击“图示”。

     ![图示磁贴](./media/data-factory-build-your-first-pipeline-using-arm/DiagramTile.png)
4. 在“图示视图”中，可以看到管道的概述，以及本教程中使用的数据集。
   
   ![图示视图](./media/data-factory-build-your-first-pipeline-using-arm/DiagramView.png) 
5. 在“图示视图”中，双击数据集 **AzureBlobOutput**。 此时将显示当前正在处理的切片。
   
    ![数据集](./media/data-factory-build-your-first-pipeline-using-arm/AzureBlobOutput.png)
6. 处理完成后，可以看到切片处于“就绪”状态。 创建按需 HDInsight 群集通常需要一段时间（大约 20 分钟）。 因此，预期管道需要花费 **大约 30 分钟** 来处理切片。
   
    ![数据集](./media/data-factory-build-your-first-pipeline-using-arm/SliceReady.png)    
7. 当切片处于“就绪”状态时，检查 Blob 存储中 **adfgetstarted** 容器内 **partitioneddata** 文件夹的输出数据。  

有关如何使用 Azure 门户边栏选项卡监视本教程中所创建管道和数据集的说明，请参阅 [Monitor datasets and pipeline](data-factory-monitor-manage-pipelines.md) （监视数据集和管道）。

也可以使用监视和管理应用来监视数据管道。 有关使用该应用程序的详细信息，请参阅 [Monitor and manage Azure Data Factory pipelines using Monitoring App](data-factory-monitor-manage-app.md) （使用监视应用来监视和管理 Azure 数据工厂管道）。 

> [!IMPORTANT]
> 成功处理切片后，将会删除输入文件。 因此，如果想要重新运行切片或重新学习本教程，请将输入文件 (input.log) 上载到 adfgetstarted 容器的 inputdata 文件夹中。
> 
> 

## <a name="data-factory-entities-in-the-template"></a>模板中的数据工厂实体
### <a name="define-data-factory"></a>定义数据工厂
如以下示例中所示，在 Resource Manager 模板中定义数据工厂：  

```json
"resources": [
{
    "name": "[variables('dataFactoryName')]",
    "apiVersion": "2015-10-01",
    "type": "Microsoft.DataFactory/datafactories",
    "location": "West US"
}
```
dataFactoryName 定义为： 

```json
"dataFactoryName": "[concat('HiveTransformDF', uniqueString(resourceGroup().id))]",
```
它是基于资源组 ID 的唯一字符串。  

### <a name="defining-data-factory-entities"></a>定义数据工厂实体
JSON 模板中定义了以下数据工厂实体： 

* [Azure 存储链接服务](#azure-storage-linked-service)
* [HDInsight 按需链接服务](#hdinsight-on-demand-linked-service)
* [Azure Blob 输入数据集](#azure-blob-input-dataset)
* [Azure Blob 输出数据集](#azure-blob-output-dataset)
* [包含复制活动的数据管道](#data-pipeline)

#### <a name="azure-storage-linked-service"></a>Azure 存储链接服务
在本部分中指定 Azure 存储帐户的名称和密钥。 有关用于定义 Azure 存储链接服务的 JSON 属性的详细信息。请参阅 [Azure Storage linked service](data-factory-azure-blob-connector.md#azure-storage-linked-service)（Azure 存储链接服务）。 

```json
{
    "type": "linkedservices",
    "name": "[variables('azureStorageLinkedServiceName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "type": "AzureStorage",
        "description": "Azure Storage linked service",
        "typeProperties": {
            "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
        }
    }
}
```
**connectionString** 使用 storageAccountName 和 storageAccountKey 参数。 可以使用配置文件传递这些参数的值。 该定义还使用了模板中定义的变量 azureStroageLinkedService 和 dataFactoryName。 

#### <a name="hdinsight-on-demand-linked-service"></a>HDInsight 按需链接服务
有关用于定义 HDInsight 按需链接服务的 JSON 属性的详细信息。请参阅 [Compute linked services](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)（计算链接服务）。  

```json
{
    "type": "linkedservices",
    "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "clusterSize": 1,
            "version": "3.2",
            "timeToLive": "00:05:00",
            "osType": "windows",
            "linkedServiceName": "[variables('azureStorageLinkedServiceName')]"
        }
    }
}
```
注意以下几点： 

* 数据工厂使用上述 JSON 创建 **基于 Windows 的** HDInsight 群集。 也可以让它创建 **基于 Linux 的** HDInsight 群集。 有关详细信息，请参阅 [On-demand HDInsight Linked Service](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) （按需 HDInsight 链接服务）。 
* 可以使用 **自己的 HDInsight 群集** ，而不使用按需 HDInsight 群集。 有关详细信息，请参阅 [HDInsight Linked Service](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) （HDInsight 链接服务）。
* HDInsight 群集在 JSON 中指定的 Blob 存储 (**linkedServiceName**).内创建**默认容器**。 HDInsight 不会在删除群集时删除此容器。 这是设计的行为。 使用按需 HDInsight 链接服务时，除非有现有的实时群集 (**timeToLive**)，否则每当需要处理切片时会创建 HDInsight 群集；并在处理完成后删除该群集。
  
    随着处理的切片越来越多，Azure Blob 存储中会出现大量的容器。 如果不需要使用它们对作业进行故障排除，则可能需要删除它们以降低存储成本。 这些容器的名称遵循模式：“adf**yourdatafactoryname**-**linkedservicename**-datetimestamp”。 使用 [Microsoft 存储资源管理器](http://storageexplorer.com/) 等工具删除 Azure Blob 存储中的容器。

有关详细信息，请参阅 [On-demand HDInsight Linked Service](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) （按需 HDInsight 链接服务）。

#### <a name="azure-blob-input-dataset"></a>Azure Blob 输入数据集
指定包含输入数据的 Blob 容器、文件夹和文件的名称。 有关用于定义 Azure Blob 数据集的 JSON 属性的详细信息，请参阅 [Azure Blob dataset properties](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties)（Azure Blob 数据集属性）。 

```json
{
    "type": "datasets",
    "name": "[variables('blobInputDatasetName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
        "[variables('azureStorageLinkedServiceName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
        "typeProperties": {
            "fileName": "[parameters('inputBlobName')]",
            "folderPath": "[concat(parameters('blobContainer'), '/', parameters('inputBlobFolder'))]",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        },
        "external": true
    }
}
```
此定义使用参数模板中定义的以下参数：blobContainer、inputBlobFolder 和 inputBlobName。 

#### <a name="azure-blob-output-dataset"></a>Azure Blob 输出数据集
指定用于保存输出数据的 Blob 容器和文件夹的名称。 有关用于定义 Azure Blob 数据集的 JSON 属性的详细信息，请参阅 [Azure Blob dataset properties](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties)（Azure Blob 数据集属性）。  

```json
{
    "type": "datasets",
    "name": "[variables('blobOutputDatasetName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
        "[variables('azureStorageLinkedServiceName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
        "typeProperties": {
            "folderPath": "[concat(parameters('blobContainer'), '/', parameters('outputBlobFolder'))]",
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

此定义使用参数模板中定义的以下参数：blobContainer 和 outputBlobFolder。 

#### <a name="data-pipeline"></a>数据管道
定义一个管道，用于在按需 Azure HDInsight 群集上通过运行 Hive 脚本来转换数据。 有关用于定义本示例中所述管道的 JSON 元素的说明，请参阅 [Pipeline JSON](data-factory-create-pipelines.md#pipeline-json)（管道 JSON）。 

```json
{
    "type": "datapipelines",
    "name": "[variables('pipelineName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
        "[variables('azureStorageLinkedServiceName')]",
        "[variables('hdInsightOnDemandLinkedServiceName')]",
        "[variables('blobInputDatasetName')]",
        "[variables('blobOutputDatasetName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "description": "Pipeline that transforms data using Hive script.",
        "activities": [
        {
            "type": "HDInsightHive",
            "typeProperties": {
                "scriptPath": "[concat(parameters('blobContainer'), '/', parameters('hiveScriptFolder'), '/', parameters('hiveScriptFile'))]",
                "scriptLinkedService": "[variables('azureStorageLinkedServiceName')]",
                "defines": {
                    "inputtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('inputBlobFolder'))]",
                    "partitionedtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('outputBlobFolder'))]"
                }
            },
            "inputs": [
            {
                "name": "[variables('blobInputDatasetName')]"
            }
            ],
            "outputs": [
            {
                "name": "[variables('blobOutputDatasetName')]"
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
            "linkedServiceName": "[variables('hdInsightOnDemandLinkedServiceName')]"
        }
        ],
        "start": "2016-10-01T00:00:00Z",
        "end": "2016-10-02T00:00:00Z",
        "isPaused": false
    }
}
```

## <a name="reuse-the-template"></a>重复使用模板
本教程创建了一个用于定义数据工厂实体的模板，以及一个用于传递参数值的模板。 若要使用同一个模板将数据工厂实体部署到不同的环境，可为每个环境创建一个参数文件，然后在部署到该环境时使用该文件。     

示例：  

```PowerShell
New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Dev.json

New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Test.json

New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Production.json
```
请注意，三条命令分别使用开发环境、测试环境和生产环境的参数文件。  

可以重复使用该模板来执行重复的任务。 例如，你需要创建许多数据工厂，其中包含用于实现相同逻辑的一个或多个管道，但每个数据工厂使用不同的 Azure 存储和 Azure SQL 数据库帐户。 在这种情况下，可以在同一个环境（开发、测试或生产）中使用包含不同参数文件的同一个模板来创建数据工厂。 

## <a name="resource-manager-template-for-creating-a-gateway"></a>用于创建网关的 Resource Manager 模板
下面是用于在后端创建逻辑网关的示例 Resource Manager 模板。 在本地计算机或 Azure IaaS VM 上安装网关，然后使用密钥向数据工厂服务注册该网关。 有关详细信息，请参阅 [Move data between on-premises and cloud](data-factory-move-data-between-onprem-and-cloud.md) （在本地与云之间移动数据）。

```json
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
```
此模板使用名为 GatewayUsingARM 的网关创建名为 GatewayUsingArmDF 的数据工厂。 

## <a name="see-also"></a>另请参阅
| 主题 | 说明 |
|:--- |:--- |
| [Data Transformation Activities](data-factory-data-transformation-activities.md) |此文提供 Azure 数据工厂支持的数据转换活动列表（例如本教程中使用的 HDInsight Hive 转换）。 |
| [Scheduling and execution](data-factory-scheduling-and-execution.md) |本文介绍 Azure 数据工厂应用程序模型的计划方面和执行方面。 |
| [管道](data-factory-create-pipelines.md) |帮助你了解 Azure 数据工厂中的管道和活动，以及如何利用它们为方案或业务构造端对端数据驱动工作流。 |
| [数据集](data-factory-create-datasets.md) |还有助于了解 Azure 数据工厂中的数据集。 |
| [使用监视应用监视和管理管道](data-factory-monitor-manage-app.md) |本文介绍如何使用监视和管理应用来监视、管理和调试管道。 |




<!--HONumber=Dec16_HO4-->


