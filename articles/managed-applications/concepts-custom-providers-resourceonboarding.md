---
title: Azure 自定义提供程序资源载入
description: 了解如何使用 Azure 自定义提供程序执行资源载入，以便将管理或配置应用到其他 Azure 资源类型。
author: jjbfour
ms.service: managed-applications
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: jobreen
ms.openlocfilehash: 65e0f795a2b0efa327aec02c01cdb3706bf91d29
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818785"
---
# <a name="azure-custom-providers-resource-onboarding-overview"></a>Azure 自定义提供程序资源加入概述

Azure 自定义提供程序资源载入是适用于 Azure 资源类型的扩展性模型。 它可让你大规模地跨现有 Azure 资源应用操作或管理。 有关详细信息，请参阅[Azure 自定义提供程序如何扩展 azure](./custom-providers-overview.md)。 本文介绍：

- 可以执行的操作。
- 资源加入基础知识，以及如何使用它。
- 在哪里可以找到要开始的指南和代码示例。

> [!IMPORTANT]
> 自定义提供程序当前提供公共预览版。
> 此预览版在提供时没有附带服务级别协议，我们不建议将其用于生产工作负荷。 某些功能可能不受支持，或可能具有受限制的功能。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="what-can-resource-onboarding-do"></a>资源载入有哪些功能？

类似于[Azure 自定义提供程序自定义资源](./custom-providers-resources-endpoint-how-to.md)，资源加入定义了一个协定，该协定将向终结点代理 "载入" 请求。 与自定义资源不同，资源加入不会创建新的资源类型。 相反，它允许扩展现有的资源类型。 和资源加入适用于 Azure 策略，因此可以大规模完成资源的管理和配置。 资源加入工作流的一些示例：

- 安装和管理到虚拟机扩展。
- 在 Azure 存储帐户上上传和配置默认值。
- 按比例启用基线诊断设置。

## <a name="resource-onboarding-basics"></a>资源加入基础知识

使用 CustomProviders/resourceProviders 和 CustomProviders/association 资源类型，通过 Azure 自定义提供程序配置资源加入。 若要为自定义提供程序启用资源载入，请在配置过程中，创建一个名为 "association" 的**resourceType** ，其中包含包含 "Extension" 的**routingType** 。 CustomProviders/association 和 CustomProviders/resourceProviders 不需要属于同一资源组。

下面是一个示例 Azure 自定义提供程序：

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

属性 | 必需？ | 说明
---|---|---
name | 是 | 终结点定义的名称。 对于资源加入，此名称必须是 "关联"。
routingType | 是 | 确定与终结点的协定的类型。 对于资源加入，有效的**routingTypes**为 "代理、缓存、扩展" 和 "Webhook，缓存，扩展"。
endpoint | 是 | 终结点，可向其路由请求。 这将处理请求的响应和任何副作用。

使用关联资源类型创建自定义提供程序后，可以使用 CustomProviders/association 进行目标。 CustomProviders/association 是可扩展任何其他 Azure 资源的扩展资源。 当创建 CustomProviders/association 的实例时，它将采用**targetResourceId**属性，该属性应该是有效的 CustomProviders/ResourceProviders 或 Microsoft. 解决方案/应用程序资源 ID。 在这些情况下，请求将转发到你创建的 CustomProviders/resourceProviders 实例上的关联资源类型。

> [!NOTE]
> 如果将 Microsoft. 解决方案/应用程序资源 ID 作为**targetResourceId**提供，则必须在名为 "public" 的托管资源组中部署 CustomProviders/resourceProviders。

示例 Azure 自定义提供程序关联：

```JSON
{
  "properties": {
    "targetResourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
    ...
  }
}
```

属性 | 必需？ | 说明
---|---|---
targetResourceId | 是 | CustomProviders/resourceProviders 或 Microsoft 应用程序的资源 ID。

## <a name="how-to-use-resource-onboarding"></a>如何使用资源载入

资源载入通过使用 CustomProviders/association 扩展资源扩展其他资源来工作。 在下面的示例中，对虚拟机发出了请求，但可以扩展任何资源。

首先，需要创建具有关联资源类型的自定义提供程序资源。 这将声明在创建了相应的 CustomProviders/association 资源（面向自定义提供程序）时将使用的回调 URL。

示例 CustomProviders/resourceProviders 创建请求：

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

创建自定义提供程序后，可以将其他资源作为目标，并将自定义提供程序的副作用应用于这些资源。

示例 CustomProviders/关联创建请求：

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

然后，此请求将转发到你创建的自定义提供程序中指定的终结点，该终结点由此形式的**targetResourceId**引用：

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

终结点应使用 application/json `Content-Type` 和有效的 JSON 响应正文来做出响应。 JSON 的**properties**对象下返回的字段将添加到关联返回响应中。

## <a name="getting-help"></a>获取帮助

如果有关于 Azure 自定义资源提供程序开发的问题，请尝试[Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers)。 可能已经回答过类似的问题，因此请先检查，然后再发布。 添加标记 ```azure-custom-providers``` 可以快速得到回复！

## <a name="next-steps"></a>后续步骤

本文介绍了自定义提供程序。 请参阅以下文章了解详细信息：

- [教程：资源加入自定义提供程序](./tutorial-custom-providers-resource-onboarding.md)
- [教程：在 Azure 中创建自定义操作和资源](./tutorial-custom-providers-101.md)
- [快速入门：创建自定义资源提供程序并部署自定义资源](./create-custom-provider.md)
- [如何：向 Azure REST API 添加自定义操作](./custom-providers-action-endpoint-how-to.md)
- [如何：向 Azure REST API 添加自定义资源](./custom-providers-resources-endpoint-how-to.md)
