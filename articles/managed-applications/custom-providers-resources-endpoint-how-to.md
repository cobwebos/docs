---
title: 将自定义资源添加到 Azure REST API
description: 了解如何将自定义资源添加到 Azure REST API。 本文将引导完成的要求和想要实现自定义资源的终结点的最佳做法。
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: b94d59b55a62797e142768dc84ec499d714bd067
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2019
ms.locfileid: "67479013"
---
# <a name="adding-custom-resources-to-azure-rest-api"></a>将自定义资源添加到 Azure REST API

本文将经历的要求和最佳实践创建实现自定义资源的 Azure 自定义资源提供程序终结点。 如果您不熟悉 Azure 自定义资源提供程序，请参阅[对自定义资源提供程序概述](./custom-providers-overview.md)。

## <a name="how-to-define-a-resource-endpoint"></a>如何定义资源终结点

**终结点**是指向一个服务，用于实现基础协定，则它与 Azure 之间的 URL。 终结点定义自定义资源提供程序中，可以是任何可公开访问的 URL。 下面的示例有**resourceType**称为`myCustomResource`由实现`endpointURL`。

示例**ResourceProvider**:

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

**终结点**实现**resourceType**必须处理的请求和响应 Azure 中的新 API。 当使用自定义资源提供程序**resourceType**是创建的它将生成一组新的 Api 在 Azure 中。 在这种情况下， **resourceType**将生成新的 Azure 资源 API 的`PUT`， `GET`，和`DELETE`若要对单个资源执行 CRUD 以及`GET`来检索所有的现有资源：

单个资源的操纵 (`PUT`， `GET`，和`DELETE`):

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource/{myCustomResourceName}
```

检索所有资源 (`GET`):

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource
```

有关自定义资源，自定义资源提供程序提供两种类型的**routingTypes**:"`Proxy`"和"`Proxy, Cache`"。

### <a name="proxy-routing-type"></a>代理路由类型

"`Proxy`" **RoutingType**代理所有请求的方法**终结点**指定自定义资源提供程序中。 何时使用"`Proxy`":

- 需要对响应的完全控制。
- 将系统集成使用现有资源。

若要详细了解"`Proxy`"资源，请参阅[自定义资源代理引用](./custom-providers-proxy-resource-endpoint-reference.md)

### <a name="proxy-cache-routing-type"></a>代理缓存路由类型

"`Proxy, Cache`" **RoutingType**代理仅`PUT`并`DELETE`请求方法**终结点**指定自定义资源提供程序中。 自定义资源提供程序将自动返回`GET`请求基于它已在其缓存中存储的内容。 如果自定义资源标记与缓存中，自定义资源提供程序还将添加/覆盖使 Api Azure 符合在响应中的字段。 何时使用"`Proxy, Cache`":

- 创建新的系统具有任何现有资源。
- 使用现有的 Azure 生态系统。

若要详细了解"`Proxy, Cache`"资源，请参阅[自定义资源缓存参考](./custom-providers-proxy-cache-resource-endpoint-reference.md)

## <a name="creating-a-custom-resource"></a>创建自定义资源

有两种主要方法创建从自定义资源提供程序的自定义资源的：

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

参数 | 需要 | 描述
---|---|---
is-full-object | *yes* | 指示属性对象包含其他选项，例如位置、 标记、 sku 和/或计划。
id | *yes* | 自定义资源的资源 ID。 这应存在的中断**ResourceProvider**
properties | *yes* | 将发送到请求正文**终结点**。

删除 Azure 自定义资源：

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

参数 | 需要 | 描述
---|---|---
id | *yes* | 自定义资源的资源 ID。 这应存在的中断**ResourceProvider**。

检索 Azure 自定义资源：

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

参数 | 需要 | 描述
---|---|---
id | *yes* | 自定义资源的资源 ID。 这应存在的中断**ResourceProvider**

### <a name="azure-resource-manager-template"></a>Azure 资源管理器模板

> [!NOTE]
> 资源要求的响应包含一个适当`id`， `name`，并`type`从**终结点**。

Azure 资源管理器模板需要`id`， `name`，和`type`已正确返回从下游终结点。 返回的资源响应应采用格式：

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

参数 | 需要 | 描述
---|---|---
resourceTypeName | *yes* | **名称**的**resourceType**自定义提供程序中定义。
resourceProviderName | *yes* | 自定义资源提供程序实例名称。
customResourceName | *yes* | 自定义资源名称。

## <a name="next-steps"></a>后续步骤

- [Azure 自定义资源提供程序的概述](./custom-providers-overview.md)
- [教程：创建 Azure 自定义资源提供程序和部署自定义资源](./create-custom-provider.md)
- [如何：向 Azure REST API 添加自定义操作](./custom-providers-action-endpoint-how-to.md)
- [参考：自定义资源代理引用](./custom-providers-proxy-resource-endpoint-reference.md)
- [参考：自定义资源缓存参考](./custom-providers-proxy-cache-resource-endpoint-reference.md)
