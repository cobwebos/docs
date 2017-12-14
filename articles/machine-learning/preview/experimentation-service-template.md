---
title: "使用 Azure 资源管理器模板创建 Azure 机器学习试验 | Microsoft 文档"
description: "本文提供了一个示例，使用 Azure 资源管理器模板创建 Azure 机器学习试验帐户。"
services: machine-learning
author: ahgyger
ms.author: ahgyger
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/14/2017
ms.openlocfilehash: c7f4bf0fed35ffb2c03e8b983260c6093032fe79
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2017
---
# <a name="configure-the-azure-machine-learning-experimentation-service"></a>配置 Azure 机器学习试验服务

## <a name="overview"></a>概述
Azure 机器学习试验服务帐户、工作区和项目都是 Azure 资源。 在这种情况下，它们可以使用资源管理器模板部署。 Resource Manager 模板为 JSON 文件，用于定义针对解决方案进行部署时所需的资源。 若要了解与部署和管理 Azure 解决方案相关联的概念，请参阅 [Azure 资源管理器概述](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)。

## <a name="deploy-a-template"></a>部署模板
部署模板只需在 Azure 命令行接口，或者在 Azure 门户中执行几个步骤即可完成。

### <a name="deploy-a-template-from-the-command-line"></a>从命令行部署模板
使用命令行接口，单个命令即可将模板部署到现有资源组。
请参阅以下有关创建模板的信息。

```sh
# Login and validate your are in the right subscription context
az login

# Create a new resource group (you can use an existing one)
az group create --name <resource group name> --location <supported Azure region>
az group deployment create -n testdeploy -g <resource group name> --template-file <template-file.json> --parameters <parameters.json>
```

### <a name="deploy-a-template-from-the-azure-portal"></a>从 Azure 门户部署模板
如果你愿意，还可以使用 Azure 门户创建和部署模板。 请按如下所述执行此操作：

1. 导航到 [Azure 门户](https://portal.azure.com)。
2. 选择“所有服务”，然后搜索“模板”。
3. 选择“模板”。
4. 单击“+ 添加”，然后复制模板信息。 
5. 选择在步骤 4 中创建的模板，然后单击“部署”。


## <a name="create-a-template-from-an-existing-azure-resource-in-the-azure-portal"></a>从 Azure 门户中的现有 Azure 资源创建模板
如果在 [Azure 门户](https://portal.azure.com)中已有可用的 Azure 机器试验帐户，则可以从该资源生成模板。 

1. 导航到 [Azure 门户](https://portal.azure.com)中的 Azure 试验帐户。
2. 在“设置”下，单击“自动化脚本”。
3. 下载模板。 

或者，你也可以手动编辑模板文件。 请参阅以下有关模板和参数文件的示例。 

### <a name="template-file-example"></a>模板文件示例
创建带有以下内容的“template-file.json”文件。 

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": {
        "accountName": {
            "type": "string",
            "metadata": {
                "description": "Name of the machine learning experimentation team account"
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location of the machine learning experimentation account and other dependent resources."
            }
        },
        "storageAccountSku": {
            "type": "string",
            "defaultValue": "Standard_LRS",
            "metadata": {
                "description": "Sku of the storage account"
            }
        }
    },
    "variables": {
        "mlexpVersion": "2017-05-01-preview",
        "stgResourceId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))]"
    },
    "resources": [
        {
            "name": "[parameters('accountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[parameters('location')]",
            "apiVersion": "2016-12-01",
            "tags": {
                "mlteamAccount": "[parameters('accountName')]"
            },
            "sku": {
                "name": "[parameters('storageAccountSku')]"
            },
            "kind": "Storage",
            "properties": {
                "encryption": {
                    "services": {
                        "blob": {
                            "enabled": true
                        }
                    },
                    "keySource": "Microsoft.Storage"
                }
            }
        },
        {
            "apiVersion": "[variables('mlexpVersion')]",
            "type": "Microsoft.MachineLearningExperimentation/accounts",
            "name": "[parameters('accountName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))]"
            ],
            "properties": {
                "storageAccount": {
                    "storageAccountId": "[variables('stgResourceId')]",
                    "accessKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('accountName')), '2016-12-01').keys[0].value]"
                }
            }
        }
    ]
}
```

### <a name="parameters"></a>parameters 
创建具有以下内容的文件并将其保存为 <parameters.json>。 

有三个可以更改的值。 
* AccountName：试验帐户的名称。
* Location：受支持的 Azure 区域之一。
* Storage Account SKU：Azure ML 仅支持标准存储，而不支持高级存储。 有关存储的详细信息，请参阅[存储简介](https://docs.microsoft.com/azure/storage/common/storage-introduction)。 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "accountName": {
         "value": "MyExperimentationAccount"
     },
     "location": {
         "value": "eastus2"
     },
     "storageAccountSku": {
         "value": "Standard_LRS"
     }
  }
}
```

## <a name="next-steps"></a>后续步骤
* [创建和安装 Azure 机器学习](quickstart-installation.md)
