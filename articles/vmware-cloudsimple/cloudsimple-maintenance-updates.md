---
title: 云简单维护和更新
titleSuffix: Azure VMware Solution by CloudSimple
description: 描述计划维护和更新的云简单服务流程
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 826fae1123b355a4143118b53ba649f0939acaf7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025021"
---
# <a name="cloudsimple-maintenance-and-updates"></a>云简单维护和更新

私有云环境设计为没有单点故障。

* ESXi 群集配置了 vSphere 高可用性 （HA）。 群集的大小为至少一个备用节点以进行恢复。
* 冗余主存储由 vSAN 提供，它至少需要三个节点来防止单个故障。 vSAN 可以配置为为较大的群集提供更高的恢复能力。
* vCenter、PSC 和 NSX 管理器 VM 配置了 RAID-10 存储，以防止存储故障。 VM 通过 vSphere HA 防止节点/网络故障。
* ESXi 主机具有冗余风扇和 NIC。
* TOR 和脊柱开关配置在 HA 对中，以提供恢复能力。

CloudSimple 持续监控以下 VM 的停机时间和可用性，并提供可用性 SL：

* ESXi 主机
* vCenter
* Psc
* NSX 管理器

CloudSimple 还会持续监控以下故障：

* 硬盘
* 物理 NIC 端口
* 服务器
* 球迷
* 电源
* 交换机
* 交换机端口

如果磁盘或节点发生故障，新节点将自动添加到受影响的 VMware 群集中，使其立即恢复健康。

云简单备份、维护和更新私有云中的这些 VMware 元素：

* ESXi
* vCenter平台服务
* 控制器
* vSAN
* NSX

## <a name="back-up-and-restore"></a>备份和还原

云简单备份包括：

* vCenter、PSC 和 DVS 规则的夜间增量备份。
* vCenter 本机 API 以在应用程序层备份组件。
* 在 VMware 管理软件更新或升级之前进行自动备份。
* 在通过 TLS1.2 加密通道传输到 Azure 之前，在源处进行 vCenter 数据加密。 数据存储在 Azure Blob 中，在其中跨区域复制数据。

您可以通过打开[支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)请求来请求还原。

## <a name="maintenance"></a>维护

CloudSimple 执行多种类型的计划维护。

### <a name="backendinternal-maintenance"></a>后端/内部维护

这种维护通常涉及重新配置物理资产或安装软件修补程序。 它不会影响所服务资产的正常使用。 随着冗余 NIC 进入每个物理机架，正常的网络流量和私有云操作不会受到影响。 仅当您的组织希望在维护期间使用完全冗余带宽时，您才会注意到性能影响。

### <a name="cloudsimple-portal-maintenance"></a>云简单门户维护

更新 CloudSimple 控制平面或基础结构时，需要一些有限的服务停机时间。 目前，维护间隔的频率可能为每月一次。 预计频率会随着时间的推移而下降。 CloudSimple 为门户维护提供了通知，并尽可能缩短间隔时间。 在门户维护期间，以下服务将继续运行，没有任何影响：

* VMware 管理平面和应用
* vCenter 访问
* 所有网络和存储
* 所有 Azure 流量

### <a name="vmware-infrastructure-maintenance"></a>VMware 基础设施维护

有时，有必要更改 VMware 基础结构的配置。  目前，这些间隔可以每 1-2 个月发生一次，但频率预计会随着时间的推移而降低。 这种类型的维护通常可以在不中断云简单服务的正常使用的情况下进行。 在 VMware 维护期间，以下服务将继续运行，没有任何影响：

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

[使用 Veeam 备份工作负载 VM](backup-workloads-veeam.md)
