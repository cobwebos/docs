---
title: "在数据工厂中使用 Resource Manager 模板 | Microsoft Docs"
description: "了解如何创建和使用 Azure Resource Manager 模板来创建数据工厂实体。"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: 
ms.assetid: 37724021-f55f-4e85-9206-6d4a48bda3d8
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2017
ms.author: shlo
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 2faab833724bf4bb3f3262517e1d724868f7524d
ms.lasthandoff: 03/29/2017


---
# <a name="use-templates-to-create-azure-data-factory-entities"></a>使用模板来创建 Azure 数据工厂实体
## <a name="overview"></a>概述
使用 Azure 数据工厂实现数据集成需求时，可能发现自己在不同环境中重复使用同一模式，或在同一解决方案内重复执行相同任务。 使用模板可轻松实现和管理这些方案。 Azure 数据工厂中的模板非常适合于涉及可重用性和重复的情况。

请考虑此情况：某组织在世界各地拥有 10 个制造工厂。 每个工厂的日志记录均存储在单独的本地 SQL Server 数据库中。 该公司希望在云中构建一个用于即席分析的数据仓库。 还希望开发、测试和生产环境具有相同逻辑但具有不同配置。

在此情况下，需在相同环境中重复同一任务，但 10 个制造工厂的每个数据工厂具有不同值。 总之，存在**重复**。 模板化允许将此泛型流抽象化（即，管道在每个数据工厂中具有相同活动），但对每个制造工厂使用单独的参数文件。

此外，鉴于该组织希望在不同环境内多次部署这 10 个数据工厂，因此模板可通过利用开发、测试和生产环境的单独参数文件来使用此**可重用性**。

## <a name="templating-with-azure-resource-manager"></a>通过 Azure Resource Manager 实现模板化
[Azure Resource Manager 模板](../azure-resource-manager/resource-group-overview.md#template-deployment)是实现 Azure 数据工厂模板化的好办法。 Resource Manager 模板通过 JSON 文件定义 Azure 解决方案的基础结构和配置。 由于 Azure Resource Manager 模板适用于所有/多数 Azure 服务，因此可广泛用于轻松管理 Azure 资产的所有资源。 若要深入了解常用的 Resource Manager 模板，请参阅[创作 Azure Resource Manager 模板](../azure-resource-manager/resource-group-authoring-templates.md)。

## <a name="tutorials"></a>教程
请参阅以下教程，获取使用 Resource Manager 模板创建数据工厂实体的分步说明：

* [教程：使用 Azure Resource Manager 模板创建复制数据的管道](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
* [教程：使用 Azure Resource Manager 模板创建处理数据的管道](data-factory-build-your-first-pipeline.md)

## <a name="data-factory-templates-on-github"></a>GitHub 上的数据工厂模板
请查看 GitHub 上的以下 Azure 快速入门模板：

* [创建数据工厂，将数据从 Azure Blob 存储复制到 Azure SQL 数据库](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy)
* [创建包含 Azure HDInsight 群集上的 Hive 活动的数据工厂](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation)
* [创建数据工厂，将数据从 Salesforce 复制到 Azure Blob](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy)
* [创建可链接活动的数据工厂：将数据从 FTP 服务器复制到 Azure Blob，调用按需 HDInsight 群集上的 Hive 脚本来转换数据，并将结果复制到 Azure SQL 数据库](https://github.com/Azure/azure-quickstart-templates/tree/master/201-data-factory-ftp-hive-blob)

随意共享 [Azure 快速启动](https://azure.microsoft.com/documentation/templates/)处的 Azure 数据工厂模板。 开发可通过此存储库共享的模板时，请参考 [contribution guide](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE)（贡献指南）。

下面部分详细介绍了如何定义 Resource Manager 模板中的数据工厂资源。

## <a name="defining-data-factory-resources-in-templates"></a>定义模板中的数据工厂资源
用于定义数据工厂的顶级模板是：

```JSON
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
    { "type": "linkedservices",
        ...
    },
    {"type": "datasets",
        ...
    },
    {"type": "dataPipelines",
        ...
    }
}
```

### <a name="define-data-factory"></a>定义数据工厂
如以下示例中所示，在 Resource Manager 模板中定义数据工厂：

```JSON
"resources": [
{
    "name": "[variables('<mydataFactoryName>')]",
    "apiVersion": "2015-10-01",
    "type": "Microsoft.DataFactory/datafactories",
    "location": "East US"
}
```
dataFactoryName 在“变量”中定义为：

```JSON
"dataFactoryName": "[concat('<myDataFactoryName>', uniqueString(resourceGroup().id))]",
```

### <a name="define-linked-services"></a>定义链接服务

```JSON
"type": "linkedservices",
"name": "[variables('<LinkedServiceName>')]",
"apiVersion": "2015-10-01",
"dependsOn": [ "[variables('<dataFactoryName>')]" ],
"properties": {
    ...
}
```

有关用于希望部署的特定链接服务的 JSON 属性详细信息，请参阅[存储链接服务](data-factory-azure-blob-connector.md#azure-storage-linked-service)或[计算链接服务](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)。 “dependsOn”参数指定相应数据工厂的名称。 以下 JSON 定义演示了如何为 Azure 存储定义链接服务：

### <a name="define-datasets"></a>定义数据集

```JSON
"type": "datasets",
"name": "[variables('<myDatasetName>')]",
"dependsOn": [
    "[variables('<dataFactoryName>')]",
    "[variables('<myDatasetLinkedServiceName>')]"
],
"apiVersion": "2015-10-01",
"properties": {
    ...
}
```
有关用于希望部署的特定数据集类型的 JSON 属性详细信息，请参阅[支持的数据存储](data-factory-data-movement-activities.md#supported-data-stores-and-formats)。 请注意，“dependsOn”参数指定相应数据工厂的名称和存储链接服务。 以下 JSON 定义演示了如何定义 Azure Blob 存储的数据集类型：

```JSON
"type": "datasets",
"name": "[variables('storageDataset')]",
"dependsOn": [
    "[variables('dataFactoryName')]",
    "[variables('storageLinkedServiceName')]"
],
"apiVersion": "2015-10-01",
"properties": {
"type": "AzureBlob",
"linkedServiceName": "[variables('storageLinkedServiceName')]",
"typeProperties": {
    "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
    "fileName": "[parameters('sourceBlobName')]",
    "format": {
        "type": "TextFormat"
    }
},
"availability": {
    "frequency": "Hour",
    "interval": 1
}
```

### <a name="define-pipelines"></a>定义管道

```JSON
"type": "dataPipelines",
"name": "[variables('<mypipelineName>')]",
"dependsOn": [
    "[variables('<dataFactoryName>')]",
    "[variables('<inputDatasetLinkedServiceName>')]",
    "[variables('<outputDatasetLinkedServiceName>')]",
    "[variables('<inputDataset>')]",
    "[variables('<outputDataset>')]"
],
"apiVersion": "2015-10-01",
"properties": {
    activities: {
        ...
    }
}
```

有关用于定义要部署的特定管道和活动的 JSON 属性详细信息，请参阅[定义管道](data-factory-create-pipelines.md#pipeline-json)。 请注意，“dependsOn”参数指定数据工厂名称和任何相应的链接服务或数据集。 以下 JSON 片段显示了将数据从 Azure Blob 存储复制到 Azure SQL 数据库的示例管道：

```JSON
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
    "start": "2016-10-03T00:00:00Z",
    "end": "2016-10-04T00:00:00Z"
}
```
## <a name="parameterizing-data-factory-template"></a>参数化数据工厂模板
有关参数化的最佳做法，请参阅[创建 Azure Resource Manager 模板的最佳做法](../azure-resource-manager/resource-manager-template-best-practices.md#parameters)一文。 通常，应将参数使用率降到最低，可改用变量时尤其如此。 仅在以下情况提供参数：

* 设置因环境（如：开发、测试和生产）而异
* 机密（如密码）

使用模板部署 Azure 数据工厂实体时，如需从 [Azure 密钥保管库](../key-vault/key-vault-get-started.md)拉取密钥，请指定**密钥保管库**和**密钥名称**，如下例所示：

```JSON
"parameters": {
    "storageAccountKey": {
        "reference": {
            "keyVault": {
                "id":"/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.KeyVault/vaults/<keyVaultName>",
             },
            "secretName": "<secretName>"
           },
       },
       ...
}
```

> [!NOTE]
> 尽管目前尚不支持导出现有数据工厂的模板，但正在积极筹备中。
>
>

