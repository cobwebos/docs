---
title: "备份 Azure VM | Microsoft Docs"
description: "发现、注册和备份 Azure 虚拟机到恢复服务保管库。"
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "虚拟机备份; 备份虚拟机; 备份和灾难恢复; arm vm 备份"
ms.assetid: 5c68481d-7be3-4e68-b87c-0961c267053e
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 8/15/2017
ms.author: trinadhk;jimpark;markgal;
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 892a88a2bc9d492f8a3afe59c05b4729f4830e6d
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="back-up-azure-virtual-machines-to-a-recovery-services-vault"></a>将 Azure 虚拟机备份到恢复服务保管库

本文详细介绍如何将 Azure VM（使用 Resource Manager 部署和经典模型部署）备份到恢复服务保管库。 备份 VM 的大部分工作是进行准备。 在进行备份或保护 VM 之前，必须完成[先决条件](backup-azure-arm-vms-prepare.md)中的步骤来准备好保护 VM 的环境。 完成先决条件后，可以启动备份操作来创建 VM 的快照。


[!INCLUDE [learn about backup deployment models](../../includes/backup-deployment-models.md)]

有关详细信息，请参阅[在 Azure 中规划 VM 备份基础结构](backup-azure-vms-introduction.md)和 [Azure 虚拟机](https://azure.microsoft.com/documentation/services/virtual-machines/)。

## <a name="triggering-the-backup-job"></a>触发备份作业
与恢复服务保管库关联的备份策略定义备份操作的运行频率和时间。 默认情况下，第一个计划的备份是初始备份。 在执行初始备份之前，“**备份作业**”边栏选项卡上的“上次备份状态”显示为“**警告(等待初始备份)**”。

![等待中的备份](./media/backup-azure-vms-first-look-arm/initial-backup-not-run.png)

除非初始备份预计马上开始，否则建议运行“立即备份” 。 以下过程从保管库仪表板开始。 当完成所有先决条件后，此过程可用于运行初始备份作业。 如果已运行初始备份作业，此过程便不适用。 关联的备份策略将确定后续的备份作业。  

若要运行初始备份作业，请执行以下操作：

1. 在保管库仪表板上，单击“备份项”下的数字，或单击“备份项”磁贴。 <br/>
  ![“设置”图标](./media/backup-azure-vms-first-look-arm/rs-vault-config-vm-back-up-now-1.png)

  “**备份项**”边栏选项卡随即打开。

  ![备份项](./media/backup-azure-vms-first-look-arm/back-up-items-list.png)

2. 在“备份项”边栏选项卡上，选择项。

  ![“设置”图标](./media/backup-azure-vms-first-look-arm/back-up-items-list-selected.png)

  “备份项”列表随即打开。 <br/>

  ![已触发备份作业](./media/backup-azure-vms-first-look-arm/backup-items-not-run.png)

3. 在“备份项”列表中，单击省略号“...”以打开上下文菜单。

  ![上下文菜单](./media/backup-azure-vms-first-look-arm/context-menu.png)

  上下文菜单随即打开。

  ![上下文菜单](./media/backup-azure-vms-first-look-arm/context-menu-small.png)

4. 在上下文菜单上，单击“立即备份”。

  ![上下文菜单](./media/backup-azure-vms-first-look-arm/context-menu-small-backup-now.png)

  “立即备份”边栏选项卡随即打开。

  ![显示“立即备份”边栏选项卡](./media/backup-azure-vms-first-look-arm/backup-now-blade-short.png)

5. 在“立即备份”边栏选项卡上，单击日历图标，使用日历控件选择保留此恢复点的最后一天，并单击“备份”。

  ![设置保留立即备份恢复点的最后一天](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

  部署通知会告知你备份作业已触发，并且可以在“备份作业”页面上监视作业的进度。 创建初始备份可能需要一些时间，具体取决于 VM 的大小。

6. 若要查看或跟踪初始备份的状态，请在保管仪表板的“备份作业”磁贴上，单击“正在进行”。

  ![“备份作业”磁贴](./media/backup-azure-vms-first-look-arm/open-backup-jobs-1.png)

  “备份作业”边栏选项卡随即打开。

  ![“备份作业”磁贴](./media/backup-azure-vms-first-look-arm/backup-jobs-in-jobs-view-1.png)

  在“**备份作业**”边栏选项卡中，可以看到所有作业的状态。 请查看 VM 的备份作业仍在进行中，还是已完成。 完成备份作业后，状态将变为“已完成”。

  > [!NOTE]
  > 在执行备份操作的过程中，Azure 备份服务将向每个 VM 中的备份扩展发出一条命令，刷新所有写入并取得一致快照。
  >
  >

## <a name="troubleshooting-errors"></a>排查错误
如果在备份虚拟机时遇到问题，请参阅 [VM 故障排除文章](backup-azure-vms-troubleshoot.md)以获取帮助。

## <a name="next-steps"></a>后续步骤
现已保护 VM，请参阅以下文章了解 VM 管理任务以及如何还原 VM。

* [管理和监视虚拟机](backup-azure-manage-vms.md)
* [恢复虚拟机](backup-azure-arm-restore-vms.md)
