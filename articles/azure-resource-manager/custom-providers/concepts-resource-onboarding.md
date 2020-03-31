---
title: 资源载入
description: 了解如何使用 Azure 自定义提供程序将管理或配置应用于其他 Azure 资源类型来执行资源入职。
author: jjbfour
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: jobreen
ms.openlocfilehash: 1846b036f12fe7e691021ec0248782cad946d9b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650404"
---
# <a name="azure-custom-providers-resource-onboarding-overview"></a>Azure 自定义提供程序资源入职概述

Azure 自定义提供程序资源载入是 Azure 资源类型的扩展模型。 它允许您大规模跨现有 Azure 资源应用操作或管理。 有关详细信息，请参阅[Azure 自定义提供程序如何扩展 Azure](overview.md)。 本文介绍：

- 资源载入可以做什么。
- 资源载入基础知识以及如何使用它。
- 在哪里可以找到指南和代码示例以开始。

> [!IMPORTANT]
> 自定义提供程序当前处于公共预览版中。
> 此预览版在提供时没有附带服务级别协议，我们不建议将其用于生产工作负荷。 某些功能可能不受支持或功能受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="what-can-resource-onboarding-do"></a>资源载入可以做什么？

与[Azure 自定义提供程序自定义资源](./custom-providers-resources-endpoint-how-to.md)类似，资源载入定义将"入职"请求代理到终结点的协定。 与自定义资源不同，资源载入不会创建新的资源类型。 相反，它允许扩展现有资源类型。 资源入职工作与 Azure 策略一起工作，因此可以大规模地管理和配置资源。 资源入职工作流的一些示例：

- 安装和管理到虚拟机扩展。
- 上载和配置 Azure 存储帐户上的默认值。
- 大规模启用基线诊断设置。

## <a name="resource-onboarding-basics"></a>资源入职基础知识

您可以使用 Microsoft.自定义提供程序/资源提供程序和 Microsoft.自定义提供程序/关联资源类型配置通过 Azure 自定义提供程序载入的资源。 要为自定义提供程序启用资源入职，在配置过程中，请创建一个称为"关联"**的资源类型**，其中包含包含"扩展"的**路由类型**。 微软.自定义提供程序/关联和微软.自定义提供程序/资源提供程序不需要属于同一资源组。

下面是 Azure 自定义提供程序的示例：

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "associations",
        "routingType": "Proxy,Cache,Extension",
        "endpoint": "https://microsoft.com/"
      }
    ]
  },
  "location": "eastus"
}
```

properties | 必需？ | 描述
---|---|---
name | 是 | 终结点定义的名称。 对于资源载入，名称必须为"关联"。
routingType | 是 | 确定与终结点的协定类型。 对于资源载入，有效的**路由类型**是"代理、缓存、扩展"和"Webhook、缓存、扩展"。
endpoint | 是 | 终结点，可向其路由请求。 这将处理请求的响应和任何副作用。

使用关联资源类型创建自定义提供程序后，可以使用 Microsoft.CustomProvider/关联进行定位。 Microsoft.自定义提供程序/关联是可以扩展任何其他 Azure 资源的扩展资源。 创建 Microsoft.CustomProviders/关联实例时，它将采用属性**目标 ResourceId**，这应该是有效的 Microsoft.自定义提供程序/资源提供程序或 Microsoft.解决方案/应用程序资源 ID。 在这些情况下，请求将转发到您创建的 Microsoft.CustomProviders/资源提供程序实例上的关联资源类型。

> [!NOTE]
> 如果 Microsoft.解决方案/应用程序资源 ID 作为**目标ResourceId**提供，则必须有一个 Microsoft.自定义提供程序/资源提供程序部署在托管资源组中，名称为"公共"。

示例 Azure 自定义提供程序关联：

```JSON
{
  "properties": {
    "targetResourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
    ...
  }
}
```

properties | 必需？ | 描述
---|---|---
目标资源Id | 是 | 微软的资源 ID.自定义提供程序/资源提供程序或 Microsoft.解决方案/应用程序。

## <a name="how-to-use-resource-onboarding"></a>如何使用资源载入

资源载入的工作原理是扩展与 Microsoft 的其他资源。 在下面的示例中，对虚拟机发出请求，但可以扩展任何资源。

首先，您需要创建具有关联资源类型的自定义提供程序资源。 这将声明将在创建相应的 Microsoft 时使用的回调 URL。

示例 Microsoft.自定义提供程序/资源提供程序创建请求：

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
  "properties": {
    "resourceTypes": [
      {
        "name": "associations",
        "routingType": "Proxy,Cache,Extension",
        "endpoint": "https://{myCustomEndpoint}/"
      }
    ]
  },
  "location": "{location}"
}
```

创建自定义提供程序后，可以定位其他资源，并将自定义提供程序的副作用应用于这些资源。

示例 Microsoft.自定义提供程序/关联创建请求：

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}/providers/Microsoft.CustomProviders/associations/{associationName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
  "properties": {
    "targetResourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
  }
}
```

然后，此请求将转发到您创建的自定义提供程序中指定的终结点，该终结点由以下窗体**中的目标 ResourceId**引用：

``` HTTP
PUT https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/associations/{associationName}
X-MS-CustomProviders-ExtensionPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}/providers/Microsoft.CustomProviders/associations/{associationName}
X-MS-CustomProviders-ExtendedResource: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}

{
  "properties": {
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
  }
}
```

终结点应使用应用程序/json`Content-Type`和有效的 JSON 响应正文进行响应。 在 JSON**的属性**对象下返回的字段将添加到关联返回响应中。

## <a name="getting-help"></a>获取帮助

如果您对 Azure 自定义资源提供程序开发有疑问，请尝试在[堆栈溢出](https://stackoverflow.com/questions/tagged/azure-custom-providers)上询问它们。 该论坛上可能已有类似问题的解答，因此，在发贴之前请先查看以往的提问。 添加标记 ```azure-custom-providers``` 可以快速得到回复！

## <a name="next-steps"></a>后续步骤

本文介绍了自定义提供程序。 请参阅以下文章以了解更多信息：

- [教程：使用自定义提供程序进行资源载入](./tutorial-resource-onboarding.md)
- [教程：在 Azure 中创建自定义操作和资源](./tutorial-get-started-with-custom-providers.md)
- [快速入门：创建自定义资源提供程序并部署自定义资源](./create-custom-provider.md)
- [如何：将自定义操作添加到 Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [如何：将自定义资源添加到 Azure REST API](./custom-providers-resources-endpoint-how-to.md)
