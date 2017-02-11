---
title: "更改 StorSimple 虚拟设备管理员密码 | Microsoft Docs"
description: "介绍了如何使用 Azure 经典门户或 StorSimple Virtual Array Web UI 更改设备管理员密码。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 52acde45-ae29-4edb-9377-46918ab7eef8
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/17/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 85fe4dae6abffebd9d68f3b4110dd45338f35bef


---
# <a name="change-the-storsimple-virtual-array-device-administrator-password"></a>更改 StorSimple Virtual Array 设备管理员密码
## <a name="overview"></a>概述
使用 Windows PowerShell 接口访问 StorSimple 虚拟设备时，需要输入设备管理员密码。 当首次预配和启动 StorSimple 设备时，默认密码为 *Password1*。 为了保证数据的安全性，默认密码在你首次登录时过期，你需要更改此密码。

在生产环境中部署设备后，还可以随时使用本地 Web UI 或 Azure 经典门户更改管理员密码。 本文介绍了上述每个过程。

## <a name="use-the-azure-classic-portal-to-change-the-password"></a>使用 Azure 经典门户更改密码
可以通过 Azure 经典门户执行以下步骤来更改设备管理员密码。

#### <a name="to-change-the-device-administrator-password-via-the-azure-classic-portal"></a>通过 Azure 经典门户更改设备管理员密码
1. 在该门户中，针对你的设备单击“设备” > “配置”。
2. 向下滚动到“设备管理员密码”部分。 提供包含 8 至 15 个字符的管理员密码。 此密码必须是大写字母、小写字母、数字和特殊字符的组合。
3. 确认该密码。
4. 单击页面底部的“保存”。

设备管理员密码现在应该已更新。 可以使用此修改后的密码在本地访问设备。

## <a name="use-the-storsimple-virtual-array-web-ui-to-change-the-password"></a>使用 StorSimple Virtual Array Web UI 更改密码
可以通过本地 Web UI 执行以下步骤来更改设备管理员密码。

#### <a name="to-change-the-device-administrator-password-via-the-local-web-ui"></a>通过本地 Web UI 更改设备管理员密码
1. 在本地 Web UI 中，针对你的设备单击“维护” > “密码更改”。
   
    ![更改 password1](./media/storsimple-ova-change-device-admin-password/image40.png)
2. 输入**当前密码**。
3. 提供**新密码**。 此密码的长度必须至少为 8 个字符。 它必须包含以下 4 项中的 3 项：小写字母、大写字母、数字和特殊字符。
   
    请注意，你的密码不能与最近用过的 24 个密码相同。
4. 再次输入密码进行确认。
   
    ![更改 password2](./media/storsimple-ova-change-device-admin-password/image41.png)
5. 在页面的底部，单击“应用”。 随后将应用新密码。 如果密码更改不成功，将会看到以下错误。
   
    ![密码错误](./media/storsimple-ova-change-device-admin-password/image42.png)
   
    成功更新密码后，你将收到通知。 然后可以使用此修改后的密码在本地访问设备。

## <a name="next-steps"></a>后续步骤
详细了解如何[管理 StorSimple Virtual Array](storsimple-ova-web-ui-admin.md)。




<!--HONumber=Nov16_HO3-->


