---
title: 弃用 Azure Site Recovery 数据加密功能 |Microsoft Docs
description: Azure Site Recovery 数据加密功能的详细信息 regarig
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/15/2019
ms.author: rajanaki
ms.openlocfilehash: 5e74466891a5926d8ae8feb3c1c48348ecf3cfe6
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/16/2019
ms.locfileid: "74134991"
---
# <a name="deprecation-of-site-recovery-data-encryption-feature"></a>弃用 Site Recovery 数据加密功能

本文档介绍了在将 Hyper-v 虚拟机灾难恢复到 Azure 的同时使用 Site Recovery 数据加密功能时需要执行的弃用详细信息和更正操作。 

## <a name="deprecation-information"></a>弃用信息


Site Recovery 数据加密功能适用于保护 Hyper-v vm 的客户，以确保复制的数据免受安全威胁的攻击。 此功能将在**2019 年12月30日**弃用。 它将替换为更高级的[静态加密](https://azure.microsoft.com/blog/azure-site-recovery-encryption-at-rest/)功能，该功能使用[存储服务加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)（SSE）。 使用 SSE 时，数据会在保留到存储之前进行加密，并在故障转移到 Azure 时进行加密，并在故障转移到 Azure 时，你的 Vm 将从加密的存储帐户运行，从而提高了恢复时间目标（RTO）。

请注意，如果你是使用此功能的现有客户，你会收到与弃用详细信息和修正步骤的通信。 


## <a name="what-are-the-implications"></a>有什么影响？

**2019 年12月30日**之后，仍使用停用加密功能的所有 vm 都不允许执行故障转移。 

## <a name="required-action"></a>必需的操作
若要继续成功的故障转移操作，请按照下面所述的步骤操作：

针对每个 VM 执行以下步骤： 
1.  [禁用复制](https://docs.microsoft.com/azure/site-recovery/site-recovery-manage-registration-and-protection#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario)。
2.  [创建新的复制策略](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-tutorial#set-up-a-replication-policy)。
3.  [启用复制](https://docs.microsoft.com/azure/site-recovery/hyper-v-vmm-azure-tutorial#enable-replication)，并选择启用了 SSE 的存储帐户。

完成到启用了 SSE 的存储帐户的初始复制后，Vm 会将静态加密用于 Azure Site Recovery。


## <a name="next-steps"></a>后续步骤
规划执行修正步骤，并在最早执行这些步骤。 如果有与此弃用相关的任何查询，请联系 Microsoft 支持部门。 若要了解有关 Hyper-v 到 Azure 方案的详细信息，请参阅[此处](hyper-v-vmm-architecture.md)。

