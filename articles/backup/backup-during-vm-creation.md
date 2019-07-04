---
title: 在创建 Azure VM 时通过 Azure 备份启用备份
description: 介绍了如何在创建 Azure VM 时通过 Azure 备份启用备份。
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: raynew
ms.openlocfilehash: a19653f7ae3900fd7999f347ef4d3ef710be1430
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67436335"
---
# <a name="enable-backup-when-you-create-an-azure-vm"></a>在创建 Azure VM 时启用备份

使用 Azure 备份服务备份 Azure 虚拟机 (VM)。 VM 是根据在备份策略中指定的计划备份的，恢复点是基于备份创建的。 恢复点存储在恢复服务保管库中。

本文详细介绍了在 Azure 门户中创建虚拟机 (VM) 时如何启用备份。  

## <a name="before-you-start"></a>开始之前

- [检查](backup-support-matrix-iaas.md#supported-backup-actions)如果创建 VM 时启用备份支持的操作系统。

## <a name="sign-in-to-azure"></a>登录 Azure

如果尚未登录到你的帐户，请先登录到 [Azure 门户](https://portal.azure.com)。

## <a name="create-a-vm-with-backup-configured"></a>创建配置了备份的 VM

1. 在 Azure 门户中，单击“创建资源”  。

2. 在 Azure 市场中，单击“计算”  ，然后选择一个 VM 映像。

3. 根据适用于 [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal) 或 [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal) 的说明设置 VM。

4. 在“管理”  选项卡上，在“启用备份”  中，单击“开启”  。
5. Azure 备份将备份到恢复服务保管库。 如果没有现有的保管库，请单击“新建”  。
6. 接受建议的保管库名称或自己指定名称。
7. 指定或创建保管库将位于其中的资源组。 资源组保管库可以不同于 VM 资源组。

    ![为 VM 启用备份](./media/backup-during-vm-creation/enable-backup.png)

8. 接受默认备份策略，或修改设置。
    - 备份策略指定执行 VM 备份快照的频率和这些备份副本的保留期。
    - 默认策略每天备份 VM 一次。
    - 你可以为 Azure VM 自定义你自己的备份策略以便每日或每周进行备份。
    - [详细了解](backup-azure-vms-introduction.md#backup-and-restore-considerations) Azure VM 的备份注意事项。
    - [详细了解](backup-instant-restore-capability.md)即时还原功能。

      ![默认备份策略](./media/backup-during-vm-creation/daily-policy.png)


> [!NOTE]
> Azure 备份服务创建单独的资源组 （而不是 VM 资源组） 来存储快照，使用的命名格式**AzureBackupRG_geography_number** (示例：AzureBackupRG_northeurope_1）。 此资源组中的数据将保留以天为单位中指定的持续时间*保留即时恢复快照*部分中的 Azure 虚拟机备份策略。  对此资源组应用锁可能会导致备份失败。<br>
根据限制策略会阻止创建资源点集合中其再次导致备份失败，则还应从任何名称/标记限制排除此资源组。


## <a name="start-a-backup-after-creating-the-vm"></a>在创建 VM 后启动备份

你的 VM 备份将根据备份策略运行。 但是，我们建议你运行一个初始备份。

创建 VM 后，请执行以下操作：

1. 在 VM 属性中，单击“备份”。  VM 状态将保持为“初始备份挂起”，直到初始备份运行
2. 单击“立即备份”  来运行按需备份。

    ![运行按需备份](./media/backup-during-vm-creation/run-backup.png)

## <a name="use-a-resource-manager-template-to-deploy-a-protected-vm"></a>使用资源管理器模板部署一个受保护的 VM

前面的步骤说明了如何使用 Azure 门户来创建虚拟机，以及使用恢复服务保管库来保护该虚拟机。 若要快速部署一台或多台虚拟机并在恢复服务保管库中保护它们，请参阅模板[部署 Windows VM 并启用备份](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/)。



## <a name="next-steps"></a>后续步骤

现在，你已保护了你的 VM，请了解如何管理和还原它们。

- [管理和监视 VM](backup-azure-manage-vms.md)
- [还原 VM](backup-azure-arm-restore-vms.md)

如果遇到任何问题，请[查看](backup-azure-vms-troubleshoot.md)故障排除指南。
