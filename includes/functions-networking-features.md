---
ms.openlocfilehash: 8d5c3fff2646536ffe5c593e13ccdab23397d44b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84906698"
---


| Feature |[消耗计划](../articles/azure-functions/functions-scale.md#consumption-plan)|[高级计划](../articles/azure-functions/functions-scale.md#premium-plan)|[专用计划](../articles/azure-functions/functions-scale.md#app-service-plan)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/azure-functions/functions-kubernetes-keda.md) |
|----------------|-----------|----------------|---------|-----------------------| ---|
|[入站 IP 限制和专用站点访问](../articles/azure-functions/functions-networking-options.md#inbound-ip-restrictions)|✅是|✅是|✅是|✅是|✅是|
|[虚拟网络集成](../articles/azure-functions/functions-networking-options.md#virtual-network-integration)|❌否|✅是（区域）|✅是（区域和网关）|✅是| ✅是|
|[虚拟网络触发器（非 HTTP）](../articles/azure-functions/functions-networking-options.md#virtual-network-triggers-non-http)|❌否| ✅是 |✅是|✅是|✅是|
|[混合连接](../articles/azure-functions/functions-networking-options.md#hybrid-connections)（仅限 Windows）|❌否|✅是|✅是|✅是|✅是|
|[出站 IP 限制](../articles/azure-functions/functions-networking-options.md#outbound-ip-restrictions)|❌否| ✅是|✅是|✅是|✅是|