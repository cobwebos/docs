---
title: 概念-私有云和群集
description: 了解 vmware 解决方案中 Azure vmware 软件定义的数据中心和 vSphere 群集的主要功能。
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: f51b76b654a43d690aa5c97fa9df99fad1f47d7a
ms.sourcegitcommit: d9cd51c3a7ac46f256db575c1dfe1303b6460d04
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82740377"
---
# <a name="azure-vmware-solution-avs-preview-private-cloud-and-cluster-concepts"></a>Azure VMware 解决方案（AVS）预览版私有云和群集概念

Azure VMware 解决方案（AVS）在 Azure 中提供基于 VMware 的私有云。 私有云是从专用裸机主机群集构建的，并通过 Azure 门户进行部署和管理。 私有云中的群集配有 VMware vSphere、vCenter、vSAN 和 NSX 软件。 AVS 私有云硬件和软件部署在 Azure 中完全集成和自动化。

Azure 订阅、AVS 私有云、vSAN 群集和主机之间存在逻辑关系。 在此图中，将显示单个 Azure 订阅中的两个私有云。 私有云代表一个开发环境和一个生产环境，每个环境都有自己的私有云。 其中每个私有云都有两个分类。 为了显示开发环境的更低潜在需求，使用较小容量主机的小型群集。 以下各部分介绍了所有这些概念。

![客户订阅中两个私有云的图像](./media/hosts-clusters-private-clouds-final.png)

## <a name="private-clouds"></a>私有云

私有云包含通过专用的裸机 Azure 主机构建的 vSAN 群集。 每个私有云可以有多个群集，所有群集均由同一 vCenter 服务器管理，以及 NSX-T 管理器。 你可以在门户中通过 CLI 或 PowerShell 部署和管理私有云。 与其他资源一样，私有云在 Azure 订阅中进行安装和管理。

订阅中的私有云数量是可缩放的。 最初，每个订阅的私有云限制为一个。

## <a name="clusters"></a>群集

你将在每个私有云中至少创建一个 vSAN 群集。 在创建私有云时，默认情况下有一个群集。 你可以使用 Azure 门户或通过 API 将其他群集添加到私有云。 所有群集的默认大小均为三个主机，可以扩展到3个主机。 群集中使用的主机类型必须相同。 下一节将介绍这些主机类型。

试用群集可用于评估，并且每个私有云只能有三个主机和一个试用群集。 你可以在评估期内按单个主机缩放试用群集。

通过门户或 API 创建、删除和缩放群集。 你仍可以使用 vSphere 和 NSX-T 管理器来管理群集配置或操作的其他方面。 群集中每个主机的所有本地存储都受 vSAN 控制。

## <a name="hosts"></a>主机

超聚合、裸机基础结构节点用于 AVS 私有云群集。 下表提供了主机的 RAM、CPU 和磁盘容量。 

| 主机类型              |             CPU             |   RAM (GB)   |  vSAN NVMe 缓存层（TB，raw）  |  vSAN SSD 容量层（TB，raw）  |
| :---                   |            :---:            |    :---:     |               :---:              |                :---:               |
| 高端（HE）          |  双重 Intel 18 核 2.3 GHz  |     576      |                3.2               |                15.20               |

用于构建或缩放群集的主机是从主机的隔离池获取的。 这些主机已通过硬件测试，并已将所有数据安全地从闪存磁盘中删除。 从群集中删除主机时，将安全地擦除内部磁盘，并将主机置于主机的隔离池中。 将主机添加到群集时，将使用来自隔离池的净化的主机。

## <a name="vmware-software-versions"></a>VMware 软件版本

AVS 私有云群集中使用的 VMware 软件的当前软件版本包括：

| 软件              |    版本   |
| :---                  |     :---:    |
| VCSA/vSphere/ESXi |    6.7 U2    | 
| ESXi                  |    6.7 U2    | 
| vSAN                  |    6.7 U2    |
| NSX-T                 |      2.5     |

对于私有云中的任何新群集，软件版本将与私有云中当前运行的版本匹配。 对于客户订阅中的任何新私有云，会安装最新版本的软件堆栈。

升级概念文档中介绍了 AVS 平台软件的常规升级策略和过程。

## <a name="host-maintenance-and-lifecycle-management"></a>主机维护和生命周期管理

主机维护和生命周期管理已完成，不会影响私有云群集的容量或性能。 自动主机维护的示例包括固件升级和硬件修复或更换。

Microsoft 负责对 NSX-t 设备（例如，NSX-T Manager 和 NSX-T）的生命周期管理。 Microsoft 还负责引导网络配置，例如创建第0层的网关和启用北南部路由。 作为你的 AVS 私有云的管理员，你需要负责网络分段、分布式防火墙规则、第1层网关和负载均衡器等 NSX-T SDN 配置。

> [!IMPORTANT]
> AVS 管理员不得修改 NSX-T 边缘或第0层网关的配置。 这可能会导致服务丢失。

## <a name="backup-and-restoration"></a>备份和还原

私有云 vCenter 和 NSX-T 配置每小时备份一次。 备份保留三天。 通过 Azure 门户中的服务请求来请求从备份还原。

## <a name="next-steps"></a>后续步骤

下一步是了解[网络和连接性概念](concepts-networking.md)。

<!-- LINKS - internal -->

<!-- LINKS - external-->
[VCSA versions]: https://kb.vmware.com/s/article/2143838
[ESXi versions]: https://kb.vmware.com/s/article/2143832
[vSAN versions]: https://kb.vmware.com/s/article/2150753

