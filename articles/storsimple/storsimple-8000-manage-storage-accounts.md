---
title: "管理 Microsoft Azure StorSimple 8000 系列设备的 StorSimple 存储帐户凭据 | Microsoft Docs"
description: "介绍如何使用 StorSimple 设备管理器的“配置”页添加、编辑、删除或更改存储帐户的安全密钥。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: 36058ad69ea670998b50cf9038741c294a5b79ab
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2017
---
# <a name="use-the-storsimple-device-manager-service-to-manage-your-storage-account-credentials"></a>使用 StorSimple 设备管理器服务管理存储帐户凭据

## <a name="overview"></a>概述

StorSimple 设备管理器服务边栏选项卡中的“配置”部分提供了可在 StorSimple 设备管理器服务中创建的所有全局服务参数。 这些参数可应用到所有连接到该服务的设备。具体参数包括：

* 存储帐户凭据
* 带宽模板 
* 访问控制记录 

本教程介绍如何添加、编辑或删除存储帐户凭据，或轮换存储帐户的安全密钥。

 ![存储帐户凭据的列表](./media/storsimple-8000-manage-storage-accounts/createnewstorageacct6.png)  

存储帐户包含 StorSimple 设备通过云服务提供程序访问存储帐户时使用的凭据。 对于 Microsoft Azure 存储帐户，这些是帐户名和主访问密钥等凭据。 

在“存储帐户凭据”边栏选项卡上，为计费订阅创建的所有存储帐户以表格格式显示，其中包含以下信息：

* **名称** – 创建帐户时分配给帐户的唯一名称。
* **已启用 SSL** – 是否启用 SSL，设备到云的通信将通过安全通道进行。
* **使用者** – 使用存储帐户的卷数。

可执行的、与存储帐户相关的最常见任务包括：

* 添加存储帐户 
* 编辑存储帐户 
* 删除存储帐户 
* 存储帐户的密钥轮换 

## <a name="types-of-storage-accounts"></a>存储帐户的类型

可在 StorSimple 设备上使用三种类型的存储帐户。

* **自动生成的存储帐户** – 顾名思义，这种存储帐户是首次创建服务时自动生成的。 若要详细了解如何创建此存储帐户，请参阅[部署本地 StorSimple 设备](storsimple-8000-deployment-walkthrough-u2.md)中的[步骤 1：创建新服务](storsimple-8000-deployment-walkthrough-u2.md#step-1-create-a-new-service)。 
* **服务订阅中的存储帐户** – 这是与服务的相同订阅关联的 Azure 存储帐户。 若要了解有关如何创建这些存储帐户的详细信息，请参阅[关于 Azure 存储帐户](../storage/common/storage-create-storage-account.md)。 
* **服务订阅外部的存储帐户** – 这是不与服务关联的、可能在创建服务之前即已存在的 Azure 存储帐户。

## <a name="add-a-storage-account"></a>添加存储帐户

可以通过（使用指定的云服务提供程序）指定与存储帐户链接的唯一友好名称和访问凭据来添加存储帐户。 还可以选择启用安全套接字层 (SSL) 模式，为设备与云之间的网络通信创建一条安全通道。

可为给定的云服务提供程序创建多个帐户。 注意，创建存储帐户后，无法更改云服务提供程序。

保存存储帐户时，服务将尝试与云服务提供程序通信。 此时会对提供的凭据和访问材料进行身份验证。 仅当身份验证成功时，才会创建存储帐户。 如果身份验证失败，则显示相应的错误消息。

使用以下过程添加 Azure 存储帐户凭据：

* 添加与 Device Manager 服务具有相同 Azure 订阅的存储帐户凭据
* 添加 Device Manager 服务订阅外部的 Azure 存储帐户凭据

[!INCLUDE [add-a-storage-account-update2](../../includes/storsimple-8000-configure-new-storage-account-u2.md)]

#### <a name="to-add-an-azure-storage-account-credential-outside-of-the-storsimple-device-manager-service-subscription"></a>添加 StorSimple 设备管理器服务订阅外部的 Azure 存储帐户凭据

1. 导航到 StorSimple 设备管理器服务，选择并双击它。 这会打开“概述”边栏选项卡。
2. 在“配置”部分中，选择“存储帐户凭据”。 这会列出与 StorSimple Device Manager 服务关联的任何现有存储帐户凭据。
3. 单击“添加”。
4. 在“添加存储帐户凭据”边栏选项卡中，执行以下步骤：
   
    1. 对于“订阅”，选择“其他”。
   
    2. 提供 Azure 存储帐户凭据的名称。
   
    3. 在“存储帐户访问密钥”文本框中，提供 Azure 存储帐户凭据的主访问密钥。 要获取此密钥，请转到“Azure 存储服务”，选择存储帐户凭据，并单击“管理帐户密钥”。 现在，可以复制主访问密钥。
   
    4. 若要启用 SSL，请单击“启用”按钮以创建用于在 StorSimple Device Manager 服务和云之间传输网络通信的安全通道。 仅当要在私有云中操作时，才单击“禁用”按钮。
   
    5. 单击 **“添加”**。 成功创建存储帐户凭据后，将收到通知。

5. 新创建的存储帐户凭据会显示在 StorSimple 配置 Device Manager 服务边栏选项卡上的“存储帐户凭据”下。
   


## <a name="edit-a-storage-account"></a>编辑存储帐户

可以编辑卷容器使用的存储帐户。 如果编辑当前正在使用的存储帐户，唯一可修改的字段是存储帐户的访问密钥。 可以提供新的存储访问密钥并保存更新的设置。

#### <a name="to-edit-a-storage-account"></a>编辑存储帐户

1. 转到 StorSimple Device Manager 服务。 在“配置”部分中，单击“存储帐户凭据”。

    ![存储帐户凭据](./media/storsimple-8000-manage-storage-accounts/editstorageacct1.png)

2. 在“存储帐户凭据”边栏选项卡上的存储帐户凭据列表中，选择并单击想要编辑的凭据。 

3. 可以修改“启用 SSL”选项。 还可以单击“更多...”，选择“同步访问密钥”来轮换存储帐户访问密钥。 有关如何执行密钥轮换的详细信息，请参阅[存储帐户的密钥轮换](#key-rotation-of-storage-accounts)。 修改设置后，单击“保存”。 

    ![保存已编辑的存储帐户凭据](./media/storsimple-8000-manage-storage-accounts/editstorageacct3.png)

4. 出现确认提示时，单击“是”。 

    ![确认修改](./media/storsimple-8000-manage-storage-accounts/editstorageacct4.png)

将更新并保存存储帐户的设置。 

## <a name="delete-a-storage-account"></a>删除存储帐户

> [!IMPORTANT]
> 仅当存储帐户未被卷容器使用时才能将其删除。 如果存储帐户正被卷容器使用，应先删除卷容器，再删除关联的存储帐户。

#### <a name="to-delete-a-storage-account"></a>删除存储帐户

1. 转到 StorSimple Device Manager 服务。 在“配置”部分中，单击“存储帐户凭据”。

2. 在存储帐户的表格列表中，将鼠标悬停在想要删除的帐户上。 单击右键调用上下文菜单，单击“删除”。

    ![删除存储帐户凭据](./media/storsimple-8000-manage-storage-accounts/deletestorageacct1.png)

3. 系统提示确认时，请单击“是”继续删除。 表格列表随即会更新以反映更改。

    ![确认删除](./media/storsimple-8000-manage-storage-accounts/deletestorageacct2.png)

## <a name="key-rotation-of-storage-accounts"></a>存储帐户的密钥轮换

出于安全原因，数据中心经常要求密钥轮换。 每个 Microsoft Azure 订阅可以有一个或多个关联的存储帐户。 对这些帐户的访问由订阅以及每个存储帐户的访问密钥控制。 

创建存储帐户时，Microsoft Azure 将生成两个 512 位存储访问密钥，用于在用户访问该存储帐户时对其进行身份验证。 如果有两个存储访问密钥，则可以重新生成密钥，且无需中断存储服务或者对该服务的访问。 当前正在使用的密钥是*主*密钥，备份密钥称为*辅助*密钥。 当 Microsoft Azure StorSimple 设备访问云存储服务提供程序时，必须提供这两个密钥中的一个。

## <a name="what-is-key-rotation"></a>什么是密钥轮换？

通常，应用程序只使用一个密钥来访问数据。 在特定的一段时间后，可以让应用程序切换为使用第二个密钥。 将应用程序切换到第二个密钥后，将停用第一个密钥，并生成新密钥。 以这种方式使用两个密钥可让应用程序在不引发任何停机的情况下访问数据。

存储帐户密钥始终以加密形式存储在服务中。务。 但是，可以通过 StorSimple 设备管理器服务重置这些密钥。 服务可以获取同一个订阅中所有存储帐户的主密钥和辅助密钥，包括在存储服务中创建的帐户，以及首次创建 StorSimple 设备管理器服务时生成的默认存储帐户。 StorSimple 设备管理器服务始终从 Azure 经典门户中获取这些密钥，然后将它们以加密形式存储。

## <a name="rotation-workflow"></a>轮换工作流

Microsoft Azure 管理员可以通过直接访问存储帐户中重新生成或更改主密钥或辅助密钥（通过 Microsoft Azure 存储服务）。 StorSimple Device Manager 服务无法自动看到此更改。

为了告知 StorSimple 设备管理器服务发生了这种更改，需要访问 StorSimple Device Manager 服务，访问存储帐户，并同步主密钥或辅助密钥（具体取决于更改了哪一个）。 然后，服务将获取最新密钥，加密密钥，并将其发送到设备。

#### <a name="to-synchronize-keys-for-storage-accounts-in-the-same-subscription-as-the-service"></a>同步与服务相同的订阅中存储帐户的密钥 
1. 转到 StorSimple Device Manager 服务。 在“配置”部分中，单击“存储帐户凭据”。
2. 在存储帐户的表格列表中，单击想要修改的存储帐户。 

    ![同步密钥](./media/storsimple-8000-manage-storage-accounts/syncaccesskey1.png)

3. 单击“更多...”，选择“同步访问密钥”进行轮换。   

    ![同步密钥](./media/storsimple-8000-manage-storage-accounts/syncaccesskey2.png)

4. 在 StorSimple Device Manager 服务中，需要更新前面在 Microsoft Azure 存储服务中更改的密钥。 如果更改（重新生成）了主访问密钥，请选择**主**密钥。 如果更改了辅助密钥，请选择**辅助**密钥。 单击“同步密钥”。
      
      ![同步密钥](./media/storsimple-8000-manage-storage-accounts/syncaccesskey3.png)

成功同步密钥后，会收到通知。

#### <a name="to-synchronize-keys-for-storage-accounts-outside-of-the-service-subscription"></a>同步服务订阅外部的存储帐户的密钥
1. 在“服务”页上，单击“配置”选项卡。
2. 单击“添加/编辑存储帐户”。
3. 在对话框中执行以下操作：
   
   1. 选择要更新其访问密钥的存储帐户。
   2. 需要更新 StorSimple 设备管理器服务中的存储访问密钥。 在此情况下，可以看到该存储访问密钥。 在“存储帐户访问密钥”框中输入新密钥。 
   3. 保存所做更改。 存储帐户访问密钥现在应已更新。

## <a name="next-steps"></a>后续步骤
* 详细了解 [StorSimple 安全性](storsimple-8000-security.md)。
* 了解有关如何[使用 StorSimple 设备管理器服务管理 StorSimple 设备](storsimple-8000-manager-service-administration.md)的详细信息。

