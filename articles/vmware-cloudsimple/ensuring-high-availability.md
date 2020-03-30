---
title: 在 Azure 上的 VMware 中运行时，确保应用程序高可用性
description: 描述云简单高可用性功能，以解决在云简单私有云中运行的应用程序的常见应用程序失败方案
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a3eed033ba6a1a6f9237116a53ec7751ae906fe4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025344"
---
# <a name="ensure-application-high-availability-when-running-in-vmware-on-azure"></a>在 Azure 上的 VMware 中运行时，确保应用程序高可用性

CloudSimple 解决方案为在 Azure 环境中在 VMware 上运行的应用程序提供高可用性。 下表列出了故障方案和关联的高可用性功能。

| 失败方案 | 应用程序受到保护？ | 平台 HA 功能 | VMware HA 功能 | Azure HA 功能 |
------------ | ------------- | ------------ | ------------ | ------------- |
| 磁盘故障 | YES | 快速更换故障节点 | [关于 vSAN 默认存储策略](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.virtualsan.doc/GUID-C228168F-6807-4C2A-9D74-E584CAF49A2A.html) |
| 风扇故障 | YES | 冗余风扇，快速更换故障节点 |  |  |
| NIC 故障 | YES | 冗余 NIC，快速更换故障节点
| 主机电源故障 | YES | 冗余电源 |  |  |
| ESXi 主机故障 | YES | 快速更换故障节点 | [VMware vSphere 高可用性](https://www.vmware.com/products/vsphere/high-availability.html) |  |  |
| VM 失败 | YES | [负载均衡器](load-balancers.md)  | [VMware vSphere 高可用性](https://www.vmware.com/products/vsphere/high-availability.html) | 用于无状态 VM 的 Azure 负载均衡器 |
| 叶开关端口故障 | YES | 冗余 NIC |  |  |
| 叶开关故障 | YES | 冗余叶开关 |  |  |
| 机架故障 | YES | 放置组 |  |  |
| 连接到本地 DC 的网络连接 | YES  | 冗余网络服务 |  | 冗余 ER 电路 |
| 与 Azure 的网络连接 | YES | |  | 冗余 ER 电路 |
| 数据中心故障 | YES |  |  | 可用性区域 |
| 区域故障 | YES  |  |  | Azure 区域 |

Azure VMware 云简单解决方案提供了以下高可用性功能。

## <a name="fast-replacement-of-failed-node"></a>快速更换故障节点

CloudSimple 控制平面软件持续监控 VMware 群集的运行状况，并检测 ESXi 节点何时发生故障。 然后，它会自动从其现成的节点池中向受影响的 VMware 群集添加新的 ESXi 主机，并将故障节点从群集中拉出。 此功能可确保快速恢复 VMware 群集中的备用容量，以便恢复 vSAN 和 VMware HA 提供的群集的恢复能力。

## <a name="placement-groups"></a>放置组

创建私有云的用户可以选择所选区域内的 Azure 区域和展示位置组。 放置组是分布在多个机架上但在同一主干网段内的一组节点。 同一放置组中的节点最多可以通过两个额外的开关跃点到达对方。 放置组始终位于单个 Azure 可用性区域内，并跨越多个机架。 CloudSimple 控制平面基于最大努力，将私有云的节点分布在多个机架上。 不同的放置组中的节点保证放置在不同的机架中。

## <a name="availability-zones"></a>可用性区域

可用性区域是一种高可用性产品，可保护您的应用程序和数据免受数据中心故障的影响。 可用性区域是 Azure 区域中的特殊物理位置。 每个区域由一个或多个数据中心组成，这些数据中心配置了独立电源、冷却和网络。 每个区域都有一个可用区域。 有关详细信息，请参阅[Azure 中有哪些可用性区域？](../availability-zones/az-overview.md)

## <a name="redundant-azure-expressroute-circuits"></a>冗余 Azure 快速路由电路

使用 ExpressRoute 与 Azure vNet 的数据中心连接具有冗余电路，可提供高可用性的网络连接链路。

## <a name="redundant-networking-services"></a>冗余网络服务

专用云的所有 CloudSimple 网络服务（包括 VLAN、防火墙、公共 IP 地址、互联网和 VPN）都旨在高度可用并支持 SLA 服务。

## <a name="azure-layer-7-load-balancer-for-stateless-vmware-vms"></a>Azure 第 7 层负载均衡器，用于无状态 VM

用户可以将 Azure 层 7 负载均衡器放在 VMware 环境中运行的无状态 Web 层 VM 前面，以实现 Web 层的高可用性。

## <a name="azure-regions"></a>Azure 区域

Azure 区域是部署在延迟定义外围线内并通过专用区域低延迟网络连接的一组数据中心。 有关详细信息，请参阅[Azure 区域](https://azure.microsoft.com/global-infrastructure/regions)。
