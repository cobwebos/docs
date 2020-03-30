---
title: Azure 快速路由：电路配置工作流
description: 本页显示配置 ExpressRoute 线路和对等互连的工作流
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: cherylmc
ms.openlocfilehash: e833e20085d7cfd8f727acb394851e96e7e19368
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864360"
---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>ExpressRoute 线路预配工作流和线路状态
本页从较高层面引导完成服务预配和路由配置工作流。

![线路工作流](./media/expressroute-workflows/expressroute-circuit-workflow.png)

下图和相应的步骤概述了端到端预配 ExpressRoute 电路的任务。 

1. 使用 PowerShell 配置 ExpressRoute 线路。 有关更多详细信息，请遵循[创建 ExpressRoute 线路](expressroute-howto-circuit-classic.md)一文中的说明。
2. 从服务提供商订购连接。 此过程根据情况而有所不同。 有关如何订购连接的详细信息，请联系连接服务提供商。
3. 通过 PowerShell 验证 ExpressRoute 线路预配状态，以确保线路预配成功。 
4. 配置路由域 如果您的连接提供商管理第 3 层配置，他们将为您的电路配置路由。 如果连接提供商仅提供第 2 层服务，则必须根据[路由要求](expressroute-routing.md)和[路由配置](expressroute-howto-routing-classic.md)页中描述的准则配置路由。
   
   * 启用 Azure 专用对等互连 - 启用此对等互连以连接到部署在虚拟网络中的 VM/云服务。

   * 启用 Microsoft 对等互连 - 启用此功能以访问 Microsoft 联机服务，如 Office 365。 所有 Azure PaaS 服务可通过 Microsoft 对等互连访问。
     
     > [!IMPORTANT]
     > 必须确保使用独立的代理/边缘，而不是用于 Internet 的 代理/边缘来连接 Microsoft。 对 ExpressRoute 和 Internet 使用相同的边缘会导致路由不对称，并造成网络连接中断。
     > 
     > 
     
     ![路由工作流](./media/expressroute-workflows/routing-workflow.png)
5. 将虚拟网络链接到 ExpressRoute 线路 - 可以将虚拟网络链接到 ExpressRoute 线路。 请按照说明[将 VNet 链接](expressroute-howto-linkvnet-arm.md)到用户的线路。 这些 VNet 可以位于 ExpressRoute 线路所在的同一 Azure 订阅中，也可以位于不同的订阅中。

## <a name="expressroute-circuit-provisioning-states"></a>ExpressRoute 线路预配状态
每条 ExpressRoute 线路有两种状态：

* 服务提供商预配状态
* 状态

“状态”表示 Microsoft 的预配状态。 创建 Expressroute 线路时，此属性设置为 Enabled

连接服务提供商预配状态表示连接服务提供商一端的状态。 该状态可能是 NotProvisioned**、Provisioning** 或 Provisioned**。 ExpressRoute 电路必须处于预配状态，以便配置对等互连。

### <a name="possible-states-of-an-expressroute-circuit"></a>ExpressRoute 线路的可能状态
本节列出了 ExpressRoute 电路的可能状态。

**创建时**

ExpressRoute 电路将在资源创建时报告以下状态。

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


**连接服务提供商正在预配线路时**

当连接提供商正在调配电路时，ExpressRoute 电路将报告以下状态。

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled


**连接服务提供商完成预配过程时**

连接提供商成功预配电路后，ExpressRoute 电路将报告以下状态。

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled


**连接服务提供商正在取消预配线路时**

如果需要取消预配 ExpressRoute 电路，则一旦服务提供商完成取消预配过程，该电路将报告以下状态。

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


如果需要，可以选择重新启用线路，或运行 PowerShell cmdlet 删除线路。  

> [!IMPORTANT]
> 当服务提供商预配状态正在预配或预配时，无法删除电路。 连接提供程序需要取消配置电路，然后才能删除它。 在 Azure 中删除 ExpressRoute 电路资源之前，Microsoft 将继续向电路收费。
> 

## <a name="routing-session-configuration-state"></a>路由会话配置状态
如果已在 Microsoft 边缘启用了 BGP 会话，则 BGP 预配状态将报告。 必须启用状态才能使用私有或 Microsoft 对等互连。

必须特意检查 Microsoft 对等互连的 BGP 会话状态。 除了 BGP 预配状态，还有另一个状态称为播发的公共前缀状态**。 播发的公共前缀状态必须处于*配置*状态，BGP 会话必须启动，路由必须端到端地工作。 

如果播发的公共前缀状态设置为需要验证** 状态，则不会启用 BGP 会话，因为播发的前缀不符合任何路由注册表中的 AS 编号。 

> [!IMPORTANT]
> 如果播发的公共前缀状态处于*手动验证*状态，则需要在[Microsoft 支持](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)下打开支持票证，并提供您拥有所通告的 IP 地址以及关联的自治系统号码的证据。
> 
> 

## <a name="next-steps"></a>后续步骤
* 配置 ExpressRoute 连接。
  
  * [创建快速路由电路](expressroute-howto-circuit-arm.md)
  * [配置路由](expressroute-howto-routing-arm.md)
  * [将 VNet 链接到 ExpressRoute 线路](expressroute-howto-linkvnet-arm.md)

