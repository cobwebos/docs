---
title: "管理 StorSimple 中的访问控制记录 | Microsoft Docs"
description: "介绍如何使用访问控制记录 (ACR)，以确定哪些主机可以连接到 StorSimple 设备上的卷。"
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 2f1475d8-36a5-4cc4-84b9-adf8a310b60c
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: alkohli
ms.openlocfilehash: a87624b5706c1d9b8c2b9926e5580996a89ce984
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="use-the-storsimple-manager-service-to-manage-access-control-records"></a>使用 StorSimple Manager 服务管理访问控制记录
## <a name="overview"></a>概述
使用访问控制记录 (ACR) 可以指定哪些主机能够连接到 StorSimple 设备上的卷。 ACR 设置为特定的卷，并包含主机的 iSCSI 限定名称 (IQN)。 当主机尝试连接到卷时，设备会在与该卷关联的 ACR 中检查 IQN 名称；如果名称匹配，则建立连接。 “配置”页上的访问控制记录部分显示包含相应主机 IQN 的所有访问控制记录。

本教程介绍了以下与 ACR 相关的常见任务：

* 添加访问控制记录 
* 编辑访问控制记录 
* 删除访问控制记录 

> [!IMPORTANT]
> * 将 ACR 分配到卷时，请确保不允许多个非群集主机并发访问卷，因为这会损坏卷。 
> * 从卷中删除 ACR 时，请确保相应主机未在访问卷，因为删除会导致读写操作中断。
> 
> 

## <a name="add-an-access-control-record"></a>添加访问控制记录
使用 StorSimple Manager 服务“配置”页添加 ACR。 通常情况下，会将一个 ACR 与一个卷相关联。

执行以下步骤，添加 ACR。

#### <a name="to-add-an-access-control-record"></a>添加访问控制记录
1. 在服务登陆页上选择服务，双击服务名称，并单击“配置”选项卡。
2. 在“访问控制记录”下的表格列表中，为 ACR 提供一个“名称”。
3. 在“iSCSI 发起程序名称”下，提供 Windows 主机的 IQN 名称。 若要获取 Windows Server 主机的 IQN，请执行以下操作：
   
   * 在 Windows 主机上启动 Microsoft iSCSI 发起程序。
   * 在“iSCSI 发起程序属性”窗口中的“配置”选项卡上，选择并复制“发起程序名称”字段中的字符串。
   * 在 Azure 经典门户中将该字符串粘贴到 ACR 表的“iSCSI 发起程序名称”字段中。
4. 单击“保存”，保存新创建的 ACR。 表格列表随即会更新以反映此添加操作。

## <a name="edit-an-access-control-record"></a>编辑访问控制记录
使用 Azure 经典门户中的“配置”页编辑 ACR。 

> [!NOTE]
> 只能修改当前未使用的 ACR。 若要编辑与当前正在使用的卷关联的 ACR，必须先使该卷脱机。
> 
> 

执行以下步骤，编辑 ACR。

#### <a name="to-edit-an-access-control-record"></a>编辑访问控制记录
1. 在服务登陆页上选择服务，双击服务名称，并单击“配置”选项卡。
2. 在访问控制记录的表格列表中，将鼠标悬停在要修改的 ACR 上。
3. 为 ACR 提供新名称和/或 IQN。
4. 单击“保存”保存修改的 ACR。 表格列表随即会更新以反映此项更改。

## <a name="delete-an-access-control-record"></a>删除访问控制记录
使用 Azure 经典门户中的“配置”页删除 ACR。 

> [!NOTE]
> 只能删除当前未使用的 ACR。 若要删除与当前正在使用的卷关联的 ACR，必须先使该卷脱机。
> 
> 

执行以下步骤，删除访问控制记录。

#### <a name="to-delete-an-access-control-record"></a>删除访问控制记录
1. 在服务登陆页上选择服务，双击服务名称，并单击“配置”选项卡。
2. 在访问控制记录 (ACR) 的表格列表中，将鼠标悬停在要删除的 ACR 上。
3. 所选 ACR 的最右侧列中会显示一个删除图标 (**x**)。 单击 **x** 图标即可删除 ACR。
4. 系统提示确认时，请单击“是”继续删除。 表格列表随即会更新以反映删除。

## <a name="next-steps"></a>后续步骤
* 详细了解[管理 StorSimple 卷](storsimple-manage-volumes.md)。
* 了解有关如何[使用 StorSimple Manager 服务管理 StorSimple 设备](storsimple-manager-service-administration.md)的详细信息。

