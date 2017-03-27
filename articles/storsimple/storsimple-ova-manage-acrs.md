---
title: "管理 StorSimple Virtual Array 的访问控制记录 | Microsoft Docs"
description: "介绍如何管理访问控制记录 (ACR)，以确定哪些主机可以连接到 StorSimple Virtual Array 上的卷。"
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 11252938-5b97-4178-8c37-f58eaa3d00b1
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: f45d8e2b848e930a790e7bf38c2b09fbdaaf2fab
ms.openlocfilehash: 30b6aedd9b4b7913f6862b34582f3ae96c9eae5f
ms.lasthandoff: 03/01/2017


---
# <a name="use-storsimple-manager-to-manage-access-control-records-for-storsimple-virtual-array"></a>使用 StorSimple Manager 管理 StorSimple 虚拟阵列的访问控制记录

## <a name="overview"></a>概述
访问控制记录 (ACR) 允许你指定哪些主机可以连接到 StorSimple Virtual Array（也称为 StorSimple 本地虚拟设备）上的卷。 ACR 设置为特定的卷，并包含主机的 iSCSI 限定名称 (IQN)。 当主机尝试连接到卷时，设备将在与该卷关联的 ACR 中检查 IQN 名称；如果名称匹配，则建立连接。 “配置”页上的**访问控制记录**部分显示包含相应主机 IQN 的所有访问控制记录。

本教程介绍了以下与 ACR 相关的常见任务：

* 获取 IQN
* 添加访问控制记录 
* 编辑访问控制记录 
* 删除访问控制记录 

> [!IMPORTANT]
> * 将 ACR 分配到卷时，请确保不允许多个非群集主机并发访问卷，因为这会损坏卷。 
> * 从卷中删除 ACR 时，请确保相应主机未在访问卷，因为删除会导致读写操作中断。
> 
> 

## <a name="get-the-iqn"></a>获取 IQN
执行以下步骤，获取正在运行 Windows Server 2012 的 Windows 主机的 IQN。

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]

## <a name="add-an-acr"></a>添加 ACR
使用 StorSimple Manager 服务“配置”页添加 ACR。 通常，一个 ACR 关联一个卷。

有关如何将 ACR 与卷关联的信息，请转到[添加卷](storsimple-ova-deploy3-iscsi-setup.md#step-3-add-a-volume)。

> [!IMPORTANT]
> 将 ACR 分配到卷时，请确保不允许多个非群集主机并发访问卷，因为这会损坏卷。
> 
> 

执行以下步骤，添加 ACR。

#### <a name="to-add-an-acr"></a>添加 ACR
1. 在服务登陆页上，选择你的服务、双击服务名称，然后单击“配置”选项卡。
   
    ![配置选项卡](./media/storsimple-ova-manage-acrs/acr1.png)
2. 在**访问控制记录**下的表格列表中，为你的 ACR 提供一个**名称**。
3. 在“iSCSI 发起程序名称”下，提供 Windows 主机的 IQN 名称。 
4. 单击页面底部的“保存”，保存新建的 ACR。 你将看到以下确认消息。
   
    ![确认消息](./media/storsimple-ova-manage-acrs/acr2.png)
5. 单击选中图标  ![选中图标](./media/storsimple-ova-manage-acrs/check-icon.png)。 表格列表随即会更新以反映此添加。

## <a name="edit-an-acr"></a>编辑 ACR
使用 Azure 经典门户中的“配置”页编辑 ACR。 

> [!NOTE]
> 应仅修改当前未在使用的 ACR。 若要编辑与当前正在使用的卷关联的 ACR，应先使该卷脱机。
> 
> 

执行以下步骤，编辑 ACR。

#### <a name="to-edit-an-acr"></a>编辑 ACR
1. 在服务登陆页上，选择你的服务、双击服务名称，然后单击“配置”选项卡。
2. 在访问控制记录的表格列表中，将鼠标悬停在要修改的 ACR 上。
3. 为 ACR 提供新名称和/或 IQN。
4. 单击页面底部的“保存”，保存修改的 ACR。 将显示确认消息。 
5. 单击选中图标  ![选中图标](./media/storsimple-ova-manage-acrs/check-icon.png)。 表格列表随即会更新以反映此项更改。

## <a name="delete-an-access-control-record"></a>删除访问控制记录
使用 Azure 经典门户中的“配置”页删除 ACR。 

> [!NOTE]
> * 应仅删除当前未在使用的 ACR。 若要删除与当前正在使用的卷关联的 ACR，应先使该卷脱机。
> * 从卷中删除 ACR 时，请确保相应主机未在访问卷，因为删除会导致读写操作中断。
> 
> 

执行以下步骤，删除访问控制记录。

#### <a name="to-delete-an-access-control-record"></a>删除访问控制记录
1. 在服务登陆页上，选择你的服务、双击服务名称，然后单击“配置”选项卡。
2. 在访问控制记录 (ACR) 的表格列表中，将鼠标悬停在要删除的 ACR 上。
3. 所选 ACR 的最右侧列中将显示一个删除图标 (**x**)。 单击 **x** 图标即可删除 ACR。 你将看到以下确认消息。
   
    ![确认消息](./media/storsimple-ova-manage-acrs/acr3.png)
4. 单击选中图标  ![选中图标](./media/storsimple-ova-manage-acrs/check-icon.png)。 表格列表随即会更新以反映删除。

## <a name="next-steps"></a>后续步骤
* 了解有关[添加卷和配置 ACR](storsimple-ova-deploy3-iscsi-setup.md#step-3-add-a-volume) 的详细信息。


