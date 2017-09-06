---
title: "准备 Azure 资源以便使用 Azure Site Recovery 将 Hyper-V VM（使用 System Center VMM）复制到 Azure | Microsoft Docs"
description: "介绍开始使用 Azure Site Recovery 将 Hyper-V VM（使用 VMM）复制到 Azure 之前需在 Azure 中准备好的资源"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 1568bdc3-e767-477b-b040-f13699ab5644
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/23/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 63b005f37ab5e15e8a1b4645446d65f1529f1bbd
ms.contentlocale: zh-cn
ms.lasthandoff: 08/22/2017

---

# <a name="step-5-prepare-azure-resources-for-hyper-v-replication-with-vmm-to-azure"></a>步骤 5：准备 Azure 资源以将 Hyper-V 复制（使用 VMM）到 Azure

确认[网络要求](vmm-to-azure-walkthrough-network.md)后，使用本文中的说明来准备 Azure 资源，以便可以使用 [Azure Site Recovery](site-recovery-overview.md) 服务将 System Center Virtual Machine Manager (VMM) 云中的本地 Hyper-V VM 复制到 Azure。

阅读本文后，欢迎在页面底部发表任何看法，或者在 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)上咨询技术问题。


## <a name="set-up-an-azure-account"></a>设置 Azure 帐户

- 获取 [Microsoft Azure 帐户](http://azure.microsoft.com/)。
- 可以从 [免费试用版](https://azure.microsoft.com/pricing/free-trial/)开始。
- 若要查看 Site Recovery 的受支持区域，请参阅 [Azure Site Recovery 定价详细信息](https://azure.microsoft.com/pricing/details/site-recovery/)中的“地域可用性”。
- 了解有关 [Site Recovery 定价](site-recovery-faq.md#pricing)的信息，并获取[定价详细信息](https://azure.microsoft.com/pricing/details/site-recovery/)。
- 确保 Azure 帐户具有创建 Azure VM 的相应[权限](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)。 [详细了解](../active-directory/role-based-access-built-in-roles.md) Azure 中基于角色的访问控制。


## <a name="set-up-an-azure-network"></a>设置 Azure 网络

- 设置 [Azure 网络](../virtual-network/virtual-network-get-started-vnet-subnet.md)。 在故障转移后创建 Azure VM 时，Azure VM 将置于此网络中。
- 该网络应位于与恢复服务保管库相同的区域
- Azure 门户中的 Site Recovery 可以使用在[资源管理器](../resource-manager-deployment-model.md)中或经典模式下设置的网络。
- 建议在开始之前先设置网络。 否则，需要在 Site Recovery 部署期间执行此操作。
- 了解[虚拟网络定价](https://azure.microsoft.com/pricing/details/virtual-network/)的信息。


## <a name="set-up-an-azure-storage-account"></a>设置 Azure 存储帐户

- Site Recovery 将本地计算机复制到 Azure 存储。 发生故障转移后，将从存储中创建 Azure VM。
- 设置标准/高级 [Azure 存储帐户](../storage/common/storage-create-storage-account.md#create-a-storage-account)来保存复制到 Azure 的数据。
- [高级存储](../storage/common/storage-premium-storage.md)通常用于 IO 性能一贯较高且延迟一贯较低、托管 IO 密集型工作负荷的虚拟机。
- 如果要将高级帐户用于存储复制的数据，则还需要创建一个标准存储帐户来存储复制日志，这些日志将捕获本地数据正在发生的更改。
- 根据要用于故障转移 Azure VM 的资源模型，需在[资源管理器模式](../storage/common/storage-create-storage-account.md)或[经典模式](../storage/common/storage-create-storage-account.md)下设置帐户。
- 建议在开始之前先设置存储帐户。 否则，需要在 Site Recovery 部署期间执行此操作。 这些帐户必须位于与恢复服务保管库相同的区域中。
- 无法在同一订阅中的资源组之间或者在不同的订阅之间移动 Site Recovery 使用的存储帐户。


## <a name="next-steps"></a>后续步骤

转到[步骤 6：准备 VMM](vmm-to-azure-walkthrough-vmm-hyper-v.md)

