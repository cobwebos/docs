---
title: Azure 资源管理器模板输出 | Microsoft Docs
description: 介绍如何使用声明性 JSON 语法定义 Azure 资源管理器模板的输出。
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/14/2017
ms.author: tomfitz
ms.openlocfilehash: e3c5a581b02f1dd7b7415ebd93de0e425ac2f8ae
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
---
# <a name="outputs-section-in-azure-resource-manager-templates"></a>Azure 资源管理器模板中的 Outputs 节
在 Outputs 节中，可以指定从部署返回的值。 例如，可能会返回用于访问已部署资源的 URI。

## <a name="define-and-use-output-values"></a>定义和使用输出值

以下示例演示如何返回公共 IP 地址的资源 ID：

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

部署后，可以使用脚本检索值。 对于 PowerShell，请使用：

```powershell
(Get-AzureRmResourceGroupDeployment -ResourceGroupName <resource-group-name> -Name <deployment-name>).Outputs.resourceID.value
```

对于 Azure CLI，请使用：

```azurecli-interactive
az group deployment show -g <resource-group-name> -n <deployment-name> --query properties.outputs.resourceID.value
```

可以使用 [reference](resource-group-template-functions-resource.md#reference) 函数从链接模板中检索输出值。 若要从链接模板中获取输出值，请使用如下所示的语法检索属性值：`"[reference('<name-of-deployment>').outputs.<property-name>.value]"`。

例如，可以通过从链接模板中检索值，设置负载均衡器上的 IP 地址。

```json
"publicIPAddress": {
    "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

不能在[嵌套模板](resource-group-linked-templates.md#link-or-nest-a-template)的 outputs 节中使用 `reference` 函数。 若要返回嵌套模板中部署的资源的值，请将嵌套模板转换为链接模板。

## <a name="available-properties"></a>可用属性

以下示例演示了输出定义的结构：

```json
"outputs": {
    "<outputName>" : {
        "type" : "<type-of-output-value>",
        "value": "<output-value-expression>"
    }
}
```

| 元素名称 | 必选 | 说明 |
|:--- |:--- |:--- |
| outputName |是 |输出值的名称。 必须是有效的 JavaScript 标识符。 |
| type |是 |输出值的类型。 输出值支持的类型与模板输入参数相同。 |
| 值 |是 |评估并作为输出值返回的模板语言表达式。 |

## <a name="recommendations"></a>建议

如果使用模板创建公共 IP 地址，请包含 outputs 节，用于返回 IP 地址和完全限定域名 (FQDN) 的详细信息。 部署后，可以使用输出值轻松检索有关公共 IP 地址和 FQDN 的详细信息。

```json
"outputs": {
    "fqdn": {
        "value": "[reference(parameters('publicIPAddresses_name')).dnsSettings.fqdn]",
        "type": "string"
    },
    "ipaddress": {
        "value": "[reference(parameters('publicIPAddresses_name')).ipAddress]",
        "type": "string"
    }
}
```

## <a name="example-templates"></a>示例模板


|模板  |说明  |
|---------|---------|
|[复制变量](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | 创建复杂变量，并输出这些值。 不部署任何资源。 |
|[公共 IP 地址](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | 创建公共 IP 地址并输出资源 ID。 |
|[负载均衡器](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | 链接到前面的模板。 创建负载均衡器时，请使用输出中的资源 ID。 |


## <a name="next-steps"></a>后续步骤
* 若要查看许多不同类型的解决方案的完整模型，请参阅 [Azure Quickstart Templates](https://azure.microsoft.com/documentation/templates/)（Azure 快速入门模板）。
* 有关用户可以使用的来自模板中的函数的详细信息，请参阅 [Azure 资源管理器模板函数](resource-group-template-functions.md)。
* 要在部署期间合并多个模板，请参阅[将已链接的模板与 Azure 资源管理器配合使用](resource-group-linked-templates.md)。
* 可能需要使用不同资源组中的资源。 使用跨多个资源组共享的存储帐户或虚拟网络时，此方案很常见。 有关详细信息，请参阅 [resourceId 函数](resource-group-template-functions-resource.md#resourceid)。