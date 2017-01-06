---
title: "教程：使用 Resource Manager 模板创建管道 | Microsoft 文档"
description: "本教程使用 Azure Resource Manager 模板创建包含复制活动的 Azure 数据工厂管道。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 1274e11a-e004-4df5-af07-850b2de7c15e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/10/2016
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: ebc5dbf790ca6012cfe9a7ea9ccee9fdacb46ffd
ms.openlocfilehash: 9ad4378ab27433858d14237fe451b16690711f3a


---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-azure-resource-manager-template"></a>教程：使用 Azure Resource Manager 模板创建包含复制活动的管道
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

本教程演示如何使用 Azure Resource Manager 模板创建和监视 Azure 数据工厂。 数据工厂中的管道将数据从 Azure Blob 存储复制到 SQL 数据库。

## <a name="prerequisites"></a>先决条件
* 通读[教程概述和先决条件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)，完成**先决条件**步骤。
* 遵循 [How to install and configure Azure PowerShell](/powershell/azureps-cmdlets-docs) （如何安装和配置 Azure PowerShell）一文中的说明，在计算机上安装最新版本的 Azure PowerShell。 本教程使用 PowerShell 部署数据工厂实体。 
* （可选）若要了解 Azure Resource Manager 模板，请参阅 [Authoring Azure Resource Manager Templates](../azure-resource-manager/resource-group-authoring-templates.md)（创作 Azure Resource Manager 模板）。

## <a name="in-this-tutorial"></a>本教程的内容
本教程创建包含以下数据工厂实体的数据工厂：

| 实体 | 说明 |
| --- | --- |
| Azure 存储链接服务 |将 Azure 存储帐户链接到数据工厂。 在本教程中，Azure 存储和 Azure SQL 数据库分别是复制活动的源数据存储和接收器数据存储。 该服务指定包含复制活动输入数据的存储帐户。 |
| Azure SQL 数据库链接服务 |将 Azure SQL 数据库链接到数据工厂。 该服务指定保存复制活动输出数据的 Azure SQL 数据库。 |
| Azure Blob 输入数据集 |引用 Azure 存储链接服务。 链接服务引用 Azure 存储帐户，Azure Blob 数据集指定用于保存输入数据的存储中的容器、文件夹和文件名。 |
| Azure SQL 输出数据集 |引用 Azure SQL 链接服务。 Azure SQL 链接服务引用 Azure SQL 服务器，Azure SQL 数据集指定保存输出数据的表的名称。 |
| 数据管道 |管道包含一个“复制”类型的活动，该活动使用 Azure Blob 数据集作为输入，使用 Azure SQL 数据集作为输出。 复制活动将数据从 Azure Blob 复制到 Azure SQL 数据库中的表。 |

数据工厂可以包含一个或多个数据管道。 管道可以包含一个或多个活动。 有两种类型的活动：[数据移动活动](data-factory-data-movement-activities.md)和[数据转换活动](data-factory-data-transformation-activities.md)。 本教程将创建包含一个活动（复制活动）的管道。

![将 Azure Blob 复制到 Azure SQL 数据库](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/CopyBlob2SqlDiagram.png) 

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
在 **C:\ADFGetStarted** 文件夹中，创建包含以下内容的名为 **ADFCopyTutorialARM.json** 的 JSON 文件：

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": {
      "storageAccountName": { "type": "string", "metadata": { "description": "Name of the Azure storage account that contains the data to be copied." } },
      "storageAccountKey": { "type": "securestring", "metadata": { "description": "Key for the Azure storage account." } },
      "sourceBlobContainer": { "type": "string", "metadata": { "description": "Name of the blob container in the Azure Storage account." } },
      "sourceBlobName": { "type": "string", "metadata": { "description": "Name of the blob in the container that has the data to be copied to Azure SQL Database table" } },
      "sqlServerName": { "type": "string", "metadata": { "description": "Name of the Azure SQL Server that will hold the output/copied data." } },
      "databaseName": { "type": "string", "metadata": { "description": "Name of the Azure SQL Database in the Azure SQL server." } },
      "sqlServerUserName": { "type": "string", "metadata": { "description": "Name of the user that has access to the Azure SQL server." } },
      "sqlServerPassword": { "type": "securestring", "metadata": { "description": "Password for the user." } },
      "targetSQLTable": { "type": "string", "metadata": { "description": "Table in the Azure SQL Database that will hold the copied data." } 
      } 
    },
    "variables": {
      "dataFactoryName": "[concat('AzureBlobToAzureSQLDatabaseDF', uniqueString(resourceGroup().id))]",
      "azureSqlLinkedServiceName": "AzureSqlLinkedService",
      "azureStorageLinkedServiceName": "AzureStorageLinkedService",
      "blobInputDatasetName": "BlobInputDataset",
      "sqlOutputDatasetName": "SQLOutputDataset",
      "pipelineName": "Blob2SQLPipeline"
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
            "name": "[variables('azureSqlLinkedServiceName')]",
            "dependsOn": [
              "[variables('dataFactoryName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
              "type": "AzureSqlDatabase",
              "description": "Azure SQL linked service",
              "typeProperties": {
                "connectionString": "[concat('Server=tcp:',parameters('sqlServerName'),'.database.windows.net,1433;Database=', parameters('databaseName'), ';User ID=',parameters('sqlServerUserName'),';Password=',parameters('sqlServerPassword'),';Trusted_Connection=False;Encrypt=True;Connection Timeout=30')]"
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
              "structure": [
                {
                  "name": "Column0",
                  "type": "String"
                },
                {
                  "name": "Column1",
                  "type": "String"
                }
              ],
              "typeProperties": {
                "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
                "fileName": "[parameters('sourceBlobName')]",
                "format": {
                  "type": "TextFormat",
                  "columnDelimiter": ","
                }
              },
              "availability": {
                "frequency": "Day",
                "interval": 1
              },
              "external": true
            }
          },
          {
            "type": "datasets",
            "name": "[variables('sqlOutputDatasetName')]",
            "dependsOn": [
              "[variables('dataFactoryName')]",
              "[variables('azureSqlLinkedServiceName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
              "type": "AzureSqlTable",
              "linkedServiceName": "[variables('azureSqlLinkedServiceName')]",
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
              "typeProperties": {
                "tableName": "[parameters('targetSQLTable')]"
              },
              "availability": {
                "frequency": "Day",
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
              "[variables('azureSqlLinkedServiceName')]",
              "[variables('blobInputDatasetName')]",
              "[variables('sqlOutputDatasetName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
              "activities": [
                {
                  "name": "CopyFromAzureBlobToAzureSQL",
                  "description": "Copy data frm Azure blob to Azure SQL",
                  "type": "Copy",
                  "inputs": [
                    {
                      "name": "[variables('blobInputDatasetName')]"
                    }
                  ],
                  "outputs": [
                    {
                      "name": "[variables('sqlOutputDatasetName')]"
                    }
                  ],
                  "typeProperties": {
                    "source": {
                      "type": "BlobSource"
                    },
                    "sink": {
                      "type": "SqlSink",
                      "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM {0}', 'emp')"
                    },
                    "translator": {
                      "type": "TabularTranslator",
                      "columnMappings": "Column0:FirstName,Column1:LastName"
                    }
                  },
                  "Policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 3,
                    "timeout": "01:00:00"
                  }
                }
              ],
              "start": "2016-10-02T00:00:00Z",
              "end": "2016-10-03T00:00:00Z"
            }
          }
        ]
      }
    ]
  }
```

## <a name="parameters-json"></a>参数 JSON
创建名为 **ADFCopyTutorialARM-Parameters.json**、包含 Azure Resource Manager 模板参数的 JSON 文件。 

> [!IMPORTANT]
> 为 **storageAccountName** 和 **storageAccountKey** 参数指定 Azure 存储帐户的名称和密钥。  
> 
> 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": { 
        "storageAccountName": { "value": "<Name of the Azure storage account>"    },
        "storageAccountKey": {
            "value": "<Key for the Azure storage account>"
        },
        "sourceBlobContainer": { "value": "adftutorial" },
        "sourceBlobName": { "value": "emp.txt" },
        "sqlServerName": { "value": "<Name of the Azure SQL server>" },
        "databaseName": { "value": "<Name of the Azure SQL database>" },
        "sqlServerUserName": { "value": "<Name of the user who has access to the Azure SQL database>" },
        "sqlServerPassword": { "value": "<password for the user>" },
        "targetSQLTable": { "value": "emp" }
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
   * 运行以下命令选择要使用的订阅。 
    ```PowerShell
    Get-AzureRmSubscription -SubscriptionName <SUBSCRIPTION NAME> | Set-AzureRmContext
    ```    
2. 运行以下命令，使用步骤 1 中创建的 Resource Manager 模板来部署数据工厂实体。

    ```PowerShell   
    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFCopyTutorialARM.json -TemplateParameterFile C:\ADFGetStarted\ADFCopyTutorialARM-Parameters.json
    ```

## <a name="monitor-pipeline"></a>监视管道

1. 使用 Azure 帐户登录到 [Azure 门户](https://portal.azure.com)。
2. 在左侧菜单中单击“数据工厂”；或者单击“更多服务”，然后单击“智能 + 分析”类别下面的“数据工厂”。
   
    ![数据工厂菜单](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/data-factories-menu.png)
3. 在“数据工厂”页中，搜索并查找你的数据工厂。 
   
    ![搜索数据工厂](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/search-for-data-factory.png)  
4. 单击你的 Azure 数据工厂。 随后将显示该数据工厂的主页。
   
    ![数据工厂主页](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/data-factory-home-page.png)  
5. 单击“图示”磁贴查看数据工厂的图示视图。
   
    ![数据工厂的图示视图](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/data-factory-diagram-view.png)
6. 在图示视图中，双击数据集 **SQLOutputDataset**。 随后将显示切片的状态。 完成复制操作后，状态将设置为“就绪”。
   
    ![输出切片处于就绪状态](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/output-slice-ready.png)
7. 切片处于“就绪”状态后，检查数据是否已复制到 Azure SQL 数据库中的 **emp** 表。

有关如何使用 Azure 门户边栏选项卡监视本教程中所创建管道和数据集的说明，请参阅 [Monitor datasets and pipeline](data-factory-monitor-manage-pipelines.md) （监视数据集和管道）。

也可以使用监视和管理应用来监视数据管道。 有关使用该应用程序的详细信息，请参阅 [Monitor and manage Azure Data Factory pipelines using Monitoring App](data-factory-monitor-manage-app.md) （使用监视应用来监视和管理 Azure 数据工厂管道）。

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
"dataFactoryName": "[concat('AzureBlobToAzureSQLDatabaseDF', uniqueString(resourceGroup().id))]"
```

它是基于资源组 ID 的唯一字符串。  

### <a name="defining-data-factory-entities"></a>定义数据工厂实体
JSON 模板中定义了以下数据工厂实体： 

1. [Azure 存储链接服务](#azure-storage-linked-service)
2. [Azure SQL 链接服务](#azure-sql-database-linked-service)
3. [Azure Blob 数据集](#azure-blob-dataset)
4. [Azure SQL 数据集](#azure-sql-dataset)
5. [包含复制活动的数据管道](#data-pipeline)

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

connectionString 使用 storageAccountName 和 storageAccountKey 参数。 可以使用配置文件传递这些参数的值。 该定义还使用了模板中定义的变量 azureStroageLinkedService 和 dataFactoryName。 

#### <a name="azure-sql-database-linked-service"></a>Azure SQL 数据库链接服务
在本部分中指定 Azure SQL 服务器名称、数据库名称、用户名和用户密码。 有关用于定义 Azure SQL 链接服务的 JSON 属性的详细信息。请参阅 [Azure SQL linked service](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties)（Azure SQL 链接服务）。  

```json
{
    "type": "linkedservices",
    "name": "[variables('azureSqlLinkedServiceName')]",
    "dependsOn": [
      "[variables('dataFactoryName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
          "type": "AzureSqlDatabase",
          "description": "Azure SQL linked service",
          "typeProperties": {
            "connectionString": "[concat('Server=tcp:',parameters('sqlServerName'),'.database.windows.net,1433;Database=', parameters('databaseName'), ';User ID=',parameters('sqlServerUserName'),';Password=',parameters('sqlServerPassword'),';Trusted_Connection=False;Encrypt=True;Connection Timeout=30')]"
          }
    }
}
```

connectionString 使用 sqlServerName、databaseName、sqlServerUserName 和 sqlServerPassword 参数，这些参数的值是使用配置文件传递的。 该定义还使用模板中的以下变量：azureSqlLinkedServiceName、dataFactoryName。

#### <a name="azure-blob-dataset"></a>Azure Blob 数据集
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
        "structure": [
        {
              "name": "Column0",
              "type": "String"
        },
        {
              "name": "Column1",
              "type": "String"
        }
          ],
          "typeProperties": {
            "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
            "fileName": "[parameters('sourceBlobName')]",
            "format": {
                  "type": "TextFormat",
                  "columnDelimiter": ","
            }
          },
          "availability": {
            "frequency": "Day",
            "interval": 1
          },
          "external": true
    }
}
```

#### <a name="azure-sql-dataset"></a>Azure SQL 数据集
指定 Azure SQL 数据库中用于保存从 Azure Blob 存储复制的数据的表的名称。 有关用于定义 Azure SQL 数据集的 JSON 属性的详细信息，请参阅 [Azure SQL dataset properties](data-factory-azure-sql-connector.md#azure-sql-dataset-type-properties)（Azure SQL 数据集属性）。 

```json
{
    "type": "datasets",
    "name": "[variables('sqlOutputDatasetName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
          "[variables('azureSqlLinkedServiceName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
          "type": "AzureSqlTable",
          "linkedServiceName": "[variables('azureSqlLinkedServiceName')]",
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
          "typeProperties": {
            "tableName": "[parameters('targetSQLTable')]"
          },
          "availability": {
            "frequency": "Day",
            "interval": 1
          }
    }
}
```

#### <a name="data-pipeline"></a>数据管道
定义将数据从 Azure blob 数据集复制到 Azure SQL 数据集的管道。 有关用于定义本示例中所述管道的 JSON 元素的说明，请参阅 [Pipeline JSON](data-factory-create-pipelines.md#pipeline-json)（管道 JSON）。 

```json
{
    "type": "datapipelines",
    "name": "[variables('pipelineName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
          "[variables('azureStorageLinkedServiceName')]",
          "[variables('azureSqlLinkedServiceName')]",
          "[variables('blobInputDatasetName')]",
          "[variables('sqlOutputDatasetName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
          "activities": [
        {
              "name": "CopyFromAzureBlobToAzureSQL",
              "description": "Copy data frm Azure blob to Azure SQL",
              "type": "Copy",
              "inputs": [
            {
                  "name": "[variables('blobInputDatasetName')]"
            }
              ],
              "outputs": [
            {
                  "name": "[variables('sqlOutputDatasetName')]"
            }
              ],
              "typeProperties": {
                "source": {
                      "type": "BlobSource"
                },
                "sink": {
                      "type": "SqlSink",
                      "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM {0}', 'emp')"
                },
                "translator": {
                      "type": "TabularTranslator",
                      "columnMappings": "Column0:FirstName,Column1:LastName"
                }
              },
              "Policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 3,
                "timeout": "01:00:00"
              }
        }
          ],
          "start": "2016-10-02T00:00:00Z",
          "end": "2016-10-03T00:00:00Z"
    }
}
```

## <a name="reuse-the-template"></a>重复使用模板
本教程创建了一个用于定义数据工厂实体的模板，以及一个用于传递参数值的模板。 管道将数据从 Azure 存储帐户复制到通过参数指定的 Azure SQL 数据库。 若要使用同一个模板将数据工厂实体部署到不同的环境，可为每个环境创建一个参数文件，然后在部署到该环境时使用该文件。     

示例：  

```PowerShell
New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFCopyTutorialARM.json -TemplateParameterFile ADFCopyTutorialARM-Parameters-Dev.json
```
```PowerShell
New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFCopyTutorialARM.json -TemplateParameterFile ADFCopyTutorialARM-Parameters-Test.json
```
```PowerShell
New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFCopyTutorialARM.json -TemplateParameterFile ADFCopyTutorialARM-Parameters-Production.json
```

请注意，三条命令分别使用开发环境、测试环境和生产环境的参数文件。  

可以重复使用该模板来执行重复的任务。 例如，你需要创建许多数据工厂，其中包含用于实现相同逻辑的一个或多个管道，但每个数据工厂使用不同的 Azure 存储和 Azure SQL 数据库帐户。 在这种情况下，可以在同一个环境（开发、测试或生产）中使用包含不同参数文件的同一个模板来创建数据工厂。   




<!--HONumber=Dec16_HO4-->


