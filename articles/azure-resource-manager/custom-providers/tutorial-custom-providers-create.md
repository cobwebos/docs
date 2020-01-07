---
title: 创建并使用自定义提供程序
description: 本教程显示如何创建并使用 Azure 自定义提供程序。 使用自定义提供程序在 Azure 上更改工作流。
author: jjbfour
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 09df78955de6423244c2d8ec94e1e1c06ecab257
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2020
ms.locfileid: "75648734"
---
# <a name="create-and-use-a-custom-provider"></a>创建并使用自定义提供程序

自定义提供程序是 Azure 与终结点之间的协定。 使用自定义提供程序，可以在 Azure 上更改工作流。 本教程介绍创建自定义提供程序的过程。 如果不熟悉 Azure 自定义提供程序，请参阅 [Azure 自定义资源提供程序概述](overview.md)。

## <a name="create-a-custom-provider"></a>创建自定义提供程序

> [!NOTE]
> 本教程不介绍如何创作终结点。 如果没有 RESTful 终结点，请按 [RESTful 终结点创作教程](./tutorial-custom-providers-function-authoring.md)中的说明操作，该教程是当前教程的基础。

创建终结点以后，可以创建自定义提供程序，以便在提供程序与终结点之间生成一个协定。 可以通过自定义提供程序指定一系列终结点定义：

```JSON
{
  "name": "myEndpointDefinition",
  "routingType": "Proxy",
  "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
}
```

properties | 必选 | 说明
---|---|---
name  | 是 | 终结点定义的名称。 Azure 会通过其 API 在“/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders<br>/resourceProviders/{resourceProviderName}/{endpointDefinitionName}”下公开此名称
**routingType** | 否 | 终结点协定类型。 如果未指定值，则值默认为“Proxy”。
**endpoint** | 是 | 终结点，可向其路由请求。 此终结点处理响应以及请求的任何附带作用。

**endpoint** 的值是 Azure 函数应用的触发器 URL。 应该将 `<yourapp>`、`<funcname>` 和 `<functionkey>` 占位符替换为所创建函数应用的值。

## <a name="define-custom-actions-and-resources"></a>定义自定义操作和资源

自定义提供程序包含一系列在 **actions** 和 **resourceTypes** 属性下建模的终结点定义。 **actions** 属性映射到由自定义提供程序公开的自定义操作，而 **resourceTypes** 属性是自定义资源。 在本教程中，自定义提供程序有一个名为 `myCustomAction` 的 **actions** 属性，和一个名为 `myCustomResources` 的 **resourceTypes** 属性。

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

## <a name="deploy-the-custom-provider"></a>部署自定义提供程序

> [!NOTE]
> 必须将 **endpoint** 值替换为触发器 URL，该 URL 来自在上一教程中创建的函数应用。

可以使用 Azure 资源管理器模板来部署以前的自定义提供程序：

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

## <a name="use-custom-actions-and-resources"></a>使用自定义操作和资源

创建自定义提供程序以后，即可使用新的 Azure API。 以下选项卡介绍如何调用和使用自定义提供程序。

### <a name="custom-actions"></a>自定义操作

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!NOTE]
> 必须将 `{subscriptionId}` 和 `{resourceGroupName}` 占位符替换为订阅和资源组（已在其中部署自定义提供程序）。

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
*action* | 是 | 在自定义提供程序中定义的操作的名称
*ids* | 是 | 自定义提供程序的资源 ID
*request-body* | 否 | 将要发送到终结点的请求正文

# <a name="templatetabtemplate"></a>[模板](#tab/template)

无。

---

### <a name="custom-resources"></a>自定义资源

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!NOTE]
> 必须将 `{subscriptionId}` 和 `{resourceGroupName}` 占位符替换为订阅和资源组（已在其中部署自定义提供程序）。

#### <a name="create-a-custom-resource"></a>创建自定义资源

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
*is-full-object* | 是 | 指示属性对象是否包含其他选项，例如位置、标记、SKU 或计划。
*id* | 是 | 自定义资源的资源 ID。 此 ID 是自定义提供程序资源 ID 的扩展。
*properties* | 是 | 将要发送到终结点的请求正文。

#### <a name="delete-a-custom-resource"></a>删除自定义资源

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

参数 | 必选 | 说明
---|---|---
*id* | 是 | 自定义资源的资源 ID。 此 ID 是自定义提供程序资源 ID 的扩展。

#### <a name="retrieve-a-custom-resource"></a>检索自定义资源

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

参数 | 必选 | 说明
---|---|---
*id* | 是 | 自定义资源的资源 ID。 此 ID 是自定义提供程序资源 ID 的扩展。

# <a name="templatetabtemplate"></a>[模板](#tab/template)

示例资源管理器模板：

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
*resourceTypeName* | 是 | 在自定义提供程序中定义的 **resourceTypes** 属性的 `name` 值。
*resourceProviderName* | 是 | 自定义提供程序实例名称。
*customResourceName* | 是 | 自定义资源名称。

---

> [!NOTE]
> 部署并使用自定义提供程序以后，记住清理所有创建的资源，包括 Azure 函数应用。

## <a name="next-steps"></a>后续步骤

本文介绍了自定义提供程序。 有关详细信息，请参阅：

- [如何：将自定义操作添加到 Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [如何：将自定义资源添加到 Azure REST API](./custom-providers-resources-endpoint-how-to.md)
