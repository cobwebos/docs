---
title: 无密码登录 Microsoft Authenticator 应用-Azure Active Directory
description: 使用 Microsoft Authenticator 应用（预览版）启用无密码登录 Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: a76aa0ca7cbda3f2db564c220ba12fec60f60509
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381865"
---
# <a name="enable-passwordless-sign-in-with-the-microsoft-authenticator-app-preview"></a>使用 Microsoft Authenticator 应用启用无密码登录（预览版）

使用 Microsoft Authenticator 应用可以登录到任何 Azure AD 帐户，且无需输入密码。 类似于 [Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-identity-verification) 的技术，Microsoft Authenticator 使用基于密钥的身份验证来启用绑定到设备的用户凭据，并使用生物识别特征或 PIN。 此身份验证方法可用于任何设备平台（包括移动设备）以及与 Microsoft 身份验证库集成的任何应用程序或网站。 

![要求用户批准登录的浏览器登录示例](./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png)

输入用户名后，从 Microsoft Authenticator 应用启用手机登录的用户将看到一条消息，告知他们在其应用中点击某个数字，而不是在输入用户名后看到密码提示。 在应用中，该用户必须输入匹配的数字，选择“批准”，提供 PIN 或生物识别特征，然后身份验证将会完成。

> [!NOTE]
> 自2017年3月起，此功能已在 Microsoft Authenticator 应用中，因此，如果为目录启用了该策略，用户可能会立即遇到此流，如果策略未启用，则会看到一条错误消息。 请注意这一点，并让用户为此更改做好准备。

## <a name="prerequisites"></a>先决条件

- Azure 多重身份验证，允许将推送通知作为验证方法 
- 已在运行 iOS 8.0 或更高版本或者 Android 6.0 或更高版本的设备上安装最新版本的 Microsoft Authenticator。

> [!NOTE]
> 如果你使用 Azure AD PowerShell 启用了以前的 Microsoft Authenticator 应用无密码登录预览，则会为你的整个目录启用此功能。 如果使用此新方法启用，则会取代 PowerShell 策略。 建议通过新的身份验证方法为租户中的所有用户启用，否则不在新策略中的用户将无法再登录 passwordlessly。 

## <a name="enable-passwordless-authentication-methods"></a>启用无密码身份验证方法

### <a name="enable-the-combined-registration-experience"></a>启用组合注册体验

无密码身份验证方法的注册功能依赖于组合注册预览。 按照[启用组合安全信息注册（预览版）](howto-registration-mfa-sspr-combined.md)一文中的步骤，启用组合注册预览。

### <a name="enable-passwordless-phone-sign-in-authentication-methods"></a>启用无密码手机登录身份验证方法

1. 登录到 [Azure 门户](https://portal.azure.com)
1. 搜索并选择“Azure Active Directory”。 选择**安全** > **身份**验证方法 > **身份验证方法策略（预览）**
1. 在**无密码手机登录**下，选择以下选项
   1. **启用**-是或否
   1. **目标**-所有用户或选择用户
1. **保存**以设置新策略

## <a name="user-registration-and-management-of-microsoft-authenticator-app"></a>Microsoft Authenticator 应用的用户注册和管理

1. 浏览到 [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)
1. 如果尚未登录，请登录
1. 单击 "**添加方法**"，选择 "验证器**应用**"，然后单击 "**添加**" 来添加验证器应用
1. 按照说明在设备上安装和配置 Microsoft Authenticator 应用
1. 单击 "**完成**"，完成身份验证器 MFA 应用安装流。 
1. 在**Microsoft Authenticator**中，从 "帐户" 下拉菜单中选择 "**启用手机登录**"。
1. 按照应用中的说明完成注册无密码手机登录。 

组织可以让他们的用户登录到[你的手机，而不是你的密码，](../user-help/microsoft-authenticator-app-phone-signin-faq.md)以便在 Microsoft Authenticator 的应用程序中设置进一步的帮助并启用手机登录。

## <a name="sign-in-with-passwordless-credential"></a>用无密码凭据登录

在公共预览版中，没有任何方法可以强制用户创建或使用此新凭据。 如果管理员已启用其租户 **，并且**用户已更新其 Microsoft Authenticator 应用程序以启用手机登录，则用户只会遇到无密码登录。

在 web 上键入用户名并选择 "**下一步**" 后，将向用户显示一个数字，并在其 Microsoft Authenticator 应用中提示用户选择合适的号码进行身份验证，而不是使用其密码。 

![使用 Microsoft Authenticator 应用程序的浏览器登录示例](./media/howto-authentication-passwordless-phone/web-sign-in-microsoft-authenticator-app.png)

## <a name="known-issues"></a>已知问题

### <a name="user-is-not-enabled-by-policy-but-still-has-passwordless-phone-sign-in-method-in-microsoft-authenticator"></a>用户未通过策略启用，但仍在 Microsoft Authenticator 中具有无密码的手机登录方法

用户有可能在某个时间点在其当前 Microsoft Authenticator 应用或更早的设备上创建无密码手机登录凭据。 一旦管理员启用了身份验证方法策略以无密码手机登录，则任何已注册了凭据的用户都将开始体验新的登录提示，无论是否已启用它们来使用该策略。 如果尚未允许用户使用策略，则在完成身份验证流后，用户将看到一个错误。 

管理员可以选择允许用户使用无密码 phone 登录，或用户必须删除该方法。 如果用户不再有已注册的设备，则可以跳到[https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)并将其删除。 如果它们仍在使用用于 MFA 的身份验证器，则可以从 Microsoft Authenticator 中选择 "**禁用"** 。  

### <a name="ad-fs-integration"></a>AD FS 集成

用户启用 Microsoft Authenticator 无密码凭据后，该用户的身份验证始终默认为发送审批通知。 此逻辑可以防止将混合租户中的用户定向到 ADFS 进行登录验证，且无需用户执行附加的步骤来单击“改用密码”。 此过程还会绕过任何本地条件访问策略和直通身份验证流。 

如果用户已挂起无人参与的无密码手机登录验证，并尝试再次登录，则用户可能会转到 ADFS 来改为输入密码。  

### <a name="azure-mfa-server"></a>Azure MFA 服务器

通过组织的本地 Azure MFA 服务器启用 MFA 的最终用户仍可以创建和使用单个无密码手机登录凭据。 如果用户尝试使用凭据升级 Microsoft Authenticator 的多个安装（5 个以上），则此项更改可能导致错误。  

### <a name="device-registration"></a>设备注册

创建此新的强凭据的先决条件之一是，安装 Microsoft Authenticator 应用的设备还必须在 Azure AD 租户内注册到单个用户。 由于当前设备注册限制，只能在单个租户中注册设备。 此项限制意味着，只能为 Microsoft Authenticator 应用中的一个工作或学校帐户启用手机登录。

> [!NOTE]
> 设备注册不同于设备管理或 "MDM"。 它仅将设备 ID 和用户 ID 在 Azure AD 目录中一起关联。  

## <a name="next-steps"></a>后续步骤

[什么是无密码？](concept-authentication-passwordless.md)

[了解设备注册](../devices/overview.md#getting-devices-in-azure-ad)

[了解 Azure 多重身份验证](../authentication/howto-mfa-getstarted.md)
