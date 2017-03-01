---
title: "StorSimple Snapshot Manager 备份策略 | Microsoft Docs"
description: "介绍如何使用 StorSimple Snapshot Manager MMC 管理单元创建和管理控制计划备份的备份策略。"
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: 04415d0b-42f0-4737-8afa-257fb2dbe5d0
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/12/2016
ms.author: v-sharos
translationtype: Human Translation
ms.sourcegitcommit: 9cf39b922e51dd60b5b2c51cb7030436c60232a5
ms.openlocfilehash: 88fc29b81c9502374a25cb34175ad1ee7c25423f


---
# <a name="use-storsimple-snapshot-manager-to-create-and-manage-backup-policies"></a>使用 StorSimple Snapshot Manager 创建和管理备份策略
## <a name="overview"></a>概述
备份策略创建在本地或云中备份卷数据的计划。 当创建备份策略时，还可以指定保留策略。 （可以保留最多 64 张快照。）有关备份策略的详细信息，请参阅 [StorSimple 8000 系列：混合云解决方案](storsimple-overview.md)中的[备份类型](storsimple-what-is-snapshot-manager.md#backup-types-and-backup-policies)。

本教程介绍如何执行以下操作：

* 创建备份策略
* 编辑备份策略
* 删除备份策略

## <a name="create-a-backup-policy"></a>创建备份策略
使用以下过程创建新的备份策略。

#### <a name="to-create-a-backup-policy"></a>创建备份策略
1. 单击桌面图标启动 StorSimple Snapshot Manager。
2. 在“**作用域**”窗格中，右键单击“**备份策略**”，然后单击“**创建备份策略**”。

    ![创建备份策略](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_BU_policy.png)

    此时将显示“**创建策略**”对话框。

    ![创建策略 -“常规”选项卡](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_general.png)
3. 在“**常规**”选项卡上，填写以下信息：

   1. 在“**名称**”文本框中，键入策略的名称。
   2. 在“**卷组**”文本框中，键入与该策略相关联的卷组名称。
   3. 选择“**本地快照**”或“**云快照**”。
   4. 选择要保留的快照数。 如果选择“**全部**”，则 64 张快照都会被保留（最大值）。
4. 单击“**计划**”选项卡。

    ![创建策略 -“计划”选项卡](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_schedule.png)
5. 在“**计划**”选项卡上，填写以下信息：

   1. 单击“**启用**”复选框计划下一次备份。
   2. 在“**设置**”下，选择“**一次**”、“**每日**”、“**每周**”或“**每月**”。
   3. 在“**开始**”文本框中，单击日历图标并选择开始日期。
   4. 在“**高级设置**”下，可以设置可选的重复计划和结束日期。
   5. 单击 **“确定”**。

创建备份策略后，以下信息会显示在“**结果**”窗格中：

* **名称** – 备份策略的名称。
* **类型** – 本地快照或云快照。
* **卷组** – 与策略关联的卷组。
* **保留** – 保留的快照数目；最大值为 64。
* **创建日期** – 创建此策略的日期。
* **已启用** – 该策略当前是否生效：**True** 表示已生效，**False** 表示未生效。

## <a name="edit-a-backup-policy"></a>编辑备份策略
使用以下过程编辑现有的备份策略。

#### <a name="to-edit-a-backup-policy"></a>编辑备份策略
1. 单击桌面图标启动 StorSimple Snapshot Manager。
2. 在“**作用域**”窗格中，单击“**备份策略**”节点。 所有备份策略将显示在“**结果**”窗格中。
3. 右键单击要编辑的策略，然后单击“**编辑**”。

    ![编辑备份策略](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Edit_BU_policy.png)
4. 当“**创建策略**”窗口出现时，输入你的更改，然后单击“**确定**”。

## <a name="delete-a-backup-policy"></a>删除备份策略
使用以下过程删除备份策略。

#### <a name="to-delete-a-backup-policy"></a>删除备份策略
1. 单击桌面图标启动 StorSimple Snapshot Manager。
2. 在“**作用域**”窗格中，单击“**备份策略**”节点。 所有备份策略将显示在“**结果**”窗格中。
3. 右键单击要删除的备份策略，然后单击“**删除**”。
4. 在出现的确认消息中单击“**是**”。

    ![删除备份策略确认](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Delete_BU_policy.png)

## <a name="next-steps"></a>后续步骤
* 了解如何[使用 StorSimple Snapshot Manager 管理你的 StorSimple 解决方案](storsimple-snapshot-manager-admin.md)。
* 了解如何[使用 StorSimple Snapshot Manager 查看和管理备份作业](storsimple-snapshot-manager-manage-backup-jobs.md)。



<!--HONumber=Nov16_HO3-->


