---
title: 启用用于 Azure AD (预览) 的无密码安全密钥登录-Azure Active Directory
description: 使用 FIDO2 安全密钥 (预览版) 启用无密码安全密钥登录 Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: a063891339a46366490447b7c7a7a1a14fd81be6
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68828912"
---
# <a name="enable-passwordless-security-key-sign-in-for-azure-ad-preview"></a>启用用于 Azure AD 的无密码安全密钥登录 (预览版)

## <a name="requirements"></a>要求

* Azure 多重身份验证
* 启用了 SSPR 的用户的组合注册预览
* FIDO2 安全密钥预览要求兼容的 FIDO2 安全密钥
* WebAuthN 需要 Windows 10 版本1809或更高版本上的 Microsoft Edge
* 基于 FIDO2 的 Windows 登录需要 Azure AD 联接 Windows 10 版本1809或更高版本

## <a name="prepare-devices-for-preview"></a>为预览版准备设备

要试验的设备必须运行 Windows 10 版本1809或更高版本。 最佳体验是在 Windows 10 版本1903或更高版本上。

## <a name="enable-security-keys-for-windows-sign-in"></a>启用 Windows 登录的安全密钥

组织可以选择使用以下一种或多种方法来启用 Windows 登录的安全密钥。

### <a name="enable-credential-provider-via-intune"></a>通过 Intune 启用凭据提供程序

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 浏览到**Microsoft Intune** > **设备注册** > windows**注册** > **windows Hello 企业** > 版**属性**。
1. 在 "**设置**" 下, 将 "**使用安全密钥登录**" 设置为 "**已启用**"。

用于登录的安全密钥的配置不依赖于配置 Windows Hello 企业版。

#### <a name="enable-targeted-intune-deployment"></a>启用目标 Intune 部署

若要以特定的设备组为目标以启用凭据提供程序, 请通过 Intune 使用以下自定义设置。 

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 浏览到**Microsoft Intune** > **设备配置** > " > " 配置文件 ""**创建配置文件**"。
1. 用以下设置配置新配置文件
   1. 名称:Windows 登录的安全密钥
   1. 描述:启用在 Windows 登录过程中使用的 FIDO 安全密钥
   1. 平台:Windows 10 及更高版本
   1. 平台类型:自定义
   1. 自定义 OMA-URI 设置:
      1. 名称:启用 Windows 登录的 FIDO 安全密钥
      1. OMA-URI:./Device/Vendor/MSFT/PassportForWork/SecurityKey/UseSecurityKeyForSignin
      1. 数据类型:整数
      1. 值：1 
1. 此策略可以分配给特定的用户、设备或组。 有关详细信息, 请参阅[将用户和设备配置文件分配到 Microsoft Intune](https://docs.microsoft.com/intune/device-profile-assign)一文。

![Intune 自定义设备配置策略创建](./media/howto-authentication-passwordless-security-key/intune-custom-profile.png)

### <a name="enable-credential-provider-via-provisioning-package"></a>通过预配包启用凭据提供程序

对于不由 Intune 管理的设备, 可以安装预配包以启用该功能。 可以从[Microsoft Store](https://www.microsoft.com/store/apps/9nblggh4tx22)安装 Windows 配置设计器应用。

1. 启动 Windows 配置设计器。
1. 选择 "**文件** > " "**新建项目**"。
1. 为项目指定一个名称, 并记下创建项目的路径。
1. 选择“**下一步**”。
1. 选择 "**预配包**" 作为**选定的项目工作流**, 然后选择 "**下一步**"。
1. 选择 **要查看和配置的设置**, 然后选择 "**下一步**"。
1. 选择“完成”。
1. 在新创建的项目中, 浏览到**运行时设置** > **WindowsHelloForBusiness** > **SecurityKeys** > **UseSecurityKeyForSignIn**。
1. 将**UseSecurityKeyForSignIn**设置为**Enabled**。
1. 选择**导出** > **预配包**
1. 在 "**生成**" 窗口的 "**描述预配包**" 中保留默认值, 然后选择 "**下一步**"。
1. 在 "**生成**" 窗口的 "**选择设置包的安全详细信息**" 下保留默认值, 然后选择 "**下一步**"。
1. 记下或更改**生成**窗口中的路径,**选择要保存预配包的位置**, 然后选择 "**下一步**"。
1. 在 "**生成预配包**" 页面上选择 "**生成**"。
1. 将创建的两个文件 (ppkg 和 cat) 保存到你稍后可以将它们应用到计算机的位置。
1. 按照[应用预配包一](https://docs.microsoft.com/windows/configuration/provisioning-packages/provisioning-apply-package)文中的指导进行操作, 以应用创建的预配包。

## <a name="obtain-fido2-security-keys"></a>获取 FIDO2 安全密钥

有关支持的密钥和制造商的详细信息, 请参阅[什么是无密码？](concept-authentication-passwordless.md)一文中的 "FIDO2 安全密钥" 部分。

> [!NOTE]
> 如果你购买并计划使用基于 NFC 的安全密钥, 将需要一个支持的 NFC 读卡器。

## <a name="enable-passwordless-authentication-method"></a>启用无密码 authentication 方法

### <a name="enable-the-combined-registration-experience"></a>启用组合注册体验

无密码身份验证方法的注册功能依赖于组合注册预览。 按照[启用组合安全信息注册 (预览版)](howto-registration-mfa-sspr-combined.md)一文中的步骤, 启用组合注册预览。

### <a name="enable-new-passwordless-authentication-method"></a>启用 new 无密码 authentication 方法

1. 登录到 [Azure 门户](https://portal.azure.com)
1. 浏览到**Azure Active Directory** > **安全** > **身份**验证方法 > **身份验证方法策略 (预览)**
1. 在每个**方法**下, 选择以下选项
   1. **启用**-是或否
   1. **目标**-所有用户或选择用户
1. **保存**每个方法

> [!WARNING]
> FIDO2 "密钥限制策略" 尚不起作用。 此功能将在正式发布之前提供, 请不要从默认值更改这些策略。

> [!NOTE]
> 不需要同时使用这两个无密码方法 (如果仅预览一个无密码方法, 只需启用此方法)。 我们鼓励您尝试这两种方法, 因为它们都有自己的好处。

## <a name="user-registration-and-management-of-fido2-security-keys"></a>FIDO2 安全密钥的用户注册和管理

1. 浏览到 [https://myprofile.microsoft.com](https://myprofile.microsoft.com)
1. 如果尚未登录, 请登录
1. 单击 "**安全信息**"
   1. 如果用户已注册至少一个 Azure 多重身份验证方法, 则可以立即注册 FIDO2 安全密钥。
   1. 如果它们没有注册至少一个 Azure 多重身份验证方法, 则它们必须添加一个。
1. 通过单击 "**添加方法**" 并选择 "**安全密钥**" 来添加 FIDO2 安全密钥
1. 选择**USB 设备**或**NFC 设备**
1. 准备好密钥并选择 "**下一步**"
1. 将显示一个框, 要求你为安全密钥创建/输入 PIN, 然后对密钥执行所需的笔势, 以生物识别或触摸。
1. 你将返回到合并的注册体验, 并要求你为令牌提供一个有意义的名称, 以便你可以确定有多个令牌。 单击“下一步”。
1. 单击 "**完成**" 完成该过程

### <a name="manage-security-key-biometric-pin-or-reset-security-key"></a>管理安全密钥生物识别、PIN 或重置安全密钥

* Windows 10 版本1809
   * 需要安全密钥供应商提供的软件
* Windows 10 版本1903或更高版本
   * 用户可以在其设备上打开**Windows 设置**>**帐户** > **安全密钥**
   * 用户可以更改其 PIN、更新生物识别或重置其安全密钥

## <a name="user-registration-and-management-of-microsoft-authenticator-app"></a>Microsoft Authenticator 应用的用户注册和管理

若要为手机登录配置 Microsoft Authenticator 应用, 请按照文章[使用 Microsoft Authenticator 应用登录到你的帐户](../user-help/user-help-auth-app-sign-in.md)中的指南进行操作。

## <a name="sign-in-with-passwordless-credential"></a>用无密码凭据登录

### <a name="sign-in-at-the-lock-screen"></a>在锁屏界面上登录

在下面的示例中, 用户 Bala Sandhu 已预配其 FIDO2 安全密钥。 Bala 可以从 Windows 10 锁屏界面中选择安全密钥凭据提供程序, 并插入用于登录 Windows 的安全密钥。

![Windows 10 锁屏界面上的安全密钥登录](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-sign-in-lock-screen.png)

### <a name="sign-in-on-the-web"></a>登录 web

在下面的示例中, 用户已设置了其 FIDO2 安全密钥。 用户可以选择通过 Windows 10 1809 版或更高版本上的 Microsoft Edge 浏览器中的 FIDO2 安全密钥登录到 web。

![Microsoft Edge 中的安全密钥登录](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-edge-sign-in.png)

## <a name="known-issues"></a>已知问题

### <a name="security-key-provisioning"></a>安全密钥预配

在公共预览版中, 管理员预配和取消预配安全密钥不可用。

### <a name="hybrid-azure-ad-join"></a>混合 Azure AD 联接

依赖于 WIA SSO (使用 FIDO2 安全密钥或无密码 Microsoft Authenticator 登录) 的用户需要使用 Windows 10 上的混合联接才能获得 SSO 的好处。 但是, 安全密钥现在仅适用于 Azure Active Directory 联接的计算机。 建议你只在纯 Azure Active Directory 联接的计算机上试用 Windows 锁屏界面的 FIDO2 安全密钥。 此限制不适用于 web。

### <a name="upn-changes"></a>UPN 更改

我们正在致力于支持允许混合 AADJ 和 AADJ 设备上的 UPN 更改的功能。 如果用户的 UPN 发生更改, 你将无法再修改 FIDO2 安全密钥来考虑这种情况。 因此, 唯一的方法是重置设备, 用户必须重新注册。

## <a name="next-steps"></a>后续步骤

[了解设备注册](../devices/overview.md)

[了解 Azure 多重身份验证](../authentication/howto-mfa-getstarted.md)
