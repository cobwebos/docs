---
title: 创建和利用 Azure 自定义提供程序
description: 本教程将讲述如何创建并使用自定义提供程序。
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 65a8e60d8216e1da16af987c9e699e24ecaec3ec
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67799126"
---
# <a name="authoring-a-restful-endpoint-for-custom-providers"></a>创作自定义提供程序的 RESTful 终结点

可以通过自定义提供程序自定义 Azure 上的工作流。 自定义提供程序是 Azure 与`endpoint`之间的协定。 本教程将详述创建自定义提供程序的过程。 如果不熟悉 Azure 自定义提供程序，请参阅[自定义资源提供程序概述](./custom-providers-overview.md)。

本教程分为以下步骤：

- 自定义提供程序是什么
- 定义自定义操作和资源
- 部署自定义提供程序

本教程将以下述教程为基础：

- [创建用于自定义提供程序的 RESTful 终结点](./tutorial-custom-providers-function-authoring.md)

## <a name="creating-a-custom-provider"></a>创建自定义提供程序

> [!NOTE]
> 本教程不讲述如何创作终结点。 如果没有 RESTful 终结点，请按 [RESTful 终结点创作教程](./tutorial-custom-providers-function-authoring.md)中的说明操作。

创建`endpoint`以后，可以创建自定义提供程序，以便在它与`endpoint`之间生成一个协定。 可以通过自定义提供程序指定一系列终结点定义。

```JSON
{
  "name": "myEndpointDefinition",
  "routingType": "Proxy",
  "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
}
```

属性 | 必选 | 说明
---|---|---
名称 | 是  | 终结点定义的名称。 Azure 会通过其 API 在“/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/<br>resourceProviders/{resourceProviderName}/{endpointDefinitionName}”下公开此名称
routingType | 否  | 确定与`endpoint`的协定的类型。 如果未指定，则会默认为 "Proxy"。
endpoint | 是  | 终结点，可向其路由请求。 此项将处理响应以及请求的任何附带作用。

在此示例中，`endpoint` 是 Azure Function 的触发器 URL。 应该将 `<yourapp>`、`<funcname>` 和 `<functionkey>` 替换为所创建函数的值。

## <a name="defining-custom-actions-and-resources"></a>定义自定义操作和资源

自定义提供程序包含一系列在 `actions` 和 `resourceTypes` 下建模的终结点定义。 `actions` 映射到由自定义提供程序公开的自定义操作，而 `resourceTypes` 是自定义资源。 在本教程中，我们将使用名为 `myCustomAction` 的 `action` 和名为 `myCustomResources` 的 `resourceType` 定义一个自定义提供程序。

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "myCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
      }
    ],
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy",
        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
      }
    ]
  },
  "location": "eastus"
}
```

将 `endpoint` 替换为触发器 URL，该 URL 来自此前在上一教程中创建的函数。

## <a name="deploying-the-custom-provider"></a>部署自定义提供程序

> [!NOTE]
> 应将 `endpoint` 替换为函数 URL。

可以使用 Azure 资源管理器模板来部署上面的自定义提供程序。

```JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.CustomProviders/resourceProviders",
            "name": "myCustomProvider",
            "apiVersion": "2018-09-01-preview",
            "location": "eastus",
            "properties": {
                "actions": [
                    {
                        "name": "myCustomAction",
                        "routingType": "Proxy",
                        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
                    }
                ],
                "resourceTypes": [
                    {
                        "name": "myCustomResources",
                        "routingType": "Proxy",
                        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
                    }
                ]
            }
        }
    ]
}
```

## <a name="using-custom-actions-and-resources"></a>使用自定义操作和资源

在创建自定义提供程序以后，即可利用新的 Azure API。 下一部分将介绍如何调用和使用自定义提供程序。

### <a name="custom-actions"></a>自定义操作

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!NOTE]
> 应该将 `{subscriptionId}` 和 `{resourceGroupName}` 替换为订阅和 resourceGroup（已在其中部署自定义提供程序）。

```azurecli-interactive
az resource invoke-action --action myCustomAction \
                          --ids /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider \
                          --request-body
                            '{
                                "hello": "world"
                            }'
```

参数 | 必选 | 说明
---|---|---
action | 是  | 在创建的自定义提供程序中定义的操作的名称。
ids | 是  | 创建的自定义提供程序的资源 ID。
request-body | 否  | 将要发送到`endpoint`的请求正文。

# <a name="templatetabtemplate"></a>[模板](#tab/template)

无。

---

### <a name="custom-resources"></a>自定义资源

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!NOTE]
> 应该将 `{subscriptionId}` 和 `{resourceGroupName}` 替换为订阅和 resourceGroup（已在其中部署自定义提供程序）。

创建自定义资源：

```azurecli-interactive
az resource create --is-full-object \
                   --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1 \
                   --properties
                    '{
                        "location": "eastus",
                        "properties": {
                            "hello" : "world"
                        }
                    }'
```

参数 | 必选 | 说明
---|---|---
is-full-object | 是  | 指示属性对象包含其他选项，例如位置、标记、sku 和/或计划。
id | 是  | 自定义资源的资源 ID。 这应该存在于创建的自定义提供程序上。
properties | 是  | 将要发送到`endpoint`的请求正文。

删除 Azure 自定义资源：

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

参数 | 必选 | 说明
---|---|---
id | 是  | 自定义资源的资源 ID。 这应该存在于创建的自定义提供程序上。

检索 Azure 自定义资源：

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

参数 | 必选 | 说明
---|---|---
id | 是  | 自定义资源的资源 ID。 这应该存在于创建的自定义提供程序上。

# <a name="templatetabtemplate"></a>[模板](#tab/template)

示例 Azure 资源管理器模板：

```JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
            "name": "myCustomProvider/myTestResourceName1",
            "apiVersion": "2018-09-01-preview",
            "location": "eastus",
            "properties": {
                "hello": "world"
            }
        }
    ]
}
```

参数 | 必选 | 说明
---|---|---
resourceTypeName | 是  | 在自定义提供程序中定义的 *resourceType* 的 `name`。
resourceProviderName | 是  | 自定义提供程序实例名称。
customResourceName | 是  | 自定义资源名称。

---

> [!NOTE]
> 部署并使用自定义提供程序以后，记住清理任何创建的资源，包括 Azure Function。

## <a name="next-steps"></a>后续步骤

本文介绍了自定义提供程序。 若要创建自定义提供程序，请继续学习下一篇文章。

- [如何：将自定义操作添加到 Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [如何：将自定义资源添加到 Azure REST API](./custom-providers-resources-endpoint-how-to.md)
