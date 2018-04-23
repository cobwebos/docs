---
title: Windows 10 登录屏幕中的 Azure AD SSPR | Microsoft Docs
description: 配置 Windows 10 登录屏幕的“Azure AD 密码重置”和“我忘记了自己的 PIN”
services: active-directory
keywords: ''
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/11/2018
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: a85cb6aea1027d657d38007a397321de10e16d07
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2018
---
# <a name="azure-ad-password-reset-from-the-login-screen"></a>登录屏幕中的“Azure AD 密码重置”

你已经部署了 Azure AD 自助密码重置 (SSPR)，但你的用户仍会在忘记密码时呼叫帮助台。 他们之所以呼叫帮助台，是因为不能通过 Web 浏览器来访问 SSPR。

安装新的 Windows 10 Fall Creators Update 后，其设备加入了 Azure AD 的用户可以在其登录屏幕上看到“重置密码”链接。 单击该链接后，用户就会体验到熟悉的与以前相同的自助密码重置 (SSPR)。

若要使用户能够从 Windows 10 登录屏幕重置其 Azure AD 密码，需满足以下要求：

* Windows 10 版本 1709，或者[已加入 Azure AD](../device-management-azure-portal.md) 的更新客户端。
* 必须启用 Azure AD 自助密码重置。
* 配置和部署设置，通过下述某个方法启用“重置密码”链接：
   * [Intune 设备配置文件](tutorial-sspr-windows.md#configure-reset-password-link-using-intune)
   * [注册表项](tutorial-sspr-windows.md#configure-reset-password-link-using-the-registry)

## <a name="configure-reset-password-link-using-intune"></a>使用 Intune 配置“重置密码”链接

### <a name="create-a-device-configuration-policy-in-intune"></a>在 Intune 中创建设备配置策略

1. 登录到 [Azure 门户](https://portal.azure.com)，单击“Intune”。
2. 转到“设备配置” > “配置文件” > “创建配置文件”，创建新的设备配置文件
   * 为配置文件提供一个有意义的名称
   * （可选）提供对配置文件的有意义说明
   * 平台：**Windows 10 及更高版本**
   * 配置文件类型：**自定义**

   ![CreateProfile][CreateProfile]

3. 配置**设置**
   * **添加**以下 OMA-URI 设置，启用“重置密码”链接
      * 提供一个有意义的名称，说明该设置执行的操作
      * （可选）提供对设置的有意义说明
      * **OMA-URI** 设置为 `./Vendor/MSFT/Policy/Config/Authentication/AllowAadPasswordReset`
      * **数据类型**设置为**整数**
      * **值**设置为 **1**
      * 单击 **“确定”**
   * 单击 **“确定”**
4. 单击“创建” 

### <a name="assign-a-device-configuration-policy-in-intune"></a>在 Intune 中分配设备配置策略

#### <a name="create-a-group-to-apply-device-configuration-policy-to"></a>创建要向其应用设备配置策略的组

1. 登录到 [Azure 门户](https://portal.azure.com)，单击“Azure Active Directory”。
2. 浏览到“用户和组” > “所有组” > “新建组”
3. 为组提供一个名称，然后在“成员身份类型”下选择“已分配”。 
   * 在“成员”下选择加入 Azure AD 的 Windows 10 设备，需向该设备应用策略。
   * 单击“选择”
4. 单击“创建” 

有关如何创建组的详细信息，可参阅[使用 Azure Active Directory 组管理对资源的访问权限](../active-directory-manage-groups.md)一文。

#### <a name="assign-device-configuration-policy-to-device-group"></a>将设备配置策略分配到设备组

1. 登录到 [Azure 门户](https://portal.azure.com)，单击“Intune”。
2. 查找此前创建的设备配置文件，方法是转到“设备配置” > “配置文件”，然后单击此前创建的配置文件
3. 将配置文件分配给一组设备 
   * 在“包括” > “选择要包括的组”下单击“分配”
   * 选择以前创建的组，然后单击“选择”
   * 单击“保存”

   ![分配][Assignment]

现在已使用 Intune 创建和分配设备配置策略，目的是启用登录屏幕上的“重置密码”链接。

## <a name="configure-reset-password-link-using-the-registry"></a>使用注册表配置“重置密码”链接

建议只使用此方法来测试设置更改。

1. 使用管理凭据登录到已加入 Azure AD 的设备
2. 以管理员身份运行 **regedit**
3. 设置以下注册表项
   * `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount`
      * `"AllowPasswordReset"=dword:00000001`

## <a name="what-do-users-see"></a>用户看到什么

配置并分配策略以后，对用户来说有哪些变化？ 用户如何知道可以在登录屏幕重置其密码？

![LoginScreen][LoginScreen]

现在，用户在尝试登录时，可以看到“重置密码”链接，用于在登录屏幕进行自助密码重置体验。 此功能允许用户重置其密码，不需使用其他设备来访问 Web 浏览器。

用户可以在[重置工作或学校密码](../active-directory-passwords-update-your-own-password.md#reset-password-at-sign-in)中发现此功能的使用指南

## <a name="common-issues"></a>常见问题

使用 Hyper-V 测试此功能时，“重置密码”链接不显示。

* 转到用于测试的 VM，单击“视图”，然后取消选中“增强会话”。

使用远程桌面测试此功能时，“重置密码”链接不显示

* 目前不支持从远程桌面进行密码重置。

## <a name="next-steps"></a>后续步骤
以下链接提供了有关使用 Azure AD 进行密码重置的其他信息

* [如何部署 SSPR？](howto-sspr-deployment.md)
* [如何从登录屏幕启用 PIN 重置？](https://docs.microsoft.com/intune/device-windows-pin-reset)
* [有关 MDM 身份验证策略的详细信息](https://docs.microsoft.com/windows/client-management/mdm/policy-csp-authentication)

[CreateProfile]: ./media/tutorial-sspr-windows/create-profile.png "创建 Intune 设备配置文件，在 Windows 10 登录屏幕上启用“重置密码”链接"
[Assignment]: ./media/tutorial-sspr-windows/profile-assignment.png "将 Intune 设备配置策略分配给一组 Windows 10 设备"
[LoginScreen]: ./media/tutorial-sspr-windows/logon-reset-password.png "在 Windows 10 登录屏幕中的“重置密码”链接"
