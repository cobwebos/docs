---
title: Azure VMware 解决方案中的 VLAN 和子网（按云简单）
description: 了解云简单私有云中的 VLAN 和子网
author: sharaths-cs
ms.author: dikamath
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2451fbb69636624db354006df2a7925ef9e75459
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024885"
---
# <a name="vlans-and-subnets-overview"></a>VLAN 和子网概述

CloudSimple 提供部署云简单服务每个区域的网络。  网络是单个 TCP 第 3 层地址空间，默认情况下启用路由。  在此区域中创建的所有私有云和子网都可以相互通信，而无需任何其他配置。  您可以使用 VLan 在 vCenter 上创建分布式端口组。

![云简单网络拓扑](media/cloudsimple-network-topology.png)

## <a name="vlans"></a>VLAN

为每个私有云创建 VLAN（第 2 层网络）。  第 2 层流量保持在私有云的边界内，允许您隔离私有云中的本地流量。  在私有云上创建的 VLAN 只能用于在专用云中创建分布式端口组。  在私有云上创建的 VLAN 会自动配置连接到私有云主机的所有交换机。

## <a name="subnets"></a>子网

通过定义子网的地址空间，可以在创建 VLAN 时创建子网。 地址空间中的 IP 地址被指定为子网网关。 每个客户和地区分配单个专用第 3 层地址空间。 您可以在网络区域中配置任何 RFC 1918 非重叠地址空间，以及本地网络或 Azure 虚拟网络。

默认情况下，所有子网可以相互通信，从而减少私有云之间路由的配置开销。 同一区域中的 PC 上的东西方数据将位于同一第 3 层网络中，并通过区域内的本地网络基础结构进行传输。 区域中的私有云之间的通信不需要出口。 此方法消除了在不同私有云中部署不同工作负载时的任何 WAN/出口性能损失。

## <a name="vspherevsan-subnets-cidr-range"></a>vSphere/vSAN 子网 CIDR 范围

私有云创建为由 vCenter 服务器管理的隔离 VMware 堆栈（ESXi 主机、vCenter、vSAN 和 NSX） 环境。  管理组件部署在为 vSphere/vSAN 子网 CIDR 选择的网络中。  网络 CIDR 范围在部署期间分为不同的子网。

* 最小 vSphere/vSAN 子网 CIDR 范围前缀： **/24**
* 最大 vSphere/vSAN 子网 CIDR 范围前缀： **/21**

> [!CAUTION]
> vSphere/vSAN CIDR 范围内的 IP 地址保留供私有云基础设施使用。  不要在任何虚拟机上使用此范围内的 IP 地址。

### <a name="vspherevsan-subnets-cidr-range-limits"></a>vSphere/vSAN 子网 CIDR 范围限制

选择 vSphere/vSAN 子网 CIDR 范围大小会影响私有云的大小。  下表显示了基于 vSphere/vSAN 子网 CIDR 的大小可以拥有的最大节点数。

| 指定的 vSphere/vSAN 子网 CIDR 前缀长度 | 最大节点数 |
|---------------------------------------------------|-------------------------|
| /24 | 26 |
| /23 | 58 |
| /22 | 118 |
| /21 | 220 |

### <a name="management-subnets-created-on-a-private-cloud"></a>在私有云上创建的管理子网

创建私有云时将创建以下管理子网。

* **系统管理**。 用于 ESXi 主机管理网络、DNS 服务器、vCenter 服务器的 VLAN 和子网。
* **VMotion**. 用于 ESXi 主机 vMotion 网络的 VLAN 和子网。
* **VSAN**. 用于 ESXi 主机 vSAN 网络的 VLAN 和子网。
* **NsxtEdgeUplink1**. VLAN 和子网，用于 VLAN 上行链路到外部网络。
* **NsxtEdgeUplink2**. VLAN 和子网，用于 VLAN 上行链路到外部网络。
* **NsxtEdge 传输**。 传输区域的 VLAN 和子网控制 NSX-T 中第 2 层网络的覆盖范围。
* **NsxtHost 传输**。 用于主机传输区域的 VLAN 和子网。

### <a name="management-network-cidr-range-breakdown"></a>管理网络 CIDR 范围细分

vSphere/vSAN 子网 CIDR 范围被划分为多个子网。  下表显示了允许前缀的细分示例。  该示例使用 192.168.0.0 作为 CIDR 范围。

示例：

| 指定的 vSphere/vSAN 子网 CIDR/前缀 | 192.168.0.0/21 | 192.168.0.0/22 | 192.168.0.0/23 | 192.168.0.0/24 |
|---------------------------------|----------------|----------------|----------------|----------------|
| 系统管理 | 192.168.0.0/24 | 192.168.0.0/24 | 192.168.0.0/25 | 192.168.0.0/26 |
| vMotion | 192.168.1.0/24 | 192.168.1.0/25 | 192.168.0.128/26 | 192.168.0.64/27 |
| vSAN | 192.168.2.0/24 | 192.168.1.128/25 | 192.168.0.192/26 | 192.168.0.96/27 |
| NSX-T 主机传输 | 192.168.4.0/23 | 192.168.2.0/24 | 192.168.1.0/25 | 192.168.0.128/26 |
| NSX-T 边缘传输 | 192.168.7.208/28 | 192.168.3.208/28 | 192.168.1.208/28 | 192.168.0.208/28 |
| NSX-T 边缘上行链路1 | 192.168.7.224/28 | 192.168.3.224/28 | 192.168.1.224/28 | 192.168.0.224/28 |
| NSX-T 边缘上行链路2 | 192.168.7.240/28 | 192.168.3.240/28 | 192.168.1.240/28 | 192.168.0.240/28 |

## <a name="next-steps"></a>后续步骤

* [创建并管理 VLAN 和子网](create-vlan-subnet.md)
