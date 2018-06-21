---
title: 将网络安全组与 Azure Site Recovery 配合使用 | Microsoft Docs
description: 介绍如何配合使用网络安全组与 Azure Site Recovery 来实现灾难恢复和迁移
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 05/21/2018
ms.author: manayar
ms.openlocfilehash: 2b35578e2dc49cd47689f62fc47c5341ea8767a4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34659193"
---
# <a name="network-security-groups-with-azure-site-recovery"></a>将网络安全组与 Azure Site Recovery 配合使用

网络安全组用于限制发往虚拟网络中的资源的网络流量。 [网络安全组 (NSG)](../virtual-network/security-overview.md#network-security-groups) 包含一个安全规则列表，这些规则可根据源或目标 IP 地址、端口和协议允许或拒绝入站或出站网络流量。

在资源管理器部署模型下，可以将 NSG 关联到子网或单个网络接口。 将 NSG 关联到子网时，规则适用于连接到该子网的所有资源。 通过将 NSG 与已经拥有关联 NSG 的子网内的单个网络接口关联，可进一步限制流量。

本文介绍如何将网络安全组与 Azure Site Recovery 配合使用。

## <a name="using-network-security-groups"></a>使用网络安全组

单个子网可以有零个或一个关联 NSG。 单个网络接口也可以有零个或一个关联 NSG。 因此，通过先将一个 NSG 关联到子网，然后将另一个 NSG 关联到 VM 的网络接口，可有效地为虚拟机提供双重流量限制。 NSG 规则在这种情况下的应用取决于流量的方向和所应用安全规则的优先级。

请考虑如下所示的具有一台虚拟机的简单示例：
-   虚拟机位于 **Contoso 子网**内。
-   **Contoso 子网**与**子网 NSG** 关联。
-   VM 网络接口还与 **VM NSG** 关联。

![将 NSG 与 Site Recovery 配合使用](./media/concepts-network-security-group-with-site-recovery/site-recovery-with-network-security-group.png)

在此示例中，对于入站流量，首先评估子网 NSG。 任何允许通过子网 NSG 的流量随后都会由 VM NSG 进行评估。 出站流量适用相反的规则，首先评估的是 VM NSG。 任何允许通过 VM NSG 的流量随后都会由子网 NSG 进行评估。

这样可实现粒度级的安全规则应用。 例如，你可能希望允许对子网下几个应用程序 VM（例如前端 VM）的入站 Internet 访问，但限制对其他 VM（例如数据库和其他后端 VM）的入站 Internet 访问。 在这种情况下，可以在子网 NSG 上设置更宽松的规则，允许 Internet 流量，并通过在 VM NSG 中拒绝访问来限制对特定 VM 的访问。 同样的规则可应用于出站流量。

设置此类 NSG 配置时，请确保将正确的优先级应用于[安全规则](../virtual-network/security-overview.md#security-rules)。 规则按优先顺序进行处理。先处理编号较小的规则，因为编号越小，优先级越高。 一旦流量与某个规则匹配，处理即会停止。 因此，不会处理优先级较低（编号较大）的、其属性与高优先级规则相同的所有规则。

将网络安全组同时应用到网络接口和子网时，你不一定总能察觉得到。 可通过查看网络接口的[有效安全规则](../virtual-network/virtual-network-network-interface.md#view-effective-security-rules)，验证已应用到网络接口的聚合规则。 还可使用 [Azure 网络观察程序](../network-watcher/network-watcher-monitoring-overview.md)中的 [IP 流验证](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md)功能来确定是否允许发往或发自网络接口的通信。 该工具会告知通信是否受允许，以及哪个网络安全规则允许或拒绝流量。

## <a name="on-premises-to-azure-replication-with-nsg"></a>使用 NSG 进行从本地到 Azure 的复制

Azure Site Recovery 支持从本地 [Hyper-V 虚拟机](hyper-v-azure-architecture.md)、[VMware 虚拟机](vmware-azure-architecture.md)和[物理服务器](physical-azure-architecture.md)向 Azure 进行灾难恢复和迁移。 对于所有本地到 Azure 的方案，复制数据都发送到 Azure 存储帐户并存储在其中。 在复制期间，无需支付任何虚拟机费用。 故障转移到 Azure 时，Site Recovery 会自动创建 Azure IaaS 虚拟机。

如果 VM 在故障转移到 Azure 后创建，则可使用 NSG 限制发往虚拟网络和 VM 的网络流量。 Site Recovery 不会在故障转移操作中创建 NSG。 建议在启动故障转移前创建所需的 Azure NSG。 可随后在故障转移期间使用具有 Site Recovery 的强大[恢复计划](site-recovery-create-recovery-plans.md)的自动化脚本，将 NSG 与故障转移 VM 自动关联。

例如，如果故障转移后的 VM 配置与上文详述的[示例方案](concepts-network-security-group-with-site-recovery.md#using-network-security-groups)类似：
-   作为目标 Azure 区域灾难恢复计划的一部分，可以创建 **Contoso VNet** 和 **Contoso 子网**。
-   作为同一灾难恢复计划的一部分，还可以创建并配置**子网 NSG** 和 **VM NSG**。
-   **子网 NSG** 随后可立即与 **Contoso 子网**关联，因为 NSG 和子网都已经可用。
-   可使用恢复计划在故障转移期间将 **VM NSG** 与 VM 关联。

创建并配置 NSG 后，建议运行[测试故障转移](site-recovery-test-failover-to-azure.md)来验证脚本化的 NSG 关联和故障转移后的 VM 连接。

## <a name="azure-to-azure-replication-with-nsg"></a>使用 NSG 进行从 Azure 到 Azure 的复制

Azure Site Recovery 支持对 [Azure 虚拟机](azure-to-azure-architecture.md)进行灾难恢复。 如果为 Azure VM 启用复制功能，Site Recovery 可以在目标区域中创建副本虚拟网络（包括子网和网关子网），并在源虚拟网络与目标虚拟网络之间创建所需的映射。 还可以预先创建目标端网络和子网，并在启用复制时使用相同的网络和子网。 在[故障转移](azure-to-azure-tutorial-failover-failback.md)前，Site Recovery 不会在目标 Azure 区域中创建任何 VM。

对于 Azure VM 复制，请确保源 Azure 区域的 NSG 规则允许复制流量的[出站连接](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges)。 还可通过此 [NSG 配置示例](azure-to-azure-about-networking.md#example-nsg-configuration)来测试和验证这些必需的规则。

Site Recovery 不会在故障转移操作中创建或复制 NSG。 建议在启动故障转移前，在目标 Azure 区域中创建所需的 NSG。 可随后在故障转移期间使用具有 Site Recovery 的强大[恢复计划](site-recovery-create-recovery-plans.md)的自动化脚本，将 NSG 与故障转移 VM 自动关联。

请考虑之前介绍的[示例方案](concepts-network-security-group-with-site-recovery.md#using-network-security-groups)：
-   如果为 VM 启用复制功能，Site Recovery 可以在目标 Azure 区域中创建 **Contoso VNet** 和 **Contoso 子网**的副本。
-   可以在目标 Azure 区域中创建**子网 NSG** 和 **VM NSG** 的所需副本（例如，分别命名为**目标子网 NSG** 和**目标 VM NSG**），从而允许使用目标区域所需的任何其他规则。
-   **目标子网 NSG** 随后可立即与目标区域子网关联，因为 NSG 和子网都已经可用。
-   在使用恢复计划进行故障转移期间，可以将**目标 VM NSG** 与 VM 关联。

创建并配置 NSG 后，建议运行[测试故障转移](azure-to-azure-tutorial-dr-drill.md)来验证脚本化的 NSG 关联和故障转移后的 VM 连接。

## <a name="next-steps"></a>后续步骤
-   详细了解[网络安全组](../virtual-network/security-overview.md#network-security-groups)。
-   详细了解 NSG [安全规则](../virtual-network/security-overview.md#security-rules)。
-   详细了解 NSG 的[高效安全规则](../virtual-network/diagnose-network-traffic-filter-problem.md)。
-   详细了解如何使用[恢复计划](site-recovery-create-recovery-plans.md)自动执行应用程序故障转移。
