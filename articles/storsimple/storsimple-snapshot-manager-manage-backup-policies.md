---
title: StorSimple Snapshot Manager 备份策略 | Microsoft Docs
description: 介绍如何使用 StorSimple Snapshot Manager MMC 管理单元创建和管理控制计划备份的备份策略。
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 04415d0b-42f0-4737-8afa-257fb2dbe5d0
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 745aeb9844f1abad075d34b34735563200ea618b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90054936"
---
# <a name="use-storsimple-snapshot-manager-to-create-and-manage-backup-policies"></a>使用 StorSimple Snapshot Manager 创建和管理备份策略
## <a name="overview"></a>概述
备份策略创建在本地或云中备份卷数据的计划。 当创建备份策略时，还可以指定保留策略。  (最多可以保留64个快照。 ) 有关备份策略的详细信息，请参阅 StorSimple 8000 系列中的 [备份类型](storsimple-what-is-snapshot-manager.md#backup-types-and-backup-policies) [：混合云解决方案](storsimple-overview.md)。

本教程介绍了如何完成以下操作：

* 创建备份策略
* 编辑备份策略
* 删除备份策略

## <a name="create-a-backup-policy"></a>创建备份策略
使用以下过程创建新的备份策略。

#### <a name="to-create-a-backup-policy"></a>创建备份策略
1. 单击桌面图标启动 StorSimple Snapshot Manager。
2. 在 **“作用域”** 窗格中，右键单击 **“备份策略”**，然后单击 **“创建备份策略”**。

    ![创建备份策略](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_BU_policy.png)

    此时会显示 **“创建策略”** 对话框。

    ![创建策略 -“常规”选项卡](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_general.png)
3. 在“常规”**** 选项卡上填写以下信息：

   1. 在 **“名称”** 文本框中，键入策略名称。
   2. 在 **“卷组”** 文本框中，键入与策略关联的卷组的名称。
   3. 选择 **“本地快照”** 或 **“云快照”**。
   4. 选择要保留的快照数。 如果选择 **“全部”**，则会保留 64 张快照（最大数目）。
4. 单击 **“计划”** 选项卡。

    ![创建策略 -“计划”选项卡](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_schedule.png)
5. 在“计划”**** 选项卡上填写以下信息：

   1. 单击 **“启用”** 复选框计划下一个备份。
   2. 在 **“设置”** 下选择 **“一次性”**、**“每日”**、**“每周”** 或 **“每月”**。
   3. 在 **“开始”** 文本框中，单击日历图标，然后选择一个开始日期。
   4. 在 **“高级设置”** 下，你可以设置可选的重复计划和结束日期。
   5. 单击" **确定**"。

创建备份策略后，**“结果”** 窗格中会显示以下信息：

* **名称** – 备份策略的名称。
* **类型** – 本地快照或云快照。
* **卷组** – 与策略关联的卷组。
* **保留数目** – 保留的快照数目，最多为 64 张。
* **创建日期** – 创建此策略的日期。
* **已启用** – 该策略当前是否生效：**True** 表示已生效，**False** 表示未生效。

## <a name="edit-a-backup-policy"></a>编辑备份策略
使用以下过程编辑现有的备份策略。

#### <a name="to-edit-a-backup-policy"></a>编辑备份策略
1. 单击桌面图标启动 StorSimple Snapshot Manager。
2. 在 **“作用域”** 窗格中，单击 **“备份策略”** 节点。 此时会在 **“结果”** 窗格中显示所有备份策略。
3. 右键单击要编辑的策略，然后单击“编辑”****。

    ![编辑备份策略](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Edit_BU_policy.png)
4. 出现 **“创建策略”** 窗口时，请输入要做的更改，然后单击 **“确定”**。

## <a name="delete-a-backup-policy"></a>删除备份策略
使用以下过程删除备份策略。

#### <a name="to-delete-a-backup-policy"></a>删除备份策略
1. 单击桌面图标启动 StorSimple Snapshot Manager。
2. 在 **“作用域”** 窗格中，单击 **“备份策略”** 节点。 此时会在 **“结果”** 窗格中显示所有备份策略。
3. 右键单击要删除的备份策略，然后单击 **“删除”**。
4. 出现确认消息时，单击 **“是”**。

    ![删除备份策略确认](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Delete_BU_policy.png)

## <a name="next-steps"></a>后续步骤
* 了解如何[使用 StorSimple Snapshot Manager 管理 StorSimple 解决方案](storsimple-snapshot-manager-admin.md)。
* 了解如何[使用 StorSimple Snapshot Manager 查看和管理备份作业](storsimple-snapshot-manager-manage-backup-jobs.md)。
