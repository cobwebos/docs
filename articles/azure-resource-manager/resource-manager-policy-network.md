---
title: "适用于网络资源的 Azure 资源策略 | Microsoft Docs"
description: "介绍了用于管理网络资源部署的 Azure Resource Manager 策略。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/05/2017
ms.author: tomfitz
ms.openlocfilehash: bca66bbdd9da9b3e4099d0d961f42c9368a17f5e
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="apply-resource-policies-to-network-resources"></a>向网络资源应用资源策略
本文展示了可应用于 Azure 虚拟网关[资源策略](resource-manager-policy.md)示例。 此策略可确保组织中部署的网关的一致性。 

## <a name="define-permitted-virtual-network-gateway-sku"></a>定义获准的虚拟网关 SKU

下面的策略限制了可以为虚拟网关部署哪些 SKU：

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Network/virtualNetworkGateways"
      },
      {
        "not": {
          "field": "Microsoft.Network/virtualNetworkGateways/sku.name",
          "in": [
            "Basic",
            "VpnGw1"
          ]
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="next-steps"></a>后续步骤
* 定义策略规则后（如上述示例所示），需要创建策略定义并将其分配给作用域。 作用域可以是订阅、资源组或资源。 若要通过门户分配策略，请参阅[使用 Azure 门户分配和管理资源策略](resource-manager-policy-portal.md)。 若要通过 REST API、PowerShell 或 Azure CLI 分配策略，请参阅[通过脚本分配和管理策略](resource-manager-policy-create-assign.md)。 
* 有关企业可如何使用 Resource Manager 有效管理订阅的指南，请参阅 [Azure 企业基架 - 出于合规目的监管订阅](resource-manager-subscription-governance.md)。

