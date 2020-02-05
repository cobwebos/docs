---
title: 在 Azure 中的 VMware 上运行时确保应用程序的高可用性
description: 介绍 AVS 高可用性功能，以解决在 AVS 私有云中运行的应用程序的常见应用程序故障方案
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b32f7f3f38098f935382cce46d8251340784b940
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025344"
---
# <a name="ensure-application-high-availability-when-running-in-vmware-on-azure"></a>在 Azure 中的 VMware 上运行时确保应用程序的高可用性

AVS 解决方案为在 Azure 环境中运行的应用程序提供高可用性。 下表列出了故障方案和关联的高可用性功能。

| 故障方案 | 受保护的应用程序？ | 平台 HA 功能 | VMware HA 功能 | Azure HA 功能 |
------------ | ------------- | ------------ | ------------ | ------------- |
| 磁盘故障 | YES | 快速替换失败的节点 | [关于 vSAN 默认存储策略](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.virtualsan.doc/GUID-C228168F-6807-4C2A-9D74-E584CAF49A2A.html) |
| 风扇故障 | YES | 冗余风扇，快速更换故障节点 |  |  |
| NIC 故障 | YES | 冗余 NIC，快速替换失败的节点
| 主机电源故障 | YES | 冗余电源 |  |  |
| ESXi 主机故障 | YES | 快速替换失败的节点 | [VMware vSphere 高可用性](https://www.vmware.com/products/vsphere/high-availability.html) |  |  |
| VM 故障 | YES | [负载均衡器](load-balancers.md)  | [VMware vSphere 高可用性](https://www.vmware.com/products/vsphere/high-availability.html) | 无状态 VMware Vm 的 Azure 负载均衡器 |
| 叶交换机端口故障 | YES | 冗余 NIC |  |  |
| 叶交换机故障 | YES | 冗余叶交换机 |  |  |
| 机架故障 | YES | 放置组 |  |  |
| 到本地 DC 的网络连接 | YES  | 冗余网络服务 |  | 冗余的 ER 线路 |
| 与 Azure 的网络连接 | YES | |  | 冗余的 ER 线路 |
| 数据中心故障 | YES |  |  | 可用性区域 |
| 区域故障 | YES  |  |  | Azure 区域 |

Azure VMware 解决方案（由 AVS 提供）提供以下高可用性功能。

## <a name="fast-replacement-of-failed-node"></a>快速替换失败的节点

AVS 控制平面软件持续监视 VMware 群集的运行状况，并在 ESXi 节点出现故障时进行检测。 然后，它会自动将新的 ESXi 主机从其可供使用的节点池中添加到受影响的 VMware 群集，并将失败的节点移出群集。 此功能可确保快速还原 VMware 群集中的备用容量，以便还原 vSAN 和 VMware HA 提供的群集复原能力。

## <a name="placement-groups"></a>放置组

创建 AVS 私有云的用户可以在所选区域中选择一个 Azure 区域和一个放置组。 放置组是分布在多个机架上但位于同一书脊网络段内的一组节点。 同一放置组中的节点可以彼此联系，最多可以有两个额外的开关跃点。 放置组始终位于单个 Azure 可用性区域内，并跨多个机架。 AVS 控制面根据最大努力在多个机架上分布 AVS 私有云的节点。 可以保证不同位置组中的节点放置在不同的机架中。

## <a name="availability-zones"></a>可用性区域

可用性区域是一种高可用性产品，可保护应用程序和数据免受数据中心故障的影响。 可用性区域是 Azure 区域内的特殊物理位置。 每个区域由一个或多个数据中心组成，这些数据中心配置了独立电源、冷却和网络。 每个区域都有一个可用性区域。 有关详细信息，请参阅[什么是 Azure 中的可用性区域？](../availability-zones/az-overview.md)。

## <a name="redundant-azure-expressroute-circuits"></a>冗余 Azure ExpressRoute 线路

数据中心使用 ExpressRoute 连接到 Azure vNet 具有冗余线路，可提供高度可用的网络连接链接。

## <a name="redundant-networking-services"></a>冗余网络服务

AVS 私有云的所有 AVS 网络服务（包括 VLAN、防火墙、公共 IP 地址、Internet 和 VPN）均设计为高度可用，并能够支持服务 SLA。

## <a name="azure-layer-7-load-balancer-for-stateless-vmware-vms"></a>无状态 VMware Vm 的 Azure 第7层负载均衡器

用户可以将 Azure 第7层负载均衡器放在 VMware 环境中运行的无状态 web 层 Vm 的前面，以实现 web 层的高可用性。

## <a name="azure-regions"></a>Azure 区域

Azure 区域是一组在延迟定义的外围部署的数据中心，并通过专用的区域低延迟网络进行连接。 有关详细信息，请参阅[Azure 区域](https://azure.microsoft.com/global-infrastructure/regions)。
