---
title: "管理 StorSimple 虚拟阵列共享 | Microsoft 文档"
description: "介绍 StorSimple Device Manager 并说明如何使用它在 StorSimple 虚拟阵列上管理共享。"
services: storsimple
documentationcenter: 
author: manuaery
manager: syadav
editor: 
ms.assetid: 0a799c83-fde5-4f3f-af0e-67535d1882b6
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.translationtype: Human Translation
ms.sourcegitcommit: 19177fe13db2aefcef7fcf37ea8fef2fa99c728f
ms.openlocfilehash: e5c62689de36baa175001f5f4f70d87568876ef0
ms.contentlocale: zh-cn
ms.lasthandoff: 01/26/2017

---
# <a name="use-the-storsimple-device-manager-service-to-manage-shares-on-the-storsimple-virtual-array"></a>使用 StorSimple Device Manager 服务在 StorSimple 虚拟阵列上管理共享

## <a name="overview"></a>概述

本教程介绍如何使用 StorSimple Device Manager 服务在 StorSimple 虚拟阵列上创建和管理共享。

StorSimple Device Manager 服务是 Azure 门户中的一个扩展，可让用户通过单个 Web 界面管理 StorSimple 解决方案。 除了管理共享和卷外，还可以使用 StorSimple Device Manager 服务查看和管理设备、查看警报、管理备份策略和管理备份目录。

## <a name="share-types"></a>共享类型

StorSimple 共享可以是以下类型：

* **本地固定**：这些共享中的数据始终保留在阵列中，不会溢出到云。
* **分层**：这些共享中的数据可溢出到云。 创建分层共享时，大约 10% 的空间预配在本地层，90% 的空间预配在云中。 例如，如果预配 1 TB 的共享，则当数据分层时，100 GB 会驻留在本地空间，900 GB 会在云中使用。 反过来说，这意味着，如果用光了设备上的所有本地空间，则无法预配分层共享（因为本地层上所需的 10% 的空间不可用）。

### <a name="provisioned-capacity"></a>预配的容量

请参阅下表了解每种共享类型的最大预配容量。

| **限制标识符** | **限制** |
| --- | --- |
| 分层共享的大小下限 |500 GB |
| 分层共享的大小上限 |20 TB |
| 本地固定共享的大小下限 |50 GB |
| 本地固定共享的大小上限 |2 TB |

## <a name="the-shares-blade"></a>“共享”边栏选项卡

StorSimple 服务摘要边栏选项卡上的“共享”菜单显示给定 StorSimple 阵列上的存储共享列表，并允许用户管理它们。

![“共享”边栏选项卡](./media/storsimple-virtual-array-manage-shares/shares-blade.png)

共享包含一系列属性：

* **共享名称** - 必须唯一且有助于识别共享的描述性名称。
* **状态** – 可以是“联机”或“脱机”。 如果共享处于脱机状态，则该共享的用户将无法访问它。
* **类型** - 指示共享是**分层**共享（默认值）还是**本地固定**共享。
* **容量** - 指定相对于可以在共享中存储的数据总量，已使用的数据量。
* **说明** - 一个可选设置，可帮助描述共享。
* **权限** - 对共享的 NTFS 权限，可以通过 Windows 资源管理器进行管理。
* **备份** - 就 StorSimple 虚拟阵列而言，已自动为所有共享启用备份功能。

![共享详细信息](./media/storsimple-virtual-array-manage-shares/share-details.png)

使用本教程中的说明执行以下任务：

* 添加共享
* 修改共享
* 使共享脱机
* 删除共享

## <a name="add-a-share"></a>添加共享

1. 在“StorSimple 服务摘要”边栏选项卡上，单击命令栏中的“+ 添加共享”。 此时将打开“添加共享”边栏选项卡。

    ![添加共享](./media/storsimple-virtual-array-manage-shares/add-share.png)

2. 在“添加共享”边栏选项卡中，执行以下操作：
   
    1. 在“共享名称”字段中，输入共享的唯一名称。 该名称必须是包含 3 到 127 个字符的字符串。

    2. 共享的可选**说明**。 可以根据说明确定共享所有者。

    3. 在“类型”下拉列表中，指定要创建**分层**共享还是要创建**本地固定**共享。 对于需要本地保证、低延迟和高性能的工作负荷，请选择“本地固定共享”。 对于所有其他数据，请选择“分层”共享。

    4. 在“容量”字段中，指定共享的大小。 分层共享的大小必须介于 500 GB 到 20 TB 之间，本地固定共享的大小必须介于 50 GB 到 2 TB 之间。

    5. 在“将默认完全权限设置给”字段中，将权限分配给要访问此共享的用户或组。 以 _john@contoso.com_ 格式指定用户或用户组的名称。 若要启用访问这些共享所需的管理员权限，建议使用用户组（而非单个用户）。 在此处分配权限以后，即可使用文件资源管理器修改这些权限。
3. 完成配置共享后，单击“创建”。 此时将使用指定的设置创建共享，你将看到一条通知。 默认情况下，将为共享启用备份功能。
4. 若要确认已成功创建共享，请转到“共享”边栏选项卡。 应看到列出该共享。
   
    ![共享创建成功](./media/storsimple-virtual-array-manage-shares/share-success.png)

## <a name="modify-a-share"></a>修改共享

当需要更改共享的说明时，请修改共享。 创建共享后，将无法修改共享的任何其他属性。

#### <a name="to-modify-a-share"></a>修改共享

1. 从“StorSimple 服务摘要”边栏选项卡上的“共享”设置中，选择要修改的共享所驻留的虚拟阵列。
2. **选择**该共享以查看当前说明并对其进行修改。
3. 单击命令栏上的“保存”以保存所做的更改。 此时将应用指定的设置，你将看到一条通知。
   
    ![ 编辑共享](./media/storsimple-virtual-array-manage-shares/share-edit.png)

## <a name="take-a-share-offline"></a>使共享脱机

想要修改或删除某个共享时，可能需要将它脱机。 脱机的共享不可用于读写访问。 需要同时在主机和设备上使共享脱机。

#### <a name="to-take-a-share-offline"></a>使共享脱机

1. 使相关的卷脱机之前，请确保它未在使用。
2. 通过执行以下步骤使阵列上的共享脱机：
   
    1. 从“StorSimple 服务摘要”边栏选项卡上的“共享”设置中，选择要使其脱机的共享所驻留的虚拟阵列。

    2. **选择**该共享，单击“...”（或者在此行中右键单击），然后从上下文菜单中选择“脱机”。
     
        ![脱机共享](./media/storsimple-virtual-array-manage-shares/shares-offline.png)

    3. 在“脱机”边栏选项卡中查看信息并确认你接受该操作。 单击“脱机”使该共享脱机。 你将看到“正在进行操作”的通知。

    4. 若要确认已成功使共享脱机，请转到“共享”边栏选项卡。 应看到共享的状态为“脱机”。

## <a name="delete-a-share"></a>删除共享

> [!IMPORTANT]
> 仅当共享已脱机时，才可以删除该共享。


若要删除共享，请完成以下步骤。

#### <a name="to-delete-a-share"></a>删除共享

1. 从“StorSimple 服务摘要”边栏选项卡上的“共享”设置中，选择要删除的共享所驻留的虚拟阵列。
2. **选择**该共享，单击“...”（或者在此行中右键单击），然后从上下文菜单中选择“删除”。
   
    ![删除共享](./media/storsimple-virtual-array-manage-shares/share-delete.png)
3. 检查要删除的共享的状态。 如果要删除的共享未处于脱机状态，请先使其脱机。 请按照[使共享脱机](#take-a-share-offline)中的步骤操作。
4. 当系统提示在“删除”边栏选项卡中进行确认时，接受确认，然后单击“删除”。 此时将删除该共享，“共享”边栏选项卡将显示虚拟阵列中共享的更新列表。

## <a name="next-steps"></a>后续步骤
了解如何[克隆 StorSimple 共享](storsimple-virtual-array-clone.md)。


