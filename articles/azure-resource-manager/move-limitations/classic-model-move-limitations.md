---
title: 将 Azure 经典部署资源移到新订阅或资源组
description: 使用 Azure 资源管理器将经典部署资源移到新的资源组或订阅。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: 4770f957b6b9eea75b50776a7491b1ca479e50e2
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723501"
---
# <a name="move-guidance-for-classic-deployment-model-resources"></a>移动经典部署模型资源的指南

若要移动通过经典模型部署的资源的步骤因是否要移动的资源在订阅中或新的订阅。

## <a name="move-in-the-same-subscription"></a>在同一订阅中移动

在同一订阅内将资源从一个资源组移到另一个资源组时存在以下限制：

* 不能移动虚拟网络（经典）。
* 虚拟机（经典）必须与云服务一起移动。
* 移动云服务时，必须移动其所有虚拟机。
* 一次只能移动一项云服务。
* 一次只能移动一个存储帐户（经典）。
* 存储帐户（经典）与虚拟机或云服务不能在同一操作中移动。

若要将经典资源移到同一订阅中的新资源组，请使用[标准移动操作](../resource-group-move-resources.md)通过门户、 Azure PowerShell、 Azure CLI 或 REST API。 使用的操作应与移动 Resource Manager 资源时所用的操作相同。

## <a name="move-across-subscriptions"></a>在订阅之间移动

将资源移到新订阅时存在以下限制：

* 必须在同一操作中移动订阅中的所有经典资源。
* 目标订阅不得包含任何其他经典资源。
* 只能通过独立的适用于经典移动的 REST API 来请求移动。 将经典资源移到新订阅时，不能使用标准的资源管理器移动命令。

要将经典资源移动到新订阅，请使用特定于经典资源的 REST 操作。 若要使用 REST，请执行以下步骤操作：

1. 检查源订阅是否可以参与跨订阅移动。 使用以下操作：

   ```HTTP
   POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     在请求正文中包括：

   ```json
   {
    "role": "source"
   }
   ```

     验证操作的响应采用以下格式：

   ```json
   {
    "status": "{status}",
    "reasons": [
      "reason1",
      "reason2"
    ]
   }
   ```

1. 检查目标订阅是否可以参与跨订阅移动。 使用以下操作：

   ```HTTP
   POST https://management.azure.com/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     在请求正文中包含以下内容：

   ```json
   {
    "role": "target"
   }
   ```

     响应的格式与源订阅验证相同。
1. 如果两个订阅都通过了验证，可使用以下操作将所有经典资源从一个订阅移到另一个订阅：

   ```HTTP
   POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01
   ```

    在请求正文中包含以下内容：

   ```json
   {
    "target": "/subscriptions/{target-subscription-id}"
   }
   ```

运行该操作可能需要几分钟。

## <a name="next-steps"></a>后续步骤

如果移动经典资源时遇到问题，请联系[支持](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。

有关移动资源的命令，请参阅[将资源移动至新资源组或订阅](../resource-group-move-resources.md)。
