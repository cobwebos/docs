---
title: 关于将 ExpressRoute 用于 Azure Site Recovery
description: 介绍如何结合使用 Azure ExpressRoute 与 Azure Site Recovery 服务来实现灾难恢复和迁移。
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/13/2019
ms.author: mayg
ms.openlocfilehash: e4525bdc6165e8e736db5f539c764d25250cb248
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75465215"
---
# <a name="azure-expressroute-with-azure-site-recovery"></a>将 Azure ExpressRoute 与 Azure Site Recovery 结合使用

使用 Microsoft Azure ExpressRoute 可通过连接服务提供商所提供的专用连接，将本地网络扩展到 Microsoft 云。 使用 ExpressRoute 可与 Microsoft Azure、Office 365 和 Dynamics 365 等 Microsoft 云服务建立连接。

本文介绍如何结合使用 Azure ExpressRoute 与 Azure Site Recovery 来实现灾难恢复和迁移。

## <a name="expressroute-circuits"></a>ExpressRoute 线路

ExpressRoute 线路表示通过连接提供商在本地基础结构与 Microsoft 云服务之间建立的逻辑连接。 可以订购多条 ExpressRoute 线路。 每条线路可以位于相同或不同的区域，且可以通过不同的连接提供程序连接到本地。 从[此处](../expressroute/expressroute-circuit-peerings.md)详细了解 ExpressRoute 线路。

ExpressRoute 线路具有多个关联的路由域。 从[此处](../expressroute/expressroute-circuit-peerings.md#peeringcompare)详细了解并比较 ExpressRoute 路由域。

## <a name="on-premises-to-azure-replication-with-expressroute"></a>使用 ExpressRoute 进行本地到 Azure 的复制

Azure Site Recovery 支持从本地 [Hyper-V 虚拟机](hyper-v-azure-architecture.md)、[VMware 虚拟机](vmware-azure-architecture.md)和[物理服务器](physical-azure-architecture.md)向 Azure 进行灾难恢复和迁移。 对于所有本地到 Azure 的方案，复制数据都发送到 Azure 存储帐户并存储在其中。 在复制期间，无需支付任何虚拟机费用。 故障转移到 Azure 时，Site Recovery 会自动创建 Azure IaaS 虚拟机。

Site Recovery 通过公共终结点将数据复制到 Azure 存储帐户或目标 Azure 区域中的副本托管磁盘。 若要将 ExpressRoute 用于 Site Recovery 复制流量，可以利用[Microsoft 对等互连](../expressroute/expressroute-circuit-peerings.md#microsoftpeering)或现有的[公共对等互连](../expressroute/about-public-peering.md)（新创建的弃用）。 在复制时，建议使用 Microsoft 对等互连作为路由域。 请注意，不支持通过专用对等互连进行复制。

确保还满足了配置服务器的[网络要求](vmware-azure-configuration-server-requirements.md#network-requirements)。 配置服务器需要连接到特定 Url 才能进行 Site Recovery 复制的业务流程。 ExpressRoute 不能用于此连接。 

如果你在本地使用代理并想要将 ExpressRoute 用于复制通信，则需要在配置服务器和进程服务器上配置代理绕过列表。 请遵循以下步骤进行配置：

- 从[此处](https://aka.ms/PsExec)下载 PsExec 工具来访问系统用户上下文。
- 通过运行以下命令行 psexec-s-i "%Programfiles%\internet explorer\ Explorer\iexplore.exe" 在系统用户上下文中打开 Internet Explorer
- 在 IE 中添加代理设置
- 在跳过列表中，添加 Azure 存储 URL *. blob.core.windows.net

这将确保只有复制流量流过 ExpressRoute，而通信可以通过代理。

在将虚拟机或服务器故障转移到 Azure 虚拟网络后，可以使用[专用对等互连](../expressroute/expressroute-circuit-peerings.md#privatepeering)访问它们。 

下图描绘了这种组合方案：![使用 ExpressRoute 进行本地到 Azure 的复制](./media/concepts-expressroute-with-site-recovery/site-recovery-with-expressroute.png)

## <a name="azure-to-azure-replication-with-expressroute"></a>使用 ExpressRoute 进行 Azure 到 Azure 的复制

Azure Site Recovery 支持对 [Azure 虚拟机](azure-to-azure-architecture.md)进行灾难恢复。 根据 Azure 虚拟机是否使用 [Azure 托管磁盘](../virtual-machines/windows/managed-disks-overview.md)，复制数据将被发送到目标 Azure 区域中的 Azure 存储帐户或副本托管磁盘。 尽管复制终结点是公共终结点，但在默认情况下，无论源虚拟网络位于哪个 Azure 区域，Azure VM 复制的复制流量都不会遍历 Internet。 对 0.0.0.0/0 地址前缀，可将 Azure 默认系统路由重写为[自定义路由](../virtual-network/virtual-networks-udr-overview.md#custom-routes)，并将 VM 流量转换为本地网络虚拟设备 (NVA)，但不建议对 Site Recovery 复制使用此配置。 如果使用自定义路由，则应在虚拟网络中为“存储”[创建一个虚拟网络服务终结点](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage)，这样复制流量就不会离开 Azure 边界。

对于 Azure VM 灾难恢复，默认情况下，复制时不需要 ExpressRoute。 在将虚拟机故障转移到目标 Azure 区域后，可以使用[专用对等互连](../expressroute/expressroute-circuit-peerings.md#privatepeering)访问它们。 请注意，无论在 Azure 区域之间复制数据，数据传输价格都适用。

如果已经使用 ExpressRoute 从本地数据中心连接到源区域中的 Azure VM，则可以计划在故障转移目标区域重新建立 ExpressRoute 连接。 进行灾难恢复时，可以使用相同的 ExpressRoute 线路通过新的虚拟网络连接连接到目标区域，也可以使用不同的 ExpressRoute 线路和连接。 [此处](azure-vm-disaster-recovery-with-expressroute.md#fail-over-azure-vms-when-using-expressroute)介绍了各种可能的方案。

可根据[此处](../site-recovery/azure-to-azure-support-matrix.md#region-support)的详述，将 Azure 虚拟机复制到同一地理群集中的任何 Azure 区域。 如果所选目标 Azure 区域与源区域不在同一地缘政治区域内，则可能需要启用 ExpressRoute Premium。 有关更多详细信息，请查看 [ExpressRoute 位置](../expressroute/expressroute-locations.md)和 [ExpressRoute 定价](https://azure.microsoft.com/pricing/details/expressroute/)。

## <a name="next-steps"></a>后续步骤
- 详细了解 [ExpressRoute 线路](../expressroute/expressroute-circuit-peerings.md)。
- 详细了解 [ExpressRoute 路由域](../expressroute/expressroute-circuit-peerings.md#peeringcompare)。
- 详细了解 [ExpressRoute 位置](../expressroute/expressroute-locations.md)。
- 详细了解如何[通过 ExpressRoute 对 Azure 虚拟机进行](azure-vm-disaster-recovery-with-expressroute.md)灾难恢复。
