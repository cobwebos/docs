---
title: "为 StorSimple Virtual Array 管理访问控制记录 |Microsoft 文档"
description: "描述如何管理访问控制记录 (Acr) 来确定哪些主机可以连接到 StorSimple 虚拟阵列上的卷。"
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
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
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="use-storsimple-device-manager-to-manage-access-control-records-for-storsimple-virtual-array"></a>使用 StorSimple 设备管理器为 StorSimple Virtual Array 管理访问控制记录

## <a name="overview"></a>概述

访问控制记录 (Acr) 允许你指定哪些主机可以连接到 StorSimple Virtual Array （也称为 StorSimple 本地虚拟设备） 上的卷。 Acr 中设置为的特定卷，并包含主机的 iSCSI 限定名称 (Iqn)。 当主机试图连接某个卷时，设备会检查与的 IQN 名称，该卷关联的 ACR，如果没有匹配项，则连接建立。 **访问控制记录**边栏选项卡中的**配置**的设备管理器服务的部分显示与该主机的相应 Iqn 的所有访问控制记录。

![管理访问控制记录](./media/storsimple-virtual-array-manage-acrs/ova-manage-acrs.png)

本教程介绍以下常见 ACR 相关的任务：

* 获取 IQN
* 添加访问控制记录
* 编辑访问控制记录
* 删除访问控制记录

> [!IMPORTANT]
> 
> * 在将 ACR 分配给卷，注意，卷是不同时并发访问多个非群集主机因为这可能会损坏卷。
> * 当从卷中删除 ACR，请确保对应的主机不访问卷，因为删除可能导致读写操作中断。


## <a name="get-the-iqn"></a>获取 IQN

执行以下步骤以获取正在运行 Windows Server 2012 的 Windows 主机的 IQN。

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]

## <a name="add-an-acr"></a>添加 ACR

你使用**访问控制记录**边栏选项卡中的**配置**你要添加 Acr 的 StorSimple 设备管理器服务的一部分。 通常情况下，你将一个卷与关联一个 ACR。

有关如何将一个 ACR 与卷关联的信息，请转到[添加卷](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume)。

> [!IMPORTANT]
> 在将 ACR 分配给卷，注意，卷是不同时并发访问多个非群集主机因为这可能会损坏卷。


执行以下步骤以添加 ACR。

#### <a name="to-add-an-acr"></a>添加 ACR

1. 在服务登录页上，选择你的服务，双击服务名称，然后在**配置**部分中，单击**访问控制记录**。
2. 在**访问控制记录**边栏选项卡，单击**添加**。
3. 在**添加 ACR**边栏选项卡中，执行以下操作：
   
    1. 提供**名称**为你的 ACR。
    
    2. 下**iSCSI 发起程序名称**，提供你的 Windows 主机的 IQN 名称。 若要获取 Windows Server 主机的 IQN，请执行以下操作：
   
    3. 在你的 Windows 主机上启动 Microsoft iSCSI 发起程序。 在 iSCSI 发起程序属性窗口中，在**配置**选项卡上，选择并复制从字符串**发起程序名称**字段。
    粘贴在此字符串**IQN**字段**添加 ACR**边栏选项卡。
   
    6. 单击**添加**以添加 ACR。  
   
        ![添加访问控制记录](./media/storsimple-virtual-array-manage-acrs/ova-add-acrs.png)
4. 更新该表格式列表以反映此添加。

## <a name="edit-an-acr"></a>编辑 ACR

你使用**访问控制记录**边栏选项卡中的**配置**的 Azure 门户中编辑 Acr 中的设备管理器服务的部分。

> [!NOTE]
> 不应修改当前正在使用的 ACR。 若要编辑与当前正在使用的卷关联的 ACR，你应首先使该卷脱机。


执行以下步骤以编辑 ACR。

#### <a name="to-edit-an-acr"></a>若要编辑 ACR

1. 在服务登录页上，选择你的服务，双击服务名称，然后在**配置**部分中，**访问控制记录**。
2. 在**访问控制记录**边栏选项卡，访问控制记录的表格列表中双击你想要修改的 ACR。
3. 在**编辑访问控制记录**边栏选项卡中，执行以下操作：
   
    1. 为 ACR 提供 IQN。
   
    2. 单击**保存**顶部的边栏选项卡以保存修改的 ACR。 你看到以下确认消息：
   
        ![编辑访问控制记录](./media/storsimple-virtual-array-manage-acrs/ova-edit-acrs.png)

## <a name="delete-an-access-control-record"></a>删除访问控制记录

你使用**配置**在 Azure 门户中删除 Acr 的页。

> [!NOTE]
> 
> * 不应删除当前正在使用的 ACR。 若要删除与当前正在使用的卷关联的 ACR，你应首先使该卷脱机。
> * 当从卷中删除 ACR，请确保对应的主机不访问卷，因为删除可能导致读写操作中断。


执行以下步骤以删除访问控制记录。

#### <a name="to-delete-an-access-control-record"></a>若要删除访问控制记录

1. 在服务登录页上，选择你的服务，双击服务名称，然后在**配置**部分中，**访问控制记录**。

2. 在**访问控制记录**边栏选项卡，访问控制记录的表格列表中双击你想要删除的 ACR。

3. 在编辑访问控制记录边栏选项卡，单击**删除**。
   
    ![删除 ACR](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs.png)

4. 当系统提示确认，请单击**删除**若要继续删除操作。 更新该表格式列表以反映删除。
   
   ![警告消息](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs-warning.png)

## <a name="next-steps"></a>后续步骤

* 详细了解[添加卷和配置 Acr](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume)。

