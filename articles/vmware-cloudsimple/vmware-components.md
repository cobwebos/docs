---
title: Azure VMware 解决方案（AVS）-AVS 私有云 VMware 组件
description: 介绍如何在 AVS 私有云上安装 VMware 组件
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3ea6c22c3957f72a0a416ce7ae42c62ff5a0791a
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77016776"
---
# <a name="avs-private-cloud-vmware-components"></a>AVS 私有云 VMware 组件

AVS 私有云是由管理域中的 vCenter 服务器管理的隔离 VMware 堆栈（ESXi 主机、vCenter、vSAN 和 NSX）环境。 通过 AVS 服务，可以在 azure 位置的 Azure 裸机基础结构上本机部署 VMware。 AVS 私有云与 Azure 云的其余部分集成在一起。 使用以下 VMware stack 组件部署一个 AVS 私有云：

* **VMware ESXi-** Azure 专用节点上的虚拟机监控程序
* **VMware vCenter-** 用于集中管理 AVS 私有云 vSphere 环境的设备
* **VMware vSAN-** 超聚合基础结构解决方案
* **VMWARE NSX 数据中心-** 网络虚拟化和安全软件  

## <a name="vmware-component-versions"></a>VMware 组件版本

使用以下软件版本部署了 AVS 私有云 VMware 堆栈。

| 组件 | 版本 | 许可版本 |
|-----------|---------|------------------|
| ESXi | 6.7 u2 | 企业 Plus |
| vCenter | 6.7 u2 | vCenter 标准版 |
| vSAN | 6.7 | Enterprise 版 |
| NSX 数据中心 | 2.4.1 | 高级 |

## <a name="esxi"></a>ESXi

创建 AVS 私有云时，在预配的 AVS 节点上安装 VMware ESXi。 ESXi 提供用于部署工作负荷虚拟机（Vm）的虚拟机监控程序。 节点提供了你的 AVS 私有云上的超聚合基础结构（计算和存储）。 节点是 AVS 私有云上的 vSphere 群集的一部分。 每个节点都有四个连接到是网络的物理网络接口。 两个物理网络接口用于在 vCenter 上创建**VSphere 分布式交换机（VDS）** ，另外两个用于创建**NSX 托管的虚拟分布式交换机（N-VDS）** 。 为实现高可用性，在主动-主动模式下配置网络接口。

详细了解 VMware ESXi

## <a name="vcenter-server-appliance"></a>vCenter 服务器设备

vCenter server 设备（VCSA）为 VMware 解决方案（AVS）提供身份验证、管理和业务流程功能。 当你创建 AVS 私有云时，将部署具有嵌入平台服务控制器（PSC）的 VCSA。 在部署 AVS 私有云时创建的 vSphere 群集上部署 VCSA。 每个 AVS 私有云都有其自己的 VCSA。 AVS 私有云的扩展将节点添加到 AVS 私有云上的 VCSA 中。

### <a name="vcenter-single-sign-on"></a>vCenter 单一登录

VCSA 上的嵌入式平台服务控制器与**VCenter 单一登录域**相关联。 域名为 " **AVS**"。 将为你创建一个默认用户 **CloudOwner@AVS.com** 以访问 vCenter。 可以为 vCenter 添加本地/Azure active directory[标识源](set-vcenter-identity.md)。

## <a name="vsan-storage"></a>vSAN 存储

AVS 私有云是在群集的本地完全配置的所有闪存 vSAN 存储中创建的。 使用 vSAN 数据存储创建 vSphere 群集需要使用同一 SKU 的至少三个节点。 默认情况下，会在 vSAN 数据存储上启用重复数据删除和压缩。 在 vSphere 群集的每个节点上创建两个磁盘组。 每个磁盘组都包含一个缓存磁盘和三个容量磁盘。

在 vSphere 群集上创建一个默认的 vSAN 存储策略，并将其应用于 vSAN 数据存储。 此策略确定如何在数据存储中设置和分配 VM 存储对象，以确保所需的服务级别。 存储策略定义了 "**容错（FTT）** " 和 "**失败" 容错方法**。 你可以创建新的存储策略并将其应用到 Vm。 若要维护 SLA，必须在 vSAN 数据存储上维护25% 的备用容量。 

### <a name="default-vsan-storage-policy"></a>默认 vSAN 存储策略

下表显示了默认的 vSAN 存储策略参数。

| VSphere 群集中的节点数 | FTT | 故障容错方法 |
|------------------------------------|-----|--------------------------|
| 3和4节点 | 第 | RAID 1 （镜像）-创建2个副本 |
| 5到16个节点 | 2 | RAID 1 （镜像）-创建3个副本 |

## <a name="nsx-data-center"></a>NSX 数据中心

NSX 数据中心在你的 AVS 私有云上提供网络虚拟化、微分段和网络安全功能。 你可以通过 NSX 配置你的 AVS 私有云上的 NSX 数据中心支持的所有服务。 创建 AVS 私有云时，会安装并配置以下 NSX 组件。

* NSXT 管理器
* 传输区域
* 主机和边缘上行链路配置文件
* 边缘传输、Ext1 和 Ext2 的逻辑交换机
* ESXi 传输节点的 IP 池
* 边缘传输节点的 IP 池
* 边缘节点
* 适用于控制器和边缘 Vm 的 DRS 消除相关性规则
* 第0层路由器
* 在 Tier0 路由器上启用 BGP

## <a name="vsphere-cluster"></a>vSphere 群集

将 ESXi 主机配置为群集，以确保 AVS 私有云的高可用性。 创建 AVS 私有云时，将在第一个群集上部署 vSphere 的管理组件。 为管理组件创建了资源池，并在此资源池中部署了所有管理 Vm。 无法删除第一个群集来缩减 AVS 私有云。 vSphere 群集使用**VSPHERE HA**为 vm 提供高可用性。 容错失败基于群集中的可用节点数。 您可以使用公式 ```Number of nodes = 2N+1``` 其中 ```N``` 是可容忍的失败次数。

### <a name="vsphere-cluster-limits"></a>vSphere 群集限制

| 资源 | 限制 |
|----------|-------|
| 创建 AVS 私有云的最小节点数（第一个 vSphere 群集） | 3 |
| AVS 私有云上的 vSphere 群集中的最大节点数 | 16 |
| AVS 私有云中的最大节点数 | 64 |
| AVS 私有云中 vSphere 群集的最大数目 | 21 |
| 新 vSphere 群集上的最小节点数 | 3 |

## <a name="vmware-infrastructure-maintenance"></a>VMware 基础结构维护

偶尔需要对 VMware 基础结构的配置进行更改。 目前，这些时间间隔可能每1-2 个月发生一次，但频率预计会随着时间的推移而拒绝。 通常，这种类型的维护不会中断 AVS 服务的正常使用。 在 VMware 维护间隔期间，以下服务将继续运行，而不会产生任何影响：

* VMware 管理平面和应用程序
* vCenter 访问
* 所有网络和存储
* 所有 Azure 流量

## <a name="updates-and-upgrades"></a>更新和升级

AVS 负责在 AVS 私有云中对 VMware 软件（ESXi、vCenter、PSC 和 NSX）进行生命周期管理。

软件更新包括：

* **修补程序**。 VMware 发布的安全修补程序或 bug 修复。
* **更新**。 VMware stack 组件的次要版本更改。
* **升级**。 VMware stack 组件的主版本更改。

从 VMware 获得关键安全修补程序后，AVS 就会对其进行测试。 根据 SLA，AVS 在一周内推出了到 AVS 私有云环境的安全修补程序。

AVS 提供对 VMware 软件组件的季度维护更新。 当 VMware 软件的新主要版本可用时，AVS 与客户合作，协调适合升级的维护时段。 

## <a name="next-steps"></a>后续步骤

* [AVS 维护和更新](cloudsimple-maintenance-updates.md)
