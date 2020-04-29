---
title: 使用模板进行条件部署
description: 介绍如何在 Azure 资源管理器模板中有条件地部署资源。
ms.topic: conceptual
ms.date: 12/03/2019
ms.openlocfilehash: f170710118c0e3de6f3643b6216ed55b83b5c7df
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80153414"
---
# <a name="conditional-deployment-in-arm-templates"></a>ARM 模板中的条件部署

有时，你需要根据需要在 Azure 资源管理器（ARM）模板中部署资源。 使用 `condition` 元素指定是否部署资源。 此元素的值解析为 true 或 false。 如果值为 true，则创建了该资源。 如果值为 false，则未创建该资源。 值只能应用到整个资源。

## <a name="new-or-existing-resource"></a>新资源或现有资源

可以使用条件部署来创建新资源或使用现有资源。 以下示例演示如何使用条件部署新的存储帐户或使用现有存储帐户。

```json
{
  "condition": "[equals(parameters('newOrExisting'),'new')]",
  "type": "Microsoft.Storage/storageAccounts",
  "apiVersion": "2017-06-01",
  "name": "[variables('storageAccountName')]",
  "location": "[parameters('location')]",
  "sku": {
    "name": "[variables('storageAccountType')]"
  },
  "kind": "Storage",
  "properties": {}
}
```

当参数 **newOrExisting** 设置为 **new** 时，条件的计算结果为 true。 将部署存储帐户。 但是，当 **newOrExisting** 设置为 **existing** 时，条件的计算结果为 false，并且不部署存储帐户。

有关使用 `condition` 元素的完整示例模板，请参阅[具有新的或现有虚拟网络、存储和公共 IP 的 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions)。

## <a name="allow-condition"></a>允许条件

可以传入指示是否允许条件的参数值。 以下示例部署 SQL Server，并可选择允许 Azure IP。

```json
{
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2015-05-01-preview",
  "name": "[parameters('serverName')]",
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
      "apiVersion": "2015-05-01-preview",
      "name": "AllowAllWindowsAzureIps",
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

有关完整的模板，请参阅 [Azure SQL 逻辑服务器](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server)。

## <a name="runtime-functions"></a>运行时函数

如果对条件性部署的资源使用 [reference](template-functions-resource.md#reference) 或 [list](template-functions-resource.md#list) 函数，则会对该函数进行评估，即使资源尚未部署。 如果该函数引用某个不存在的资源，则会出现错误。

请使用 [if](template-functions-logical.md#if) 函数，以确保仅当资源已部署时，才根据条件评估函数。 请查看示例模板的 [if 函数](template-functions-logical.md#if)，该模板将 if 和 reference 用于进行条件部署的资源。

[将资源设置为依赖于](define-resource-dependency.md)条件资源，这与设置任何其他资源完全一样。 条件资源未部署时，Azure 资源管理器会自动将其从所需依赖项中删除。

## <a name="condition-with-complete-mode"></a>完整模式条件

如果以[完整模式](deployment-modes.md)部署模板并且由于条件的计算结果为 false 而未部署资源，则结果取决于用于部署模板的 REST API 版本。 如果使用 2019-05-10 之前的版本，则**不会删除**该资源。 如果使用 2019-05-10 或更高版本，则**会删除**该资源。 最新版本的 Azure PowerShell 和 Azure CLI 在条件为 false 时会删除该资源。

## <a name="next-steps"></a>后续步骤

* 有关创建模板的建议，请参阅[ARM 模板最佳实践](template-best-practices.md)。
* 若要创建资源的多个实例，请参阅[ARM 模板中的资源迭代](copy-resources.md)。