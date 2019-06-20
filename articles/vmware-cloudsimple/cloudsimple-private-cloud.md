---
title: 通过 CloudSimple-Azure 的 VMware 解决方案中的私有云
description: 了解有关 CloudSimple 私有云和概念。
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e3ab53466cae5bd39d85f8d846c8e59ad94f1f2d
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165746"
---
# <a name="cloudsimple-private-cloud-overview"></a>CloudSimple 私有云概述

CloudSimple 转换，并在几分钟内扩展到公有云的 VMware 工作负荷。 使用 CloudSimple 服务，你可以部署 VMware 本机 Azure 裸机裸机基础结构上。 你的部署上的 Azure 位置，并完全集成与 Azure 云的其余部分。

* CloudSimple 解决方案提供了完成 VMware 操作连续性。 此解决方案为您提供公有云的优势：
  * 弹性
  * 创新
  * 效率
* 使用 CloudSimple，您受益于云消耗模型，可降低总拥有成本。 它还提供按需预配、 随-您的增长，且容量优化。
* CloudSimple 是与完全兼容：
  * 现有的工具
  * 技能
  * 进程
* 此兼容性使您的团队来管理 Azure 云上的工作负荷，而不会中断你的策略：
  * 网络
  * 安全  
  * 数据保护  
  * 审核
* CloudSimple 管理基础结构和所有必要的网络和管理服务。 CloudSimple 服务使您的团队可以专注于：
  * 业务价值
  * 应用程序预配
  * 业务连续性
  * 支持
  * 策略强制执行

## <a name="private-cloud-environment-overview"></a>私有云环境概述

私有云是一个独立的 VMware 堆栈，如这些环境：

* ESXi 主机
* vCenter
* vSAN
* NSX

私有云由自己管理域中的 vCenter 服务器管理。

在堆栈上运行：

* 专用节点
* 独立裸机硬件节点

用户使用，通过本机 VMware 工具，包括堆栈：

* vCenter
* NSX 管理器

你可以部署的 Azure 位置中的专用的节点。 然后您可以使用 Azure 和 CloudSimple 管理它们。 私有云由一个或多个 vSphere 群集和每个群集包含 3 到 16 个节点。

可以创建私有云使用预配的节点：

* 即用即付节点
* 保留的专用节点

您可以连接到的本地环境和使用以下连接的 Azure 网络的私有云：

* 安全
* 专用 VPN
* Azure ExpressRoute

私有云环境可消除具有单点故障：

* ESXi 群集配置有 vSphere 高可用性和大小将调整为具有复原能力的至少一个备用节点。
* vSAN 提供冗余的主存储。 vSan 要求至少三个节点才能针对单个故障提供保护。 你可以配置为更大的群集提供更高的复原能力的 vSAN。
* 与 RAID 10 存储策略，以防止存储发生故障，可以配置 vCenter、 PSC 和 NSX 管理器 Vm。 然后，它们受到 vSphere HA 针对节点和网络故障。

## <a name="scenarios-for-deploying-a-private-cloud"></a>部署私有云方案

* **数据中心停用或迁移**

  * 当达到你的现有数据中心的限制或刷新硬件时，获取更多的容量。
  * 将所需的容量添加到云中，并消除管理硬件刷新的难题。
  * 降低风险和成本的云迁移，相比耗时转换或重新组织了架构。
  * 使用熟悉的 VMware 工具和技术来加速云迁移。 在云中使用 Azure 服务来更新您的步调应用程序。

* **按需展开**

  * 扩展到云，以满足未预料到的需要，如新的开发环境或季节性容量突发情况。
  * 根据需要创建新的容量，并一直只要你需要它。
  * 减少您的先期投资，加快速度的预配，并跨本地和云降低具有相同的体系结构和策略的复杂性。

* **灾难恢复和在 Azure 云中的虚拟桌面**

  * 建立到数据、 应用和在 Azure 云中的桌面的远程访问。 具有高带宽连接时，将上传 / 下载数据快，若要从事件中恢复。 低延迟网络提供快速响应时间，用户期望从桌面应用。

  * 复制所有策略和网络在云中使用 CloudSimple 门户和熟悉的 VMware 工具。 这种复制可减少工作量和风险的创建和管理灾难恢复和 VDI 的实现。

* **高性能应用程序和数据库**

  * 超聚合体系结构提供的 CloudSimple 运行要求最高工作负荷。
  * 运行 Oracle、 Microsoft SQL server、 中间件系统和高性能无 SQL 数据库。

  * 将云作为 25 Gbps 的高速网络连接使用你自己数据中心的体验。 高速连接使你可以运行跨本地、 在 Azure 上，VMware 的混合应用程序和 Azure 专用的工作负荷，而不会影响性能。

* **真正的混合**

  * VMware 和 Azure 服务跨统一 DevOps。
  * 优化 Azure 服务和可以应用跨所有工作负荷的解决方案的 VMware 管理。
  * 访问公有云服务而无需展开你的数据中心或重新构建您的应用程序。
  * 集中管理标识、 访问控制策略，日志记录和监视的 VMware 在 Azure 上的应用程序。

## <a name="limits"></a>限制

下表显示了在私有云的资源上的节点限制。

| Resource | 限制 |
|----------|-------|
| 要创建私有云的节点的最小数量 | 3 |
| 私有云上的群集中的节点的最大数目 | 16 |
| 在私有云中的节点最大数目 | 64 |
| 在新群集上的节点的最小数目 | 3 |

## <a name="next-steps"></a>后续步骤

* 了解如何[创建私有云](https://docs.azure.cloudsimple.com/create-private-cloud/)
* 了解如何[配置私有云环境](quickstart-create-private-cloud.md)