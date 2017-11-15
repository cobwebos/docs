---
title: "通过 StorSimple 设备管理器更改密码 | Microsoft 文档"
description: "介绍如何使用 StorSimple Manager 服务更改 StorSimple Snapshot Manager 和设备管理员的密码。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: f178509c-f4e1-48a8-90b2-d4ad050eeb30
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/02/2017
ms.author: alkohli
ms.openlocfilehash: 843d0a4bcc1807a6b1995d067e0f9e572307775d
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2017
---
# <a name="use-the-storsimple-manager-service-to-change-your-storsimple-passwords"></a>使用 StorSimple Manager 服务更改 StorSimple 密码
> [!NOTE]
> 已弃用 StorSimple 经典门户。 按弃用计划，StorSimple 设备管理器将自动移至新的 Azure 门户。 会收到有关此移动的电子邮件和门户通知。 此文档亦将在稍后停用。 若要查看本文的适用于新 Azure 门户的版本，请转到[使用 StorSimple Manager 服务更改 StorSimple 密码](storsimple-8000-change-passwords.md)。 如有任何与移动相关的问题，请参阅[常见问题解答：移动到 Azure 门户](storsimple-8000-move-azure-portal-faq.md)。

## <a name="overview"></a>概述
Azure 经典门户“配置”页包含用户可以在由 StorSimple Manager 服务管理的 StorSimple 设备上重新配置的所有设备参数。 本教程介绍如何使用“配置”页更改设备管理员或 StorSimple Snapshot Manager 的密码。

## <a name="change-the-device-administrator-password"></a>更改设备管理员密码
使用 Windows PowerShell 接口访问 StorSimple 设备时，需要输入设备管理员密码。 向服务注册第一个 StorSimple 设备时，此接口的默认密码为 *Password1*。 为了确保数据的安全，必须在注册过程结束时更改此密码。 如果不更改此密码，则无法退出注册过程。 有关详细信息，请参阅[步骤 3︰通过 Windows PowerShell for StorSimple 配置和注册设备](storsimple-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)。

然后，用户可以通过 Azure 经典门户更改首次在注册过程中通过 Windows PowerShell 界面设置的密码。 执行以下步骤，更改设备管理员密码。

#### <a name="to-change-the-device-administrator-password"></a>更改设备管理员密码
1. 在经典门户中，针对设备单击“设备” > “配置”。
2. 向下滚动到“设备管理员密码”部分。 提供包含 8 至 15 个字符的管理员密码。 此密码必须是下述 3 项或 3 项以上要求的组合：大写字符、小写字符、数字字符、特殊字符。
3. 确认该密码。
4. 单击页面底部的“保存”  。

现在应该会更新设备管理员密码。 此修改过的密码可用于访问 Windows PowerShell 界面。

## <a name="change-the-storsimple-snapshot-manager-password"></a>更改 StorSimple Snapshot Manager 密码
StorSimple Snapshot Manager 软件驻留在 Windows 主机上，可让管理员以本地和云快照的形式管理 StorSimple 设备的备份。

在 StorSimple Snapshot Manager 中配置设备时，系统会提示用户提供设备 IP 地址和密码对存储设备进行身份验证。 此密码是通过 Windows PowerShell 界面首次配置的。 有关详细信息，请参阅[步骤 3︰通过 Windows PowerShell for StorSimple 配置和注册设备](storsimple-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)。

然后，用户可以通过经典门户更改在注册过程中通过 Windows PowerShell 界面首次设置的密码。 执行以下步骤，更改 StorSimple Snapshot Manager 密码。

#### <a name="to-change-the-storsimple-snapshot-manager-password"></a>更改 StorSimple Snapshot Manager 密码
1. 在经典门户中，针对设备单击“设备” > “配置”。
2. 向下滚动到 **StorSimple Snapshot Manager** 部分。 输入包含 14 或 15 个字符的密码。 请确保该密码包含 3 项或 3 项以上下述要求的组合：大写字符、小写字符、数字字符、特殊字符。
3. 确认该密码。
4. 单击页面底部的“保存”  。

StorSimple Snapshot Manager 密码现在应已更新。

## <a name="next-steps"></a>后续步骤
* 详细了解 [StorSimple 安全性](storsimple-security.md)。
* 详细了解如何[修改设备配置](storsimple-modify-device-config.md)。
* 了解有关如何[使用 StorSimple Manager 服务管理 StorSimple 设备](storsimple-manager-service-administration.md)的详细信息。

