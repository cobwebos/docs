---
title: 在组织的网络上注册个人设备 - Azure Active Directory | Microsoft Docs
description: 了解如何在组织的网络上注册个人设备，以便可以访问组织的受保护资源。
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: lizross
ms.reviewer: jairoc
ms.openlocfilehash: 1f321ea3a7fffd4d54887fb41aee25b17d391e67
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2019
ms.locfileid: "54825392"
---
# <a name="register-your-personal-device-on-your-organizations-network"></a>在组织的网络上注册个人设备
在组织的网络上注册个人设备（通常为电话或平板电脑）。 设备注册后，将能够访问组织的受限资源。

>[!Note]
>本文使用 Windows 设备进行演示，但还可以注册运行 iOS、Android 或 macOS 的设备。

## <a name="what-happens-when-you-register-your-device"></a>注册设备时会发生什么情况
在组织的网络上注册设备时，将发生以下操作：

- Windows 在组织的网络上注册设备。

- （可选）根据组织的选择，可能需要通过[多重身份验证](multi-factor-authentication-end-user-first-time.md)或[安全信息](user-help-security-info-overview.md)设置双重验证。

- （可选）根据组织的选择，可能会自动注册移动设备管理，如 Microsoft Intune。 有关在 Microsoft Intune 中注册的详细信息，请参阅[在 Intune 中注册设备](https://docs.microsoft.com/intune-user-help/enroll-your-device-in-intune-all)。

- 使用工作或学校帐户的用户名和密码完成登录过程。

## <a name="to-register-your-windows-device"></a>注册 Windows 设备

按照以下步骤在网络上注册个人设备。

1. 打开“设置”，然后选择“帐户”。

    ![“设置上的帐户”屏幕](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. 选择“访问工作单位或学校”，然后从“访问工作单位或学校”屏幕中选择“连接”。

    ![突出显示“访问工作单位或学校”和“连接”选项的屏幕](./media/user-help-register-device-on-network/register-device-access-work-school-connect.png)

3. 在“添加工作或学校帐户”屏幕上，键入工作或学校帐户的电子邮件地址，然后选择“下一步”。 例如，alain@contoso.com。

4. 登录工作或学校帐户，然后选择“登录”。

5. 完成注册过程的其余部分，包括审批你的身份验证请求（如果你使用双重验证）和设置 Windows Hello（如有必要）。

## <a name="to-verify-that-youre-registered"></a>验证是否已注册
可以通过查看你的设置确保已注册。

1. 打开“设置”，然后选择“帐户”。

    ![“设置上的帐户”屏幕](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. 选择“访问工作单位或学校”，确保能看到工作或学校帐户。

    ![“访问工作单位或学校”屏幕，含已连接的 contoso 帐户](./media/user-help-register-device-on-network/register-device-setup-verify.png)

## <a name="next-steps"></a>后续步骤
将个人设备注册到组织的网络中后，应该能够访问大多数资源。

- 如果组织希望你加入工作设备，请参阅[将工作设备加入组织的网络](user-help-join-device-on-network.md)。



