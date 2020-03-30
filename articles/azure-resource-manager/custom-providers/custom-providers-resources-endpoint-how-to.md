---
title: 将自定义资源添加到 Azure REST API
description: 了解如何将自定义资源添加到 Azure REST API。 本文将介绍希望实现自定义资源的终结点的要求和最佳实践。
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: b6c5f5b8e437ad2dc2e8a3be3f3f2ed03a613b44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650521"
---
# <a name="adding-custom-resources-to-azure-rest-api"></a>将自定义资源添加到 Azure REST API

本文将介绍创建实现自定义资源的 Azure 自定义资源提供程序终结点的要求和最佳实践。 如果您不熟悉 Azure 自定义资源提供程序，请参阅[自定义资源提供程序的概述](overview.md)。

## <a name="how-to-define-a-resource-endpoint"></a>如何定义资源终结点

**终结点**是指向服务的 URL，该服务在它与 Azure 之间实现基础协定。 终结点在自定义资源提供程序中定义，可以是任何可公开访问的 URL。 下面的示例具有称为`myCustomResource`由`endpointURL`实现**的资源类型**。

示例**资源提供程序**：

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResource",
        "routingType": "Proxy, Cache",
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

## <a name="building-a-resource-endpoint"></a>构建资源终结点

实现**资源类型的****终结点**必须处理 Azure 中新 API 的请求和响应。 创建具有**资源类型的**自定义资源提供程序时，它将在 Azure 中生成一组新的 API。 在这种情况下，**资源类型**将为 生成`PUT`新的 Azure 资源 API，`GET`并在`DELETE`单个资源上执行 CRUD，以及`GET`检索所有现有资源：

操作单个资源`PUT`（、 `GET``DELETE`和 ）：

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource/{myCustomResourceName}
```

检索所有资源`GET`（ ）：

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource
```

对于自定义资源，自定义资源提供程序提供两种类型的**路由类型**："`Proxy`和""。`Proxy, Cache`

### <a name="proxy-routing-type"></a>代理路由类型

"`Proxy`**路由类型**"代理所有请求方法到自定义资源提供程序中指定的**终结点**。 何时使用""：`Proxy`

- 需要完全控制响应。
- 将系统集成与现有资源。

要了解有关""资源`Proxy`"的更多[信息，请参阅自定义资源代理引用](proxy-resource-endpoint-reference.md)

### <a name="proxy-cache-routing-type"></a>代理缓存路由类型

"`Proxy, Cache`**路由类型**"仅`PUT`代理，并将`DELETE`方法请求到自定义资源提供程序中指定的**终结点**。 自定义资源提供程序将根据存储在其缓存`GET`中的内容自动返回请求。 如果自定义资源标记为缓存，自定义资源提供程序还将在响应中添加/覆盖字段，以使 API Azure 兼容。 何时使用""：`Proxy, Cache`

- 创建没有现有资源的新系统。
- 使用现有的 Azure 生态系统。

要了解有关""资源`Proxy, Cache`的详细信息，请参阅[自定义资源缓存引用](proxy-cache-resource-endpoint-reference.md)

## <a name="creating-a-custom-resource"></a>创建自定义资源

从自定义资源提供程序创建自定义资源有两种主要方法：

- Azure CLI
- Azure 资源管理器模板

### <a name="azure-cli"></a>Azure CLI

创建自定义资源：

```azurecli-interactive
az resource create --is-full-object \
                   --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName} \
                   --properties \
                    '{
                        "location": "eastus",
                        "properties": {
                            "myProperty1": "myPropertyValue1",
                            "myProperty2": {
                                "myProperty3": "myPropertyValue3"
                            }
                        }
                    }'
```

参数 | 必选 | 描述
---|---|---
is-full-object | *是的* | 指示属性对象包含其他选项，例如位置、标记、sku 和/或计划。
id | *是的* | 自定义资源的资源 ID。 这应该存在于**资源提供程序**的外
properties | *是的* | 将发送到**终结点**的请求正文。

删除 Azure 自定义资源：

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

参数 | 必选 | 描述
---|---|---
id | *是的* | 自定义资源的资源 ID。 这应该存在于**资源提供程序**中。

检索 Azure 自定义资源：

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

参数 | 必选 | 描述
---|---|---
id | *是的* | 自定义资源的资源 ID。 这应该存在于**资源提供程序**的外

### <a name="azure-resource-manager-template"></a>Azure 资源管理器模板

> [!NOTE]
> `id`资源要求响应包含适当的 、`name`和`type`从**终结点**。

Azure 资源管理器模板要求`id`从`name`下游终结点正确`type`返回 和 。 返回的资源响应应为以下形式：

示例**终结点**响应：

``` JSON
{
  "properties": {
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3": "myPropertyValue3"
    }
  },
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{customResourceName}",
  "name": "{customResourceName}",
  "type": "Microsoft.CustomProviders/resourceProviders/{resourceTypeName}"
}
```

示例 Azure 资源管理器模板：

```JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.CustomProviders/resourceProviders/{resourceTypeName}",
            "name": "{resourceProviderName}/{customResourceName}",
            "apiVersion": "2018-09-01-preview",
            "location": "eastus",
            "properties": {
                "myProperty1": "myPropertyValue1",
                "myProperty2": {
                    "myProperty3": "myPropertyValue3"
                }
            }
        }
    ]
}
```

参数 | 必选 | 描述
---|---|---
resourceTypeName | *是的* | 自定义提供程序中定义的**资源类型****的名称**。
resourceProviderName | *是的* | 自定义资源提供程序实例名称。
customResourceName | *是的* | 自定义资源名称。

## <a name="next-steps"></a>后续步骤

- [Azure 自定义资源提供程序概述](overview.md)
- [快速入门：创建 Azure 自定义资源提供程序并部署自定义资源](./create-custom-provider.md)
- [教程：在 Azure 中创建自定义操作和资源](./tutorial-get-started-with-custom-providers.md)
- [如何：将自定义操作添加到 Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [引用：自定义资源代理引用](proxy-resource-endpoint-reference.md)
- [参考：自定义资源缓存引用](proxy-cache-resource-endpoint-reference.md)
