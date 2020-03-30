---
title: 私有云 VMware 组件
titleSuffix: Azure VMware Solution by CloudSimple
description: 描述如何在私有云上安装 VMware 组件
author: sharaths-cs
ms.author: dikamath
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9c9b80cd4d8a7a7ac5597d10bbb87095564bd461
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279502"
---
# <a name="private-cloud-vmware-components"></a>私有云 VMware 组件

私有云是一个孤立的 VMware 堆栈（ESXi 主机、vCenter、vSAN 和 NSX），由管理域中的 vCenter 服务器管理。  CloudSimple 服务允许您在 Azure 位置的 Azure 裸机基础结构上本机部署 VMware。  私有云与 Azure 云的其余部分集成。  使用以下 VMware 堆栈组件部署私有云：

* **VMware ESXi -** Azure 专用节点上的虚拟机管理程序
* **VMware vCenter -** 用于私有云 vSphere 环境集中管理的装置
* **VMware vSAN -** 超融合基础架构解决方案
* **VMware NSX 数据中心 -** 网络虚拟化和安全软件  

## <a name="vmware-component-versions"></a>VMware 组件版本

私有云 VMware 堆栈使用以下软件版本部署。

| 组件 | 版本 | 许可版本 |
|-----------|---------|------------------|
| ESXi | 6.7U2 | 企业加号 |
| vCenter | 6.7U2 | vCenter 标准 |
| vSAN | 6.7 | Enterprise |
| NSX 数据中心 | 2.4.1 | 高级 |

## <a name="esxi"></a>ESXi

当您创建私有云时，VMware ESXi 安装在预配的云简单节点上。  ESXi 提供用于部署工作负载虚拟机 （VM） 的虚拟机。  节点在私有云上提供超融合基础架构（计算和存储）。  节点是私有云上的 vSphere 群集的一部分。  每个节点有四个物理网络接口连接到底层网络。  两个物理网络接口用于在 vCenter 上创建**vSphere 分布式交换机 （VDS），** 两个接口用于创建**NSX 管理的虚拟分布式交换机 （N-VDS）。**  网络接口在主动-主动模式下配置，以便获得高可用性。

了解有关 VMware ESXi 的更多

## <a name="vcenter-server-appliance"></a>vCenter 服务器设备

vCenter 服务器设备 （VCSA） 通过云简单为 VMware 解决方案提供身份验证、管理和编排功能。 创建私有云时，将部署具有嵌入式平台服务控制器 （PSC） 的 VCSA。  VCSA 部署在部署私有云时创建的 vSphere 群集上。  每个私有云都有自己的 VCSA。  私有云的扩展会将节点添加到私有云上的 VCSA。

### <a name="vcenter-single-sign-on"></a>vCenter 单点登录

VCSA 上的嵌入式平台服务控制器与**vCenter 单点登录域**相关联。  域名是**云简单.local。**  将创建一**CloudOwner@cloudsimple.com**个默认用户，供您访问 vCenter。  您可以[为 vCenter](set-vcenter-identity.md)添加本地/Azure 活动目录标识源。

## <a name="vsan-storage"></a>vSAN 存储

私有云使用群集本地的完全配置全闪存 vSAN 存储创建。  创建具有 vSAN 数据存储的 vSphere 群集至少需要三个具有相同 SKU 的节点。  默认情况下，在 vSAN 数据存储上启用重复数据消除和压缩。  在 vSphere 群集的每个节点上创建两个磁盘组。 每个磁盘组包含一个缓存磁盘和三个容量磁盘。

在 vSphere 群集上创建默认 vSAN 存储策略，并应用于 vSAN 数据存储。  此策略确定如何在数据存储中预配和分配 VM 存储对象，以确保所需的服务级别。  存储策略定义 **"无法容忍故障 "（FTT）** 和**故障容差方法**。  您可以创建新的存储策略并将其应用于 VM。 要维护 SLA，必须在 vSAN 数据存储上保持 25% 的备用容量。  

### <a name="default-vsan-storage-policy"></a>默认 vSAN 存储策略

下表显示了默认 vSAN 存储策略参数。

| vSphere 群集中的节点数 | FTT | 故障容差方法 |
|------------------------------------|-----|--------------------------|
| 3 个节点和 4 个节点 | 1 | RAID 1（镜像） - 创建 2 个副本 |
| 5 到 16 个节点 | 2 | RAID 1（镜像） - 创建 3 个副本 |

## <a name="nsx-data-center"></a>NSX 数据中心

NSX 数据中心在您的私有云上提供网络虚拟化、微分段和网络安全功能。  您可以通过 NSX 配置 NSX 数据中心支持的所有服务。  创建私有云时，将安装并配置以下 NSX 组件。

* NSXT 管理器
* 运输区
* 主机和边缘上行链路配置文件
* 边缘传输、Ext1 和 Ext2 的逻辑交换机
* ESXi 传输节点的 IP 池
* 边缘传输节点的 IP 池
* 边缘节点
* 用于控制器和边缘 VM 的 DRS 抗关联规则
* 第 0 层路由器
* 在第 0 层路由器上启用 BGP

## <a name="vsphere-cluster"></a>vSphere 群集

ESXi 主机配置为群集，以确保私有云的高可用性。  创建私有云时，vSphere 的管理组件将部署在第一个群集上。  为管理组件创建资源池，并在此资源池中部署所有管理 VM。 无法删除第一个群集以收缩私有云。  vSphere 群集为使用**vSphere HA**的 VM 提供高可用性。  不能容忍的失败取决于群集中的可用节点数。  您可以使用要容忍的失败数```Number of nodes = 2N+1```的```N```公式。

### <a name="vsphere-cluster-limits"></a>vSphere 群集限制

| 资源 | 限制 |
|----------|-------|
| 创建私有云的最小节点数（第一个 vSphere 群集） | 3 |
| 私有云上的 vSphere 群集中的最大节点数 | 16 |
| 私有云中的最大节点数 | 64 |
| 私有云中最大 vSphere 群集数 | 21 |
| 新 vSphere 群集上的最小节点数 | 3 |

## <a name="vmware-infrastructure-maintenance"></a>VMware 基础设施维护

有时，有必要更改 VMware 基础结构的配置。 目前，这些间隔可以每 1-2 个月发生一次，但频率预计会随着时间的推移而降低。 这种类型的维护通常可以在不中断云简单服务的正常使用的情况下进行。 在 VMware 维护期间，以下服务将继续运行，没有任何影响：

* VMware 管理平面和应用
* vCenter 访问
* 所有网络和存储
* 所有 Azure 流量

## <a name="updates-and-upgrades"></a>更新和升级

CloudSimple 负责私有云中 VMware 软件（ESXi、vCenter、PSC 和 NSX）的生命周期管理。

软件更新包括：

* **补丁**。 VMware 发布的安全修补程序或错误修复。
* **更新**. VMware 堆栈组件的次要版本更改。
* **升级**。 VMware 堆栈组件的主要版本更改。

CloudSimple 在 VMware 提供关键安全修补程序后，立即测试它。 根据 SLA，CloudSimple 将在一周内将安全修补程序推出到私有云环境。

CloudSimple 提供 VMware 软件组件的季度维护更新。 当新的主要版本的 VMware 软件可用时，CloudSimple 与客户合作，协调一个合适的维护窗口进行升级。  

## <a name="next-steps"></a>后续步骤

* [云简单维护和更新](cloudsimple-maintenance-updates.md)
