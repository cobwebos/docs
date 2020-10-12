---
title: StorSimple Snapshot Manager 备份目录 | Microsoft Docs
description: 介绍如何使用 StorSimple Snapshot Manager MMC 管理单元查看和管理备份目录。
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 6abdbfd2-22ce-45a5-aa15-38fae4c8f4ec
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 2efc35e65ca1db2b5241e1d3b2798e068880c87e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90054987"
---
# <a name="use-storsimple-snapshot-manager-to-manage-the-backup-catalog"></a>使用 StorSimple Snapshot Manager 管理备份目录

## <a name="overview"></a>概述
StorSimple Snapshot Manager 的主要功能是允许以快照的形式创建 StorSimple 卷的应用程序一致备份副本。 然后，这些快照将在名为“备份目录”的 XML 文件中列出。** 备份目录先按卷组，然后按本地快照或云快照组织快照。

本教程介绍如何使用“备份目录”**** 节点来完成以下任务：

* 还原卷
* 克隆卷或卷组
* 删除备份
* 恢复文件
* 还原 Storsimple Snapshot Manager 数据库

你可以展开 **“作用域”** 窗格中的 **“备份目录”** 节点，然后再展开卷组，通过这种方式查看备份目录。

* 如果你单击卷组名称，则 **“结果”** 窗格将显示卷组可用的本地快照和云快照的数量。 
* 如果你单击 **“本地快照”** 或 **“云快照”**，则 **“结果”** 窗格显示有关每个备份快照的以下信息（具体取决于你的 **“视图”** 设置）：
  
  * **名称** – 创建快照的时间。
  * **类型** – 此快照是本地快照还是云快照。
  * **所有者** – 内容所有者。 
  * **可用** – 快照当前是否可用。 **True** 指示快照可用，并且可以还原；**False** 指示快照不再可用。 
  * **已导入** – 备份是否已导入。 “True”**** 指示在 StorSimple Snapshot Manager 中配置该设备时，已从 StorSimple Device Manager 服务导入备份；“False”**** 指示备份未导入，但已由 StorSimple Snapshot Manager 创建。 （可以轻松地识别导入的卷组，因为这些卷组添加了后缀，后缀用于标识导入的卷组来自哪个设备。）
    
    ![备份目录](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Backup_catalog.png)
* 如果你展开 **“本地快照”** 或 **“云快照”**，然后单击某个快照名称，则 **“结果”** 窗格将显示有关你选择的快照的以下信息：
  
  * **名称** – 用驱动器盘符标识的卷。 
  * **本地名称** – 驱动器的本地名称（如果有）。 
  * 设备**** – 卷驻留的设备名称。 
  * **可用** – 快照当前是否可用。 **True** 指示快照可用，并且可以还原；**False** 指示快照不再可用。 

## <a name="restore-a-volume"></a>还原卷
使用以下过程从备份中还原卷。

#### <a name="prerequisites"></a>必备条件
如果尚未进行操作，则创建一个卷和卷组，并删除该卷。 默认情况下，StorSimple Snapshot Manager 在允许删除卷之前会对其进行备份。 如果无意中删除卷或出于某种原因需要对数据进行恢复，则此预防措施可以避免数据丢失。 

在创建预防性备份时，StorSimple Snapshot Manager 显示以下消息。

![自动快照消息](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Automatic_snap.png) 

> [!IMPORTANT]
> 如果该卷是卷组的一部分，则无法将其删除。 删除选项不可用。 <br>
> 
> 

#### <a name="to-restore-a-volume"></a>还原卷
1. 单击桌面图标启动 StorSimple Snapshot Manager。 
2. 在 **“作用域”** 窗格中，展开 **“备份目录”** 节点，展开卷组，然后单击 **“本地快照”** 或 **“云快照”**。 此时会在 **“结果”** 窗格中显示备份快照列表。
3. 找到要还原的备份，右键单击该备份，然后单击 **“还原”**。
   
    ![还原备份目录](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_BU_catalog.png) 
4. 在“确认”页上查看详细信息，键入“确认”****，并单击“确定”****。 StorSimple Snapshot Manager 使用备份来还原卷。
   
    ![还原确认消息](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_volume_msg.png) 
5. 可以在还原操作运行时对其进行监视。 在 **“作用域”** 窗格中，展开 **“作业”** 节点，然后单击 **“正在运行”**。 此时会在 **“结果”** 窗格中显示作业详细信息。 还原作业完成后，作业详细信息会传输到“过去 24 小时”**** 列表。

## <a name="clone-a-volume-or-volume-group"></a>克隆卷或卷组
使用以下过程创建卷或卷组的副本（克隆）。

#### <a name="to-clone-a-volume-or-volume-group"></a>克隆卷或卷组
1. 单击桌面图标启动 StorSimple Snapshot Manager。
2. 在 **“作用域”** 窗格中，展开 **“备份目录”** 节点，展开卷组，然后单击 **“云快照”**。 此时会在 **“结果”** 窗格中显示备份列表。
3. 找到要克隆的卷或卷组，右键单击该卷或卷组的名称，然后单击 **“克隆”**。 此时将显示 **“克隆云快照”** 对话框。
   
    ![克隆云快照](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 
4. 按以下步骤完成 **“克隆云快照”** 对话框操作： 
   
   1. 在 **“名称”** 文本框中，键入克隆的卷的名称。 该名称将显示在 **“卷”** 节点中。 
   2. （可选）选择“**驱动器**”，并从下拉列表中选择驱动器号。
   3.  (可选) 选择 " **文件夹 (NTFS) **"，然后键入文件夹路径或单击 "浏览" 并选择该文件夹的位置。 
   4. 单击“创建”。
5. 完成克隆过程后，必须初始化克隆的卷。 启动服务器管理器，并启动磁盘管理。 有关详细说明，请参阅[装载卷](storsimple-snapshot-manager-manage-volumes.md#mount-volumes)。 完成初始化后，卷将列在 **“作用域”** 窗格中的 **“卷”** 节点下。  如果看不到列出的卷，刷新卷列表（右键单击“**卷**”节点，并单击“**刷新**”）。

## <a name="delete-a-backup"></a>删除备份
使用以下过程从备份目录中删除快照。 

> [!NOTE]
> 删除快照会删除与快照关联的备份数据。 但是，从云中清除数据的过程可能需要一些时间。<br>


#### <a name="to-delete-a-backup"></a>删除备份
1. 单击桌面图标启动 StorSimple Snapshot Manager。
2. 在 **“作用域”** 窗格中，展开 **“备份目录”** 节点，展开卷组，然后单击 **“本地快照”** 或 **“云快照”**。 此时会在 **“结果”** 窗格中显示快照列表。
3. 右键单击要删除的快照，然后单击“删除”****。
4. 出现确认消息时，单击 **“确定”**。

## <a name="recover-a-file"></a>恢复文件
如果文件被意外从卷中删除，可以通过以下方法恢复文件：检索删除日期之前的快照，使用该快照创建克隆卷，并将该文件从克隆卷复制到原来的卷。

#### <a name="prerequisites"></a>必备条件
在开始之前，请确保具有卷组的最新备份。 然后，删除存储在该卷组中一个卷上的某个文件。 最后，使用以下步骤从备份中还原已删除的文件。 

#### <a name="to-recover-a-deleted-file"></a>恢复已删除的文件
1. 单击桌面上的 StorSimple Snapshot Manager 图标。 StorSimple Snapshot Manager 控制台窗口随即出现。 
2. 在“**作用域**”窗格中，展开“**备份目录**”节点，并浏览到包含已删除文件的快照。 通常情况下，应选择在删除前不久创建的快照。
3. 找到要克隆的卷，右键单击该卷，然后单击 **“克隆”**。 此时将显示 **“克隆云快照”** 对话框。
   
    ![克隆云快照](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 
4. 按以下步骤完成 **“克隆云快照”** 对话框操作： 
   
   1. 在 **“名称”** 文本框中，键入克隆的卷的名称。 该名称将显示在 **“卷”** 节点中。 
   2.  (可选) 选择 " **驱动器**"，然后从下拉列表中选择驱动器号。 
   3. （可选） 选择“文件夹 (NTFS)”****，键入文件夹路径或单击“浏览”**** 并选择该文件夹的位置。 
   4. 单击“创建”。 
5. 完成克隆过程后，必须初始化克隆的卷。 启动服务器管理器，并启动磁盘管理。 有关详细说明，请参阅[装载卷](storsimple-snapshot-manager-manage-volumes.md#mount-volumes)。 完成初始化后，卷将列在 **“作用域”** 窗格中的 **“卷”** 节点下。 
   
     如果看不到列出的卷，刷新卷列表（右键单击“**卷**”节点，并单击“**刷新**”）。
6. 打开包含克隆卷的 NTFS 文件夹，展开 **“卷”** 节点，然后打开克隆卷。 找到要恢复的文件，并将其复制到主卷。
7. 还原文件之后，可以删除包含克隆卷的 NTFS 文件夹。

## <a name="restore-the-storsimple-snapshot-manager-database"></a>还原 StorSimple Snapshot Manager 数据库
应定期在主计算机上备份 StorSimple Snapshot Manager 数据库。 如果发生灾难或主计算机由于任何原因出现故障，可以从备份中进行还原。 创建数据库备份是一个手动过程。

#### <a name="to-back-up-and-restore-the-database"></a>备份和还原数据库
1. 停止 Microsoft StorSimple Management Service：
   
   1. 启动服务器管理器。
   2. 在服务器管理器仪表板的“工具”**** 菜单上，选择“服务”****。
   3. 在“服务”**** 窗口中，选择“Microsoft StorSimple Management Service”****。
   4. 在右窗格的 **“Microsoft StorSimple 管理服务”** 下，单击 **“停止服务”**。
2. 在主计算机上，浏览到 C:\ProgramData\Microsoft\StorSimple\BACatalog。 
   
   > [!NOTE]
   > ProgramData 是隐藏的文件夹。
   > 
   > 
3. 找到目录 XML 文件，复制文件，并将副本保存在安全的位置或者云中。 如果主机发生故障，可以使用此备份文件来帮助恢复 StorSimple Snapshot Manager 中创建的备份策略。
   
    ![Azure StorSimple 备份目录文件](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_bacatalog.png)
4. 重新启动 Microsoft StorSimple Management Service： 
   
   1. 在服务器管理器仪表板的“工具”**** 菜单上，选择“服务”****。
   2. 在“服务”**** 窗口中，选择“Microsoft StorSimple Management Service”****。
   3. 在右窗格的 **“Microsoft StorSimple 管理服务”** 下，单击 **“重新启动服务”**。
5. 在主计算机上，浏览到 C:\ProgramData\Microsoft\StorSimple\BACatalog。 
6. 删除目录 XML 文件，并将其替换为创建的备份版本。 
7. 单击桌面 StorSimple Snapshot Manager 图标，以启动 StorSimple Snapshot Manager。 

## <a name="next-steps"></a>后续步骤
* 了解有关[使用 StorSimple Snapshot Manager 管理 StorSimple 解决方案](storsimple-snapshot-manager-admin.md)的详细信息。
* 了解有关 [StorSimple Snapshot Manager任务和工作流](storsimple-snapshot-manager-admin.md#storsimple-snapshot-manager-tasks-and-workflows)的详细信息。

