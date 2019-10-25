---
title: Azure 资源管理器模板的条件部署
description: 介绍如何在 Azure 资源管理器模板中有条件地部署资源。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: tomfitz
ms.openlocfilehash: b6d707fc4bbc5fa57ffb0c809d7f70efebef99e9
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2019
ms.locfileid: "72881665"
---
# <a name="conditional-deployment-in-resource-manager-templates"></a>资源管理器模板中的条件部署

有时，您需要根据需要在模板中部署资源。 使用 `condition` 元素指定是否部署资源。 此元素的值解析为 true 或 false。 如果值为 true，则创建了该资源。 如果值为 false，则未创建该资源。 值只能应用到整个资源。

## <a name="new-or-existing-resource"></a>新资源或现有资源

您可以使用条件部署来创建新资源或使用现有资源。 下面的示例演示如何使用条件来部署新的存储帐户或使用现有的存储帐户。

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[parameters('location')]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

如果将参数**newOrExisting**设置为**new**，则条件的计算结果为 true。 已部署存储帐户。 但是，当**newOrExisting**设置为 "**现有**" 时，条件的计算结果为 false，并且不部署存储帐户。

有关使用 `condition` 元素的完整示例模板，请参阅[具有新的或现有虚拟网络、存储和公共 IP 的 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions)。

## <a name="allow-condition"></a>允许条件

你可以传入一个参数值，该值指示是否允许某个条件。 下面的示例部署一个 SQL server，并可选择允许 Azure Ip。

```json
{
    "type": "Microsoft.Sql/servers",
    "name": "[parameters('serverName')]",
    "apiVersion": "2015-05-01-preview",
    "location": "[parameters('location')]",
    "properties": {
        "administratorLogin": "[parameters('administratorLogin')]",
        "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
        "version": "12.0"
    },
    "resources": [
        {
            "condition": "[parameters('allowAzureIPs')]",
            "type": "firewallRules",
            "name": "AllowAllWindowsAzureIps",
            "apiVersion": "2015-05-01-preview",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Sql/servers/', parameters('serverName'))]"
            ],
            "properties": {
                "endIpAddress": "0.0.0.0",
                "startIpAddress": "0.0.0.0"
            }
        }
    ]
}
```

有关完整的模板，请参阅[AZURE SQL 逻辑服务器](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server)。

## <a name="runtime-functions"></a>运行时函数

如果将[引用](resource-group-template-functions-resource.md#reference)或[列表](resource-group-template-functions-resource.md#list)函数与有条件部署的资源结合使用，则即使未部署资源，也会计算该函数。 如果函数引用的资源不存在，则会收到错误。

使用[if](resource-group-template-functions-logical.md#if)函数可确保仅在部署资源时计算函数的条件。 请参阅[if 函数](resource-group-template-functions-logical.md#if)以获取一个示例模板，该模板使用 if，并使用有条件部署的资源引用。

## <a name="condition-with-complete-mode"></a>具有完整模式的条件

如果使用 "[完整" 模式](deployment-modes.md)部署模板，而未部署资源，因为条件的计算结果为 false，则结果取决于用于部署模板的 REST API 版本。 如果使用早于2019-05-10 的版本，则不会**删除**该资源。 对于2019-05-10 或更高版本，将**删除**该资源。 当条件为 false 时，Azure PowerShell 和 Azure CLI 的最新版本将删除该资源。

## <a name="next-steps"></a>后续步骤

* 有关创建模板的建议，请参阅 [Azure 资源管理器模板的最佳做法](template-best-practices.md)。
* 若要创建资源的多个实例，请参阅[Azure 资源管理器模板中的资源、属性或可变迭代](resource-group-create-multiple.md)。