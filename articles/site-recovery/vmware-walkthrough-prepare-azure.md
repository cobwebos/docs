---
title: "准备好 Azure 资源以便使用 Azure Site Recovery 将本地 VMware VM 复制到 Azure| Microsoft Docs"
description: "介绍了需要先在 Azure 中准备哪些资源，才能开始使用 Azure Site Recovery 服务将本地 VMware VM 复制到 Azure"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 4e320d9b-8bb8-46bb-ba21-77c5d16748ac
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 40abff72278c9f8d9f701023fd473fe52c17b421
ms.contentlocale: zh-cn
ms.lasthandoff: 08/22/2017

---
# <a name="step-5-prepare-azure-resources-for-vmware-replication-to-azure"></a>步骤 5：准备好 Azure 资源以便将 VMWare 复制到 Azure


请按照本文中的说明操作，准备 Azure 资源，以便可以使用 [Azure Site Recovery](site-recovery-overview.md) 服务将本地计算机复制到 Azure。

请将评论和问题发布到这篇文章的底部，或者发布到 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。

## <a name="before-you-start"></a>开始之前

请确保已了解[先决条件](vmware-walkthrough-prerequisites.md)

## <a name="set-up-an-azure-account"></a>设置 Azure 帐户

- 获取 [Microsoft Azure 帐户](http://azure.microsoft.com/)。
- 可以从 [免费试用版](https://azure.microsoft.com/pricing/free-trial/)开始。
- 若要查看 Site Recovery 的受支持区域，请参阅 [Azure Site Recovery 定价详细信息](https://azure.microsoft.com/pricing/details/site-recovery/)中的“地域可用性”。
- 了解有关 [Site Recovery 定价](site-recovery-faq.md#pricing)的信息，并获取[定价详细信息](https://azure.microsoft.com/pricing/details/site-recovery/)。



## <a name="set-up-an-azure-network"></a>设置 Azure 网络

- 设置 Azure 网络。 在故障转移后创建 Azure VM 时，Azure VM 将置于此网络中。
- Azure 门户中的 Site Recovery 可以使用在 [Resource Manager](../resource-manager-deployment-model.md) 或经典模式下设置的网络。
- 该网络应位于与恢复服务保管库相同的区域
- 了解[虚拟网络定价](https://azure.microsoft.com/pricing/details/virtual-network/)。
- 详细了解故障转移后的 [Azure VM 连接性](site-recovery-network-design.md)。


## <a name="set-up-an-azure-storage-account"></a>设置 Azure 存储帐户

- Site Recovery 将本地计算机复制到 Azure 存储。 发生故障转移后，通过存储创建 Azure VM。
- 设置用于所复制数据的 [Azure 存储帐户](../storage/common/storage-create-storage-account.md#create-a-storage-account)。
- Azure 门户中的 Site Recovery 可以使用在 Resource Manager 或经典模式下设置的存储帐户。
- 存储帐户可以是标准帐户，也可以是[高级](../storage/common/storage-premium-storage.md)帐户。
- 如果创建的是高级帐户，还需要额外使用标准帐户来记录数据。


## <a name="next-steps"></a>后续步骤

转到[步骤 6：准备 VMware 资源](vmware-walkthrough-prepare-vmware.md)

