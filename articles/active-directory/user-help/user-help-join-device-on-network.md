---
title: 将工作设备加入组织的网络 - Azure Active Directory | Microsoft Docs
description: 了解如何将工作设备加入组织的网络。
services: active-directory
author: eross-msft
manager: mtillman
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: lizross
ms.reviewer: jairoc
ms.openlocfilehash: 34d3c12c83aeac7e92aa019abc38d9c4109883bd
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2018
ms.locfileid: "40180425"
---
# <a name="join-your-work-device-to-your-organizations-network"></a>将工作设备加入组织的网络
将工作专用的 Windows 10 设备加入组织的网络，以便能够访问可能受限的资源。

## <a name="what-happens-when-you-join-your-device"></a>加入设备时会发生什么情况
在将 Windows 10 设备加入组织的网络中时，会发生以下操作：

- Windows 向组织的网络注册设备，使你能够使用个人帐户访问资源。 设备注册后，Windows 将设备加入网络，因此你可以使用组织的用户名和密码来登录并访问受限的资源。

- （可选）根据组织的选择，可能需要通过[多重身份验证](multi-factor-authentication-end-user-first-time.md)或[安全信息](user-help-security-info-overview.md)设置双重验证。

- （可选）根据组织的选择，可能会自动注册移动设备管理，如 Microsoft Intune。 有关在 Microsoft Intune 中注册的详细信息，请参阅[在 Intune 中注册设备](https://docs.microsoft.com/intune-user-help/enroll-your-device-in-intune-all)。

- 用户需要完成登录过程，之后便可使用组织帐户自动登录。

## <a name="to-join-a-brand-new-windows-10-device"></a>加入全新的 Windows 10 设备
如果设备是全新的，并且尚未进行设置，则可以完成 Windows 全新体验 (OOBE) 过程，将设备加入网络。

1. 启动新设备并开始 OOBE 过程。

2. 在“Microsoft 登录”屏幕上，键入工作或学校电子邮件地址。

    ![包含电子邮件地址的登录屏幕](./media/user-help-join-device-on-network/join-device-oobe-signin.png)

3. 在“输入密码”屏幕上，键入密码。

    ![“输入密码”屏幕](./media/user-help-join-device-on-network/join-device-oobe-password.png)

4. 在移动设备上，同意设备，以便它可以访问你的帐户。 

    ![“移动通知”屏幕](./media/user-help-join-device-on-network/join-device-oobe-mobile.png)

5. 完成 OOBE 过程，包括设置隐私设置和设置 Windows Hello（如有必要）。

    设备现已加入组织的网络。

## <a name="to-make-sure-youre-joined"></a>确保你已加入
可以通过查看你的设置确保已加入。

1. 打开“设置”，然后选择“帐户”。

    ![“设置上的帐户”屏幕](./media/user-help-join-device-on-network/join-device-settings-accounts.png)

2. 选择“访问工作单位或学校”，确保可看到如下所示文本：已连接到 <your_organization> Azure AD。

    ![“访问工作单位或学校”屏幕，含已连接的 contoso 帐户](./media/user-help-join-device-on-network/join-device-oobe-verify.png)


## <a name="to-join-an-already-configured-windows-10-device"></a>加入已配置的 Windows 10 设备
如果已拥有设备一段时间并且已设置该设备，可执行以下步骤将设备加入网络。

1. 打开“设置”，然后选择“帐户”。

2. 选择“访问工作单位或学校”，然后选择“连接”。

    ![访问工作单位或学校和连接链接](./media/user-help-join-device-on-network/join-device-access-work-school-connect.png)

3. 在“设置工作或学校帐户”屏幕中，选择“将此设备加入 Azure Active Directory”。

    ![“设置工作或学校帐户”屏幕](./media/user-help-join-device-on-network/join-device-setup-join-aad.png)

4. 在“开始登录”屏幕中，键入电子邮件地址（例如 alain@contoso.com）并选择“下一步”。

    ![“开始登录”屏幕](./media/user-help-join-device-on-network/join-device-setup-get-signed-in.png)

5. 在“输入密码”屏幕中输入密码，然后选择“登录”。

    ![输入密码](./media/user-help-join-device-on-network/join-device-setup-password.png)

6. 在移动设备上，同意设备，以便它可以访问你的帐户。 

    ![“移动通知”屏幕](./media/user-help-join-device-on-network/join-device-setup-mobile.png)

7. 在“确保这是你的组织”屏幕中，查看信息确保正确，然后选择“加入”。

    ![“确保这是你的组织”验证屏幕](./media/user-help-join-device-on-network/join-device-setup-confirm.png)

8. 在“已设置完毕”屏幕中，单击“完成”。

    ![“已设置完毕”屏幕](./media/user-help-join-device-on-network/join-device-setup-finish.png)

## <a name="to-make-sure-youre-joined"></a>确保你已加入
可以通过查看你的设置确保已加入。

1. 打开“设置”，然后选择“帐户”。

    ![“设置上的帐户”屏幕](./media/user-help-join-device-on-network/join-device-settings-accounts.png)

2. 选择“访问工作单位或学校”，确保可看到如下所示文本：已连接到 <your_organization> Azure AD。

    ![“访问工作单位或学校”屏幕，含已连接的 contoso 帐户](./media/user-help-join-device-on-network/join-device-setup-verify.png)

## <a name="next-steps"></a>后续步骤
设备加入组织的网络后，应能够使用工作或学校帐户信息访问所有资源。

- 如果组织希望你能够注册个人设备（例如手机），请参阅[在组织的网络上注册个人设备](user-help-register-device-on-network.md)。

