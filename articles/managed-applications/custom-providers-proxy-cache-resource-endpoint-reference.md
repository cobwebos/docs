---
title: 自定义资源缓存参考
description: Azure 自定义资源提供程序的自定义资源缓存引用。 本文会通过实现缓存自定义资源的终结点的要求。
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 6a3d570d9695516a293b601b3d34c2bcba6b058d
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478883"
---
# <a name="custom-resource-cache-reference"></a>自定义资源缓存参考

本文会通过实现缓存自定义资源的终结点的要求。 如果您不熟悉 Azure 自定义资源提供程序，请参阅[对自定义资源提供程序概述](./custom-providers-overview.md)。

## <a name="how-to-define-a-cache-resource-endpoint"></a>如何定义缓存资源终结点

可通过指定创建代理资源**routingType**到"代理服务器，缓存"。

示例自定义资源提供程序：

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResources",
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

## <a name="building-proxy-resource-endpoint"></a>构建代理资源终结点

**终结点**实现的"代理服务器，缓存"资源**终结点**必须处理的请求和响应 Azure 中的新 API。 在这种情况下， **resourceType**将生成新的 Azure 资源 API 的`PUT`， `GET`，和`DELETE`若要对单个资源执行 CRUD 以及`GET`来检索所有的现有资源：

> [!NOTE]
> Azure API 将生成的请求方法`PUT`， `GET`，并`DELETE`，但缓存**终结点**仅需要处理`PUT`和`DELETE`。
> 我们建议**终结点**还实现`GET`。

### <a name="create-a-custom-resource"></a>创建自定义资源

Azure API 传入的请求：

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

然后，此请求将转发给**终结点**窗体中：

``` HTTP
PUT https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

同样，从响应**终结点**然后转发回给客户。 从终结点的响应应返回：

- 有效的 JSON 对象文档。 所有数组和字符串应都嵌套在顶级对象下。
- `Content-Type`标头应设置为"application/json;charset = utf-8"。
- 自定义资源提供程序将覆盖`name`， `type`，和`id`请求的字段。
- 自定义资源提供程序将只返回字段下的`properties`缓存终结点的对象。

**终结点**响应：

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

`name`， `id`，和`type`字段将自动生成自定义资源的自定义资源提供程序。

Azure 自定义资源提供程序响应：

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

### <a name="remove-a-custom-resource"></a>删除自定义资源

Azure API 传入的请求：

``` HTTP
Delete https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

然后，此请求将转发给**终结点**窗体中：

``` HTTP
Delete https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

同样，从响应**终结点**然后转发回给客户。 从终结点的响应应返回：

- 有效的 JSON 对象文档。 所有数组和字符串应都嵌套在顶级对象下。
- `Content-Type`标头应设置为"application/json;charset = utf-8"。
- Azure 自定义资源提供程序只会从其缓存删除该项，如果返回 200 级别响应。 即使资源不存在，**终结点**应返回 204。

**终结点**响应：

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

Azure 自定义资源提供程序响应：

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

### <a name="retrieve-a-custom-resource"></a>检索自定义资源

Azure API 传入的请求：

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

该请求将**不**转发给**终结点**。

Azure 自定义资源提供程序响应：

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

### <a name="enumerate-all-custom-resources"></a>枚举所有自定义资源

Azure API 传入的请求：

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

此请求将**不**转发给**终结点**。

Azure 自定义资源提供程序响应：

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "value" : [
        {
            "name": "{myCustomResourceName}",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
            "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
            "properties": {
                "myProperty1": "myPropertyValue1",
                "myProperty2": {
                    "myProperty3" : "myPropertyValue3"
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>后续步骤

- [Azure 自定义资源提供程序的概述](./custom-providers-overview.md)
- [教程：创建 Azure 自定义资源提供程序和部署自定义资源](./create-custom-provider.md)
- [如何：向 Azure REST API 添加自定义操作](./custom-providers-action-endpoint-how-to.md)
- [参考：自定义资源代理引用](./custom-providers-proxy-resource-endpoint-reference.md)
