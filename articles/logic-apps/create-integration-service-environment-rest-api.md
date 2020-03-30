---
title: 使用逻辑应用 REST API 创建集成服务环境 （ISEs）
description: 通过使用逻辑应用 REST API 创建集成服务环境 （ISE），以便可以从 Azure 逻辑应用访问 Azure 虚拟网络 （VNET）
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 2c6e35b1e7d160064998004f87c5b14d0eaeac5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127654"
---
# <a name="create-an-integration-service-environment-ise-by-using-the-logic-apps-rest-api"></a>使用逻辑应用 REST API 创建集成服务环境 （ISE）

本文演示如何通过逻辑应用 REST API 创建[*集成服务环境*（ISE），](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)用于逻辑应用和集成帐户需要访问[Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)的方案。 ISE 是一个隔离的环境，它使用专用存储和其他资源，这些资源与"全局"多租户逻辑应用服务分开。 这种分离还减少了其他 Azure 租户可能对应用性能产生的影响。 ISE 还为您提供自己的静态 IP 地址。 这些 IP 地址与公共多租户服务中的逻辑应用共享的静态 IP 地址分开。

要使用 Azure 门户创建 ISE，请参阅[从 Azure 逻辑应用连接到 Azure 虚拟网络](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)。

> [!IMPORTANT]
> 在 ISE 中运行的逻辑应用、内置触发器、内置操作和连接器使用不同于基于消耗的定价计划的定价计划。 要了解 ISEs 的定价和计费的工作原理，请参阅[逻辑应用定价模型](../logic-apps/logic-apps-pricing.md#fixed-pricing)。 有关定价费率，请参阅[逻辑应用定价](../logic-apps/logic-apps-pricing.md)。

## <a name="prerequisites"></a>先决条件

* [启用 ISE 访问](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access)[的先决条件](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites)和要求与在 Azure 门户中创建 ISE 时相同

* 一种工具，可用于使用 HTTPS PUT 请求调用逻辑应用 REST API 来创建 ISE。 例如，您可以使用[Postman](https://www.getpostman.com/downloads/)，也可以生成执行此任务的逻辑应用。

## <a name="send-the-request"></a>发送请求

要通过调用逻辑应用 REST API 创建 ISE，请发出此 HTTPS PUT 请求：

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> 逻辑应用 REST API 2019-05-01 版本要求您对 ISE 连接器发出自己的 HTTP PUT 请求。

部署通常需要两个小时才能完成。 有时，部署可能需要长达四个小时。 要检查部署状态，请在 Azure 工具栏上的[Azure 门户](https://portal.azure.com)中，选择通知图标，该图标将打开通知窗格。

> [!NOTE]
> 如果部署失败或删除 ISE，Azure 在释放子网之前可能需要长达一个小时。 此延迟意味着您可能需要等待，然后再在另一个 ISE 中重用这些子网。
>
> 如果删除虚拟网络，Azure 通常会在释放子网之前最多需要两个小时，但此操作可能需要更长时间。 
> 删除虚拟网络时，请确保没有资源仍然连接。 
> 请参阅[删除虚拟网络](../virtual-network/manage-virtual-network.md#delete-a-virtual-network)。

## <a name="request-header"></a>请求标头

在请求标头中，包括以下属性：

* `Content-type`：将此属性值设置为`application/json`。

* `Authorization`：为有权访问要使用的 Azure 订阅或资源组的客户设置此属性值。

### <a name="request-body-syntax"></a>请求正文语法

下面是请求正文语法，它描述了创建 ISE 时要使用的属性：

```json
{
   "id": "/subscriptions/{Azure-subscription-ID/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-1}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-2}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-3}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-4}",
            }
         ]
      }
   }
}
```

### <a name="request-body-example"></a>请求正文示例

此示例请求正文显示示例值：

```json
{
   "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Logic/integrationServiceEnvironments/Fabrikam-ISE",
   "name": "Fabrikam-ISE",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "WestUS2",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-1",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-2",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-3",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-4",
            }
         ]
      }
   }
}
```

## <a name="next-steps"></a>后续步骤

* [向集成服务环境添加资源](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [管理集成服务环境](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)

