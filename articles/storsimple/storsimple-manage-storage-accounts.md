---
title: "管理 StorSimple 存储帐户 | Microsoft 文档"
description: "说明如何使用 StorSimple Manager 的“配置”页添加、编辑、删除或更改存储帐户的安全密钥。"
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: 93207c40-e0eb-489e-8724-59fb94907081
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: v-sharos
ms.openlocfilehash: 4bf8b397d81e12bc48fe3d0ce16d5fff705cedbc
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2017
---
# <a name="use-the-storsimple-manager-service-to-manage-your-storage-account"></a>使用 StorSimple Manager 服务管理存储帐户
> [!NOTE]
> 已弃用 StorSimple 经典门户。 根据弃用计划，StorSimple 设备管理器将自动迁移至新的 Azure 门户。 你将收到有关此移动的电子邮件和门户通知。 另外，此文档也将在稍后停用。 若要查看本文中新的 Azure 门户版本，请转到[使用 StorSimple Manager 服务来管理存储帐户](storsimple-8000-manage-storage-accounts.md)。 任何与移动相关的问题，请参阅[常见问题解答：转移到 Azure 门户](storsimple-8000-move-azure-portal-faq.md)。

## <a name="overview"></a>概述
“配置”页显示可在 StorSimple Manager 服务中创建的所有全局服务参数。 这些参数可应用到所有连接到该服务的设备。具体参数包括：

* 存储帐户 
* 带宽模板 
* 访问控制记录 

本教程说明如何使用“配置”页添加、编辑或删除存储帐户，或轮换存储帐户的安全密钥。

 ![“配置”页](./media/storsimple-manage-storage-accounts/HCS_ConfigureService.png)  

存储帐户包含设备通过云服务提供程序访问存储帐户时使用的凭据。 对于 Microsoft Azure 存储帐户，这些是帐户名和主访问密钥等凭据。 

在“配置”页上，为计费订阅创建的所有存储帐户以表格格式显示，其中包含以下信息：

* **名称** – 创建帐户时分配给帐户的唯一名称。
* **已启用 SSL** – 是否启用 SSL，设备到云的通信将通过安全通道进行。
* **使用者** – 使用存储帐户的卷数。

与可以在“配置”页上执行的存储帐户相关的最常见任务包括：

* 添加存储帐户 
* 编辑存储帐户 
* 删除存储帐户 
* 存储帐户的密钥轮换 

## <a name="types-of-storage-accounts"></a>存储帐户的类型
可在 StorSimple 设备上使用三种类型的存储帐户。

* **自动生成的存储帐户** – 顾名思义，这种存储帐户是首次创建服务时自动生成的。 若要详细了解如何创建此存储帐户，请参阅[部署本地 StorSimple 设备](storsimple-deployment-walkthrough.md)中的[步骤 1：创建新服务](storsimple-deployment-walkthrough-u1.md#step-1-create-a-new-service)。 
* **服务订阅中的存储帐户** – 这是与服务的相同订阅关联的 Azure 存储帐户。 若要了解有关如何创建这些存储帐户的详细信息，请参阅[关于 Azure 存储帐户](../storage/common/storage-create-storage-account.md)。 
* **服务订阅外部的存储帐户** – 这是不与服务关联的、可能在创建服务之前即已存在的 Azure 存储帐户。

## <a name="add-a-storage-account"></a>添加存储帐户
可以通过（使用指定的云服务提供程序）指定与存储帐户链接的唯一友好名称和访问凭据来添加存储帐户。 还可以选择启用安全套接字层 (SSL) 模式，为设备与云之间的网络通信创建一条安全通道。

可为给定的云服务提供程序创建多个帐户。 注意，创建存储帐户后，无法更改云服务提供程序。

保存存储帐户时，服务将尝试与云服务提供程序通信。 此时会对提供的凭据和访问材料进行身份验证。 仅当身份验证成功时，才会创建存储帐户。 如果身份验证失败，则显示相应的错误消息。

StorSimple 也支持 Azure 门户中创建的 Resource Manager 存储帐户。 尝试创建卷容器时，下拉列表中不会显示 Resource Manager 存储帐户选项，只显示在 Azure 经典门户中创建的存储帐户。 需要使用如下所述的添加存储帐户的过程添加 Resource Manager 存储帐户。

> [!NOTE]
> 添加存储帐户的过程根据使用的 StorSimple 软件版本而有所不同。 请务必遵循适用于自己的 StorSimple 版本的相应过程。
> 
> 

[!INCLUDE [add-a-storage-account-update1](../../includes/storsimple-configure-new-storage-account-u1.md)]

[!INCLUDE [add-a-storage-account](../../includes/storsimple-configure-new-storage-account.md)]

## <a name="edit-a-storage-account"></a>编辑存储帐户
可以编辑卷容器使用的存储帐户。 如果编辑当前正在使用的存储帐户，唯一可修改的字段是存储帐户的访问密钥。 可以提供新的存储访问密钥并保存更新的设置。

#### <a name="to-edit-a-storage-account"></a>编辑存储帐户
1. 在服务登录页上，选择服务、双击服务名称，然后单击“配置”。
2. 单击“添加/编辑存储帐户”。
3. 在“添加/编辑存储帐户”对话框中，执行以下操作：
   
   1. 在“存储帐户”的下拉列表中，选择要修改的现有帐户。 这可能还包括首次创建服务时自动生成的存储帐户。
   2. 如果需要，可以修改“启用 SSL 模式”选项。
   3. 还可以选择轮换存储帐户密钥。 有关如何执行密钥轮换的详细信息，请参阅[存储帐户的密钥轮换](#key-rotation-of-storage-accounts)。
   4. 单击勾选图标![勾选图标](./media/storsimple-manage-storage-accounts/HCS_CheckIcon.png)保存设置。 设置会在“配置”页中更新。 单击“保存”保存更新的设置。
      
      ![编辑存储帐户](./media/storsimple-manage-storage-accounts/HCs_AddEditStorageAccount.png)

## <a name="delete-a-storage-account"></a>删除存储帐户
> [!IMPORTANT]
> 仅当存储帐户未被卷容器使用时才能将其删除。 如果存储帐户正被卷容器使用，应先删除卷容器，再删除关联的存储帐户。
> 
> 

#### <a name="to-delete-a-storage-account"></a>删除存储帐户
1. 在 StorSimple Manager 服务登录页上，选择服务、双击服务名称，然后单击“配置”。
2. 在存储帐户的表格列表中，将鼠标悬停在想要删除的帐户上。
3. 该存储帐户的最右侧列中会显示一个删除图标 (**x**)。 单击 **x** 图标即可删除凭据。
4. 系统提示确认时，请单击“是”继续删除。 表格列表随即会更新以反映更改。

## <a name="key-rotation-of-storage-accounts"></a>存储帐户的密钥轮换
出于安全原因，数据中心经常要求密钥轮换。 

> [!NOTE]
> 以下密钥轮换信息和过程仅适用于 Microsoft Azure 存储帐户。 如果使用其他云服务提供程序，可以通过该提供程序的仪表板管理存储帐户密钥。
> 
> 

每个 Microsoft Azure 订阅可以有一个或多个关联的存储帐户。 对这些帐户的访问由订阅以及每个存储帐户的访问密钥控制。 

创建存储帐户时，Microsoft Azure 将生成两个 512 位存储访问密钥，用于在用户访问该存储帐户时对其进行身份验证。 如果有两个存储访问密钥，则可以重新生成密钥，且无需中断存储服务或者对该服务的访问。 当前正在使用的密钥是*主*密钥，备份密钥称为*辅助*密钥。 当 Microsoft Azure StorSimple 设备访问云存储服务提供程序时，必须提供这两个密钥中的一个。

## <a name="what-is-key-rotation"></a>什么是密钥轮换？
通常，应用程序只使用一个密钥来访问数据。 在特定的一段时间后，可以让应用程序切换为使用第二个密钥。 将应用程序切换到第二个密钥后，将停用第一个密钥，并生成新密钥。 以这种方式使用两个密钥可让应用程序在不引发任何停机的情况下访问数据。

存储帐户密钥始终以加密形式存储在服务中。务。 但是，可以通过 StorSimple Manager 服务重置这些密钥。 服务可以获取同一个订阅中所有存储帐户的主密钥和辅助密钥，包括在存储服务中创建的帐户，以及首次创建 StorSimple Manager 服务时生成的默认存储帐户。 StorSimple Manager 服务始终从 Azure 经典门户中获取这些密钥，然后将它们以加密形式存储。

## <a name="rotation-workflow"></a>轮换工作流
Microsoft Azure 管理员可以通过直接访问存储帐户中重新生成或更改主密钥或辅助密钥（通过 Microsoft Azure 存储服务）。 StorSimple Manager 服务无法自动看到此更改。

为了告知 StorSimple Manager 服务发生了这种更改，需要访问 StorSimple Manager 服务，访问存储帐户，并同步主密钥或辅助密钥（具体取决于更改了哪一个）。 然后，服务将获取最新密钥，加密密钥，并将其发送到设备。

#### <a name="to-synchronize-keys-for-storage-accounts-in-the-same-subscription-as-the-service-azure-only"></a>同步与服务相同的订阅中存储帐户的密钥（仅限 Azure）
1. 在“服务”页上，单击“配置”选项卡。
2. 单击“添加/编辑存储帐户”。
3. 在对话框中执行以下操作：
   
   1. 选择要为其同步密钥的存储帐户。 显示的存储帐户密钥已加密。
   2. 根据前面在 Microsoft Azure 存储服务中更改了哪个密钥，需要在 StorSimple Manager 服务中更新该密钥。 如果更改（重新生成）了主访问密钥，请单击“同步主密钥”。 如果更改了辅助密钥，请单击“同步辅助密钥”。
      
      ![同步密钥](./media/storsimple-manage-storage-accounts/HCS_KeyRotationStorageAccountSameSubscriptionAsService.png)

#### <a name="to-synchronize-keys-for-storage-accounts-outside-of-the-service-subscription"></a>同步服务订阅外部的存储帐户的密钥
1. 在“服务”页上，单击“配置”选项卡。
2. 单击“添加/编辑存储帐户”。
3. 在对话框中执行以下操作：
   
   1. 选择要更新其访问密钥的存储帐户。
   2. 需要更新 StorSimple Manager 服务中的存储访问密钥。 在此情况下，可以看到该存储访问密钥。 在“存储帐户访问密钥”框中输入新密钥。 
   3. 保存所做更改。 存储帐户访问密钥现在应已更新。

## <a name="next-steps"></a>后续步骤
* 详细了解 [StorSimple 安全性](storsimple-security.md)。
* 了解有关如何[使用 StorSimple Manager 服务管理 StorSimple 设备](storsimple-manager-service-administration.md)的详细信息。

