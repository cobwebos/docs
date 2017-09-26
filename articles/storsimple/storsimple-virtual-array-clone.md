---
title: "克隆 StorSimple 虚拟阵列备份 | Microsoft 文档"
description: "了解如何从 StorSimple 虚拟阵列克隆备份和恢复文件。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: af6e979c-55e3-477c-b53e-a76a697f80c9
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2016
ms.author: alkohli
ms.translationtype: Human Translation
ms.sourcegitcommit: c78eda33a64d630ba18aba6a174db372eb41dde9
ms.openlocfilehash: 768c9a1c906999f4690c9c8f7d075743ab1678ff
ms.contentlocale: zh-cn
ms.lasthandoff: 01/26/2017

---
# <a name="clone-from-a-backup-of-your-storsimple-virtual-array"></a>从 StorSimple 虚拟阵列的备份克隆

## <a name="overview"></a>概述

本文分步介绍如何克隆 Microsoft Azure StorSimple 虚拟阵列上的共享或卷的备份集。 使用克隆的备份恢复已删除或已丢失的文件。 本文还提供了在配置为文件服务器的 StorSimple 虚拟阵列上执行项目级恢复的详细步骤。

## <a name="clone-shares-from-a-backup-set"></a>从备份集克隆共享

**尝试克隆共享之前，请确保设备上有足够空间来完成此操作。** 若要从备份克隆，请在 [Azure 门户](https://portal.azure.com/)中执行以下步骤。

#### <a name="to-clone-a-share"></a>克隆共享

1. 浏览到“设备”边栏选项卡。 选择并单击你的设备，然后单击“共享”。 选择要克隆的共享，右键单击该共享以调用上下文菜单。 选择“克隆”。
   
   ![克隆备份](./media/storsimple-virtual-array-clone/cloneshare1.png)
2. 在“克隆”边栏选项卡中，单击“备份”>“选择”，然后执行以下操作： 
   
   a.    基于时间范围在此设备上筛选备份。 可以从“过去 7 天”、“过去 30 天”和“过去一年”中进行选择。
   
   b.保留“数据库类型”设置，即设置为“共享”。    在显示的已筛选备份列表中，选择要从中克隆的备份。
   
   c.    单击 **“确定”**。
   
   ![克隆备份](./media/storsimple-virtual-array-clone/cloneshare3.png)
3. 在“克隆”边栏选项卡中，单击“目标设置”，然后执行以下操作：
   
   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。    提供共享名。 共享名必须包含 3 到 127 个字符。
   
   b.保留“数据库类型”设置，即设置为“共享”。    （可选）提供克隆共享的说明。
   
   c.    不能更改要还原到的共享的类型。 分层共享克隆为分层共享，本地固定共享克隆为本地固定共享。
   
   d.单击“下一步”。    容量设置为等于要从中克隆的共享的大小。
   
   e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。    为此共享分配管理员。 克隆完成后，能够通过文件资源管理器修改共享属性。
   
   f.    单击“确定”。
   
   ![克隆备份](./media/storsimple-virtual-array-clone/cloneshare6.png)

4. 单击“克隆”启动克隆作业。 作业完成后，克隆操作将启动，并通知你。 若要监视克隆进度，请转到“作业”边栏选项卡，然后单击要查看作业详细信息的作业。
5. 成功创建克隆后，导航回设备上的“共享”边栏选项卡。
6. 现在可在设备上的共享列表中查看新克隆的共享。 分层共享克隆为分层共享，本地固定共享克隆为本地固定共享。
   
   ![克隆备份](./media/storsimple-virtual-array-clone/cloneshare10.png)

## <a name="clone-volumes-from-a-backup-set"></a>从备份集克隆卷

若要从备份克隆，必须在 Azure 门户中执行类似于克隆共享时执行的步骤。 克隆操作将备份克隆到同一虚拟设备上的新卷；不能克隆到不同的设备上。

#### <a name="to-clone-a-volume"></a>克隆卷

1. 浏览到“设备”边栏选项卡。 选择并单击你的设备，然后单击“卷”。 选择要克隆的卷，右键单击该卷以调用上下文菜单。 选择“克隆”。
   
   ![克隆卷](./media/storsimple-virtual-array-clone/clonevolume1.png)
2. 在“克隆”边栏选项卡中，单击“备份”，然后执行以下操作： 
   
   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。    基于时间范围在此设备上筛选备份。 可以从“过去 7 天”、“过去 30 天”和“过去一年”中进行选择。 
   
   b.保留“数据库类型”设置，即设置为“共享”。    在显示的已筛选备份列表中，选择要从中克隆的备份。
   
   c.    单击 **“确定”**。
   
   ![克隆备份](./media/storsimple-virtual-array-clone/clonevolume3.png)
3. 在“克隆”边栏选项卡中，单击“目标卷设置”，然后执行以下操作：
   
   a. 将自动填充设备名称。
   
   b.保留“数据库类型”设置，即设置为“共享”。 为**克隆的卷**提供卷名。 卷名必须包含 3 至 127 个字符。
   
   c. 卷类型自动设置为原始卷。 分层卷克隆为分层卷，本地固定卷克隆为本地固定卷。
   
   d.单击“下一步”。 对于“连接的主机”，单击“选择”。
   
   ![克隆备份](./media/storsimple-virtual-array-clone/clonevolume4.png)
4. 在“连接的主机”边栏选项卡中，从现有 ACR 中选择，或添加一个新 ACR。 若要添加新 ACR，需要提供 ACR 名称和主机 IQN。 单击“选择”。
   
   ![克隆备份](./media/storsimple-virtual-array-clone/clonevolume5.png)
5. 单击“克隆”启动克隆作业。
   
   ![克隆备份](./media/storsimple-virtual-array-clone/clonevolume6.png)  
6. 创建克隆作业后，将开始克隆。 创建克隆后，它将显示在设备上的“卷”边栏选项卡中。 请注意，分层卷将克隆为分层卷，本地固定卷将克隆为本地固定卷。
   
   ![克隆备份](./media/storsimple-virtual-array-clone/clonevolume8.png)
7. 当卷在卷列表中显示为联机时，则表示该卷可供使用。 在 iSCSI 发起程序主机上，在 iSCSI 发起程序属性窗口中刷新目标的列表。 包含已克隆卷名的新目标在状态列下应显示为“非活动”状态。
8. 选择目标并单击“连接”。 发起程序连接到目标后，状态应更改为“已连接”。
9. 在“磁盘管理”窗口中，将显示已装载的卷，如以下插图所示。 右键单击“发现的卷”（单击磁盘名称），然后单击“ **联机**”。

> [!IMPORTANT]
> 尝试从备份集克隆卷或共享时，如果克隆作业失败，可能仍然会在门户中创建目标卷或共享。 请务必删除门户中的此目标卷或共享，以在最大程度上减少此元素将引发的任何问题。
> 
> 

## <a name="item-level-recovery-ilr"></a>项目级恢复 (ILR)

此版本在配置为文件服务器的 StorSimple 虚拟阵列上引入了项目级恢复 (ILR)。 该功能允许从 StorSimple 设备上的所有共享的云备份对文件和文件夹进行粒度恢复。 用户可使用自助服务模型从最近的备份中检索已删除的文件。

每个共享都有包含最新备份的 *.backups* 文件夹。 用户可导航到所需的备份，从备份中复制相关的文件和文件夹并进行还原。 此功能使用户无需呼叫管理员便可从备份中还原文件。

1. 执行 ILR 时，可通过文件资源管理器查看备份。 单击想要为查看其备份的特定共享。 将在存储所有备份的共享下看到创建的 *.backups* 文件夹。 展开 *.backups* 文件夹以查看备份。 该文件夹将显示整个备份层次结构的分解视图。 此视图是按需创建的，并且通常只需几秒钟的时间。
   
   最后五个备份以这种方式显示，并且可用于执行项目级恢复。 最近的五个备份包括默认计划的备份和手动备份。
   
   * **计划备份**的名称为 &lt;设备名&gt;DailySchedule-YYYYMMDD-HHMMSS-UTC。
   * **手动备份** 的名称为 Ad-hoc-YYYYMMDD-HHMMSS-UTC。
     
     ![](./media/storsimple-virtual-array-clone/image14.png)

2. 识别包含最新版本的已删除文件的备份。 尽管文件夹名称包含上述每种情况中的 UTC 时间戳，但该文件夹的创建时间是备份开始时的实际设备时间。 使用文件夹时间戳查找和标识备份。

3. 在上一步中标识的备份中查找想要还原的文件夹或文件。 请注意，用户只能查看对其具有权限的文件或文件夹。 如果无法访问某些文件或文件夹，请联系共享管理员。 管理员可以使用文件资源管理器编辑共享权限，并授予你访问特定文件或文件夹的权限。 建议的最佳做法是将共享管理员设置为用户组而非单个用户。

4. 将文件或文件夹复制到 StorSimple 文件服务器上的相应共享。

## <a name="next-steps"></a>后续步骤

了解有关如何[使用本地 Web UI 管理 StorSimple Virtual Array](storsimple-ova-web-ui-admin.md) 的详细信息。


