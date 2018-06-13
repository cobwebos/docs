---
title: 更改 StorSimple 密码 | Microsoft Docs
description: 介绍如何使用 StorSimple 设备管理器服务更改 StorSimple Snapshot Manager 和设备管理员的密码。
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: 7762f8499c67672f0a2ffed99e98baea4c940fa0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
ms.locfileid: "23108165"
---
# <a name="use-the-storsimple-device-manager-service-to-change-your-storsimple-passwords"></a>使用 StorSimple 设备管理器服务更改 StorSimple 密码

## <a name="overview"></a>概述
Azure 门户中的“设备设置”选项包含可以在由 StorSimple 设备管理器服务管理的 StorSimple 设备上重新配置的所有设备参数。 本教程介绍如何使用“设备设置”下面的“安全性”选项更改设备管理员或 StorSimple Snapshot Manager 的密码。

## <a name="change-the-device-administrator-password"></a>更改设备管理员密码
使用 Windows PowerShell 接口访问 StorSimple 设备时，需要输入设备管理员密码。 向服务注册第一个 StorSimple 设备时，此接口的默认密码为 *Password1*。 为了确保数据的安全，必须在注册过程结束时更改此密码。 如果不更改此密码，则无法退出注册过程。 有关详细信息，请参阅[步骤 3︰通过 Windows PowerShell for StorSimple 配置和注册设备](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)。

以后可以通过 Azure 门户更改首次在注册过程中通过 Windows PowerShell 界面设置的密码。 执行以下步骤，更改设备管理员密码。

#### <a name="to-change-the-device-administrator-password"></a>更改设备管理员密码
1. 转到 StorSimple Device Manager 服务并单击“设备”。

2. 在设备的表格列表中，选择并单击要更改其密码的设备。

    ![](./media/storsimple-8000-change-passwords/changepwd1.png)

3. 在“设置”边栏选项卡中，转到“设备设置”>“安全性”。

    ![](./media/storsimple-8000-change-passwords/changepwd2.png)

4. 在“安全设置”边栏选项卡中，单击“密码”更改设备管理员密码。

    ![](./media/storsimple-8000-change-passwords/changepwd3.png)

5. 在“密码”边栏选项卡中，提供包含 8 至 15 个字符的管理员密码。 此密码必须是下述 3 项或 3 项以上要求的组合：大写字符、小写字符、数字字符、特殊字符。

6. 确认该密码。

    ![](./media/storsimple-8000-change-passwords/changepwd4.png)

7. 单击“保存”，出现确认提示时，单击“是”。

    ![](./media/storsimple-8000-change-passwords/changepwd6.png)

现在应该会更新设备管理员密码。 此修改过的密码可用于访问 Windows PowerShell 界面。

## <a name="set-the-storsimple-snapshot-manager-password"></a>设置 StorSimple Snapshot Manager 密码
StorSimple Snapshot Manager 软件驻留在 Windows 主机上，可让管理员以本地和云快照的形式管理 StorSimple 设备的备份。

在 StorSimple Snapshot Manager 中配置设备时，系统会提示用户提供设备 IP 地址和密码对存储设备进行身份验证。

可以通过 Azure 门户设置或更改 StorSimple Snapshot Manager 的密码。 执行以下步骤设置或更改 StorSimple Snapshot Manager 密码。

#### <a name="to-set-the-storsimple-snapshot-manager-password"></a>设置 StorSimple Snapshot Manager 密码
1. 转到 StorSimple Device Manager 服务并单击“设备”。

2. 在设备的表格列表中，选择并单击要设置或更改其 StorSimple Snapshot Manager 密码的设备。

     ![](./media/storsimple-8000-change-passwords/changepwd1.png)

3. 在“设置”边栏选项卡中，转到“设备设置”>“安全性”。

     ![](./media/storsimple-8000-change-passwords/changepwd2.png)

4. 在“安全设置”边栏选项卡中，单击“密码”设置或更改 StorSimple Snapshot Manager 密码。

     ![](./media/storsimple-8000-change-passwords/changepwd3.png) 

5. 在“密码”边栏选项卡中，输入包含 14 或 15 个字符的密码。 请确保该密码包含 3 项或 3 项以上下述要求的组合：大写字符、小写字符、数字字符、特殊字符。

6. 确认该密码。

     ![](./media/storsimple-8000-change-passwords/changepwd5.png)

7. 单击“保存”，出现确认提示时，单击“是”。

     ![](./media/storsimple-8000-change-passwords/changepwd6.png)

StorSimple Snapshot Manager 密码现在应已更新。

## <a name="next-steps"></a>后续步骤
* 详细了解 [StorSimple 安全性](storsimple-8000-security.md)。
* 详细了解如何[修改设备配置](storsimple-8000-modify-device-config.md)。
* 了解有关如何[使用 StorSimple 设备管理器服务管理 StorSimple 设备](storsimple-8000-manager-service-administration.md)的详细信息。

