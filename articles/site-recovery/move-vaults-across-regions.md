---
title: 将 Azure Site Recovery 保管库移动到另一个区域
description: 描述如何将恢复服务保管库 (Azure Site Recovery) 移动到另一个 Azure 区域
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/31/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 32dff9a165125ab1949560ce36438ae266cd3036
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2019
ms.locfileid: "74090298"
---
# <a name="move-a-recovery-services-vault-and-azure-site-recovery-configuration-to-another-azure-region"></a>将恢复服务保管库和 Azure Site Recovery 配置移到另一个 Azure 区域

在各种情况下，你可能希望将现有 Azure 资源从一个区域移到另一个区域。 例如，可管理性、治理原因或公司合并和收购。 移动 Azure VM 时，可能需要移动的相关资源之一是灾难恢复配置。 

将现有灾难恢复配置从一个区域移到另一个区域并没有最佳方法。 这是因为你已根据源 VM 区域配置目标区域。 当你决定更改源区域时，目标区域以前存在的配置无法重复使用，必须重置。 本文定义了重新配置灾难恢复设置以及将此配置移到不同区域的分步过程。

在本文档中，你将：

> [!div class="checklist"]
> * 验证移动的先决条件。
> * 确定 Azure Site Recovery 已使用的资源。
> * 禁用复制。
> * 删除资源。
> * 根据 VM 的新源区域设置 Site Recovery。

> [!IMPORTANT]
> 目前，将恢复服务保管库和灾难恢复配置按原样移到不同的区域并没有最佳方法。 本文将指导你完成禁用复制并在新区域中进行设置的过程。

## <a name="prerequisites"></a>先决条件

- 在尝试将 Azure VM 移到另一区域之前，请确保删除灾难恢复配置。 

  > [!NOTE]
  > 如果 Azure VM 的新目标区域与灾难恢复目标区域相同，则可以使用现有的复制配置并进行移动。 按照[将 Azure IaaS VM 移到另一个 Azure 区域](azure-to-azure-tutorial-migrate.md)中的步骤进行操作。

- 请确保作出明智的决定，并通知利益干系人。 在移动 VM 完成之前，VM 不会受到灾难保护。

## <a name="identify-the-resources-that-were-used-by-azure-site-recovery"></a>识别 Azure Site Recovery 使用的资源
我们建议你在继续下一步之前执行此步骤。 在复制 VM 时，更容易确定相关资源。

对于要复制的每个 Azure VM，请转至“受保护的项”   > “已复制的项”   > “属性”  并确定以下资源：

- 目标资源组
- 缓存存储帐户
- 目标存储帐户（如果使用基于非托管磁盘的 Azure VM） 
- 目标网络


## <a name="disable-the-existing-disaster-recovery-configuration"></a>禁用现有的灾难恢复配置

1. 转到“恢复服务保管库”。
2. 在“受保护的项”   > “已复制的项”  中，右键单击计算机，选择“禁用复制”  。
3. 对于要移动的所有 VM 重复执行此步骤。

> [!NOTE]
> 移动服务不会从受保护的服务器上卸载。 你必须手动卸载它。 如果你打算再次保护服务器，可以跳过卸载移动服务的步骤。

## <a name="delete-the-resources"></a>删除资源

1. 转到“恢复服务保管库”。
2. 选择“删除”。 
3. 删除[之前确定](#identify-the-resources-that-were-used-by-azure-site-recovery)的所有其他资源。
 
## <a name="move-azure-vms-to-the-new-target-region"></a>将 Azure VM 移到新目标区域

根据要求，按照以下文章中的步骤将 Azure VM 移到目标区域：

- [将 Azure VM 移到另一区域](azure-to-azure-tutorial-migrate.md)
- [将 Azure VM 移到可用性区域中](move-azure-VMs-AVset-Azone.md)

## <a name="set-up-site-recovery-based-on-the-new-source-region-for-the-vms"></a>根据 VM 的新源区域设置 Site Recovery

按照[为 Azure VM 设置灾难恢复](azure-to-azure-tutorial-enable-replication.md)中的步骤为移到新区域的 Azure VM 配置灾难恢复。
