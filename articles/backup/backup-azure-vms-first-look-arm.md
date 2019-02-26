---
title: 使用 Azure 备份服务通过 VM 设置备份 Azure VM
description: 了解如何使用 Azure 备份服务备份 Azure VM
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2019
ms.author: raynew
ms.openlocfilehash: 40557d4e71dfea5996396cde634f7a1c80913556
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430533"
---
# <a name="back-up-an-azure-vm-from-the-vm-settings"></a>通过 VM 设置备份 Azure VM

本文说明如何使用 [Azure 备份](backup-overview.md)服务备份 Azure VM。 可以使用多个方法备份 Azure VM：

- 单个 Azure VM：本文中的说明介绍如何直接通过 VM 设置备份 Azure VM。
- 多个 Azure VM：可以设置一个恢复服务保管库，然后为多个 Azure VM 配置备份。 按照[此文](backup-azure-arm-vms-prepare.md)中针对此方案的说明操作。

 

## <a name="before-you-start"></a>开始之前

1. [了解](backup-architecture.md#how-does-azure-backup-work)备份工作原理，并[验证](backup-support-matrix.md#azure-vm-backup-support)支持要求。 
2. [概要了解](backup-azure-vms-introduction.md) Azure VM 备份。

### <a name="azure-vm-agent-installation"></a>Azure VM 代理安装

为了备份 Azure VM，Azure 备份会在 VM 代理上安装一个扩展，该代理在计算机上运行。 如果 VM 是根据 Azure 市场映像创建的，则代理将运行。 在某些情况下（例如创建自定义 VM，或者从本地迁移计算机）， 可能需要手动安装代理。 

- 如果需要手动安装 VM 代理，请按 [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) 或 [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) VM 的说明操作。 
- 在安装代理后启用备份时，Azure 备份会将备份扩展安装到代理。 它可以在没有用户干预的情况下更新和修补扩展。

## <a name="back-up-from-azure-vm-settings"></a>通过 Azure VM 设置进行备份


1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 单击“所有服务”，在“筛选器”中键入“虚拟机”，然后单击“虚拟机”。 
3. 从 VM 列表中选择要备份的 VM。
4. 在 VM 菜单上单击“备份”。 
5. 在“恢复服务保管库”中执行以下操作：
  - 如果已有一个保管库，请单击“选择现有”，然后选择一个保管库。
  - 如果没有保管库，请单击“新建”。 指定保管库的名称。 它在 VM 所在的区域和资源组中创建。 直接通过 VM 设置启用备份时，不能修改这些设置。

  ![启用备份向导](./media/backup-azure-vms-first-look-arm/vm-menu-enable-backup-small.png)

6. 在“选择备份策略”中执行以下操作：

  - 保留默认策略。 这样会每天一次在指定的时间备份 VM，并在保管库中保留备份 30 天。
  - 选择现有的备份策略（如果有）。
  - 创建一项新策略，然后定义策略设置。  

  ![选择备份策略](./media/backup-azure-vms-first-look-arm/set-backup-policy.png)

7. 单击“启用备份”。 这样会将备份策略与 VM 相关联。 

    ![“启用备份”按钮](./media/backup-azure-vms-first-look-arm/vm-management-menu-enable-backup-button.png)

8. 可在门户通知中跟踪配置进度。
9. 待作业完成后，请在 VM 菜单中单击“备份”。 页面会显示 VM 的备份状态、有关恢复点的信息、正在运行的作业以及发出的警报。

  ![备份状态](./media/backup-azure-vms-first-look-arm/backup-item-view-update.png)

10. 启用备份后，会运行[初始备份](#run-the-initial-backup)。 可以立即启动初始备份，也可以等待它按备份计划启动。
    - 在初始备份完成之前，“上次备份状态”显示为“警告(初始备份挂起)”。
    - 若要查看下一个计划的备份何时运行，请单击备份策略名称。
    
   

> [!NOTE]
> Azure 备份会创建一个单独的资源组（不同于 VM 资源组）来存储还原点，采用的命名格式为 **AzureBackupRG_geography_number**（例如：AzureBackupRG_northeurope_1）。 不应锁定该资源组。



## <a name="run-a-backup-immediately"></a>立即运行备份 

1. 若要立即运行备份，请在 VM 菜单中单击“备份” > “立即备份”。

    ![运行备份](./media/backup-azure-vms-first-look-arm/backup-now-update.png)

2. 在“立即备份”中，使用日历控件选择保留此恢复点的最后一天，然后单击“确定”。
  
    ![备份保留日期](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

3. 门户通知会告知你备份作业已触发。 若要监视备份进度，请单击“查看所有作业”。




## <a name="back-up-from-the-recovery-services-vault"></a>从恢复服务保管库备份

按照本文中的说明为 Azure VM 启用备份，方法是：设置一个 Azure 备份恢复服务保管库，然后在保管库中启用备份。

## <a name="next-steps"></a>后续步骤

- 如果在学习本文中的任何过程时遇到困难，请参阅[故障排除指南](backup-azure-vms-troubleshoot.md)。
- [了解](backup-azure-manage-vms.md)备份管理。

