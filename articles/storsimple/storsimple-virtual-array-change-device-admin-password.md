---
title: "更改 StorSimple 虚拟阵列设备管理员密码 | Microsoft 文档"
description: "介绍如何使用 Azure 门户或 StorSimple 虚拟阵列 Web UI 更改设备管理员密码。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 11490814-d9fd-4dc7-9c3b-55dd2c23eaf1
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: f37fd4de0d6895f55508d1a51d981a047be15031
ms.openlocfilehash: 260a23003d705e6598da8c51bb5a96f2539a0014
ms.lasthandoff: 03/01/2017

---
# <a name="change-the-storsimple-virtual-array-device-administrator-password-via-storsimple-device-manager"></a>通过 StorSimple Device Manager 更改 StorSimple 虚拟阵列设备管理员密码

## <a name="overview"></a>概述

使用 Windows PowerShell 接口访问 StorSimple 虚拟阵列时，需要输入设备管理员密码。 当首次预配和启动 StorSimple 设备时，默认密码为 *Password1*。 为了保证数据的安全性，默认密码在你首次登录时过期，你需要更改此密码。

在生产环境中部署设备后，还可以随时使用本地 Web UI 或 Azure 门户更改设备管理员密码。 本文介绍了上述每个过程。

 ![“设备”边栏选项卡](./media/storsimple-virtual-array-change-device-admin-password/ova-devices-blade.png)

## <a name="use-the-azure-portal-to-change-the-password"></a>使用 Azure 门户更改密码

执行以下步骤可通过 Azure 门户更改设备管理员密码。

#### <a name="to-change-the-device-administrator-password-via-the-azure-portal"></a>通过 Azure 门户更改设备管理员密码

1. 在服务登陆页上，选择你的服务、双击服务名称，然后在“管理”部分中单击“设备”。 这将打开“设备”边栏选项卡，其中列出了所有 StorSimple 虚拟阵列设备。

2. 在“设备”边栏选项卡中，双击需要更改密码的设备。

3. 在设备的“设置”边栏选项卡中，单击“安全性”。

4. 在“安全设置”边栏选项卡中，执行以下操作：
   
   1. 向下滚动到“设备管理员密码”部分。 提供包含 8 至 15 个字符的管理员密码。
   2. 确认该密码。
   3. 单击边栏选项卡顶部的“保存”。

现在将更新设备管理员密码。 可以使用此修改后的密码在本地访问设备。

![“安全设置”边栏选项卡](./media/storsimple-virtual-array-change-device-admin-password/ova-change-device-pwd.png)

## <a name="use-the-local-web-ui-to-change-the-password"></a>使用本地 Web UI 更改密码

可以通过本地 Web UI 执行以下步骤来更改设备管理员密码。

#### <a name="to-change-the-device-administrator-password-via-the-local-web-ui"></a>通过本地 Web UI 更改设备管理员密码

1. 在本地 Web UI 中，针对你的设备单击“维护” > “密码更改”。
   
    ![更改 password1](./media/storsimple-virtual-array-change-device-admin-password/image40.png)
2. 输入**当前密码**。
3. 提供**新密码**。 此密码的长度必须至少为 8 个字符。 它必须包含以下 4 项中的 3 项：小写字母、大写字母、数字和特殊字符。
   
    请注意，你的密码不能与最近用过的 24 个密码相同。
4. 再次输入密码进行确认。
   
    ![更改 password2](./media/storsimple-virtual-array-change-device-admin-password/image41.png)
5. 在页面的底部，单击“应用”。 现在将应用新密码。 如果密码更改不成功，将会看到以下错误：
   
    ![密码错误](./media/storsimple-virtual-array-change-device-admin-password/image42.png)
   
    成功更新密码后，你将收到通知。 然后可以使用此修改后的密码在本地访问设备。


## <a name="next-steps"></a>后续步骤
了解如何[管理你的 StorSimple Virtual Array](storsimple-ova-web-ui-admin.md)。


