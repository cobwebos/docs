---
title: Azure 自定义资源提供程序的概述
description: 了解有关 Azure 自定义资源提供程序以及如何扩展 Azure API 平面以适合你的工作流。
author: jjbfour
ms.service: managed-applications
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: f418cd6c5470740ce123448ddbbe54cb6e89dabe
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2019
ms.locfileid: "67475936"
---
# <a name="azure-custom-resource-providers-overview"></a>Azure 自定义资源提供程序概述

Azure 自定义资源提供程序是到 Azure 的可扩展平台。 它允许定义可用于扩展默认的自定义 Api 的 Azure 体验。 本文档说明：

- 如何生成和部署 Azure 自定义资源提供程序。
- 如何使用 Azure 自定义资源提供程序来扩展现有的工作流。
- 要查找指南和代码示例，若要开始的位置。

![自定义提供程序概述](./media/custom-providers-overview/overview.png)

> [!IMPORTANT]
> 自定义提供程序目前处于公共预览状态。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="what-can-custom-resource-providers-do"></a>自定义资源提供程序可以做什么

下面是可以使用 Azure 自定义资源提供程序实现的目的的一些示例：

- 扩展 Azure 资源管理器 REST API，使之包括内部和外部服务。
- 启用基于 Azure 的现有工作流的自定义方案。
- 自定义 Azure 资源管理器模板控件和效果。

## <a name="what-is-a-custom-resource-provider"></a>什么是自定义资源提供程序

通过创建 Azure 终结点之间的合约进行 azure 自定义资源提供程序。 此协定定义的新资源和通过新的资源的操作列表**Microsoft.CustomProviders/resourceProviders**。 自定义资源提供程序然后会在 Azure 中的这些新的 Api。 Azure 自定义资源提供程序包括三个部分： 自定义资源提供程序**终结点**，和自定义资源。

## <a name="how-to-build-custom-resource-providers"></a>如何生成自定义资源提供程序

自定义资源提供程序是一系列 Azure 和终结点之间的约定。 此协定描述 Azure 应如何与终结点进行交互。 资源提供程序行为和等代理将转发到和从指定请求和响应**终结点**。 资源提供程序可以指定两种类型的协定： [ **resourceTypes** ](./custom-providers-resources-endpoint-how-to.md)并[**操作**](./custom-providers-action-endpoint-how-to.md)。 通过终结点定义启用这些策略。 终结点定义包含三个字段：**名称**， **routingType**，并**终结点**。

示例终结点：

```JSON
{
  "name": "{endpointDefinitionName}",
  "routingType": "Proxy",
  "endpoint": "https://{endpointURL}/"
}
```

属性 | 需要 | 描述
---|---|---
name | *yes* | 终结点定义的名称。 Azure 将公开此名称在其 API 通过 /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/<br>resourceProviders/{resourceProviderName}/{endpointDefinitionName}'
routingType | *no* | 确定使用的协定类型**终结点**。 如果未指定，则将默认为"代理"。
endpoint | *yes* | 要将请求路由到的终结点。 这将处理响应，以及请求的任何方面的副作用。

### <a name="building-custom-resources"></a>生成自定义资源

**资源类型**描述添加到 Azure 的新自定义资源。 这些公开基本 RESTful CRUD 方法。 请参阅[有关创建自定义资源的详细信息](./custom-providers-resources-endpoint-how-to.md)

示例自定义资源提供程序**资源类型**:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus"
}
```

上面的示例中的 Api 添加到 Azure:

HttpMethod | 示例 URI | 描述
---|---|---
PUT | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | 若要创建新的资源的 Azure REST API 调用。
DELETE | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | 要删除现有资源的 Azure REST API 调用。
GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | 检索现有的资源的 Azure REST API 调用。
GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources?api-version=2018-09-01-preview | Azure REST API 调用来检索现有资源的列表。

### <a name="building-custom-actions"></a>构建自定义操作

**操作**描述添加到 Azure 的新操作。 这些可公开基础资源提供程序上或下嵌套**resourceType**。 请参阅[有关创建自定义操作的详细信息](./custom-providers-action-endpoint-how-to.md)

示例自定义资源提供程序**操作**:

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
  "location": "eastus"
}
```

上面的示例中的 Api 添加到 Azure:

HttpMethod | 示例 URI | 描述
---|---|---
发布 | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomAction?api-version=2018-09-01-preview | 若要激活此操作的 Azure REST API 调用。

## <a name="looking-for-help"></a>查找有关的帮助

如果你有 Azure 自定义资源提供程序开发的问题，请尝试询问[Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers)。 类似的问题可能已提出和解答，因此请查看之前发布的第一次。 将标记添加```azure-custom-providers```以获得快速响应 ！

## <a name="next-steps"></a>后续步骤

在本文中，您学习了有关自定义提供程序的信息。 请转到下一篇文章中创建自定义提供程序。

- [教程：创建 Azure 自定义资源提供程序和部署自定义资源](./create-custom-provider.md)
- [如何：向 Azure REST API 添加自定义操作](./custom-providers-action-endpoint-how-to.md)
- [如何：将自定义资源添加到 Azure REST API](./custom-providers-resources-endpoint-how-to.md)
