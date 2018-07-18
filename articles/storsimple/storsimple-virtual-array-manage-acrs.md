---
title: 管理 StorSimple 虚拟阵列的访问控制记录 | Microsoft 文档
description: 介绍如何管理访问控制记录 (ACR)，以确定哪些主机可以连接到 StorSimple Virtual Array 上的卷。
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: e154bb4f-faab-4d92-a593-900c3ddc9595
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2ce65aa4efba735305208f7a6d761bc2814d1b8f
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38718880"
---
# <a name="use-storsimple-device-manager-to-manage-access-control-records-for-storsimple-virtual-array"></a>使用 StorSimple Device Manager 管理 StorSimple 虚拟阵列的访问控制记录

## <a name="overview"></a>概述

访问控制记录 (ACR) 允许指定哪些主机可以连接到 StorSimple Virtual Array（也称为 StorSimple 本地虚拟设备）上的卷。 ACR 设置为特定的卷，并包含主机的 iSCSI 限定名称 (IQN)。 当主机尝试连接到卷时，设备会在与该卷关联的 ACR 中检查 IQN 名称；如果名称匹配，则建立连接。 Device Manager 服务的“配置”部分中的“访问控制记录”边栏选项卡显示包含相应主机 IQN 的所有访问控制记录。

![管理访问控制记录](./media/storsimple-virtual-array-manage-acrs/ova-manage-acrs.png)

本教程介绍了以下与 ACR 相关的常见任务：

* 获取 IQN
* 添加访问控制记录
* 编辑访问控制记录
* 删除访问控制记录

> [!IMPORTANT]
> 
> * 将 ACR 分配到卷时，请确保不允许多个非群集主机并发访问卷，因为这会损坏卷。
> * 从卷中删除 ACR 时，请确保相应主机未在访问卷，因为删除会导致读写操作中断。


## <a name="get-the-iqn"></a>获取 IQN

执行以下步骤，获取正在运行 Windows Server 2012 的 Windows 主机的 IQN。

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]

## <a name="add-an-acr"></a>添加 ACR

使用 StorSimple Device Manager 服务的“配置”部分中的“访问控制记录”边栏选项卡添加 ACR。 通常，会将一个 ACR 与一个卷相关联。

有关如何将 ACR 与卷关联的信息，请转到[添加卷](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume)。

> [!IMPORTANT]
> 将 ACR 分配到卷时，请确保不允许多个非群集主机并发访问卷，因为这会损坏卷。


执行以下步骤，添加 ACR。

#### <a name="to-add-an-acr"></a>添加 ACR

1. 在服务登陆页上，选择你的服务、双击服务名称，并在“配置”部分中单击“访问控制记录”。
2. 在“访问控制记录”边栏选项卡中，单击“添加”。
3. 在“添加 ACR”边栏选项卡中，执行以下操作：
   
    1. 为 ACR 提供“名称”。
    
    2. 在“iSCSI 发起程序名称”下，提供 Windows 主机的 IQN 名称。 若要获取 Windows Server 主机的 IQN，请执行以下操作：
   
    3. 在 Windows 主机上启动 Microsoft iSCSI 发起程序。 在“iSCSI 发起程序属性”窗口中的“配置”选项卡上，选择并复制“发起程序名称”字段中的字符串。
    在“添加 ACR”边栏选项卡上的“IQN”字段中粘贴此字符串。
   
    6. 单击“添加” 以添加该 ACR。  
   
        ![添加访问控制记录](./media/storsimple-virtual-array-manage-acrs/ova-add-acrs.png)
4. 表格列表随即会更新以反映此添加。

## <a name="edit-an-acr"></a>编辑 ACR

在 Azure 门户中，使用 Device Manager 服务的“配置”部分中的“访问控制记录”边栏选项卡编辑 ACR。

> [!NOTE]
> 不应修改当前正在使用的 ACR。 若要编辑与当前正在使用的卷关联的 ACR，应先使该卷脱机。


执行以下步骤，编辑 ACR。

#### <a name="to-edit-an-acr"></a>编辑 ACR

1. 在服务登陆页上，选择服务、双击服务名称，然后在“配置”部分中单击“访问控制记录”。
2. 在“访问控制记录”边栏选项卡上，从访问控制记录的表格列表中双击要修改的 ACR。
3. 在“编辑访问控制记录”边栏选项卡中，执行以下操作：
   
    1. 为 ACR 提供 IQN。
   
    2. 单击边栏选项卡顶部的“保存”保存已修改的 ACR。 会看到以下确认消息：
   
        ![编辑访问控制记录](./media/storsimple-virtual-array-manage-acrs/ova-edit-acrs.png)

## <a name="delete-an-access-control-record"></a>删除访问控制记录

使用 Azure 门户中的“配置”页删除 ACR。

> [!NOTE]
> 
> * 不应删除当前正在使用的 ACR。 若要删除与当前正在使用的卷关联的 ACR，应先使该卷脱机。
> * 从卷中删除 ACR 时，请确保相应主机未在访问卷，因为删除会导致读写操作中断。


执行以下步骤，删除访问控制记录。

#### <a name="to-delete-an-access-control-record"></a>删除访问控制记录

1. 在服务登陆页上，选择服务、双击服务名称，然后在“配置”部分中单击“访问控制记录”。

2. 在“访问控制记录”边栏选项卡上，从访问控制记录的表格列表中双击要删除的 ACR。

3. 在“编辑访问控制记录”边栏选项卡中，单击“删除”。
   
    ![删除 ACR](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs.png)

4. 系统提示确认时，请单击“删除”继续删除。 表格列表随即会更新以反映删除。
   
   ![警告消息](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs-warning.png)

## <a name="next-steps"></a>后续步骤

* 了解有关[添加卷和配置 ACR](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume) 的详细信息。

