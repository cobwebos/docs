---
title: 部署 StorSimple 设备管理器服务 | Microsoft 文档
description: 说明如何在 Azure 门户中创建和删除 StorSimple Device Manager 服务，并介绍了如何管理服务注册密钥。
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 28499494-8c4d-4a7f-9d44-94b2b8a93c93
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2016
ms.author: alkohli
ms.openlocfilehash: 1881a0625b107ae1a90e5b772f5296a4d728973d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
ms.locfileid: "23110295"
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-virtual-array"></a>为 StorSimple 虚拟阵列部署 StorSimple Device Manager 服务
## <a name="overview"></a>概述

StorSimple Device Manager 服务在 Microsoft Azure 中运行并连接到多个 StorSimple 设备。 创建该服务后，可以使用它从浏览器中运行的 Microsoft Azure 门户管理设备。 这使得可以从单个中央位置监视连接到 StorSimple Device Manager 服务的所有设备，因此最大限度地减少了管理负担。

与 StorSimple Device Manager 服务相关的常见任务包括：

* 创建服务
* 删除服务
* 获取服务注册密钥
* 重新生成服务注册密钥

本教程介绍了如何执行上述每个任务。 本文中包含的信息仅适用于 StorSimple Virtual Array。 有关 StorSimple 8000 系列的详细信息，请转到[部署 StorSimple Manager 服务](storsimple-manage-service.md)。

## <a name="create-a-service"></a>创建服务

若要创建服务，需要具有以下项：

* 一个具有企业协议的订阅
* 一个活动的 Microsoft Azure 存储帐户
* 用于访问管理的帐单信息

在创建服务时，还可以选择生成存储帐户。

单个服务可以管理多台设备。 但是，一台设备不能跨越多个服务。 大型企业可以具有多个服务实例，以便使用不同的订阅、组织，甚至是部署位置。

> [!NOTE]
> 需要隔离 StorSimple 设备管理器服务的实例，管理 StorSimple 8000 系列设备和 StorSimple 虚拟阵列。


执行以下步骤，创建服务。

[!INCLUDE [storsimple-virtual-array-create-new-service](../../includes/storsimple-virtual-array-create-new-service.md)]

## <a name="delete-a-service"></a>删除服务

删除服务之前，请确保没有连接的设备正在使用它。 如果服务正在使用中，停用连接的设备。 停用操作将断开设备与服务之间的连接，但将设备数据保留在云中。

> [!IMPORTANT]
> 删除服务后，该操作不可逆。 使用该服务的任何设备都需要恢复出厂设置，才能与其他服务一起使用。 在这种情况下，设备上的本地数据以及配置都将丢失。
 

可以执行以下步骤来删除服务。

#### <a name="to-delete-a-service"></a>删除服务

1. 转到“所有资源”。 搜索 StorSimple Device Manager 服务。 选择要删除的服务。
   
    ![选择要删除的服务](./media/storsimple-virtual-array-manage-service/deleteservice2.png)
2. 转到服务仪表板，确保没有设备连接到该服务。 如果没有设备注册到此服务，则还将看到指示此内容的横幅消息。 单击“删除” 。
   
    ![删除服务](./media/storsimple-virtual-array-manage-service/deleteservice3.png)

3. 当系统提示确认时，请单击确认通知中的“是”。 
   
    ![确认服务删除](./media/storsimple-virtual-array-manage-service/deleteservice4.png)
4. 可能需要花费几分钟时间才能删除服务。 成功删除服务后，会收到通知。
   
    ![成功删除服务](./media/storsimple-virtual-array-manage-service/deleteservice6.png)

将刷新服务列表。

 ![已更新的服务列表](./media/storsimple-virtual-array-manage-service/deleteservice7.png)

## <a name="get-the-service-registration-key"></a>获取服务注册密钥
成功创建服务后，需要为 StorSimple 设备注册该服务。 若要注册第一台 StorSimple 设备，需要使用服务注册密钥。 若要向现有 StorSimple 服务注册额外的设备，需要使用注册密钥和服务数据加密密钥（后者是在注册期间在第一台设备上生成的）。 有关服务数据加密密钥的详细信息，请参阅 [StorSimple 安全性](storsimple-security.md)。 可以通过访问服务的“密钥”边栏选项卡来获取注册密钥。

执行以下步骤，获取服务注册密钥。

#### <a name="to-get-the-service-registration-key"></a>获取服务注册密钥
1. 在“StorSimple Device Manager”边栏选项卡中，转到“管理”&gt;“密钥”。
   
   ![“密钥”边栏选项卡](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. 在“密钥”边栏选项卡中，会显示服务注册密钥。 使用“复制”图标复制该注册密钥。 

将服务注册密钥保存在安全位置。 要为其他设备注册此服务，需要使用此密钥以及服务数据加密密钥。 获取服务注册密钥后，需要通过 StorSimple 接口的 Windows PowerShell 配置设备。

## <a name="regenerate-the-service-registration-key"></a>重新生成服务注册密钥
如果系统要求执行密钥轮换或者如果服务管理员列表发生更改，需要重新生成服务注册密钥。 重新生成密钥时，新密钥仅用于注册后续设备。 已注册的设备不受此过程影响。

执行以下步骤，重新生成服务注册密钥。

#### <a name="to-regenerate-the-service-registration-key"></a>重新生成服务注册密钥
1. 在“StorSimple Device Manager”边栏选项卡中，转到“管理”&gt;“密钥”。
   
   ![“密钥”边栏选项卡](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. 在“密钥”边栏选项卡中，单击“重新生成”。
   
   ![单击“重新生成”](./media/storsimple-virtual-array-manage-service/getregkey5.png)
3. 在“重新生成服务注册密钥”边栏选项卡中，查看重新生成密钥时所需的操作。 后续将注册到此服务的所有设备将使用新的注册密钥。 单击“重新生成”以确认。 完成注册后，将收到通知。
   
   ![确认重新生成密钥](./media/storsimple-virtual-array-manage-service/getregkey3.png)
4. 将显示新的服务注册密钥。
   
    ![确认重新生成密钥](./media/storsimple-virtual-array-manage-service/getregkey4.png)
   
   复制此密钥，并保存密钥以供为任何新设备注册此服务。

## <a name="next-steps"></a>后续步骤
* 了解如何[开始](storsimple-virtual-array-deploy1-portal-prep.md)使用 StorSimple 虚拟阵列。
* 了解如何[管理 StorSimple 设备](storsimple-ova-web-ui-admin.md)。

