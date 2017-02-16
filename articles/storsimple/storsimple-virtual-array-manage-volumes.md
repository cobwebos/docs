---
title: "管理 StorSimple 虚拟阵列上的卷 | Microsoft 文档"
description: "介绍 StorSimple Device Manager 并说明如何使用它在 StorSimple 虚拟阵列上管理卷。"
services: storsimple
documentationcenter: 
author: manuaery
manager: syadav
editor: 
ms.assetid: caa6a26b-b7ba-4a05-b092-1a79450225cf
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
translationtype: Human Translation
ms.sourcegitcommit: c78eda33a64d630ba18aba6a174db372eb41dde9
ms.openlocfilehash: a507bf1866952cb79fa6334fed80c88cd207cd0a

---
# <a name="use-storsimple-device-manager-service-to-manage-volumes-on-the-storsimple-virtual-array"></a>使用 StorSimple Device Manager 服务在 StorSimple 虚拟阵列上管理卷

## <a name="overview"></a>概述

本教程介绍如何使用 StorSimple Device Manager 服务在 StorSimple 虚拟阵列上创建和管理卷。

StorSimple Device Manager 服务是 Azure 门户中的一个扩展，可让用户通过单个 Web 界面管理 StorSimple 解决方案。 除了管理共享和卷外，还可以使用 StorSimple Device Manager 服务查看和管理设备、查看警报、查看和管理备份策略和备份目录。

## <a name="volume-types"></a>卷类型

StorSimple 卷可以是：

* **本地固定**：这些卷中的数据始终保留在阵列中，不会溢出到云。
* **分层**：这些卷中的数据可溢出到云中。 创建分层卷时，大约 10% 的空间预配在本地层，90% 的空间预配在云中。 例如，如果预配 1 TB 的卷，则当数据分层时，100 GB 会驻留在本地空间，900 GB 会在云中使用。 反过来说，这意味着，如果用光了设备上的所有本地空间，则无法预配分层卷（因为本地层上所需的 10% 的空间不可用）。

### <a name="provisioned-capacity"></a>预配的容量
请参阅下表，了解每种卷类型的最大预配容量。

| **限制标识符**                                       | **限制**     |
|------------------------------------------------------------|---------------|
| 分层卷的大小下限                            | 500 GB        |
| 分层卷的大小上限                            | 5 TB          |
| 本地固定卷的大小下限                    | 50 GB         |
| 本地固定卷的大小上限                    | 500 GB        |

## <a name="the-volumes-blade"></a>“卷”边栏选项卡
StorSimple 服务摘要边栏选项卡上的“卷”菜单显示给定 StorSimple 阵列上的存储卷列表，并允许用户管理它们。

![“卷”边栏选项卡](./media/storsimple-virtual-array-manage-volumes/volumes-blade.png)

卷包括一系列属性：

* **卷名称** – 必须保持唯一且有助于识别卷的描述性名称。
* **状态** – 可以是“联机”或“脱机”。 如果某个卷已脱机，允许访问该卷的发起程序（服务器）将看不到该卷。
* **类型** – 指示卷是**分层**卷（默认设置）还是**本地固定**卷。
* **容量** - 指定相对于可以由发起程序（服务器）存储的数据总量，已使用的数据量。
* **备份** - 就 StorSimple 虚拟阵列而言，已自动为所有卷启用备份功能。
* **已连接主机** - 指定允许访问此卷的发起程序（服务器）。

![卷详细信息](./media/storsimple-virtual-array-manage-volumes/volume-details.png)

使用本教程中的说明执行以下任务：

* 添加卷
* 修改卷
* 使卷脱机
* 删除卷

## <a name="add-a-volume"></a>添加卷

1. 在“StorSimple 服务摘要”边栏选项卡上，单击命令栏中的“+ 添加卷”。 此时将打开“添加卷”边栏选项卡。
   
    ![添加卷](./media/storsimple-virtual-array-manage-volumes/add-volume.png)
2. 在“添加卷”边栏选项卡中，执行以下操作：
   
   * 在“卷名”字段中，输入卷的唯一名称。 该名称必须是包含 3 到 127 个字符的字符串。
   * 在“类型”下拉列表中，指定要创建**分层**卷还是要创建**本地固定**卷。 对于需要本地保证、低延迟和高性能的工作负荷，请选择“本地固定卷”。 对于所有其他数据，请选择“分层卷”。
   * 在“容量”字段中，指定卷的大小。 分层卷的大小必须介于 500 GB 到 5 TB 之间，本地固定卷的大小必须介于 50 GB 到 500 GB 之间。
   * * 单击“已连接主机”，选择要连接到此卷的 iSCSI 发起程序所对应的访问控制记录 (ACR)，然后单击“选择”。
3. 若要添加新连接的主机，请单击“新增”，输入主机的名称及其 iSCSI 限定名称 (IQN)，然后单击“添加”。
   
    ![添加卷](./media/storsimple-virtual-array-manage-volumes/volume-add-acr.png)
4. 完成配置卷后，单击“创建”。 此时将使用指定的设置创建卷，成功创建后，将会显示一条通知。 默认情况下，将为卷启用备份功能。
5. 若要确认已成功创建卷，请转到“卷”边栏选项卡。 此时会看到列出的卷。
   
    ![卷创建成功](./media/storsimple-virtual-array-manage-volumes/volume-success.png)

## <a name="modify-a-volume"></a>修改卷

需要更改访问卷的主机时，可以修改卷。 一旦创建卷，就不能修改卷的其他属性。

#### <a name="to-modify-a-volume"></a>修改卷

1. 从“StorSimple 服务摘要”边栏选项卡上的“卷”设置中，选择要修改的卷所驻留的虚拟阵列。
2. **选择**卷，然后单击“已连接主机”，以便查看当前连接的主机并将其修改为其他服务器。
   
    ![编辑卷](./media/storsimple-virtual-array-manage-volumes/volume-edit-acr.png)
3. 单击命令栏上的“保存”以保存所做的更改。 此时将应用指定的设置，并会显示一条通知。

## <a name="take-a-volume-offline"></a>使卷脱机

想要修改或删除某个卷时，可能需要将它脱机。 脱机的卷不可用于读写访问。 需要同时在主机和设备上使卷脱机。

#### <a name="to-take-a-volume-offline"></a>使卷脱机

1. 使卷脱机之前，请确保相关的卷未被使用。
2. 先在主机上使卷脱机。 这样可以消除任何造成卷上数据损坏的潜在风险。 有关具体的步骤，请参阅适用于你的主机操作系统的说明。
3. 主机上的卷脱机后，执行以下步骤在阵列上使卷脱机：
   
   * 从“StorSimple 服务摘要”边栏选项卡上的“卷”设置中，选择要脱机的卷所驻留的虚拟阵列。
   * **选择**该卷，单击“...”（或者在此行中右键单击），然后从上下文菜单中选择“脱机”。
     
        ![脱机卷](./media/storsimple-virtual-array-manage-volumes/volume-offline.png)
   * 在“脱机”边栏选项卡中查看信息并确认接受该操作。 单击“脱机”使该卷脱机。 此时会显示“正在进行操作”的通知。
   * 若要确认已成功将卷脱机，请转到“卷”边栏选项卡。 此时会看到卷的状态为“脱机”。
     
       ![脱机卷确认](./media/storsimple-virtual-array-manage-volumes/volume-offline-confirm.png)

## <a name="delete-a-volume"></a>删除卷

> [!IMPORTANT]
> 仅当卷已脱机时，才可以删除该卷。
> 
> 

若要删除卷，请完成以下步骤。

#### <a name="to-delete-a-volume"></a>删除卷

1. 从“StorSimple 服务摘要”边栏选项卡上的“卷”设置中，选择要删除的卷所驻留的虚拟阵列。
2. **选择**该卷，单击“...”（或者在此行中右键单击），然后从上下文菜单中选择“删除”。
   
    ![删除卷](./media/storsimple-virtual-array-manage-volumes/volume-delete.png)
3. 检查要删除的卷的状态。 如果要删除的卷未脱机，请根据[使卷脱机](#take-a-volume-offline)中的步骤使其脱机。
4. 当系统提示在“删除”边栏选项卡中进行确认时，接受确认，然后单击“删除”。 随即会删除该卷，“卷”边栏选项卡将显示虚拟阵列中卷的更新列表。

## <a name="next-steps"></a>后续步骤

了解如何[克隆 StorSimple 卷](storsimple-virtual-array-clone.md)。




<!--HONumber=Jan17_HO4-->


