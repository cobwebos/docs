---
title: 自定义提供程序概述
description: 了解 Azure 自定义资源提供程序，以及如何扩展 Azure API 平面以适应工作流。
author: jjbfour
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: fd21117219ea3db6946e7a1b889d92702af65b58
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2020
ms.locfileid: "75650482"
---
# <a name="azure-custom-resource-providers-overview"></a>Azure 自定义资源提供程序概述

Azure 自定义资源提供程序是 Azure 的可扩展平台。 它允许你定义可用于丰富默认 Azure 体验的自定义 Api。 本文档介绍：

- 如何生成和部署 Azure 自定义资源提供程序。
- 如何利用 Azure 自定义资源提供程序扩展现有工作流。
- 在哪里可以找到要开始的指南和代码示例。

![自定义提供程序概述](./media/overview/overview.png)

> [!IMPORTANT]
> 自定义提供程序当前提供公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="what-can-custom-resource-providers-do"></a>自定义资源提供程序可执行的操作

下面是可以通过 Azure 自定义资源提供程序实现的一些示例：

- 扩展 Azure 资源管理器 REST API 以包括内部和外部服务。
- 启用自定义方案，使其位于现有 Azure 工作流之上。
- 自定义 Azure 资源管理器模板控件和效果。

## <a name="what-is-a-custom-resource-provider"></a>什么是自定义资源提供程序

Azure 自定义资源提供程序是通过在 Azure 和终结点之间创建约定来完成的。 此协定通过新资源**CustomProviders/resourceProviders**定义新资源和操作的列表。 自定义资源提供程序随后会在 Azure 中公开这些新 Api。 Azure 自定义资源提供程序由三个部分组成：自定义资源提供程序、**终结点**和自定义资源。

## <a name="how-to-build-custom-resource-providers"></a>如何生成自定义资源提供程序

自定义资源提供程序是 Azure 与终结点之间的约定列表。 此合同介绍 Azure 应该如何与终结点进行交互。 资源提供程序的行为类似于代理，并将向指定的**终结点**转发请求和响应。 资源提供程序可以指定两种类型的协定： [**resourceTypes**](./custom-providers-resources-endpoint-how-to.md)和[**操作**](./custom-providers-action-endpoint-how-to.md)。 通过终结点定义启用这些。 终结点定义由三个字段组成：**名称**、 **routingType**和**终结点**。

示例端点：

```JSON
{
  "name": "{endpointDefinitionName}",
  "routingType": "Proxy",
  "endpoint": "https://{endpointURL}/"
}
```

属性 | 需要 | Description
---|---|---
name | 是 | 终结点定义的名称。 Azure 会通过其 API 在“/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/<br>resourceProviders/{resourceProviderName}/{endpointDefinitionName}”下公开此名称
routingType | 否 | 确定**终结点**的协定类型。 如果未指定，则会默认为 "Proxy"。
endpoint | 是 | 终结点，可向其路由请求。 此项将处理响应以及请求的任何附带作用。

### <a name="building-custom-resources"></a>构建自定义资源

**ResourceTypes**介绍添加到 Azure 的新自定义资源。 这些方法公开基本的 RESTful CRUD 方法。 了解[有关创建自定义资源的详细信息](./custom-providers-resources-endpoint-how-to.md)

包含**resourceTypes**的示例自定义资源提供程序：

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

为上述示例添加到 Azure 的 Api：

HttpMethod | 示例 URI | Description
---|---|---
PUT | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | 用于创建新资源的 Azure REST API 调用。
DELETE | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | Azure REST API 调用删除现有资源。
GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | Azure REST API 调用来检索现有资源。
GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources?api-version=2018-09-01-preview | Azure REST API 调用来检索现有资源的列表。

### <a name="building-custom-actions"></a>构建自定义操作

**操作**描述添加到 Azure 的新操作。 它们可以在资源提供程序的顶层公开，也可以嵌套在**resourceType**下。 了解[有关创建自定义操作的详细信息](./custom-providers-action-endpoint-how-to.md)

包含**操作**的示例自定义资源提供程序：

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

为上述示例添加到 Azure 的 Api：

HttpMethod | 示例 URI | Description
---|---|---
POST | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomAction?api-version=2018-09-01-preview | Azure REST API 调用来激活该操作。

## <a name="looking-for-help"></a>寻求帮助

如果有关于 Azure 自定义资源提供程序开发的问题，请尝试[Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers)。 该论坛上可能已有类似问题的解答，因此，在发贴之前请先查看以往的提问。 添加标记 ```azure-custom-providers``` 可以快速得到回复！

## <a name="next-steps"></a>后续步骤

本文介绍了自定义提供程序。 若要创建自定义提供程序，请继续学习下一篇文章。

- [快速入门：创建 Azure 自定义资源提供程序和部署自定义资源](./create-custom-provider.md)
- [教程：在 Azure 中创建自定义操作和资源](./tutorial-get-started-with-custom-providers.md)
- [如何：将自定义操作添加到 Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [如何：将自定义资源添加到 Azure REST API](./custom-providers-resources-endpoint-how-to.md)
