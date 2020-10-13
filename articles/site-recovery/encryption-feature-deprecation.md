---
title: 弃用 Azure Site Recovery 数据加密功能 |Microsoft Docs
description: 有关 Azure Site Recovery 数据加密功能的详细信息
services: site-recovery
author: Sharmistha-Rai
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/15/2019
ms.author: sharrai
ms.openlocfilehash: 5860928d71c0e7431190908d5df5d7496e8ffb17
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89426310"
---
# <a name="deprecation-of-site-recovery-data-encryption-feature"></a>弃用 Site Recovery 数据加密功能

本文档介绍弃用详细信息，以及在配置从 Hyper-V 虚拟机到 Azure 的灾难恢复时使用 Site Recovery 数据加密功能需采取的补救操作。 

## <a name="deprecation-information"></a>弃用信息


Site Recovery 数据加密功能可供保护 Hyper-V VM 的客户使用，确保复制的数据免受安全威胁。 该功能将于 **2022 年 4 月 30 日**弃用。 它会被替换为更高级的[静态加密](https://azure.microsoft.com/blog/azure-site-recovery-encryption-at-rest/)功能，后者使用[存储服务加密](../storage/common/storage-service-encryption.md) (SSE)。 有了 SSE，数据就可以在保存到存储之前先加密，在检索时解密。在故障转移到 Azure 以后，VM 就会从加密的存储帐户运行，从而改进恢复时间目标 (RTO)。

请注意，如果你是使用此功能的现有客户，你会收到通信材料，其中包含弃用详情以及补救步骤。 


## <a name="what-are-the-implications"></a>这会产生什么影响？

**2022 年 4 月 30 日**以后，任何仍使用已停用加密功能的 VM 都不得执行故障转移。 

## <a name="required-action"></a>必需的操作
若要继续执行成功的故障转移操作和复制，请执行下述步骤：

针对每个 VM 执行以下步骤： 
1.  [禁用复制](./site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario)。
2.  [创建新的复制策略](./hyper-v-azure-tutorial.md#set-up-a-replication-policy)。
3.  [启用复制](./hyper-v-vmm-azure-tutorial.md#enable-replication)并选择已启用 SSE 的存储帐户。

在初始复制到已启用 SSE 的存储帐户以后，VM 会将静态加密与 Azure Site Recovery 配合使用。


## <a name="next-steps"></a>后续步骤
做好执行补救步骤的计划，并尽早执行这些步骤。 如果有与此弃用相关的任何查询，请联系 Microsoft 支持部门。 若要详细了解从 Hyper-V 故障转移到 Azure 的方案，请参阅[此文](hyper-v-vmm-architecture.md)。
