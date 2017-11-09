---
title: "管理 StorSimple 中的访问控制记录 | Microsoft Docs"
description: "介绍如何使用访问控制记录 (ACR)，以确定哪些主机可以连接到 StorSimple 设备上的卷。"
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2017
ms.author: alkohli
ms.openlocfilehash: 9173e34f889ce1c082b20bb382cb6ca9a03dd797
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-manager-service-to-manage-access-control-records"></a>使用 StorSimple Manager 服务管理访问控制记录

## <a name="overview"></a>概述
使用访问控制记录 (ACR) 可以指定哪些主机能够连接到 StorSimple 设备上的卷。 ACR 设置为特定的卷，并包含主机的 iSCSI 限定名称 (IQN)。 当主机尝试连接到卷时，设备会在与该卷关联的 ACR 中检查 IQN 名称；如果名称匹配，则建立连接。 StorSimple 设备管理器服务边栏选项卡的“配置”部分中的访问控制记录显示包含相应主机 IQN 的所有访问控制记录。

本教程介绍了以下与 ACR 相关的常见任务：

* 添加访问控制记录
* 编辑访问控制记录
* 删除访问控制记录

> [!IMPORTANT]
> * 将 ACR 分配到卷时，请确保不允许多个非群集主机并发访问卷，因为这会损坏卷。
> * 从卷中删除 ACR 时，请确保相应主机未在访问卷，因为删除会导致读写操作中断。

## <a name="get-the-iqn"></a>获取 IQN

执行以下步骤，获取正在运行 Windows Server 2012 的 Windows 主机的 IQN。

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]


## <a name="add-an-access-control-record"></a>添加访问控制记录
使用 StorSimple 设备管理器服务边栏选项卡中的“配置”部分可以添加 ACR。 通常，一个 ACR 关联一个卷。

执行以下步骤，添加 ACR。

#### <a name="to-add-an-acr"></a>添加 ACR

1. 转到 StorSimple 设备管理器服务，双击服务名称，在“配置”部分中单击“访问控制记录”。
2. 在“访问控制记录”边栏选项卡中，单击“+ 添加 ACR”。

    ![单击“添加 ACR”](./media/storsimple-8000-manage-acrs/createacr1.png)

3. 在“添加 ACR”边栏选项卡中，执行以下步骤：

    1. 为 ACR 提供名称。
    
    2. 在“iSCSI 发起程序名称(IQN)”下，提供 Windows Server 主机的 IQN 名称。

    3. 单击“添加”创建 ACR。

        ![单击“添加 ACR”](./media/storsimple-8000-manage-acrs/createacr2.png)

4.  新添加的 ACR 会显示在 ACR 的表格列表中。

    ![单击“添加 ACR”](./media/storsimple-8000-manage-acrs/createacr5.png)


## <a name="edit-an-access-control-record"></a>编辑访问控制记录
使用 StorSimple 设备管理器服务边栏选项卡中的“配置”部分可以编辑 ACR。

> [!NOTE]
> 建议只修改当前未在使用的 ACR。 若要编辑与当前正在使用的卷关联的 ACR，必须先使该卷脱机。

执行以下步骤，编辑 ACR。

#### <a name="to-edit-an-access-control-record"></a>编辑访问控制记录
1.  转到 StorSimple 设备管理器服务，双击服务名称，在“配置”部分中单击“访问控制记录”。

    ![转到访问控制记录](./media/storsimple-8000-manage-acrs/createacr1.png)

2. 在访问控制记录的表格列表中，单击并选择要修改的 ACR。

    ![编辑访问控制记录](./media/storsimple-8000-manage-acrs/editacr1.png)

3. 在“编辑访问控制记录”边栏选项卡中，提供对应于另一台主机的不同 IQN。

    ![编辑访问控制记录](./media/storsimple-8000-manage-acrs/editacr2.png)

4. 单击“保存” 。 出现确认提示时，单击“是”。 

    ![编辑访问控制记录](./media/storsimple-8000-manage-acrs/editacr3.png)

5. 更新 ACR 后，系统会发出通知。 表格列表也会更新以反映更改。

   
## <a name="delete-an-access-control-record"></a>删除访问控制记录
使用 StorSimple 设备管理器服务边栏选项卡中的“配置”部分可以删除 ACR。

> [!NOTE]
> 只能删除当前未使用的 ACR。 若要删除与当前正在使用的卷关联的 ACR，必须先使该卷脱机。

执行以下步骤，删除访问控制记录。

#### <a name="to-delete-an-access-control-record"></a>删除访问控制记录
1.  转到 StorSimple 设备管理器服务，双击服务名称，在“配置”部分中单击“访问控制记录”。

    ![转到访问控制记录](./media/storsimple-8000-manage-acrs/createacr1.png)

2. 在访问控制记录的表格列表中，单击并选择要删除的 ACR。

    ![转到访问控制记录](./media/storsimple-8000-manage-acrs/deleteacr1.png)

3. 单击右键调用上下文菜单，选择“删除”。

    ![转到访问控制记录](./media/storsimple-8000-manage-acrs/deleteacr2.png)

4. 当系统提示确认时，请检查信息，单击“删除”。

    ![转到访问控制记录](./media/storsimple-8000-manage-acrs/deleteacr3.png)

5. 删除完成后，系统会发出通知。 表格列表随即会更新以反映删除。

    ![转到访问控制记录](./media/storsimple-8000-manage-acrs/deleteacr5.png)

## <a name="next-steps"></a>后续步骤
* 详细了解[管理 StorSimple 卷](storsimple-8000-manage-volumes-u2.md)。
* 了解有关如何[使用 StorSimple Manager 服务管理 StorSimple 设备](storsimple-8000-manager-service-administration.md)的详细信息。

