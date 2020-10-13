---
title: 网络安全组-它的工作原理
titlesuffix: Azure Virtual Network
description: 了解网络安全组如何帮助你筛选 Azure 资源之间的网络流量。
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/24/2020
ms.author: kumud
ms.reviewer: kumud
ms.openlocfilehash: e60cdfb00d0dc9d446bd52a72e9fd15676acd285
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89458189"
---
# <a name="how-network-security-groups-filter-network-traffic"></a>网络安全组如何筛选网络流量
<a name="network-security-groups"></a>

可以使用 Azure 网络安全组来筛选进出 Azure 虚拟网络中的 Azure 资源的网络流量。 网络安全组包含[安全规则](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules)，这些规则可允许或拒绝多种 Azure 资源的入站和出站网络流量。 可以为每项规则指定源和目标、端口以及协议。

可以将资源从多个 Azure 服务部署到一个 Azure 虚拟网络中。 如需完整列表，请参阅[可部署到虚拟网络中的服务](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network)。 可将零个或一个网络安全组与虚拟机中的每个虚拟网络[子网](virtual-network-manage-subnet.md#change-subnet-settings)和[网络接口](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group)相关联。 可将同一网络安全组关联到选定的任意数量的子网和网络接口。

下图描述了如何使用不同的方案来部署网络安全组，以便网络流量通过 TCP 端口 80 出入 Internet：

![NSG 处理](./media/network-security-group-how-it-works/network-security-group-interaction.png)

请参阅上图和以下文本，了解 Azure 如何处理网络安全组的入站和出站规则：

## <a name="inbound-traffic"></a>入站流量

对于入站流量，Azure 先处理与某个子网相关联的网络安全组（如果有）中的规则，然后处理与网络接口相关联的网络安全组（如果有）中的规则。

- **VM1**：系统会处理 *NSG1* 中的安全规则，因为它与 *Subnet1* 关联，而 *VM1* 位于 *Subnet1* 中。 除非创建了一条允许端口 80 入站流量的规则，否则流量会被 [DenyAllInbound](https://docs.microsoft.com/azure/virtual-network/security-overview#denyallinbound) 默认安全规则拒绝，并且永远不会被 *NSG2* 评估，因为 *NSG2* 关联到网络接口。 如果 *NSG1* 有一条允许端口 80 的安全规则，则流量会由 *NSG2* 处理。 若要允许从端口 80 到虚拟机的流量，*NSG1* 和 *NSG2* 必须指定一条规则来允许从 Internet 到端口 80 的流量。
- **VM2**：系统会处理 *NSG1* 中的规则，因为 *VM2* 也在 *Subnet1* 中。 *VM2* 没有关联到其网络接口的网络安全组，因此会接收 *NSG1* 所允许的所有流量，或者会拒绝 *NSG1* 所拒绝的所有流量。 当网络安全组关联到子网时，对于同一子网中的所有资源，流量要么被允许，要么被拒绝。
- **VM3**：由于没有网络安全组关联到 *Subnet2*，系统允许流量进入子网并由 *NSG2* 处理，因为 *NSG2* 关联到已附加到 *VM3* 的网络接口。
- **VM4**：允许流量发往 *VM4*，因为网络安全组没有关联到 *Subnet3* 或虚拟机中的网络接口。 如果没有关联的网络安全组，则允许所有网络流量通过子网和网络接口。

## <a name="outbound-traffic"></a>出站流量

对于出站流量，Azure 先处理与某个网络接口相关联的网络安全组（如果有）中的规则，然后处理与子网相关联的网络安全组（如果有）中的规则。

- **VM1**：系统会处理 *NSG2* 中的安全规则。 除非创建一条安全规则来拒绝从端口 80 到 Internet 的出站流量，否则 *NSG1* 和 *NSG2* 中的 [AllowInternetOutbound](https://docs.microsoft.com/azure/virtual-network/security-overview#allowinternetoutbound) 默认安全规则都会允许该流量。 如果 *NSG2* 有一条拒绝端口 80 的安全规则，则流量会被拒绝，不会由 *NSG1* 评估。 若要拒绝从虚拟机到端口 80 的流量，则两个网络安全组或其中的一个必须有一条规则来拒绝从端口 80 到 Internet 的流量。
- **VM2**：所有流量都会通过网络接口发送到子网，因为附加到 *VM2* 的网络接口没有关联的网络安全组。 系统会处理 *NSG1* 中的规则。
- **VM3**：如果 *NSG2* 有一条拒绝端口 80 的安全规则，则流量会被拒绝。 如果 *NSG2* 有一条允许端口 80 的安全规则，则允许从端口 80 到 Internet 的出站流量，因为没有关联到 *Subnet2* 的网络安全组。
- **VM4**：允许来自 *VM4* 的所有网络流量，因为网络安全组没有关联到已附加到虚拟机的网络接口，也没有关联到 *Subnet3*。


## <a name="intra-subnet-traffic"></a>子网内部流量

需要注意的是，与子网关联的 NSG 中的安全规则可能会影响子网中 VM 之间的连接。 例如，如果将规则添加到拒绝所有入站和出站流量的 NSG1**，则 VM1** 和 VM2** 将无法再相互通信。 必须专门添加另一个规则来允许此通信。 

可以通过查看网络接口的[有效安全规则](virtual-network-network-interface.md#view-effective-security-rules)，轻松查看已应用到网络接口的聚合规则。 还可以使用 Azure 网络观察程序中的 [IP 流验证](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json)功能来确定是否允许发往或发自网络接口的通信。 "IP 流验证" 告诉你是允许还是拒绝通信，以及哪些网络安全规则允许或拒绝流量。

> [!NOTE]
> 网络安全组关联到子网或关联到部署在经典部署模型中的虚拟机和云服务，以及关联到资源管理器部署模型中的子网或网络接口。 若要详细了解 Azure 部署模型，请参阅[了解 Azure 部署模型](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

> [!TIP]
> 除非有特定原因，否则我们建议你将网络安全组关联到子网或网络接口，但不能同时将两者关联。 由于关联到子网的网络安全组中的规则可能与关联到网络接口的网络安全组中的规则冲突，因此可能会出现意外的必须进行故障排除的通信问题。

## <a name="next-steps"></a>后续步骤

* 若要了解哪些 Azure 资源可以部署到虚拟网络中并与网络安全组关联，请参阅 [Azure 服务的虚拟网络集成](virtual-network-for-azure-services.md)。
* 如果从未创建过网络安全组，可以先完成一个快速[教程](tutorial-filter-network-traffic.md)，获取一些创建经验。
* 如果已熟悉网络安全组，需要对其进行管理，请参阅[管理网络安全组](manage-network-security-group.md)。 
* 如果有通信问题，需要对网络安全组进行故障排除，请参阅[诊断虚拟机网络流量筛选器问题](diagnose-network-traffic-filter-problem.md)。 
* 了解如何通过[网络安全组流日志](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)来分析出入具有关联网络安全组的资源的网络流量。
