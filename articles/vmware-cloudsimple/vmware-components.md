---
title: Azure VMware 解决方案 (按 CloudSimple)-私有云 VMware 组件
description: 介绍如何在私有云上安装 VMware 组件
author: sharaths-cs
ms.author: dikamath
ms.date: 04/30/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 89bc9c07ae74da1a4269a505627a7626e478ef99
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812178"
---
# <a name="private-cloud-vmware-components"></a>私有云 VMware 组件

私有云是由管理域中的 vCenter 服务器管理的隔离 VMware 堆栈 (ESXi 主机、vCenter、vSAN 和 NSX) 环境。  CloudSimple 服务允许你在 Azure 位置的 Azure 裸机基础结构上本机部署 VMware。  私有云与 Azure 云的其余部分集成在一起。  私有云使用以下 VMware stack 组件进行部署:

* **VMware ESXi-** Azure 专用节点上的虚拟机监控程序
* **VMware vCenter-** 用于集中管理私有云 vSphere 环境的设备
* **VMware vSAN-** 超聚合基础结构解决方案
* **VMWARE NSX 数据中心-** 网络虚拟化和安全软件  

## <a name="vmware-component-versions"></a>VMware 组件版本

使用以下软件版本部署私有云 VMware 堆栈。

| 组件 | Version | 许可版本 |
|-----------|---------|------------------|
| ESXi | 6.7U1 | 企业 Plus |
| vCenter | 6.7U1 | vCenter 标准版 |
| vSAN | 6.7 | 企业 |
| NSX 数据中心 | 2.3 | 高级 |

## <a name="esxi"></a>ESXi

在创建私有云时, 在预配的 CloudSimple 节点上安装 VMware ESXi。  ESXi 提供用于部署工作负荷虚拟机 (Vm) 的虚拟机监控程序。  节点在私有云上提供超聚合基础结构 (计算和存储)。  节点是私有云上的 vSphere 群集的一部分。  每个节点都有四个连接到是网络的物理网络接口。  两个物理网络接口用于在 vCenter 上创建**VSphere 分布式交换机 (VDS)** , 另外两个用于创建**NSX 托管的虚拟分布式交换机 (N-VDS)** 。  为实现高可用性, 在主动-主动模式下配置网络接口。

详细了解 VMware ESXi

## <a name="vcenter-server-appliance"></a>vCenter 服务器设备

vCenter server 设备 (VCSA) 通过 CloudSimple 为 VMware 解决方案提供身份验证、管理和业务流程功能。 当你创建私有云时, 将部署具有嵌入平台服务控制器 (PSC) 的 VCSA。  在部署私有云时创建的 vSphere 群集上部署 VCSA。  每个私有云都有其自己的 VCSA。  私有云的扩展将节点添加到私有云上的 VCSA 中。

### <a name="vcenter-single-sign-on"></a>vCenter 单一登录

VCSA 上的嵌入式平台服务控制器与**VCenter 单一登录域**相关联。  域名为**cloudsimple**。  系统会创建 **CloudOwner@cloudsimple.com** 一个默认用户以访问 vCenter。  可以为 vCenter 添加本地/Azure active directory[标识源](https://docs.azure.cloudsimple.com/set-vcenter-identity/)。

## <a name="vsan-storage"></a>vSAN 存储

私有云是在群集的本地完全配置的所有闪存 vSAN 存储中创建的。  使用 vSAN 数据存储创建 vSphere 群集需要使用同一 SKU 的至少三个节点。  默认情况下, 在 vSAN 数据存储中启用了重复数据删除和压缩。  在 vSphere 群集的每个节点上创建两个磁盘组。 每个磁盘组都包含一个缓存磁盘和三个容量磁盘。

在 vSphere 群集上创建一个默认的 vSAN 存储策略, 并将其应用于 vSAN 数据存储。  此策略确定如何在数据存储中设置和分配 VM 存储对象, 以确保所需的服务级别。  存储策略定义了 "**容错 (FTT)** " 和 "**失败" 容错方法**。  你可以创建新的存储策略并将其应用到 Vm。 若要维护 SLA, 必须在 vSAN 数据存储上维护 25% 的备用容量。  

### <a name="default-vsan-storage-policy"></a>默认 vSAN 存储策略

下表显示了默认的 vSAN 存储策略参数。

| VSphere 群集中的节点数 | FTT | 故障容错方法 |
|------------------------------------|-----|--------------------------|
| 3和4节点 | 1 | RAID 1 (镜像)-创建2个副本 |
| 5到16个节点 | 2 | RAID 1 (镜像)-创建3个副本 |

## <a name="nsx-data-center"></a>NSX 数据中心

NSX 数据中心在私有云上提供网络虚拟化、微分段和网络安全功能。  你可以通过 NSX 配置你的私有云上的 NSX 数据中心支持的所有服务。  创建私有云时, 会安装并配置以下 NSX 组件。

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

将 ESXi 主机配置为群集, 以确保私有云的高可用性。  在创建私有云时, 将在第一个群集上部署 vSphere 的管理组件。  为管理组件创建了资源池, 并在此资源池中部署了所有管理 Vm。 无法删除第一个群集来压缩私有云。  vSphere 群集使用**VSPHERE HA**为 vm 提供高可用性。  容错失败基于群集中的可用节点数。  您可以使用公式```Number of nodes = 2N+1``` , 其中```N```是可容忍的失败次数。

### <a name="vsphere-cluster-limits"></a>vSphere 群集限制

| Resource | 限制 |
|----------|-------|
| 要创建私有云的最小节点数 (第一个 vSphere 群集) | 3 |
| 私有云上的 vSphere 群集中的最大节点数 | 16 |
| 私有云中节点的最大数目 | 64 |
| 私有云中 vSphere 群集的最大数目 | 21 |
| 新 vSphere 群集上的最小节点数 | 3 |

## <a name="vmware-infrastructure-maintenance"></a>VMware 基础结构维护

偶尔需要对 VMware 基础结构的配置进行更改。 目前, 这些时间间隔可能每1-2 个月发生一次, 但频率预计会随着时间的推移而拒绝。 通常, 这种类型的维护不会中断 CloudSimple 服务的正常使用。 在 VMware 维护间隔期间, 以下服务将继续运行, 而不会产生任何影响:

* VMware 管理平面和应用程序
* vCenter 访问
* 所有网络和存储
* 所有 Azure 流量

## <a name="updates-and-upgrades"></a>更新和升级

CloudSimple 负责在私有云中对 VMware 软件 (ESXi、vCenter、PSC 和 NSX) 进行生命周期管理。

软件更新包括:

* **修补程序**。 VMware 发布的安全修补程序或 bug 修复。
* **更新**。 VMware stack 组件的次要版本更改。
* **升级**。 VMware stack 组件的主版本更改。

CloudSimple 可从 VMware 获取关键安全修补程序后立即将其测试。 按照 SLA, CloudSimple 将安全修补程序推出到一周内的私有云环境中。

CloudSimple 提供对 VMware 软件组件的季度维护更新。 当 VMware 软件的新主要版本可用时, CloudSimple 与客户合作, 协调适合升级的维护时段。  

## <a name="next-steps"></a>后续步骤

* [CloudSimple 维护和更新](cloudsimple-maintenance-updates.md)