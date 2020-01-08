---
title: 向 Azure REST API 添加自定义资源
description: 了解如何向 Azure REST API 添加自定义资源。 本文将指导你要实现自定义资源的终结点的要求和最佳实践。
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: b6c5f5b8e437ad2dc2e8a3be3f3f2ed03a613b44
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2020
ms.locfileid: "75650521"
---
# <a name="adding-custom-resources-to-azure-rest-api"></a>向 Azure REST API 添加自定义资源

本文将介绍创建用于实现自定义资源的 Azure 自定义资源提供程序终结点的要求和最佳实践。 如果不熟悉 Azure 自定义资源提供程序，请参阅[自定义资源提供程序概述](overview.md)。

## <a name="how-to-define-a-resource-endpoint"></a>如何定义资源终结点

**终结点**是指向服务的 URL，该服务在其和 Azure 之间实现基础协定。 终结点在自定义资源提供程序中定义，可以是任何可公开访问的 URL。 下面的示例具有名为 `myCustomResource` 由 `endpointURL`实现的**resourceType** 。

示例**ResourceProvider**：

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

实现**resourceType**的**终结点**必须处理 Azure 中新 API 的请求和响应。 创建具有**resourceType**的自定义资源提供程序时，它将在 Azure 中生成一组新的 api。 在这种情况下， **resourceType**将为 `PUT`、`GET`和 `DELETE` 生成新的 AZURE 资源 API，以便在单个资源上执行 CRUD，并 `GET` 检索所有现有资源：

操作单个资源（`PUT`、`GET`和 `DELETE`）：

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource/{myCustomResourceName}
```

检索所有资源（`GET`）：

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource
```

对于自定义资源，自定义资源提供程序提供了两种类型的**routingTypes**： "`Proxy`" 和 "`Proxy, Cache`"。

### <a name="proxy-routing-type"></a>代理路由类型

"`Proxy`" **routingType**将所有请求方法代理到自定义资源提供程序中指定的**终结点**。 何时使用 "`Proxy`"：

- 需要完全控制响应。
- 将系统与现有资源集成。

若要了解有关 "`Proxy`" 资源的详细信息，请参阅[自定义资源代理引用](proxy-resource-endpoint-reference.md)

### <a name="proxy-cache-routing-type"></a>代理缓存路由类型

"`Proxy, Cache`" **routingType**代理仅 `PUT` 并向自定义资源提供程序中指定的**终结点**`DELETE` 请求方法。 自定义资源提供程序将根据其在缓存中存储的内容自动返回 `GET` 请求。 如果将自定义资源标记为缓存，则自定义资源提供程序还会在响应中添加/覆盖字段，使 Api 符合 Azure。 何时使用 "`Proxy, Cache`"：

- 创建没有现有资源的新系统。
- 使用现有的 Azure 生态系统。

若要了解有关 "`Proxy, Cache`" 资源的详细信息，请参阅[自定义资源缓存引用](proxy-cache-resource-endpoint-reference.md)

## <a name="creating-a-custom-resource"></a>创建自定义资源

创建自定义资源提供程序的自定义资源有两种主要方法：

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

参数 | 需要 | Description
---|---|---
is-full-object | 是 | 指示属性对象包含其他选项，例如位置、标记、sku 和/或计划。
id | 是 | 自定义资源的资源 ID。 这应该存在于**ResourceProvider**中。
properties | 是 | 将发送到**终结点**的请求正文。

删除 Azure 自定义资源：

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

参数 | 需要 | Description
---|---|---
id | 是 | 自定义资源的资源 ID。 这应该存在于**ResourceProvider**中。

检索 Azure 自定义资源：

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

参数 | 需要 | Description
---|---|---
id | 是 | 自定义资源的资源 ID。 这应该存在于**ResourceProvider**中。

### <a name="azure-resource-manager-template"></a>Azure 资源管理器模板

> [!NOTE]
> 资源要求响应包含来自**终结点**的相应 `id`、`name`和 `type`。

Azure 资源管理器模板需要从下游终结点正确返回 `id`、`name`和 `type`。 返回的资源响应的形式应为：

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

参数 | 需要 | Description
---|---|---
resourceTypeName | 是 | 在自定义提供程序中定义的**resourceType**的**名称**。
resourceProviderName | 是 | 自定义资源提供程序实例名称。
customResourceName | 是 | 自定义资源名称。

## <a name="next-steps"></a>后续步骤

- [Azure 自定义资源提供程序概述](overview.md)
- [快速入门：创建 Azure 自定义资源提供程序和部署自定义资源](./create-custom-provider.md)
- [教程：在 Azure 中创建自定义操作和资源](./tutorial-get-started-with-custom-providers.md)
- [如何：将自定义操作添加到 Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [参考：自定义资源代理引用](proxy-resource-endpoint-reference.md)
- [参考：自定义资源缓存引用](proxy-cache-resource-endpoint-reference.md)
