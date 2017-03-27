---
title: "管理 StorSimple 存储帐户 | Microsoft Docs"
description: "说明如何使用 StorSimple Manager 的“配置”页添加、编辑、删除或轮换与 StorSimple Virtual Array 关联的存储帐户的安全密钥。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: fb6459c7-a4b1-4b69-a000-72edd42e9478
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/29/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: a2419943ab8591e397ad55bd400042436fdf0373


---
# <a name="use-the-storsimple-manager-service-to-manage-storage-accounts-for-storsimple-virtual-array"></a>使用 StorSimple Manager 服务管理 StorSimple Virtual Array 的存储帐户
## <a name="overview"></a>概述
“配置”页显示可在 StorSimple Manager 服务中创建的全局服务参数。 这些参数可应用到所有连接到该服务的设备，包括：

* 存储帐户 
* 访问控制记录 

本教程说明如何使用“配置”页添加、编辑或删除 StorSimple Virtual Array 的存储帐户。 本教程中的信息仅适用于运行 2016 年 3 月 GA 版软件的 StorSimple Virtual Array。

 ![“配置”页](./media/storsimple-ova-manage-storage-accounts/configure_service_page.png)  

存储帐户包含设备通过云服务提供程序访问存储帐户时使用的凭据。 对于 Microsoft Azure 存储帐户，这些是帐户名和主访问密钥等凭据。 

在“配置”页上，为计费订阅创建的所有存储帐户以表格格式显示，其中包含以下信息：

* **名称** – 创建帐户时分配给帐户的唯一名称。
* **已启用 SSL** – 是否启用 SSL，设备到云的通信将通过安全通道进行。

与可以在“配置”页上执行的存储帐户相关的最常见任务包括：

* 添加存储帐户 
* 编辑存储帐户 
* 删除存储帐户 

## <a name="types-of-storage-accounts"></a>存储帐户的类型
可在 StorSimple 设备上使用三种类型的存储帐户。

* **自动生成的存储帐户** – 顾名思义，此类型的存储帐户在首次创建服务时自动生成。 若要了解有关如何创建此存储帐户的详细信息，请参阅[新建服务](storsimple-ova-manage-service.md#create-a-service)。 
* **服务订阅中的存储帐户** – 这些帐户是与服务的相同订阅关联的 Azure 存储帐户。 若要了解有关如何创建这些存储帐户的详细信息，请参阅[关于 Azure 存储帐户](../storage/storage-create-storage-account.md)。 
* **服务订阅外部的存储帐户** – 这些帐户是不与服务关联的、可能在创建服务之前即已存在的 Azure 存储帐户。

每个 StorSimple Virtual Array 在关联的存储帐户中创建容器（带有前缀 hcs）。 此容器具有你的设备的所有云数据。 不要删除此容器（通过 Azure 存储服务访问它），因为此操作会导致数据丢失。

## <a name="add-a-storage-account"></a>添加存储帐户
可以通过提供与存储帐户链接的唯一友好名称和访问凭据，将存储帐户添加到 StorSimple Manager 服务配置。 还可以选择启用安全套接字层 (SSL) 模式，为设备与云之间的网络通信创建安全通道。

可为给定的云服务提供程序创建多个帐户。 保存存储帐户时，服务将尝试与云服务提供程序通信。 此时将对提供的凭据和访问材料进行身份验证。 仅当身份验证成功时，才会创建存储帐户。 如果身份验证失败，则显示相应的错误消息。

StorSimple 也支持在 Azure 门户中创建的 Resource Manager 存储帐户。 下拉列表中不会显示 Resource Manager 存储帐户选项，只显示在 Azure 经典门户中创建的存储帐户。 需要使用如下所述的添加存储帐户过程添加 Resource Manager 存储帐户。

下面详细介绍了添加 Azure 经典存储帐户的过程。

[!INCLUDE [add-a-storage-account](../../includes/storsimple-ova-configure-new-storage-account.md)]

## <a name="edit-a-storage-account"></a>编辑存储帐户
你可以编辑由你的设备使用的存储帐户。 如果编辑当前正在使用的存储帐户，可供修改的字段是存储帐户的访问密钥和 SSL 模式。 可以提供新的存储访问密钥，或修改“启用 SSL 模式”选项并保存更新的设置。

#### <a name="to-edit-a-storage-account"></a>编辑存储帐户
1. 在服务登录页上，选择你的服务、双击服务名称，然后单击“配置”。
2. 单击“添加/编辑存储帐户”。
3. 在“添加/编辑存储帐户”对话框中，执行以下操作：
   
   1. 在“存储帐户”的下拉列表中，选择要修改的现有帐户。 
   2. 如果需要，可以修改“启用 SSL 模式”选项。
   3. 可以选择重新生成存储帐户访问密钥。 有关详细信息，请参阅[重新生成存储帐户密钥](../storage/storage-create-storage-account.md#manage-your-storage-access-keys)。 提供新的存储帐户密钥。 对于 Azure 存储帐户，这是主访问密钥。 
   4. 单击选中图标![选中图标](./media/storsimple-ova-manage-storage-accounts/checkicon.png)，保存设置。 设置将在“配置”页上更新。 
   5. 在页面底部，单击“保存”，保存新更新的设置。 
      
      ![编辑存储帐户](./media/storsimple-ova-manage-storage-accounts/modifyexistingstorageaccount.png)

## <a name="delete-a-storage-account"></a>删除存储帐户
> [!IMPORTANT]
> 仅可以删除未在使用的存储帐户。 如果存储帐户正在使用中，你将收到通知。
> 
> 

#### <a name="to-delete-a-storage-account"></a>删除存储帐户
1. 在 StorSimple Manager 服务登录页上，选择你的服务、双击服务名称，然后单击“配置”。
2. 在存储帐户的表格列表中，将鼠标悬停在想要删除的帐户上。
3. 该存储帐户的最右侧列中将显示一个删除图标 (**x**)。 单击 **x** 图标即可删除凭据。
4. 系统提示确认时，请单击“是”继续删除。 表格列表随即会更新以反映更改。
5. 在页面底部，单击“保存”，保存新更新的设置。

## <a name="next-steps"></a>后续步骤
* 了解如何[管理你的 StorSimple Virtual Array](storsimple-ova-web-ui-admin.md)。




<!--HONumber=Nov16_HO3-->


