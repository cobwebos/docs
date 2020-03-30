---
title: Azure VMware 解决方案（按云简单 - 私有云）
description: 了解云简单私有云和概念。
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4fb930603455ed1a5df5d357fcab669f41a0c28c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024938"
---
# <a name="cloudsimple-private-cloud-overview"></a>云简单私有云概述

云简单在几分钟内将 VMware 工作负载转换为公共云。 使用 CloudSimple 服务，可以在 Azure 裸机基础结构上本机部署 VMware。 部署位于 Azure 位置，并完全集成 Azure 云的其余部分。

云简单解决方案提供完整的 VMware 操作连续性。 此解决方案为您提供了以下公共云优势：

* 弹性
* 创新
* 效率

借助 CloudSimple，您可以受益于降低总拥有成本的云消费模式。 它还提供按需预配、即用即付和容量优化。

CloudSimple 完全兼容：

* 现有工具
* 技能
* 进程

此兼容性使您的团队能够管理 Azure 云上的工作负载，而不会中断这些类型的策略：

* 网络
* 安全性  
* 数据保护  
* 审核

CloudSimple 管理基础架构以及所有必要的网络和管理服务。 云简单服务使您的团队能够专注于：

* 业务价值
* 应用程序预配
* 业务连续性
* 支持
* 策略强制执行

## <a name="private-cloud-environment-overview"></a>私有云环境概述

私有云是一个孤立的 VMware 堆栈，支持：

* ESXi 主机
* vCenter
* vSAN
* NSX

私有云通过云简单门户进行管理。 它们有自己的 vCenter 服务器。

堆栈在以下上运行：

* 专用节点
* 隔离裸机硬件节点

用户通过本机 VMware 工具使用堆栈，包括：

* vCenter
* NSX 管理器

可以在 Azure 位置部署专用节点。 然后，您可以使用 Azure 和云简单管理它们。 私有云由一个或多个 vSphere 群集组成，每个群集包含 3 到 16 个节点。

您可以使用已购买、即用即付的节点或保留的专用节点创建私有云。

您可以使用以下连接将私有云连接到本地环境和 Azure 网络：

* 安全
* 专用 VPN
* Azure ExpressRoute

私有云环境旨在消除单点故障：

* ESXi 群集配置具有 vSphere 高可用性，大小可至少具有一个备用节点以进行恢复。
* vSAN 提供冗余主存储。 vSan 至少需要三个节点来防止单个故障。 您可以配置 vSAN 以为较大的群集提供更高的恢复能力。
* 您可以使用 RAID-10 存储策略配置 vCenter、PSC 和 NSX 管理器 VM，以防止存储故障。 vSphere HA 可防止节点和网络故障。

## <a name="scenarios-for-deploying-a-private-cloud"></a>部署私有云的方案

下面是私有云部署的一些示例用例。

### <a name="data-center-retirement-or-migration"></a>数据中心停用或迁移

* 达到现有数据中心或刷新硬件的限制时，获取其他容量。
* 在云中添加所需的容量，并消除管理硬件更新的麻烦。
* 与耗时的转换或重新架构相比，降低云迁移的风险和成本。
* 使用熟悉的 VMware 工具和技能加快云迁移。 在云中，使用 Azure 服务以您的速度实现应用程序的现代化。

### <a name="expand-on-demand"></a>按需扩展

* 扩展到云以满足意外需求，例如新的开发环境或季节性容量突发。
* 按需创建新容量，并仅在需要时保留容量。
* 通过本地和云中的相同体系结构和策略，减少前期投资，加快预配速度，并降低复杂性。

### <a name="disaster-recovery-and-virtual-desktops-in-the-azure-cloud"></a>Azure 云中的灾难恢复和虚拟桌面

* 在 Azure 云中建立对数据、应用和桌面的远程访问。 使用高带宽连接，您可以快速上传/下载数据以从事件中恢复。 低延迟网络为您提供用户期望从桌面应用快速响应时间。

* 使用 CloudSimple 门户和熟悉的 VMware 工具在云中复制所有策略和网络。 复制减少了创建和管理 DR 和 VDI 实现的工作和风险。

### <a name="high-performance-applications-and-databases"></a>高性能应用程序和数据库

* 使用 CloudSimple 提供的超融合架构运行您最苛刻的工作负载。
* 运行 Oracle、微软 SQL 服务器、中间件系统和高性能无 SQL 数据库。
* 通过高速 25 Gbps 网络连接，将云体验为您自己的数据中心。 通过高速连接，您可以运行跨本地、Azure 上的 VMware 和 Azure 专用工作负荷的混合应用，而不会降低性能。

### <a name="true-hybrid"></a>真正的混合

* 跨 VMware 和 Azure 服务统一 DevOps。
* 针对可应用于所有工作负荷的 Azure 服务和解决方案优化 VMware 管理。
* 访问公共云服务，而无需扩展数据中心或重新构建应用程序。
* 集中 Azure 上 VMware 应用程序的标识、访问控制策略、日志记录和监视。

## <a name="limits"></a>限制

下表列出了私有云资源的节点限制。

| 资源 | 限制 |
|----------|-------|
| 创建私有云的最小节点数 | 3 |
| 私有云群集中的最大节点数 | 16 |
| 私有云中的最大节点数 | 64 |
| 新群集上的节点最小数量 | 3 |

## <a name="next-steps"></a>后续步骤

* 了解如何[创建私有云](create-private-cloud.md)
* 了解如何[配置私有云环境](quickstart-create-private-cloud.md)
