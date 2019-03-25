---
title: 当使用 Azure 备份创建 Azure VM 启用备份
description: 介绍如何使用 Azure 备份创建 Azure VM 时启用备份。
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: raynew
ms.openlocfilehash: d96b898c8f72abd7e4eb3522ae046e9fc926f387
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403572"
---
# <a name="enable-backup-when-you-create-an-azure-vm"></a>在创建 Azure VM 时启用备份

使用 Azure 备份服务来备份 Azure 虚拟机 (Vm)。 根据备份策略中指定的计划备份的 Vm 和从备份创建恢复点。 恢复点存储在恢复服务保管库中。

在 Azure 门户中，这篇文章详细介绍如何在创建虚拟机 (VM) 时启用备份。  

## <a name="before-you-start"></a>开始之前

- [检查](backup-support-matrix-iaas.md#supported-backup-actions)如果创建 VM 时启用备份支持的操作系统。

## <a name="sign-in-to-azure"></a>登录 Azure

如果你不已登录到你的帐户，登录到[Azure 门户](https://portal.azure.com)。
 
## <a name="create-a-vm-with-backup-configured"></a>使用配置的备份创建的 VM

1. 在 Azure 门户中，单击**创建资源**。

2. 在 Azure Marketplace 中，单击**计算**，然后选择 VM 映像。

3. 根据所使用的 VM 设置[Windows](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)或[Linux](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal)说明。

4. 上**管理**选项卡上，在**启用备份**，单击**上**。
5. Azure 备份备份到恢复服务保管库。 单击**创建新**如果没有现有的保管库。
6. 接受建议的保管库名称或指定自己。
7. 指定或创建保管库将位于的资源组。 资源组保管库可以不同于 VM 资源组。

    ![为 VM 启用备份](./media/backup-during-vm-creation/enable-backup.png) 

8. 接受默认备份策略，或修改的设置。
    - 备份策略指定如何频繁执行备份快照的 VM，并保留这些备份副本的时间长度。 
    - 默认策略将每天一次备份 VM。
    - 您可以自定义自己的备份策略以进行每日或每周备份 Azure VM。
    - [了解详细信息](backup-azure-vms-introduction.md#backup-and-restore-considerations)有关 Azure Vm 备份时的注意事项。
    - [了解详细信息](backup-instant-restore-capability.md)关于即时还原功能。

      ![默认备份策略](./media/backup-during-vm-creation/daily-policy.png) 


## <a name="start-a-backup-after-creating-the-vm"></a>创建 VM 后启动备份 

根据备份策略将运行你的 VM 备份。 但是，我们建议你运行初始备份。 

创建 VM 后，请执行以下操作：

1. 在 VM 属性中，单击**备份**。 VM 状态是初始备份挂起，直到运行初始备份
2. 单击**立即备份**运行按需备份。

    ![运行按需备份](./media/backup-during-vm-creation/run-backup.png) 

## <a name="use-a-resource-manager-template-to-deploy-a-protected-vm"></a>使用资源管理器模板部署一个受保护的虚拟机

前面的步骤说明如何使用 Azure 门户创建虚拟机并在恢复服务保管库中对其进行保护。 若要快速部署一个或多个 Vm，并在恢复服务保管库中保护它们，请参阅模板[部署 Windows VM 并启用备份](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/)。



## <a name="next-steps"></a>后续步骤 

现在，已保护了 VM，了解如何管理和还原它们。

- [管理和监视 Vm](backup-azure-manage-vms.md) 
- [还原 VM](backup-azure-arm-restore-vms.md) 

如果遇到任何问题，[查看](backup-azure-vms-troubleshoot.md)故障排除指南。
