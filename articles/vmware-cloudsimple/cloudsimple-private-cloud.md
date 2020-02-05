---
title: Azure VMware 解决方案（AVS）-AVS 私有云
description: 了解 AVS 私有云和概念。
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2688edf281a6d8bc3d61e8e294c920f115f0f3f6
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024938"
---
# <a name="avs-private-cloud-overview"></a>AVS 私有云概述

AVS 在几分钟内将 VMware 工作负荷转换和扩展到公共云。 使用 AVS 服务，可以在 Azure 裸机基础结构上本机部署 VMware。 你的部署位于 Azure 位置，并与 Azure 云的其余部分完全集成。

AVS 解决方案提供完整的 VMware 运营连续性。 此解决方案为你提供了的公有云权益：

* 弹性
* 创新
* 效率

使用 AVS，你可以受益于云消耗模型，降低总拥有成本。 它还提供按需预配、即用即付和容量优化。

AVS 完全兼容：

* 现有工具
* 技能
* 流程

这种兼容性使你的团队能够在 Azure 云中管理工作负荷，而不会中断以下类型的策略：

* 网络
* 安全性  
* 数据保护  
* 审核

AVS 管理基础结构以及所有必要的网络和管理服务。 此 AVS 服务使你的团队能够专注于：

* 业务价值
* 应用程序预配
* 业务连续性
* 支持
* 策略强制执行

## <a name="avs-private-cloud-environment-overview"></a>AVS 私有云环境概述

AVS 私有云是一种独立的 VMware 堆栈，它支持：

* ESXi 主机
* vCenter
* vSAN
* NSX

AVS 私有云通过 AVS 门户进行管理。 它们在自己的管理域中具有自己的 vCenter 服务器。

堆栈运行于：

* 专用节点
* 隔离裸机硬件节点

用户通过本机 VMware 工具使用堆栈，其中包括：

* vCenter
* NSX 管理器

可以在 Azure 位置部署专用节点。 然后，你可以通过 Azure 和 AVS 对它们进行管理。 AVS 私有云由一个或多个 vSphere 群集组成，每个群集包含3到16个节点。

可以使用购买的、即用即付节点或保留的专用节点创建 AVS 私有云。

可以使用以下连接将 AVS 私有云连接到本地环境和 Azure 网络：

* 安全
* 专用 VPN
* Azure ExpressRoute

AVS 私有云环境旨在消除单一故障点：

* ESXi 群集配置为具有 vSphere 高可用性，并且大小设置为至少有一个备用节点用于复原。
* vSAN 提供冗余的主存储。 vSan 至少需要三个节点才能针对单一故障提供保护。 可以配置 vSAN，为更大的群集提供更高的复原能力。
* 可以配置包含 RAID-1、PSC 和 NSX 管理器的 Vm，并将其用于保护存储故障。 vSphere HA 可防范节点和网络故障。

## <a name="scenarios-for-deploying-an-avs-private-cloud"></a>部署 AVS 私有云的方案

下面是适用于 AVS 私有云部署的一些示例用例。

### <a name="data-center-retirement-or-migration"></a>数据中心停用或迁移

* 达到现有数据中心或刷新硬件的限制时，可获得更多容量。
* 在云中添加所需的容量，消除了管理硬件刷新的麻烦。
* 与耗时的转换或示意图相比，降低云迁移的风险和成本。
* 使用熟悉的 VMware 工具和技能加快云迁移。 在云中，使用 Azure 服务按进度实现应用程序的现代化。

### <a name="expand-on-demand"></a>按需扩展

* 扩展到云以满足意外需求，如新开发环境或季节性容量突发。
* 按需创建新容量，并只在需要时保留。
* 利用跨本地和云的相同体系结构和策略，减少前期投资，加快预配速度并降低复杂性。

### <a name="disaster-recovery-and-virtual-desktops-in-the-azure-cloud"></a>Azure 云中的灾难恢复和虚拟桌面

* 建立对 Azure 云中数据、应用和桌面的远程访问。 通过高带宽连接，可以快速上传/下载数据，以便从事件中恢复。 低延迟网络为用户带来了桌面应用程序的快速响应时间。

* 使用 AVS 门户和熟悉的 VMware 工具复制云中的所有策略和网络。 复制降低了创建和管理 DR 和 VDI 实现的工作量和风险。

### <a name="high-performance-applications-and-databases"></a>高性能应用程序和数据库

* 使用 AVS 提供的超聚合体系结构运行最苛刻的工作负载。
* 运行 Oracle、Microsoft SQL server、中间件系统和高性能的非 SQL 数据库。
* 体验云，作为你自己的数据中心，提供高速 25 Gbps 网络连接。 高速连接使你可以运行跨本地、Azure 上的 VMware 和 Azure 专用工作负荷的混合应用，而不会影响性能。

### <a name="true-hybrid"></a>真实混合

* 跨 VMware 和 Azure 服务统一 DevOps。
* 优化可跨所有工作负荷应用的 Azure 服务和解决方案的 VMware 管理。
* 访问公有云服务，无需扩展你的数据中心或重塑你的应用程序。
* 为 Azure 上的 VMware 应用程序集中标识、访问控制策略、日志记录和监视。

## <a name="limits"></a>限制

下表列出了对 AVS 私有云的资源的节点限制。

| 资源 | 限制 |
|----------|-------|
| 创建 AVS 私有云的最小节点数 | 3 |
| AVS 私有云上群集中的最大节点数 | 16 |
| AVS 私有云中的最大节点数 | 64 |
| 新群集上的最小节点数 | 3 |

## <a name="next-steps"></a>后续步骤

* 了解如何[创建 AVS 私有云](create-private-cloud.md)
* 了解如何[配置 AVS 私有云环境](quickstart-create-private-cloud.md)
