---
title: "大规模备份 Azure 虚拟机 | Microsoft Docs"
description: "同时将多个虚拟机备份到 Azure"
services: backup
keywords: "虚拟机备份; 虚拟机备份; 备份 vm; 备份 vm; 备份 Azure vm; 备份和灾难恢复"
author: markgalioto
ms.author: markgal
ms.date: 09/16/2017
ms.topic: article
ms.service: backup
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: f144db921a8b2d01dbfe883a48574c4fabdae6f7
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---

# <a name="use-azure-portal-to-back-up-multiple-virtual-machines"></a>使用 Azure 门户备份多个虚拟机

在 Azure 中备份数据时，数据存储在名为恢复服务保管库的 Azure 资源中。 恢复服务保管库资源位于大多数 Azure 服务的“设置”菜单中。 在大多数 Azure 服务的“设置”菜单中集成恢复服务保管库的好处在于显著简化数据备份。 但是，单独使用公司中的每个数据库或虚拟机非常麻烦。 如果将所有虚拟机的数据备份到一个部门或一个位置，会发生什么情况？ 通过创建备份策略和将此策略应用到目标虚拟机，可轻松备份多个虚拟机。 本教程介绍了如何完成以下操作：

> [!div class="checklist"]
> * 创建恢复服务保管库
> * 定义备份策略
> * 应用备份策略以保护多个虚拟机
> * 对保护的虚拟机触发按需备份作业

## <a name="log-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com/)。

## <a name="create-a-recovery-services-vault"></a>创建恢复服务保管库

恢复服务保管库包含备份数据和应用于受保护虚拟机的备份策略。 备份虚拟机是在本地完成的过程。 无法将某个位置的虚拟机备份到另一个位置的恢复服务保管库。 因此，每个包含要备份的虚拟机的 Azure 位置中必须至少存在一个恢复服务保管库。

1. 在左侧菜单上，选择“更多服务”，然后在服务列表中键入“恢复服务”。 键入时，系统将筛选出资源列表。 看见列表中的恢复服务保管库时，请将其选中以打开“恢复服务保管库”菜单。

    ![打开“恢复服务保管库”菜单](./media/tutorial-backup-vm-at-scale/full-browser-open-rs-vault.png) <br/>

2. 在“恢复服务保管库”菜单中，单击“添加”，打开恢复服务保管库菜单。

    ![打开保管库菜单](./media/tutorial-backup-vm-at-scale/provide-vault-detail-2.png)

3. 在“恢复服务保管库”菜单中， 

    - 在“名称”中键入“myRecoveryServicesVault”，
    - 当前订阅 ID 显示在“订阅”中。 如有其他订阅，可为新保管库选择另一订阅。
    - 对于“资源组”，请选择“使用现有”，然后选择“myResourceGroup”。 如果没有“myResourceGroup”，请选择“新建”，然后键入“myResourceGroup”。
    - 从“位置”下拉菜单中选择“西欧”。
    - 单击“创建”，创建恢复服务保管库。

恢复服务保管库必须与当前受保护的虚拟机处于相同位置。 如果多个区域中有虚拟机，请在每个区域中创建恢复服务保管库。 本教程在西欧创建恢复服务保管库的原因在于，西欧是创建“myVM”（通过快速入门创建的虚拟机）的位置。

创建恢复服务保管库可能需要数分钟。 可以在门户右上区域监视状态通知。 创建保管库后，它会显示在“恢复服务保管库”的列表中。

创建恢复服务保管库时，保管库默认具有异地冗余存储。 为提供数据弹性，异地冗余存储会多次跨两个 Azure 区域复制数据。

## <a name="set-backup-policy-to-protect-vms"></a>设置备份策略以保护 VM

创建恢复服务保管库后，下一步是为数据类型配置保管库和设置备份策略。 备份策略是有关恢复点创建频率和时间的计划。 策略还包含恢复点的保留范围。 本教程假设你的企业是一家涵盖酒店、体育馆、餐厅和租赁用地的综合体育中心，并且你要保护虚拟机上的数据。 请按照以下步骤创建用于财务数据的备份策略。

1. 在恢复服务保管库列表中，选择“myRecoveryServicesVault”以打开其仪表板。

   ![打开“方案”菜单](./media/tutorial-backup-vm-at-scale/open-vault-from-list.png)

2. 在保管库仪表板菜单中，单击“备份”以打开“备份”菜单。

3. 在“备份目标”菜单的“工作负荷的运行位置”下拉菜单中选择“Azure”。 从“要备份的内容”下拉列表中选择“虚拟机”，然后单击“备份”。

    这些操作为此恢复服务保管库与虚拟机交互的准备操作。 恢复服务保管库具有默认策略，该策略每天创建一个还原点，并将还原点保留 30 天。

    ![打开“方案”菜单](./media/tutorial-backup-vm-at-scale/backup-goal.png)

4. 若要创建新策略，在“备份策略”菜单的“选择备份策略”下拉菜单中，选择“新建”。

    ![选择工作负荷](./media/tutorial-backup-vm-at-scale/create-new-policy.png)

5. 对于“备份策略”菜单中的“策略名称”，请键入“财务”。 对备份策略输入以下更改： 
    - 对于“备份频率”，请将时区设置为“中部时间”。 由于此综合体育中心位于德克萨斯，其所有者希望时间为当地时间。 将备份频率设置为每日凌晨 3:30。
    - 对于“每日备份点的保留期”，请将时间段设置为 90 天。
    - 对于“每周备份点的保留期”，请使用“星期一”还原点并将其保留 52 周。
    - 对于“每月备份点的保留期”，使用当月第一个星期日作为还原点，并将其保留 36 个月。
    - 取消选中“每年备份点的保留期”选项。 财务主管不希望数据保留期超过 36 个月。
    - 单击“确定”以创建备份策略。

    ![选择工作负荷](./media/tutorial-backup-vm-at-scale/set-new-policy.png) 

    创建备份策略后，将策略与虚拟机关联。

6. 在“选择虚拟机”对话框中选择“myVM”，然后单击“确定”，将备份策略部署到虚拟机。 

    随即显示位于同一位置且尚未与备份策略关联的所有虚拟机。 选择“myVMH1”和“myVMR1”，将其与“财务”策略关联。

    ![选择工作负荷](./media/tutorial-backup-vm-at-scale/choose-vm-to-protect.png) 

    部署完成后，会收到告知部署已成功完成的通知。

## <a name="initial-backup"></a>初始备份

已启用恢复服务保管库备份，但尚未创建初始备份。 它是触发首次备份的灾难恢复最佳做法，可让数据受到保护。 

运行按需备份作业：

1. 在保管库仪表板上，单击“备份项”下的“3”，打开“备份项”菜单。

    ![“设置”图标](./media/tutorial-backup-vm-at-scale/tutorial-vm-back-up-now.png)

    “备份项”菜单随即打开。

2. 在“备份项”菜单上，单击“Azure 虚拟机”，打开与保管库关联的虚拟机列表。

    ![“设置”图标](./media/tutorial-backup-vm-at-scale/three-virtual-machines.png)

    “备份项”列表随即打开。

    ![已触发备份作业](./media/tutorial-backup-vm-at-scale/initial-backup-context-menu.png)

3. 在“备份项”列表中，单击省略号“...”以打开上下文菜单。

4. 在上下文菜单上，选择“立即备份”。

    ![上下文菜单](./media/tutorial-backup-vm-at-scale/context-menu.png)

    “立即备份”菜单随即打开。

5. 在“立即备份”菜单中，输入恢复点保留的最后日期，然后单击“备份”。

    ![设置保留立即备份恢复点的最后一天](./media/tutorial-backup-vm-at-scale/backup-now-short.png)

    部署通知会告知你备份作业已触发，并且可以在“备份作业”页面上监视作业的进度。 创建初始备份可能需要一些时间，具体取决于虚拟机的大小。

    初始备份作业完成后，可在“备份作业”菜单中查看其状态。 按需备份作业已为“myVM”创建初始还原点。 若要备份其他虚拟机，请对每个虚拟机重复上述操作。 

    ![“备份作业”磁贴](./media/tutorial-backup-vm-at-scale/initial-backup-complete.png)
  
## <a name="clean-up-resources"></a>清理资源

如果计划继续使用后续教程，请勿清除在本教程中创建的资源。 如果不打算继续，请在 Azure 门户中执行以下步骤来删除此教程创建的所有资源。

1. 在“myRecoveryServicesVault”仪表板上，单击“备份项”下的“3”，打开“备份项”菜单。

    ![“设置”图标](./media/tutorial-backup-vm-at-scale/tutorial-vm-back-up-now.png)


2. 在“备份项”菜单上，单击“Azure 虚拟机”，打开与保管库关联的虚拟机列表。

    ![“设置”图标](./media/tutorial-backup-vm-at-scale/three-virtual-machines.png)

    “备份项”列表随即打开。

3. 在“备份项”菜单中，单击省略号以打开上下文菜单。

    ![“设置”图标](./media/tutorial-backup-vm-at-scale/context-menu-to-delete-vm.png)

4. 在上下文菜单上，选择“停止备份”以打开“停止备份”菜单。 

    ![“设置”图标](./media/tutorial-backup-vm-at-scale/context-menu-for-delete.png)

5. 在“停止备份”菜单上，选择上部的下拉列表菜单，然后选择“删除备份数据”。

6. 在“输入备份项名称”对话框中，键入“myVM”。
 
7. 验证备份项（显示勾号）后，“停止备份”按钮处于启用状态。 单击“停止备份”，停用策略并删除还原点。 

    ![单击“停止备份”以删除保管库](./media/tutorial-backup-vm-at-scale/provide-reason-for-delete.png)。

8. 在“myRecoveryServicesVault”菜单中，单击“删除”。

    ![单击“停止备份”以删除保管库](./media/tutorial-backup-vm-at-scale/deleting-the-vault.png)

    删除此保管库后，将返回恢复服务保管库列表。


## <a name="next-steps"></a>后续步骤

本教程使用 Azure 门户执行了以下操作：

> [!div class="checklist"]
> * 创建恢复服务保管库
> * 设置保管库以保护虚拟机
> * 创建自定义备份和保留策略
> * 分配策略以保护多个虚拟机
> * 触发虚拟机的按需备份

继续阅读下一教程，学习如何从磁盘还原 Azure 虚拟机。 

> [!div class="nextstepaction"]
> [使用 CLI 还原 VM](./tutorial-restore-disk.md)

