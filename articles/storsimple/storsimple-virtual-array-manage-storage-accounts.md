---
title: "管理 StorSimple 虚拟阵列存储帐户凭据 | Microsoft 文档"
description: "介绍如何使用 StorSimple 设备管理器的“配置”页添加、编辑、删除或轮换与 StorSimple 虚拟阵列关联的存储帐户凭据的安全密钥。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 234bf8bb-d5fe-40be-9d25-721d7482bc3b
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.openlocfilehash: a4ce2d329d0e1399cffaf886adf2b95e34b9cd7b
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2017
---
# <a name="use-storsimple-device-manager-to-manage-storage-account-credentials-for-storsimple-virtual-array"></a>使用 StorSimple Device Manager 管理 StorSimple 虚拟阵列的存储帐户凭据

## <a name="overview"></a>概述
StorSimple 虚拟阵列的 StorSimple Device Manager 服务边栏选项卡的“配置”部分提供了可以在 StorSimple Manager 服务中创建的全局服务参数。 这些参数可应用到所有连接到该服务的设备，包括：

* 存储帐户凭据
* 访问控制记录
  
  ![Device Manager 服务仪表板](./media/storsimple-virtual-array-manage-storage-accounts/ova-storageaccts-dashboard.png)  

本教程介绍如何为 StorSimple 虚拟阵列添加、编辑或删除存储帐户凭据。 本教程中的信息仅适用于 StorSimple 虚拟阵列。 有关如何管理 8000 系列中的存储帐户的信息，请参阅[使用 StorSimple Manager 服务管理存储帐户](storsimple-manage-storage-accounts.md)。

存储帐户凭据包含设备通过云服务提供程序访问存储帐户时使用的凭据。 对于 Microsoft Azure 存储帐户，这些是帐户名和主访问密钥等凭据。

在“存储帐户凭据”边栏选项卡上，为计费订阅创建的所有存储帐户凭据以表格格式显示，其中包含以下信息：

* **名称** – 创建帐户时分配给帐户的唯一名称。
* **已启用 SSL** – 是否启用 SSL，设备到云的通信将通过安全通道进行。
  
  ![“配置”部分](./media/storsimple-virtual-array-manage-storage-accounts/ova-storageaccountcredentials-blade.png)

可以在“存储帐户凭据”边栏选项卡上执行的与存储帐户凭据相关的最常见任务包括：

* 添加存储帐户凭据
* 编辑存储帐户凭据
* 删除存储帐户凭据

## <a name="types-of-storage-account-credentials"></a>存储帐户凭据的类型
可在 StorSimple 设备上使用三种类型的存储帐户凭据。

* **自动生成的存储帐户凭据** - 顾名思义，此类型的存储帐户凭据在首次创建服务时自动生成。 若要了解有关如何创建此存储帐户凭据的详细信息，请参阅[新建服务](storsimple-virtual-array-manage-service.md#create-a-service)。
* **服务订阅中的存储帐户凭据** - 这些是与服务的相同订阅关联的 Azure 存储帐户凭据。 若要了解有关如何创建这些存储帐户凭据的详细信息，请参阅[关于 Azure 存储帐户](../storage/common/storage-create-storage-account.md)。
* **服务订阅外部的存储帐户凭据** - 这些是不与服务关联的、可能在创建服务之前即已存在的 Azure 存储帐户凭据。

## <a name="add-a-storage-account-credential"></a>添加存储帐户凭据
可以通过提供与存储帐户链接的唯一友好名称和访问凭据，将存储帐户凭据添加到 StorSimple Device Manager 服务配置。 还可以选择启用安全套接字层 (SSL) 模式，为设备与云之间的网络通信创建安全通道。

可为给定的云服务提供程序创建多个帐户。 保存存储帐户凭据时，服务将尝试与云服务提供程序通信。 此时会对提供的凭据和访问材料进行身份验证。 仅当身份验证成功时，才会创建存储帐户凭据。 如果身份验证失败，则显示相应的错误消息。

使用以下过程添加 Azure 存储帐户凭据：

* 添加与 Device Manager 服务具有相同 Azure 订阅的存储帐户凭据
* 添加 Device Manager 服务订阅外部的 Azure 存储帐户凭据

#### <a name="to-add-a-storage-account-credential-that-has-the-same-azure-subscription-as-the-device-manager-service"></a>添加与 Device Manager 服务具有相同 Azure 订阅的存储帐户凭据

1. 导航到 Device Manager 服务，选择并双击它。 这会打开“概述”边栏选项卡。
2. 在“配置”部分中，选择“存储帐户凭据”。
3. 单击 **“添加”**。
4. 在“添加存储帐户”边栏选项卡中，执行以下操作：
   
    1. 对于“订阅”，选择“当前”。
    2. 提供 Azure 存储帐户的名称。
    3. 选择“启用”，创建用于在 StorSimple 设备和云之间传输网络通信的安全通道。 仅当要在私有云中操作时，才选择“禁用”。
    4. 单击 **“添加”**。 成功创建存储帐户后，将收到通知。<br></br>
   
        ![添加现有的存储帐户凭据](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-storageacct.png)

#### <a name="to-add-an-azure-storage-account-credential-that-is-outside-of-the-device-manager-service-subscription"></a>添加 Device Manager 服务订阅外部的 Azure 存储帐户凭据

1. 导航到 Device Manager 服务，选择并双击它。 这会打开“概述”边栏选项卡。
2. 在“配置”部分中，选择“存储帐户凭据”。 这会列出与 StorSimple Device Manager 服务关联的任何现有存储帐户凭据。
3. 单击 **“添加”**。
4. 在“添加存储帐户”边栏选项卡中，执行以下操作：
   
    1. 对于“订阅”，选择“其他”。
   
    2. 提供 Azure 存储帐户凭据的名称。
   
    3. 在“存储帐户访问密钥”文本框中，提供 Azure 存储帐户凭据的主访问密钥。 要获取此密钥，请转到“Azure 存储服务”，选择存储帐户凭据，并单击“管理帐户密钥”。 现在，可以复制主访问密钥。
   
    4. 若要启用 SSL，请单击“启用”按钮以创建用于在 StorSimple Device Manager 服务和云之间传输网络通信的安全通道。 仅当要在私有云中操作时，才单击“禁用”按钮。
   
    5. 单击 **“添加”**。 成功创建存储帐户凭据后，将收到通知。

5. 新创建的存储帐户凭据会显示在 StorSimple 配置 Device Manager 服务边栏选项卡上的“存储帐户凭据”下。
   
    ![在 Device Manager 服务订阅外部添加 Azure 存储帐户凭据](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-outside-storageacct.png)

## <a name="edit-a-storage-account-credential"></a>编辑存储帐户凭据
可以编辑设备所使用的存储帐户凭据。 如果编辑当前正在使用的存储帐户凭据，可供修改的字段是存储帐户凭据的访问密钥和 SSL 模式。 可以提供新的存储访问密钥，或修改“启用 SSL 模式”选项并保存更新的设置。

#### <a name="to-edit-a-storage-account-credential"></a>编辑存储帐户凭据
1. 导航到 Device Manager 服务，选择并双击它。 这会打开“概述”边栏选项卡。
2. 在“配置”部分中，选择“存储帐户凭据”。 这会列出与 StorSimple Device Manager 服务关联的任何现有存储帐户凭据。
3. 在存储帐户凭据的表格列表中，选择并双击要修改的帐户。
4. 在存储帐户凭据的“属性”边栏选项卡中，执行以下操作：
   
   1. 如果需要，可以修改“启用 SSL 模式”选项。
   2. 可以选择重新生成存储帐户凭据访问密钥。 有关详细信息，请参阅[重新生成存储帐户密钥](../storage/common/storage-create-storage-account.md#manage-your-storage-access-keys)。 提供新的存储帐户凭据密钥。 对于 Azure 存储帐户，这是主访问密钥。
   3. 单击“属性”边栏选项卡顶部的“保存”以保存设置。 “存储帐户凭据”边栏选项卡上将更新设置。
      
      ![编辑存储帐户凭据](./media/storsimple-virtual-array-manage-storage-accounts/ova-edit-storageacct.png)

## <a name="delete-a-storage-account-credential"></a>删除存储帐户凭据
> [!IMPORTANT]
> 仅可以删除未在使用的存储帐户凭据。 如果存储帐户凭据正在使用中，将收到通知。
> 
> 

#### <a name="to-delete-a-storage-account-credential"></a>删除存储帐户凭据
1. 导航到 Device Manager 服务，选择并双击它。 这会打开“概述”边栏选项卡。
2. 在“配置”部分中，选择“存储帐户凭据”。 这会列出与 StorSimple Device Manager 服务关联的任何现有存储帐户凭据。
3. 在存储帐户凭据的表格列表中，选择并双击要删除的帐户。
4. 在存储帐户凭据的“属性”边栏选项卡中，执行以下操作：
   
   1. 单击“删除”，删除这些凭据。
   2. 系统提示确认时，请单击“是”继续删除。 表格列表随即会更新以反映更改。
      
      ![删除存储帐户凭据](./media/storsimple-virtual-array-manage-storage-accounts/ova-del-storageacct.png)

## <a name="synchronizing-storage-account-credential-keys"></a>同步存储帐户凭据密钥
出于安全原因，数据中心经常要求密钥轮换。 Microsoft Azure 管理员可以通过直接访问存储帐户凭据（通过 Microsoft Azure 存储服务）来重新生成或更改主密钥或辅助密钥。 StorSimple Device Manager 服务无法自动看到此更改。

为了告知 StorSimple Device Manager 服务发生了这种更改，需要访问 StorSimple Device Manager 服务，访问存储帐户凭据，并同步主密钥或辅助密钥（具体取决于更改了哪一个）。 然后，服务将获取最新密钥，加密密钥，并将其发送到设备。

#### <a name="to-synchronize-keys-for-storage-account-credentials-in-the-same-subscription-as-the-service-azure-only"></a>同步与服务相同的订阅中存储帐户凭据的密钥（仅限 Azure）
1. 在服务登陆边栏选项卡上，选择服务、双击服务名称，然后在“配置”部分中单击“存储帐户凭据”。
2. 在“存储帐户凭据”边栏选项卡上的存储帐户凭据列表中，选择要同步其密钥的存储帐户凭据。
3. 在所选存储帐户凭据的“属性”边栏选项卡中，执行以下操作：
   
    1. 单击“更多”，并单击“同步访问密钥”。
   
    2. 当系统提示确认时，单击“同步密钥”以完成同步。
    
4. 在 StorSimple Device Manager 服务中，需要更新前面在 Microsoft Azure 存储服务中更改的密钥。 在“同步存储帐户密钥”边栏选项卡中，如果更改了主访问密钥（重新生成），请单击“主”，并单击“同步密钥”。 如果更改了辅助密钥，请单击“辅助”，并单击“同步密钥”。
   
    ![同步访问密钥](./media/storsimple-virtual-array-manage-storage-accounts/ova-sync-acess-key.png)

## <a name="next-steps"></a>后续步骤
* 了解如何[管理 StorSimple Virtual Array](storsimple-ova-web-ui-admin.md)。

