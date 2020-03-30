---
title: 将自定义操作添加到 Azure REST API
description: 了解如何将自定义操作添加到 Azure REST API。 本文将介绍希望实现自定义操作的终结点的要求和最佳实践。
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 6110a7952b7c29609d2b98e135b61032aec3fa52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650391"
---
# <a name="adding-custom-actions-to-azure-rest-api"></a>将自定义操作添加到 Azure REST API

本文将介绍创建实现自定义操作的 Azure 自定义资源提供程序终结点的要求和最佳实践。 如果您不熟悉 Azure 自定义资源提供程序，请参阅[自定义资源提供程序的概述](overview.md)。

## <a name="how-to-define-an-action-endpoint"></a>如何定义操作终结点

**终结点**是指向服务的 URL，该服务在它与 Azure 之间实现基础协定。 终结点在自定义资源提供程序中定义，可以是任何可公开访问的 URL。 下面的示例具有称为`myCustomAction`由`endpointURL`实现**的操作**。

示例**资源提供程序**：

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "myCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus",
  "type": "Microsoft.CustomProviders/resourceProviders",
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
  "name": "{resourceProviderName}"
}
```

## <a name="building-an-action-endpoint"></a>构建操作终结点

实现**操作**的**终结点**必须处理 Azure 中新 API 的请求和响应。 创建具有**操作**的自定义资源提供程序时，它将在 Azure 中生成一组新的 API。 在这种情况下，该操作将为`POST`调用生成新的 Azure 操作 API：

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction
```

Azure API 传入请求：

``` HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

然后，此请求将以以下形式转发到**终结点**：

``` HTTP
POST https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

同样，从**端点**的响应然后转发回客户。 来自终结点的响应应返回：

- 有效的 JSON 对象文档。 所有数组和字符串都应嵌套在顶部对象下。
- 标头`Content-Type`应设置为"应用程序/json;"字符_utf-8"。

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

Azure 自定义资源提供程序响应：

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

## <a name="calling-a-custom-action"></a>调用自定义操作

从自定义资源提供程序调用自定义操作有两种主要方法：

- Azure CLI
- Azure 资源管理器模板

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az resource invoke-action --action {actionName} \
                          --ids /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName} \
                          --request-body \
                            '{
                                "myProperty1": "myPropertyValue1",
                                "myProperty2": {
                                    "myProperty3": "myPropertyValue3"
                                }
                            }'
```

参数 | 必选 | 描述
---|---|---
action | *是的* | 资源**提供程序**中定义的操作的名称。
ids | *是的* | **资源提供程序**的资源 ID 。
request-body | *不* | 将发送到**终结点**的请求正文。

### <a name="azure-resource-manager-template"></a>Azure 资源管理器模板

> [!NOTE]
> 操作在 Azure 资源管理器模板中的支持有限。 为了在模板中调用操作，它必须在其名称中包含[`list`](../templates/template-functions-resource.md#list)前缀。

具有列表操作的示例**资源提供程序**：

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "listMyCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus"
}
```

示例 Azure 资源管理器模板：

``` JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "resourceIdentifier": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
        "apiVersion": "2018-09-01-preview",
        "functionValues": {
            "myProperty1": "myPropertyValue1",
            "myProperty2": {
                "myProperty3": "myPropertyValue3"
            }
        }
    },
    "resources": [],
    "outputs": {
        "myCustomActionOutput": {
            "type": "object",
            "value": "[listMyCustomAction(variables('resourceIdentifier'), variables('apiVersion'), variables('functionValues'))]"
        }
    }
}
```

参数 | 必选 | 描述
---|---|---
资源标识符 | *是的* | **资源提供程序**的资源 ID 。
apiVersion | *是的* | 资源运行时的 API 版本。 这应始终为"2018-09-01 预览"。
functionValues | *不* | 将发送到**终结点**的请求正文。

## <a name="next-steps"></a>后续步骤

- [Azure 自定义资源提供程序概述](overview.md)
- [快速入门：创建 Azure 自定义资源提供程序并部署自定义资源](./create-custom-provider.md)
- [教程：在 Azure 中创建自定义操作和资源](./tutorial-get-started-with-custom-providers.md)
- [如何：将自定义资源添加到 Azure REST API](./custom-providers-resources-endpoint-how-to.md)
