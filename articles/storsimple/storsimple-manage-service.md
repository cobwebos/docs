---
title: "部署 StorSimple Manager 服务 | Microsoft Docs"
description: "介绍了如何在 Azure 经典门户中创建和删除 StorSimple Manager 服务，并介绍了如何管理服务注册密钥。"
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: bc1d5650-275c-42ed-bc77-cdb596f85943
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/03/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ee22a31e9c0ec23d9b042dc894cafe0fc346e742
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2017
---
# <a name="deploy-the-storsimple-manager-service-in-the-azure-classic-portal"></a>在 Azure 经典门户中部署 StorSimple Manager 服务
> [!NOTE]
> 已弃用 StorSimple 经典门户。 根据弃用计划，StorSimple 设备管理器将自动迁移至新的 Azure 门户。 你将收到有关此移动的电子邮件和门户通知。 另外，此文档也将在稍后停用。 若要查看本文中新的 Azure 门户版本，请转到[在 Azure 经典门户中部署 StorSimple Manager 服务](storsimple-8000-manage-service.md)。 任何与移动相关的问题，请参阅[常见问题解答：转移到 Azure 门户](storsimple-8000-move-azure-portal-faq.md)。


## <a name="overview"></a>概述
StorSimple Manager 服务在 Microsoft Azure 中运行并连接到多台 StorSimple 设备。 在创建该服务后，可以使用它从在浏览器中运行的 Microsoft Azure 经典门户中管理设备。 这使得可以从单个中央位置监视连接到 StorSimple Manager 服务的所有设备，因此最大限度地降低了管理负担。

StorSimple Manager 登陆页会列出可以用来管理 StorSimple 存储设备的所有 StorSimple Manager 服务。 对于每项 StorSimple Manager 服务，StorSimple Manager 页上提供了以下信息：

* **名称** – 在创建 StorSimple Manager 服务时为其分配的名称。 **在创建服务后无法更改服务名称。这也适用于其他实体，例如，无法在 Azure 经典门户中重命名的设备、卷、卷容器和备份策略。**
* **状态** – 服务的状态，可以是**活动**、**正在创建**或**联机**。
* **位置** – 会在其中部署 StorSimple 设备的地理位置。
* **订阅** – 与服务关联的计费订阅。

可以通过 StorSimple Manager 页执行的常见任务包括：

* 创建服务
* 删除服务
* 获取服务注册密钥
* 重新生成服务注册密钥

本教程介绍了如何执行其中每个任务。

## <a name="create-a-service"></a>创建服务
如果要部署 StorSimple 设备，可以使用“快速创建”选项创建 StorSimple Manager 服务。 若要创建服务，需要具有以下项：

* 一个具有企业协议的订阅
* 一个活动的 Microsoft Azure 存储帐户
* 用于访问管理的帐单信息

在创建服务时，还可以选择生成默认存储帐户。

单个服务可以管理多台设备。 但是，一台设备不能跨越多个服务。 大型企业可能具有多个服务实例，这些服务实例使用不同的订阅、组织甚至不同的部署位置进行工作。 请注意，需要隔离 StorSimple Manager 服务的各个实例来管理 StorSimple 8000 系列设备和 StorSimple Virtual Array。

> [!IMPORTANT] 
> 如果未使用的服务创建于 2016 年 8 月之前（在此资源上未执行设备操作），将无法通过 Azure 门户或 Azure 经典门户对其进行管理。 建议在 Azure 门户中创建新的服务。

执行以下步骤，创建服务。

[!INCLUDE [storsimple-create-new-service](../../includes/storsimple-create-new-service.md)]

## <a name="delete-a-service"></a>删除服务
删除服务之前，请确保没有连接的设备正在使用它。 如果服务正在使用中，停用连接的设备。 停用操作将断开设备与服务之间的连接，但将设备数据保留在云中。

> [!IMPORTANT] 
> 删除服务后，该操作不可逆。 使用该服务的任何设备都需要恢复出厂设置，才能与其他服务一起使用。 在这种情况下，设备上的本地数据以及配置都将丢失。

可以执行以下步骤来删除服务。

### <a name="to-delete-a-service"></a>删除服务
1. 在“StorSimple Manager服务”页上，选择要删除的服务。
2. 单击页面底部的“删除”。
3. 在确认通知中单击“是”。 可能需要花费几分钟时间才能删除服务。

## <a name="get-the-service-registration-key"></a>获取服务注册密钥
成功创建服务后，需要为 StorSimple 设备注册该服务。 若要注册第一台 StorSimple 设备，需要使用服务注册密钥。 若要向现有 StorSimple 服务注册额外的设备，需要使用注册密钥和服务数据加密密钥（后者是在注册期间在第一台设备上生成的）。 有关服务数据加密密钥的详细信息，请参阅 [StorSimple 安全性](storsimple-security.md)。 可以通过访问“服务”页上的“注册密钥”来获取注册密钥。

可以执行以下步骤来获取服务注册密钥。

[!INCLUDE [storsimple-get-service-registration-key](../../includes/storsimple-get-service-registration-key.md)]

将服务注册密钥保存在安全位置。 要为其他设备注册此服务，需要使用此密钥以及服务数据加密密钥。 获取服务注册密钥后，需要通过 StorSimple 接口的 Windows PowerShell 配置设备。

有关如何使用此注册密钥的详细信息，请参阅[步骤 3：通过 Windows PowerShell for StorSimple 配置和注册设备](storsimple-deployment-walkthrough.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)。

## <a name="regenerate-the-service-registration-key"></a>重新生成服务注册密钥
如果系统要求执行密钥轮换或者如果服务管理员列表发生更改，需要重新生成服务注册密钥。 重新生成密钥时，新密钥仅用于注册后续设备。 已注册的设备不受此过程影响。

执行以下步骤，重新生成服务注册密钥。

### <a name="to-regenerate-the-service-registration-key"></a>重新生成服务注册密钥
1. 在“StorSimple Manager 服务”页上，单击“注册密钥”。
2. 在“服务注册密钥”对话框中，单击“重新生成”。
3. 将显示确认消息。 单击“确定”以继续执行重新生成操作。
4. 将显示新的服务注册密钥。
5. 复制此密钥，并保存密钥以供为任何新设备注册此服务。
6. 单击选中图标  ![选中图标](./media/storsimple-manage-service/HCS_CheckIcon.png) 关闭此对话框。

## <a name="next-steps"></a>后续步骤
* 详细了解 [StorSimple 部署过程](storsimple-deployment-walkthrough-u2.md)。
* 详细了解如何[管理 StorSimple 存储帐户](storsimple-manage-storage-accounts.md)。
* 详细了解如何[使用 StorSimple Manager 服务管理 StorSimple 设备](storsimple-manager-service-administration.md)。
