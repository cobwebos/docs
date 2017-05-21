---
title: "将 Azure 资源部署到多个资源组 | Microsoft Docs"
description: "介绍了在部署期间如何将多个 Azure 资源组作为目标。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/11/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 942e3b4fbc9a954eae0cc609e800d0fe0b882475
ms.contentlocale: zh-cn
ms.lasthandoff: 05/11/2017


---

# <a name="deploy-azure-resources-to-more-than-one-resource-group"></a>将 Azure 资源部署到多个资源组

通常情况下，你将模板中的所有资源部署到单个资源组。 不过，在某些情况下，你可能希望将一组资源部署在一起但将其放置在不同的资源组中。 例如，你可能希望将 Azure Site Recovery 的备份虚拟机部署到一个单独的资源组和位置。 Resource Manager 允许你使用嵌套的模板将不同于父模板所用资源组的多个不同资源组作为目标。

资源组是应用程序及其资源集合的生命周期容器。 你将在模板外部创建资源组，并指定在部署期间要用作目标的资源组。 有关资源组的简介，请参阅 [Azure Resource Manager 概述](resource-group-overview.md)。

## <a name="example-template"></a>示例模板

若要将不同的资源作为目标，在部署期间必须使用嵌套的或链接的模板。 `Microsoft.Resources/deployments` 资源类型提供了一个 `resourceGroup` 参数，该参数允许你指定与父模板所用资源组不同的资源组。 在运行部署之前，所有资源组都必须存在。 下面的示例部署两个存储帐户 - 一个在部署期间指定的资源组中，另一个在名为 `crossResourceGroupDeployment` 的资源组中：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "StorageAccountName1": {
            "type": "string"
        },
        "StorageAccountName2": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "nestedTemplate",
            "type": "Microsoft.Resources/deployments",
            "resourceGroup": "crossResourceGroupDeployment",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "type": "Microsoft.Storage/storageAccounts",
                            "name": "[parameters('StorageAccountName2')]",
                            "apiVersion": "2015-06-15",
                            "location": "West US",
                            "properties": {
                                "accountType": "Standard_LRS"
                            }
                        }
                    ]
                },
                "parameters": {}
            }
        },
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('StorageAccountName1')]",
            "apiVersion": "2015-06-15",
            "location": "West US",
            "properties": {
                "accountType": "Standard_LRS"
            }
        }
    ]
}
```

如果将 `resourceGroup` 设置为不存在的资源组的名称，则部署将失败。 如果没有为 `resourceGroup` 提供值，则 Resource Manager 将使用父资源组。  

## <a name="deploy-the-template"></a>部署模板

若要部署示例模板，可以使用 Azure PowerShell 或 Azure CLI。 必须使用 2017 年 5 月或之后发布的 Azure PowerShell 或 Azure CLI 版本。 这些示例假定你已在本地将模板保存为名为 **crossrgdeployment.json** 的文件。

对于 PowerShell：

```powershell
Login-AzureRmAccount

New-AzureRmResourceGroup -Name mainResourceGroup -Location "South Central US"
New-AzureRmResourceGroup -Name crossResourceGroupDeployment -Location "Central US"
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName mainResourceGroup `
  -TemplateFile c:\MyTemplates\crossrgdeployment.json
```

对于 Azure CLI：

```azurecli
az login

az group create --name mainResourceGroup --location "South Central US"
az group create --name crossResourceGroupDeployment --location "Central US"
az group deployment create \
    --name ExampleDeployment \
    --resource-group mainResourceGroup \
    --template-file crossrgdeployment.json
```

在部署完成后，可以看到两个资源组。 每个资源组包含一个存储帐户。

## <a name="next-steps"></a>后续步骤

* 若要了解如何在模板中定义参数，请参阅[了解 Azure Resource Manager 模板的结构和语法](resource-group-authoring-templates.md)。
* 有关解决常见部署错误的提示，请参阅[排查使用 Azure Resource Manager 时的常见 Azure 部署错误](resource-manager-common-deployment-errors.md)。
* 有关部署需要 SAS 令牌的模板的信息，请参阅[使用 SAS 令牌部署专用模板](resource-manager-powershell-sas-token.md)。
