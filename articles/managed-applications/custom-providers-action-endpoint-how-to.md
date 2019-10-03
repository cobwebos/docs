---
title: 将自定义操作添加到 Azure REST API
description: 了解如何将自定义操作添加到 Azure REST API。 本文将引导完成的要求和想要实现自定义操作的终结点的最佳做法。
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 6fbd20c201e1b141b7276e3283599b00cdefd118
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795304"
---
# <a name="adding-custom-actions-to-azure-rest-api"></a>向 Azure REST API 添加自定义操作

本文将经历的要求和最佳实践创建实现自定义操作的 Azure 自定义资源提供程序终结点。 如果您不熟悉 Azure 自定义资源提供程序，请参阅[对自定义资源提供程序概述](./custom-providers-overview.md)。

## <a name="how-to-define-an-action-endpoint"></a>如何定义操作终结点

**终结点**是指向一个服务，用于实现基础协定，则它与 Azure 之间的 URL。 终结点定义自定义资源提供程序中，可以是任何可公开访问的 URL。 下面的示例有**操作**称为`myCustomAction`由实现`endpointURL`。

示例**ResourceProvider**:

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

## <a name="building-an-action-endpoint"></a>生成操作终结点

**终结点**实现**操作**必须处理的请求和响应 Azure 中的新 API。 当使用自定义资源提供程序**操作**是创建的它将生成一组新的 Api 在 Azure 中。 在这种情况下，操作将生成的新的 Azure 操作 API`POST`调用：

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction
```

Azure API 传入的请求：

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

然后，此请求将转发给**终结点**窗体中：

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

同样，从响应**终结点**然后转发回给客户。 从终结点的响应应返回：

- 有效的 JSON 对象文档。 所有数组和字符串应都嵌套在顶级对象下。
- `Content-Type`标头应设置为"application/json;charset = utf-8"。

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

有两种主要方法的调用从自定义资源提供程序自定义操作：

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

参数 | 必填 | 描述
---|---|---
action | *yes* | 在中定义的操作的名称**ResourceProvider**。
id | *yes* | 资源 ID **ResourceProvider**。
请求正文 | *no* | 将发送到请求正文**终结点**。

### <a name="azure-resource-manager-template"></a>Azure 资源管理器模板

> [!NOTE]
> 操作提供有限的支持 Azure 资源管理器模板中。 为了使要在模板内调用的操作，它必须包含[ `list` ](../azure-resource-manager/resource-group-template-functions-resource.md#list)中其名称的前缀。

示例**ResourceProvider**具有列表操作：

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

参数 | 必填 | 描述
---|---|---
resourceIdentifier | *yes* | 资源 ID **ResourceProvider**。
apiVersion | *yes* | 资源运行时 API 版本。 这应始终为"2018年-09-01-预览"。
functionValues | *no* | 将发送到请求正文**终结点**。

## <a name="next-steps"></a>后续步骤

- [Azure 自定义资源提供程序的概述](./custom-providers-overview.md)
- [快速入门：创建 Azure 自定义资源提供程序和部署自定义资源](./create-custom-provider.md)
- [教程：在 Azure 中创建自定义操作和资源](./tutorial-custom-providers-101.md)
- [如何：将自定义资源添加到 Azure REST API](./custom-providers-resources-endpoint-how-to.md)
