---
title: "StorSimple Snapshot Manager 卷组 | Microsoft Docs"
description: "介绍如何使用 StorSimple Snapshot Manager MMC 管理单元创建和管理卷组。"
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: 7a232414-6a28-4b81-bd7b-cf61e28b33d7
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: 6067a88cd42d29c3d2f4b74580095424de77561e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="use-storsimple-snapshot-manager-to-create-and-manage-volume-groups"></a>使用 StorSimple Snapshot Manager 创建和管理卷组
## <a name="overview"></a>概述
可以使用“**作用域**”窗格上的“**卷组**”节点将卷分配到卷组，查看有关卷组的信息，计划备份以及编辑卷组。

卷组是用来确保应用程序一致性备份的相关卷的池。 有关详细信息，请参阅[卷和卷组](storsimple-what-is-snapshot-manager.md#volumes-and-volume-groups)以及[与 Windows 卷影复制服务的集成](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service)。

> [!IMPORTANT]
> * 卷组中的所有卷必须来自单个云服务提供商。
> * 配置卷组时，不要在同一卷组混合群集共享卷 (CSV) 和非 CSV。 StorSimple Snapshot Manager 不支持在同一快照中混合 CSV 和非 CSV。

![卷组节点](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Volume_groups.png)

**图 1：StorSimple Snapshot Manager 卷组节点** 

本教程介绍如何使用 StorSimple Snapshot Manager 执行以下操作：

* 查看卷组的相关信息
* 创建卷组
* 备份卷组
* 编辑卷组
* 删除卷组

在“**操作**”窗格中也提供了所有这些操作。

## <a name="view-volume-groups"></a>查看卷组
如果单击“**卷组**”节点，“**结果**”窗格会显示有关每个卷组的以下信息，具体取决于所选择的列。 （“**结果**”窗格中的列是可以配置的。 右键单击“**卷**”节点，选择“**查看**”，并选择“**添加/删除列**”。）

| 结果列 | 说明 |
|:--- |:--- |
| Name |“**名称**”列包含卷组的名称。 |
| 应用程序 |“**应用程序**”列显示在 Windows 主机上当前已安装且正在运行的 VSS 编写器的数目。 |
| 选定 |“**选定**”列显示卷组中包含的卷数。 零 (0) 表示没有任何应用程序与卷组中的卷相关联。 |
| 已导入 |“**已导入**”列显示已导入卷数。 设置为“True”时，此列指示卷组是从 Azure 门户导入的，而不是在 StorSimple Snapshot Manager 中创建的。 |

> [!NOTE]
> StorSimple Snapshot Manager 卷组还会显示在 Azure 门户中的“备份策略”选项卡上。
> 
> 

## <a name="create-a-volume-group"></a>创建卷组
使用以下过程创建卷组。

#### <a name="to-create-a-volume-group"></a>创建卷组
1. 单击桌面图标启动 StorSimple Snapshot Manager。
2. 在“**作用域**”窗格中，右键单击“**卷组**”，并单击“**创建卷组**”。
   
    ![创建卷组](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Create_volume_group.png)
   
    此时会显示“**创建卷组**”对话框。
   
    ![创建卷组对话框](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_CreateVolumeGroup_dialog.png)
3. 输入以下信息：
   
   1. 在“**名称**”框中，为新卷组键入一个唯一的名称。
   2. 在“**应用程序**”框中，选择与添加到卷组的卷相关联的应用程序。
      
       “**应用程序**”框仅列出使用 StorSimple 卷且已为其启用 VSS 编写器的应用程序。 仅当编写器可以识别的所有卷都是 StorSimple 卷时，才启用 VSS 编写器。 如果“应用程序”框为空，则表明没有安装任何使用 Azure StorSimple 卷且具有支持的 VSS 编写器的应用程序。 （目前，Azure StorSimple 支持 Microsoft Exchange 和 SQL Server。）有关 VSS 编写器的详细信息，请参阅[与 Windows 卷影复制服务的集成](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service)。
      
       如果选择一个应用程序，则会自动选择与之关联的所有卷。 反之，如果选择与特定应用程序相关联的卷，则会在“**应用程序**”框中自动选择该应用程序。 
   3. 在“**卷**”框中，选择要添加到卷组的 StorSimple 卷。 
      
      * 可以添加具有单个或多个分区的卷。 （多分区卷可以是动态磁盘或具有多个分区的基本磁盘。）包含多个分区的卷被视为单一单元。 因此，如果仅将其中一个分区添加到卷组，所有其他分区同时也会自动被添加到该卷组。 将一个多分区卷添加到某个卷组后，该多分区卷会继续被视为单一单元。
      * 可以通过不向其分配任何卷创建空卷组。 
      * 不要在同一卷组中混合群集共享卷 (CSV) 和非 CSV。 StorSimple Snapshot Manager 不支持在同一快照中混合 CSV 卷和非 CSV 卷。
4. 单击“**确定**”以保存卷组。

## <a name="back-up-a-volume-group"></a>备份卷组
使用以下过程备份卷组。

#### <a name="to-back-up-a-volume-group"></a>备份卷组
1. 单击桌面图标启动 StorSimple Snapshot Manager。
2. 在“**作用域**”窗格中，展开“**卷组**”节点，右键单击卷组名称，并单击“**执行备份**”。
   
    ![立即备份卷组](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Take_backup.png)
3. 在“**执行备份**”对话框中，选择“**本地快照**”或“**云快照**”，并单击“**创建**”。
   
    ![执行备份对话框](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_TakeBackup_dialog.png)
4. 要确认备份正在运行，请展开“**作业**”节点，再单击“**正在运行**”。 此时会列出备份。
5. 要查看已完成的快照，请展开“**备份目录**”节点，展开卷组名称，并单击“**本地快照**”或“**云快照**”。 如果已成功完成，则会列出备份。

## <a name="edit-a-volume-group"></a>编辑卷组
使用以下过程编辑卷组。

#### <a name="to-edit-a-volume-group"></a>编辑卷组
1. 单击桌面图标启动 StorSimple Snapshot Manager。
2. 在“**作用域**”窗格中，展开“**卷组**”节点，右键单击卷组名称，并单击“**编辑**”。
3. 此时将显示“创建卷组”对话框。 可以更改“**名称**”、“**应用程序**”和“**卷**”条目。
4. 单击“确定”  保存更改。

## <a name="delete-a-volume-group"></a>删除卷组
使用以下过程删除卷组。 

> [!WARNING]
> 这还将删除与卷组关联的所有备份。
> 
> 

#### <a name="to-delete-a-volume-group"></a>删除卷组
1. 单击桌面图标启动 StorSimple Snapshot Manager。
2. 在“**作用域**”窗格中，展开“**卷组**”节点，右键单击卷组名称，并单击“**删除**”。
3. 此时会显示“**删除卷组**”对话框。 在文本框中键入“**确认**”，并单击“**确定**”。
   
    已删除的卷组即会从“**结果**”窗格的列表中消失，而且与该卷组相关联的所有备份也将从备份目录中删除。

## <a name="next-steps"></a>后续步骤
* 了解如何[使用 StorSimple Snapshot Manager 管理 StorSimple 解决方案](storsimple-snapshot-manager-admin.md)。
* 了解如何[使用 StorSimple Snapshot Manager 创建和管理备份策略](storsimple-snapshot-manager-manage-backup-policies.md)。

