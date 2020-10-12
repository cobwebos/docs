---
title: Azure ExpressRoute：线路配置工作流
description: 本页显示配置 ExpressRoute 线路和对等互连的工作流
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/24/2020
ms.author: duau
ms.custom: contperfq1
ms.openlocfilehash: bb0d3cebd9382ef2c81b217417cfbcb6198b6ebb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89565917"
---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>ExpressRoute 线路预配工作流和线路状态

本文从较高层面引导完成服务预配和路由配置工作流。 以下部分概述了用于端到端预配 ExpressRoute 线路的任务。

## <a name="workflow-steps"></a>工作流步骤

### <a name="1-prerequisites"></a>1.先决条件

确保满足先决条件。 有关完整列表，请参阅[先决条件和清单](expressroute-prerequisites.md)。

* Azure 订阅已创建。
* 已与 ExpressRoute 合作伙伴建立了物理连接，或通过ExpressRoute Direct 配置了物理连接。 查看位置，请参阅[位置和合作伙伴](expressroute-locations-providers.md#partners)，以查看跨对等互连位置的 ExpressRoute 合作伙伴和 ExpressRoute Direct 连接。

### <a name="2-order-connectivity-or-configure-expressroute-direct"></a>2.订购连接或配置 ExpressRoute Direct

从服务提供商订购连接或配置 ExpressRoute Direct 连接。

#### <a name="expressroute-partner-model"></a>ExpressRoute 合作伙伴模型

从服务提供商订购连接。 此过程根据情况而有所不同。 有关如何订购连接的详细信息，请联系连接服务提供商。

* 选择 ExpressRoute 合作伙伴
* 选择对等互连位置
* 选择带宽
* 选择计费模型
* 选择标准或高级外接程序

#### <a name="expressroute-direct-model"></a>ExpressRoute Direct 模型

* 跨对等互连位置查看可用的 ExpressRoute Direct 容量。
* 通过在 Azure 订阅中创建 ExpressRoute Direct 资源来保留端口。
* 请求并接收授权书，并从对等互连位置提供商处订购物理交叉连接。
* 使用 [Azure Monitor](expressroute-monitoring-metrics-alerts.md#expressroute-direct-metrics) 启用管理员状态并查看轻型级别和物理链接。

### <a name="3-create-an-expressroute-circuit"></a>3.创建 ExpressRoute 线路

#### <a name="expressroute-partner-model"></a>ExpressRoute 合作伙伴模型

验证 ExpressRoute 合作伙伴是否已准备好预配连接。 从发布服务密钥的那一刻起，将对 ExpressRoute 线路进行计费。 使用[创建 ExpressRoute 线路](expressroute-howto-circuit-portal-resource-manager.md)中的说明来创建线路。

#### <a name="expressroute-direct-model"></a>ExpressRoute Direct 模型

确保跨两个链接启用了物理链接和管理状态。 有关指导，请参阅[如何配置 ExpressRoute Direct](how-to-expressroute-direct-portal.md)。 从发布服务密钥的那一刻起，将对 ExpressRoute 线路进行计费。 使用[创建 ExpressRoute 线路](expressroute-howto-circuit-portal-resource-manager.md)中的说明来创建线路。

### <a name="4-service-provider-provisions-connectivity"></a>4.服务提供商预配连接

本部分仅适用于 ExpressRoute 合作伙伴连接模型：

* 向连接服务提供商提供服务密钥 (s-key)。
* 提供连接服务提供商所需的其他信息（例如 VPN ID）。
* 如果由提供商管理路由配置，请提供必要的详细信息。

使用 PowerShell、Azure 门户或 CLI 验证 ExpressRoute 线路预配状态，可确保线路预配成功。

### <a name="5-configure-routing-domains"></a>5.配置路由域

配置路由域 如果连接服务提供商管理第 3 层配置，他们将为你的线路配置路由。 如果连接服务提供商只提供第 2 层服务或使用 ExpressRoute Direct，必须根据[路由要求](expressroute-routing.md)和[路由配置](expressroute-howto-routing-classic.md)文章中所述的每条指导原则来配置路由。

#### <a name="for-azure-private-peering"></a>对于 Azure 专用对等互连

启用专用对等互连以连接到部署在 Azure 虚拟网络中的 VM 和云服务。

* 路径 1（共 30 条）的对等互连子网
* 路径 2（共 30 条）的对等互连子网
* 对等互连的 VLAN ID
* 对等互连的 ASN
* ExpressRoute ASN = 12076
* MD5 哈希（可选）

#### <a name="for-microsoft-peering"></a>对于 Microsoft 对等互连

启用此类以访问 Microsoft 联机服务，如 Microsoft 365。 此外，所有的 Azure PaaS 服务也可通过 Microsoft 对等互连访问。 必须确保使用独立的代理/边缘，而不是用于 Internet 的代理/边缘来连接 Microsoft。 对 ExpressRoute 和 Internet 使用相同的边缘会导致路由不对称，并造成网络连接中断。

* 路径 1（共 30 条）的对等互连子网 - 必须是公共 IP
* 路径 2（共 30 条）的对等互连子网 - 必须是公共 IP
* 对等互连的 VLAN ID
* 对等互连的 ASN
* 播发的前缀 - 必须是公共 IP 前缀
* 客户 ASN（如果与对等互连 ASN 不同，可省略）
* 用于 IP 和 ASN 验证的 RIR / IRR
* ExpressRoute ASN = 12076
* MD5 哈希（可选）

### <a name="6-start-using-the-expressroute-circuit"></a>6.开始使用 ExpressRoute 线路

* 可以将 Azure 虚拟网络链接到 ExpressRoute 线路，以允许从本地连接到 Azure 虚拟网络。 请参阅[将 VNet 链接到线路](expressroute-howto-linkvnet-arm.md)一文以获取指导。 这些 VNet 可以位于 ExpressRoute 线路所在的同一 Azure 订阅中，也可以位于不同的订阅中。
* 通过 Microsoft 对等互连连接到 Azure 服务和 Microsoft 云服务。

##  <a name="expressroute-partner-circuit-provisioning-states"></a><a name="expressroute-circuit-provisioning-states"></a>ExpressRoute 合作伙伴线路预配状态

以下部分概述了 ExpressRoute 线路状态与 ExpressRoute 合作伙伴连接模型的不同之处。
每条 ExpressRoute 合作伙伴线路有两种状态：

* ServiceProviderProvisioningState 表示连接服务提供商一端的状态。 该状态可能是 NotProvisioned  、Provisioning  或 Provisioned  。 ExpressRoute 线路必须处于 Provisioned 状态，才能配置对等互连。 此状态仅适用于 ExpressRoute 合作伙伴线路，而不会显示在 ExpressRoute Direct 线路的属性中。

* “状态”表示 Microsoft 的预配状态。 创建 ExpressRoute 线路时，此属性设置为 Enabled

### <a name="possible-states-of-an-expressroute-circuit"></a>ExpressRoute 线路的可能状态

本部分概述了 ExpressRoute 合作伙伴连接模型下创建的 ExpressRoute 线路的可能状态。

**创建时**

ExpressRoute 线路会在资源创建时报告以下状态。

```output
ServiceProviderProvisioningState : NotProvisioned
Status                           : Enabled
```

**连接服务提供商正在预配线路时**

连接服务提供商预配线路时，ExpressRoute 线路将报告以下状态。

```output
ServiceProviderProvisioningState : Provisioning
Status                           : Enabled
```

**连接服务提供商已完成预配过程时**

连接服务提供商成功预配线路后，ExpressRoute 线路将报告以下状态。

```output
ServiceProviderProvisioningState : Provisioned
Status                           : Enabled
```

**连接服务提供商正在取消预配线路时**

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