---
title: "使用 Azure Site Recovery 复制到 Azure 的先决条件 | Microsoft 文档"
description: "本文总结了使用 Azure Site Recovery 服务将 VM 和物理计算机复制到 Azure 的先决条件。"
services: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: jwhit
editor: tysonn
ms.assetid: e24eea6c-50a7-4cd5-aab4-2c5c4d72ee2d
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/01/2017
ms.author: rajanaki
ms.openlocfilehash: fb5b8c9ac96ac44d0112919664a177f33ef392da
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
#  <a name="prerequisites-for-replicating-azure-virtual-machines-to-another-region-by-using-azure-site-recovery"></a>使用 Azure Site Recovery 将 Azure 虚拟机复制到另一个区域的先决条件

> [!div class="op_single_selector"]
> * [从 Azure 复制到 Azure](site-recovery-azure-to-azure-prereq.md)
> * [从本地复制到 Azure](site-recovery-prereq.md)

Azure Site Recovery 服务有助于实现业务连续性和灾难恢复 (BCDR) 策略，因为它可以安排：
* 将 Azure 虚拟机复制到另一个 Azure 区域。
* 将本地物理服务器和虚拟机复制到 Azure 或辅助数据中心。 

当主要位置发生故障时，可以故障转移到辅助位置，使应用和工作负荷保持可用。 当主要位置恢复正常时，可以故障回复到主要位置。 有关 Site Recovery 的详细信息，请参阅[什么是 Site Recovery？](site-recovery-overview.md)。

本文总结了开始使用 Site Recovery 从本地复制到 Azure 所需的先决条件。

欢迎在本文底部发表任何看法，或者在 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)上咨询技术问题。


## <a name="azure-requirements"></a>Azure 要求

**要求** | **详细信息**
--- | ---
**Azure 帐户** | 一个 [Microsoft Azure](http://azure.microsoft.com/) 帐户。<br/><br/> 可以从 [免费试用版](https://azure.microsoft.com/pricing/free-trial/)开始。
**Site Recovery 服务** | 有关 Site Recovery 定价的详细信息，请参阅[站点恢复定价](https://azure.microsoft.com/pricing/details/site-recovery/)。 建议在要用作灾难恢复位置的目标 Azure 区域中创建恢复服务保管库。 例如，如果源 VM 在美国东部运行，你想要复制到美国中部，则建议在美国中部创建保管库。|
**Azure 容量** | 对于要用作灾难恢复位置的目标 Azure 区域，需要有一个为虚拟机、存储帐户和网络组件提供足够容量的订阅。 可以联系支持人员添加更多容量。
**存储指南** | 确保按照[存储指南](../storage/common/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks)配置源 Azure 虚拟机，以免产生任何性能问题。 如果遵从默认设置，Site Recovery 会基于源配置创建所需的存储帐户。 如果自定义和选择自己的设置，请确保遵从[虚拟机磁盘的可伸缩性目标](../storage/common/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks)。
**网络指南** | 需要将特定 URL 或 IP 范围的 Azure VM 出站连接列入允许列表。 有关更多详细信息，请参阅 [Azure 虚拟机复制网络指南](site-recovery-azure-to-azure-networking-guidance.md)一文。
**Azure VM** | 确保 Windows 或 Linux VM 具有所有最新的根证书。 如果没有最新的根证书，VM 会由于安全约束无法注册到 Site Recovery。

>[!NOTE]
>有关对特定配置的支持的更多详细信息，请阅读[支持矩阵](site-recovery-support-matrix-azure-to-azure.md)。

## <a name="next-steps"></a>后续步骤
- 详细了解 [Azure 虚拟机复制网络指南](site-recovery-azure-to-azure-networking-guidance.md)。
- [复制 Azure 虚拟机](site-recovery-azure-to-azure.md)，开始对工作负荷进行保护。
