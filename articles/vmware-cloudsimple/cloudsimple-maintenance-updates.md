---
title: Azure VMware 解决方案（AVS）-AVS 维护和更新
description: 描述计划的维护和更新的 AVS 服务过程
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: bf5937183fc20579ecd21aca8543a0a78d4b9ff3
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025021"
---
# <a name="avs-maintenance-and-updates"></a>AVS 维护和更新

AVS 私有云环境设计为无单点故障。

* ESXi 群集是通过 vSphere 高可用性（HA）配置的。 群集的大小调整为至少有一个备用节点用于复原。
* VSAN 提供冗余的主存储，它至少需要三个节点才能针对单一故障提供保护。 可以配置 vSAN，为更大的群集提供更高的复原能力。
* vCenter、PSC 和 NSX Manager Vm 配置了10个存储，以防止存储故障。 Vm 受 vSphere HA 的节点/网络故障保护。
* ESXi 主机具有冗余风扇和 Nic。
* 在 HA 对中配置 TOR 和书脊交换机，以提供复原能力。

AVS 持续监视以下 Vm 的正常运行时间和可用性，并提供可用性 Sla：

* ESXi 主机
* vCenter
* PSC
* NSX 管理器

AVS 还会持续监视以下故障：

* 硬盘
* 物理 NIC 端口
* 服务器
* 转动
* Power
* 交换机
* 交换机端口

如果某个磁盘或节点发生故障，则会自动将新的节点添加到受影响的 VMware 群集，以使其立即返回到运行状况。

AVS 在 AVS 私有云中备份、维护和更新这些 VMware 元素：

* ESXi
* vCenter 平台服务
* 控制器
* vSAN
* NSX

## <a name="back-up-and-restore"></a>备份和还原

AVS 备份包括：

* VCenter、PSC 和 DVS 规则的夜间增量备份。
* vCenter 本机 Api，用于在应用程序层备份组件。
* 在更新或升级 VMware 管理软件之前自动备份。
* 在数据通过 TLS 1.2 加密通道传输到 Azure 之前，源上的 vCenter 数据加密。 数据存储在 Azure blob 中，其中跨区域复制数据。

可以通过打开[支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)来请求还原。

## <a name="maintenance"></a>维护

AVS 执行多种类型的计划内维护。

### <a name="backendinternal-maintenance"></a>后端/内部维护

此维护通常涉及重新配置物理资产或安装软件修补程序。 它不会影响正在为其提供服务的资产的正常使用。 通过冗余 Nic 进入每个物理机架，普通网络流量和 AVS 私有云操作不会受到影响。 仅当你的组织希望在维护时间间隔内使用完全冗余的带宽时，你才会注意到性能影响。

### <a name="avs-portal-maintenance"></a>AVS 门户维护

更新 AVS 控制平面或基础结构时，需要一些有限的服务停机。 目前，维护间隔可以与每月一次的频率相同。 频率预计会随着时间的推移而拒绝。 AVS 为门户维护提供通知，并使间隔尽可能短。 在门户维护间隔期间，以下服务将继续运行，而不会产生任何影响：

* VMware 管理平面和应用程序
* vCenter 访问
* 所有网络和存储
* 所有 Azure 流量

### <a name="vmware-infrastructure-maintenance"></a>VMware 基础结构维护

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

[使用 Veeam 备份工作负荷 Vm](backup-workloads-veeam.md)
