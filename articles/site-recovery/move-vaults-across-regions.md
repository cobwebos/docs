---
title: 将 Azure Site Recovery 配置移到另一个 Azure 区域 | Microsoft Docs
description: 有关将 Site Recovery 配置移到另一个 Azure 区域的指导
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/31/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: ba0e2577d6fb8bac66322917936fe06825af0d96
ms.sourcegitcommit: 6ad03fa28a0f60cb6dce6144f728c2ceb56ff6e2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2019
ms.locfileid: "68708460"
---
# <a name="moving-recovery-services-vault-and--azure-site-recovery-configuration-to-another-azure-region"></a>将恢复服务保管库和 Azure Site Recovery 配置移到另一个 Azure 区域

在多种情况下，出于易管理性、监管或公司合并/收购等原因，你希望将现有的 Azure 资源从一个区域移到另一个区域。移动 Azure VM 时，你可能想要移动的相关资源之一是相同 VM 的灾难恢复配置。 将现有灾难恢复配置从一个区域移到另一个区域并没有最佳的方法。 本质原因在于，目标区域是根据源 VM 区域配置的，当你决定更改目标区域时，预先存在的现有目标区域配置可能无法重用，而必须重置。 本文定义了重新配置灾难恢复设置以及将此配置转移到不同区域的分步过程。

在本文档中，你将：

> [!div class="checklist"]
> * 验证进行移动的先决条件
> * 识别 Azure Site Recovery 使用的资源 
> * 禁用复制
> * 删除资源 
> * 根据 VM 的新源区域重新设置 Site Recovery。

> [!IMPORTANT]
> 目前没有所谓的最佳方法可将恢复服务保管库和灾难恢复配置移到不同的区域，本文档将引导客户完成禁用复制并在新区域中重新设置复制的过程。

## <a name="prerequisites"></a>先决条件

- 在尝试将 Azure VM 移到另一区域之前，请确保删除灾难恢复配置。 

> [!NOTE]
> 如果 Azure VM 的新目标区域与灾难恢复目标区域相同，则可以使用现有的复制配置，并根据[此处](azure-to-azure-tutorial-migrate.md)所述的步骤进行迁移。 

- 确保做出明智的决策，并告知利益干系人在完成 VM 迁移之前，VM 不会得到灾难保护。 

## <a name="identify-the-resources-that-were-used-by-azure-site-recovery"></a>识别 Azure Site Recovery 使用的资源
建议先完成此步骤，然后再继续下一步骤，因为在复制 VM 期间可以更轻松地识别相关资源。

对于每个正在复制的 Azure VM，导航到“受保护的项” > “已复制的项”>“属性”，并识别以下资源   

- 目标资源组
- 缓存存储帐户
- 目标存储帐户（如果使用基于非托管磁盘的 Azure VM） 
- 目标网络


## <a name="disable-existing-disaster-recovery-configuration"></a>禁用现有的灾难恢复配置

1. 导航到“恢复服务保管库”  
2.  依次转到“受保护的项”   > “复制的项”  ，右键单击计算机，再单击“禁用复制”  。
3. 针对要移动的所有 VM 重复此步骤。
> [!NOTE]
> 不会从受保护的服务器中卸载移动服务，需要手动卸载。 如果你打算再次保护服务器，可以跳过卸载移动服务的步骤。

## <a name="delete-the-resources"></a>删除资源

1. 导航到“恢复服务保管库” 
2. 单击“删除” 
3. 继续删除在[上一步骤](#identify-the-resources-that-were-used-by-azure-site-recovery)中识别的所有其他资源
 
## <a name="move-azure-vms-to-the-new-target-region"></a>将 Azure VM 移到新目标区域

根据要求遵循下述步骤将 Azure VM 移到目标区域。

- [将 Azure VM 移到另一区域](azure-to-azure-tutorial-migrate.md)
- [将 Azure VM 移到可用性区域中](move-azure-VMs-AVset-Azone.md)

## <a name="re-set-up-site-recovery-based-on-the-new-source-region-for-the-vms"></a>根据 VM 的新源区域重新设置 Site Recovery

使用[此处](azure-to-azure-tutorial-enable-replication.md)所述的步骤，为已移到新区域的 Azure VM 配置灾难恢复
