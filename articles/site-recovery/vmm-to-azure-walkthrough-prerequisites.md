---
title: "查看使用 Azure Site Recovery 执行 Hyper-V 到 Azure 复制（通过 System Center VMM）的先决条件 | Microsoft Docs"
description: "介绍在 VMM 云中使用 Azure Site Recovery 设置从本地 Hyper-V VM 到 Azure 的复制、故障转移和恢复时的先决条件。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: a1c30fd5-c979-473c-af44-4f725ad3e3ba
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/24/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 47c178c66ec98fe5d333edd725b64465026e73ed
ms.contentlocale: zh-cn
ms.lasthandoff: 08/22/2017

---



# <a name="step-2-review-the-prerequisites-for-hyper-v-with-vmm-to-azure-replication"></a>步骤 2：查看 Hyper-V（通过 VMM）到 Azure 复制的先决条件

查看[方案体系结构](vmm-to-azure-walkthrough-architecture.md)以后，请阅读本文，确保了解部署先决条件。 

## <a name="prerequisites-and-limitations"></a>先决条件和限制

**要求** | **详细信息**
--- | ---
**Azure 帐户** | 需要一个 [Microsoft Azure 帐户](http://azure.microsoft.com/)。
**Azure 存储** | 需要使用一个 Azure 存储帐户来存储复制的数据。<br/><br/> 存储帐户必须与 Azure 恢复服务保管库位于相同的区域中。<br/><br/>可使用[异地冗余存储](../storage/common/storage-redundancy.md#geo-redundant-storage)或本地冗余存储。 建议使用异地冗余存储。 借助异地冗余存储，在发生区域性故障或无法恢复主要区域时可复原数据。<br/><br/> 可以使用标准 Azure 存储帐户，也可以使用 Azure [高级存储](../storage/common/storage-premium-storage.md)。 高级存储可托管 IO 密集型工作负荷，通常用于 IO 性能一贯较高且延迟一贯较低的 VM。 如果为复制的数据使用高级存储，则还需要一个标准存储帐户。 标准存储帐户用于存储复制日志，以便捕获对本地数据所做的更改。
**Azure 网络** | 需要一个 [Azure 网络](../virtual-network/virtual-network-get-started-vnet-subnet.md)，在故障转移后 Azure VM 将连接到该网络。 该 Azure 网络必须与恢复服务保管库位于相同的区域中。
本地 VMM 服务器 | 需要一个或多个在 System Center 2012 R2 或更高版本上运行的 VMM 服务器。<br/><br/> 每台 VMM 服务器必须有一个或多个私有云。 每个云需要一个或多个主机组。<br/><br/> VMM 服务器需要访问 Internet。
本地 Hyper-V | Hyper-V 主机服务器必须至少运行 Windows Server 2012 R2（启用了 Hyper-V 角色）或 Microsoft Hyper-V Server 2012 R2。 必须安装最新更新。<br/><br/> Hyper-V 主机必须位于 VMM 主机组（位于 VMM 云中）中。<br/><br/> 主机必须有一个或多个需要进行复制操作的 VM。<br/><br/> Hyper-V 主机必须连接到 Internet 才能复制到 Azure，不管是直接复制还是使用代理进行复制。 Hyper-V 服务器上必须有文章 [2961977](https://support.microsoft.com/kb/2961977) 中介绍的修补程序。
**本地 Hyper-V VM** | 想要复制的虚拟机应正在运行[受支持的操作系统](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)，并且符合 [Azure 先决条件](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)。 启用复制后可以修改 VM 名称。 




## <a name="next-steps"></a>后续步骤

转到[第 3 步：计划容量](vmm-to-azure-walkthrough-capacity.md)

