---
title: Azure ExpressRoute：线路配置工作流
description: 此页面显示了用于配置 ExpressRoute 线路和对等互连的工作流
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/24/2020
ms.author: cherylmc
ms.custom: contperfq1
ms.openlocfilehash: 229b7c145fa38443d2bc5f99005078ffa7f77065
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2020
ms.locfileid: "88814070"
---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>ExpressRoute 线路预配工作流和线路状态

本文逐步讲解服务预配和路由配置工作流。 以下部分概述了用于端到端预配 ExpressRoute 线路的任务。

## <a name="workflow-steps"></a>工作流步骤

### <a name="1-prerequisites"></a>1.先决条件

确保满足先决条件。 有关完整列表，请参阅 [先决条件和清单](expressroute-prerequisites.md)。

* 已创建 Azure 订阅。
* 物理连接已与 ExpressRoute 合作伙伴建立了连接，或已通过 ExpressRoute Direct 进行了配置。 查看位置，请参阅 [位置和合作伙伴](expressroute-locations-providers.md#partners) ，查看 expressroute 合作伙伴和对等互连位置的 expressroute 直接连接。

### <a name="2-order-connectivity-or-configure-expressroute-direct"></a>2. 订单连接或配置 ExpressRoute 直接

从服务提供商订购连接或配置 ExpressRoute 直接连接。

#### <a name="expressroute-partner-model"></a>ExpressRoute 合作伙伴模型

从服务提供商订购连接。 此过程根据情况而有所不同。 有关如何订购连接的详细信息，请联系连接服务提供商。

* 选择 ExpressRoute 合作伙伴
* 选择对等互连位置
* 选择带宽
* 选择计费模型
* 选择标准或高级外接程序

#### <a name="expressroute-direct-model"></a>ExpressRoute 直接模型

* 跨对等互连位置查看可用的 ExpressRoute 直接容量。
* 通过在 Azure 订阅中创建 ExpressRoute 直接资源来保留端口。
* 请求并接收授权，并从对等位置提供程序对物理交叉连接进行排序。
* 使用 [Azure Monitor](expressroute-monitoring-metrics-alerts.md#expressroute-direct-metrics)启用 "管理状态" 和 "查看"。

### <a name="3-create-an-expressroute-circuit"></a>3. 创建 ExpressRoute 线路

#### <a name="expressroute-partner-model"></a>ExpressRoute 合作伙伴模型

验证 ExpressRoute 合作伙伴是否已准备好预配连接。 从发布服务密钥的那一刻起，将对 ExpressRoute 线路进行计费。 使用 [创建 ExpressRoute 线路](expressroute-howto-circuit-portal-resource-manager.md) 中的说明创建线路。

#### <a name="expressroute-direct-model"></a>ExpressRoute 直接模型

确保两个链接都启用了物理链接和管理状态。 有关指导，请参阅 [如何配置 ExpressRoute 直通](how-to-expressroute-direct-portal.md) 。 从发布服务密钥的那一刻起，将对 ExpressRoute 线路进行计费。 使用 [创建 ExpressRoute 线路](expressroute-howto-circuit-portal-resource-manager.md) 中的说明创建线路。

### <a name="4-service-provider-provisions-connectivity"></a>4. 服务提供程序设置连接

本部分仅适用于 ExpressRoute 合作伙伴连接性模型：

* 向连接服务提供商提供服务密钥 (s 键) 。
* 提供连接服务提供商所需的附加信息 (例如，VPN ID) 。
* 如果提供程序管理路由配置，请提供必要的详细信息。

可以通过使用 PowerShell、Azure 门户或 CLI 验证 ExpressRoute 线路预配状态，确保已成功预配线路。

### <a name="5-configure-routing-domains"></a>5. 配置路由域

配置路由域 如果连接服务提供商管理第 3 层配置，他们将为你的线路配置路由。 如果连接服务提供商只提供第2层服务，或者使用 ExpressRoute Direct，则必须按照 [路由要求](expressroute-routing.md) 和 [路由配置](expressroute-howto-routing-classic.md) 文章中所述的指导原则来配置路由。

#### <a name="for-azure-private-peering"></a>对于 Azure 专用对等互连

启用私有对等互连以连接到在 Azure 虚拟网络中部署的 Vm 和云服务。

* 路径 1 (/30) 对等互连子网
* 路径 2 (/30) 对等互连子网
* 对等互连的 VLAN ID
* 对等互连的 ASN
* ExpressRoute ASN = 12076
* MD5 哈希 (可选) 

#### <a name="for-microsoft-peering"></a>对于 Microsoft 对等互连

启用此类以访问 Microsoft 联机服务，如 Office 365。 此外，所有的 Azure PaaS 服务也可通过 Microsoft 对等互连访问。 你必须确保使用独立的代理/边缘来连接到 Microsoft，而不是用于 Internet 的代理/边缘。 对 ExpressRoute 和 Internet 使用相同的边缘会导致路由不对称，并造成网络连接中断。

* 路径 1 (/30) 的对等互连子网必须是公共 IP
* 路径 2 (/30) 的对等互连子网必须是公共 IP
* 对等互连的 VLAN ID
* 对等互连的 ASN
* 播发的前缀-必须是公共 IP 前缀
* 客户 ASN (可选（如果不同于对等互连 ASN) 
* 用于 IP 和 ASN 验证的 RIR/IRR
* ExpressRoute ASN = 12076
* MD5 哈希 (可选) 

### <a name="6-start-using-the-expressroute-circuit"></a>6. 开始使用 ExpressRoute 线路

* 可以将 Azure 虚拟网络链接到 ExpressRoute 线路，以便能够从本地连接到 Azure 虚拟网络。 有关指导，请参阅将 [VNet 链接到线路一](expressroute-howto-linkvnet-arm.md) 文。 这些 VNet 可以位于 ExpressRoute 线路所在的同一 Azure 订阅中，也可以位于不同的订阅中。
* 通过 Microsoft 对等互连连接到 Azure 服务和 Microsoft 云服务。

##  <a name="expressroute-partner-circuit-provisioning-states"></a><a name="expressroute-circuit-provisioning-states"></a>ExpressRoute 合作伙伴线路预配状态

以下部分概述了 ExpressRoute 合作伙伴连接模型的不同 ExpressRoute 线路状态。
每个 ExpressRoute 合作伙伴线路都有两种状态：

* **ServiceProviderProvisioningState** 表示连接提供商端的状态。 该状态可能是 NotProvisioned  、Provisioning  或 Provisioned  。 ExpressRoute 线路必须处于已预配状态才能配置对等互连。 **此状态仅适用于 expressroute 合作伙伴线路，并且不显示在 Expressroute 直接线路的属性中**。

* "**状态**" 表示 Microsoft 的预配状态。 创建 ExpressRoute 线路时，此属性设置为 "已启用"

### <a name="possible-states-of-an-expressroute-circuit"></a>ExpressRoute 线路的可能状态

本部分概述了在 ExpressRoute 合作伙伴连接模式下创建的 ExpressRoute 线路的可能状态。

**创建时**

ExpressRoute 线路会在资源创建时报告以下状态。

```output
ServiceProviderProvisioningState : NotProvisioned
Status                           : Enabled
```

**当连接服务提供商正在预配线路时**

连接服务提供商预配线路时，ExpressRoute 线路将报告以下状态。

```output
ServiceProviderProvisioningState : Provisioning
Status                           : Enabled
```

**当连接服务提供商完成预配过程时**

连接服务提供商成功预配线路后，ExpressRoute 线路将报告以下状态。

```output
ServiceProviderProvisioningState : Provisioned
Status                           : Enabled
```

**当连接服务提供商取消对线路的预配时**

如果需要取消预配 ExpressRoute 线路，在服务提供商完成取消预配过程后，线路将报告以下状态。

```output
ServiceProviderProvisioningState : NotProvisioned
Status                           : Enabled
```

如果需要，可以选择重新启用线路，或运行 PowerShell cmdlet 删除线路。  

> [!IMPORTANT]
> ServiceProviderProvisioningState 为 Provisioning 或 Provisioned 时，无法删除线路。 连接服务提供商需要取消预配线路，然后才能将其删除。 在 Azure 中删除 ExpressRoute 线路资源之前，Microsoft 将继续对线路进行计费。
> 

## <a name="routing-session-configuration-state"></a>路由会话配置状态

BGP 预配状态会报告是否已在 Microsoft Edge 上启用 BGP 会话。 必须启用该状态才能使用专用对等互连或 Microsoft 对等互连。

必须特意检查 Microsoft 对等互连的 BGP 会话状态。 除了 BGP 预配状态，还有另一个状态称为 *播发的公共前缀状态*。 播发的公共前缀状态必须是“已配置”状态，BGP 会话才能启动，才能进行端到端的路由。 

如果播发的公共前缀状态设置为需要验证状态，则不会启用 BGP 会话，因为播发的前缀不符合任何路由注册表中的 AS 编号。

> [!IMPORTANT]
> 如果播发的公共前缀状态是“手动验证”状态，则需要向 [Microsoft 支持](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)开具支持票证，并提供拥有播发 IP 地址的证明以及相关的自治系统编号。
> 
> 

## <a name="next-steps"></a>后续步骤

* 配置 ExpressRoute 连接。
  
  * [创建 ExpressRoute 线路](expressroute-howto-circuit-arm.md)
  * [配置路由](expressroute-howto-routing-arm.md)
  * [将 VNet 链接到 ExpressRoute 线路](expressroute-howto-linkvnet-arm.md)