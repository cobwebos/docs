---
title: 自定义提供程序概述
description: 了解 Azure 自定义资源提供程序以及如何扩展 Azure API 平面以适合工作流。
author: jjbfour
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 68b8bd187d58cd71778b8a922684cc3817a0715d
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398462"
---
# <a name="azure-custom-resource-providers-overview"></a>Azure 自定义资源提供程序概述

Azure 自定义资源提供程序是 Azure 的扩展平台。 它允许您定义可用于丰富默认 Azure 体验的自定义 API。 本文档介绍：

- 如何生成和部署 Azure 自定义资源提供程序。
- 如何使用 Azure 自定义资源提供程序来扩展现有工作流。
- 在哪里可以找到指南和代码示例以开始。

![自定义提供程序概述](./media/overview/overview.png)

> [!IMPORTANT]
> 自定义提供程序当前处于公共预览版中。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="what-can-custom-resource-providers-do"></a>自定义资源提供程序可以做什么

以下是使用 Azure 自定义资源提供程序可以实现的一些示例：

- 扩展 Azure 资源管理器 REST API 以包括内部和外部服务。
- 在现有 Azure 工作流之上启用自定义方案。
- 自定义 Azure 资源管理器模板控件和效果。

## <a name="what-is-a-custom-resource-provider"></a>什么是自定义资源提供程序

Azure 自定义资源提供程序是通过在 Azure 和终结点之间创建协定来进行的。 此协定通过新资源 Microsoft 定义新资源和操作**的列表**。 然后，自定义资源提供程序将在 Azure 中公开这些新 API。 Azure 自定义资源提供程序由三部分组成：自定义资源提供程序、**终结点**和自定义资源。

## <a name="how-to-build-custom-resource-providers"></a>如何构建自定义资源提供程序

自定义资源提供程序是 Azure 和终结点之间的协定列表。 此协定描述 Azure 应如何与终结点进行交互。 资源提供程序的行为类似于代理，并将请求和响应转发到指定终结点和从指定的**终结点**。 资源提供程序可以指定两种类型的协定：[**资源类型**](./custom-providers-resources-endpoint-how-to.md)和[**操作**](./custom-providers-action-endpoint-how-to.md)。 这些通过终结点定义启用。 终结点定义由三个字段组成：**名称**、**路由类型**和**终结点**。

示例终结点：

```JSON
{
  "name": "{endpointDefinitionName}",
  "routingType": "Proxy",
  "endpoint": "https://{endpointURL}/"
}
```

properties | 必选 | 说明
---|---|---
name | *是的* | 终结点定义的名称。 Azure 会通过其 API 在“/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/<br>resourceProviders/{resourceProviderName}/{endpointDefinitionName}”下公开此名称
routingType | *不* | 确定**终结点的协定类型**。 如果未指定，则会默认为 "Proxy"。
endpoint | *是的* | 终结点，可向其路由请求。 此项将处理响应以及请求的任何附带作用。

### <a name="building-custom-resources"></a>构建自定义资源

**资源类型**描述添加到 Azure 的新自定义资源。 这些公开了基本的 RESTful CRUD 方法。 [查看有关创建自定义资源的更多信息](./custom-providers-resources-endpoint-how-to.md)

示例自定义资源提供程序与**资源类型**：

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

为上述示例添加到 Azure 的 API：

HttpMethod | 示例 URI | 说明
---|---|---
PUT | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>我的自定义资源/[自定义资源名称]？api版本_2018-09-01预览 | Azure REST API 调用以创建新资源。
DELETE | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>我的自定义资源/[自定义资源名称]？api版本_2018-09-01预览 | 要删除现有资源的 Azure REST API 调用。
GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>我的自定义资源/[自定义资源名称]？api版本_2018-09-01预览 | 用于检索现有资源的 Azure REST API 调用。
GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>我的自定义资源？api版本_2018-09-01预览 | 用于检索现有资源列表的 Azure REST API 调用。

### <a name="building-custom-actions"></a>构建自定义操作

**操作**描述添加到 Azure 的新操作。 这些可以公开在资源提供程序的顶部或嵌套在**资源类型**下。 查看[有关创建自定义操作的更多](./custom-providers-action-endpoint-how-to.md)

**示例自定义**资源提供程序的操作 ：

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

为上述示例添加到 Azure 的 API：

HttpMethod | 示例 URI | 说明
---|---|---
POST | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>我的自定义行动？api版本_2018-09-01预览 | Azure REST API 调用以激活操作。

## <a name="looking-for-help"></a>寻求帮助

如果对 Azure 自定义资源提供程序开发有疑问，请尝试询问[堆栈溢出](https://stackoverflow.com/questions/tagged/azure-custom-providers)。 该论坛上可能已有类似问题的解答，因此，在发贴之前请先查看以往的提问。 添加标记 ```azure-custom-providers``` 可以快速得到回复！

## <a name="next-steps"></a>后续步骤

本文介绍了自定义提供程序。 若要创建自定义提供程序，请继续学习下一篇文章。

- [快速入门：创建 Azure 自定义资源提供程序并部署自定义资源](./create-custom-provider.md)
- [教程：在 Azure 中创建自定义操作和资源](./tutorial-get-started-with-custom-providers.md)
- [如何：将自定义操作添加到 Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [如何：将自定义资源添加到 Azure REST API](./custom-providers-resources-endpoint-how-to.md)
