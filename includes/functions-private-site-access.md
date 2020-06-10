---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 5e0cff7bde6e80a776d694820ca7b69dafa7c0d9
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83648811"
---
专用站点访问是指使应用只能从专用网络（例如 Azure 虚拟网络）进行访问。

* 配置服务终结点时，可在[高级](../articles/azure-functions/functions-premium-plan.md)、[消耗](../articles/azure-functions/functions-scale.md#consumption-plan)和[应用服务](../articles/azure-functions/functions-scale.md#app-service-plan)计划中获取专用站点访问。
    * 可以在“平台功能” > “网络” > “配置访问限制” > “添加规则”下为每个应用配置服务终结点。 现在可以选择虚拟网络作为规则类型。
    * 有关详细信息，请参阅[虚拟网络服务终结点](../articles/virtual-network/virtual-network-service-endpoints-overview.md)。
    * 请记住，使用服务终结点时，即使配置了虚拟网络集成，你的函数也还是对 Internet 具有完全出站访问权限。
* 还可在配置了内部负载均衡器 (ILB) 的应用服务环境中获取专用站点访问。 有关详细信息，请参阅[在应用服务环境中创建和使用内部负载均衡器](../articles/app-service/environment/create-ilb-ase.md)。

若要了解如何设置专用站点访问，请参阅[建立 Azure Functions 专用站点访问](../articles/azure-functions/functions-create-private-site-access.md)。