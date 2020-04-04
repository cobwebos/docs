---
title: 使用 Microsoft 身份验证器应用进行无密码登录 - Azure 活动目录
description: 使用 Microsoft 身份验证器应用（预览）为 Azure AD 启用无密码登录
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 853e7143834a340b870b71ef1a287dab136e2783
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654055"
---
# <a name="enable-passwordless-sign-in-with-the-microsoft-authenticator-app-preview"></a>使用 Microsoft 身份验证器应用启用无密码登录（预览版）

使用 Microsoft Authenticator 应用可以登录到任何 Azure AD 帐户，且无需输入密码。 类似于 [Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-identity-verification) 的技术，Microsoft Authenticator 使用基于密钥的身份验证来启用绑定到设备的用户凭据，并使用生物识别特征或 PIN。 此身份验证方法可用于任何设备平台（包括移动设备），以及任何与 Microsoft 身份验证库集成的应用或网站。 

![要求用户批准登录的浏览器登录示例](./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png)

输入用户名后，打开 Microsoft 身份验证器应用的电话登录的用户将看到一条消息，告诉他们点击应用中的号码，而不是在输入用户名后看到密码提示。 在应用中，该用户必须输入匹配的数字，选择“批准”，提供 PIN 或生物识别特征，然后身份验证将会完成。

> [!NOTE]
> 此功能自 2017 年 3 月起在 Microsoft 身份验证器应用中使用，因此，当为目录启用策略时，用户可能会立即遇到此流，如果策略未启用，则可能会看到错误消息。 请注意这一点，并让用户为此更改做好准备。

## <a name="prerequisites"></a>先决条件

- Azure 多重身份验证，允许推送通知作为验证方法 
- 已在运行 iOS 8.0 或更高版本或者 Android 6.0 或更高版本的设备上安装最新版本的 Microsoft Authenticator。

> [!NOTE]
> 如果使用 Azure AD PowerShell 启用了以前的 Microsoft 身份验证器应用无密码登录预览，则已为您的整个目录启用该预览。 如果启用此新方法，它将取代 PowerShell 策略。 我们建议通过新的身份验证方法为租户中的所有用户启用，否则新策略中未登录的用户将无法再以无密码方式登录。 

## <a name="enable-passwordless-authentication-methods"></a>启用无密码身份验证方法

### <a name="enable-the-combined-registration-experience"></a>启用组合注册体验

无密码身份验证方法的注册功能依赖于组合的注册预览。 按照文章"[启用组合安全信息注册（预览）"](howto-registration-mfa-sspr-combined.md)中的步骤启用合并注册预览。

### <a name="enable-passwordless-phone-sign-in-authentication-methods"></a>启用无密码电话登录身份验证方法

1. 登录到 Azure[门户](https://portal.azure.com)
1. 搜索并选择“Azure Active Directory”**。 选择**安全** > **身份验证方法** > **身份验证方法策略（预览）**
1. 在**无密码电话登录下**，选择以下选项
   1. **启用**- 是 或 否
   1. **目标**- 所有用户或选择用户
1. **保存**以设置新策略

## <a name="user-registration-and-management-of-microsoft-authenticator-app"></a>微软身份验证器应用的用户注册和管理

1. 浏览到 [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)
1. 尚未登录
1. 通过单击 **"添加方法**"、选择**身份验证器应用**和单击"**添加"** 添加身份验证器应用
1. 按照说明在设备上安装和配置 Microsoft 身份验证器应用
1. 单击 **"完成"** 以完成身份验证器 MFA 应用设置流。 
1. 在**Microsoft 身份验证器中**，从帐户下拉菜单**中选择"启用电话登录**"
1. 按照应用中的说明完成无密码电话登录的注册。 

组织可以将用户指向[使用手机登录的文章，而不是密码](../user-help/microsoft-authenticator-app-phone-signin-faq.md)，以便在 Microsoft 身份验证器应用中设置和启用电话登录时提供进一步帮助。 要应用这些设置，您可能需要注销并重新登录到租户。 

## <a name="sign-in-with-passwordless-credential"></a>使用无密码凭据登录

在公共预览版中，没有任何方法可以强制用户创建或使用此新凭据。 用户只有在管理员启用其租户**且**用户已更新其 Microsoft 身份验证器应用以启用电话登录后才会遇到无密码登录。

在 Web 上键入用户名并选择 **"下一步"** 后，将为用户提供一个号码，并在其 Microsoft 身份验证器应用中提示用户选择适当的号码进行身份验证，而不是使用其密码。 

![使用 Microsoft 身份验证器应用登录的浏览器示例](./media/howto-authentication-passwordless-phone/web-sign-in-microsoft-authenticator-app.png)

## <a name="known-issues"></a>已知问题

### <a name="user-is-not-enabled-by-policy-but-still-has-passwordless-phone-sign-in-method-in-microsoft-authenticator"></a>用户未通过策略启用，但仍在 Microsoft 身份验证器中具有无密码电话登录方法

用户可能在某个时候在其当前的 Microsoft 身份验证器应用中或较早的设备上创建了无密码的电话登录凭据。 一旦管理员启用无密码电话登录的身份验证方法策略，任何已注册凭据的用户将开始体验新的登录提示，无论他们是否已启用该策略。 如果策略不允许用户使用凭据，则在完成身份验证流后，他们将看到错误。 

管理员可以选择允许用户使用无密码电话登录，或者用户必须删除该方法。 如果用户不再拥有已注册的设备，他们可以转到[https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)并删除它。 如果他们仍在使用 MFA 的身份验证器，则可以选择从 Microsoft 身份验证器内**禁用电话登录**。  

### <a name="ad-fs-integration"></a>AD FS 集成

用户启用 Microsoft Authenticator 无密码凭据后，该用户的身份验证始终默认为发送审批通知。 此逻辑可防止混合租户中的用户被定向到 ADFS 进行登录验证，而用户无需执行其他步骤单击"改用密码"。 此过程还会绕过任何本地条件访问策略和直通身份验证流。 

如果用户有未应答的无密码电话登录验证待定，并尝试再次登录，则用户可能会被带到 ADFS 输入密码。  

### <a name="azure-mfa-server"></a>Azure MFA 服务器

通过组织的本地 Azure MFA 服务器启用 MFA 的最终用户仍可以创建和使用单个无密码电话登录凭据。 如果用户尝试使用凭据升级 Microsoft Authenticator 的多个安装（5 个以上），则此项更改可能导致错误。  

### <a name="device-registration"></a>设备注册

创建此新的强凭据的先决条件之一是，安装 Microsoft 身份验证器应用的设备也必须在 Azure AD 租户中注册给单个用户。 由于当前设备注册限制，设备只能在单个租户中注册。 此项限制意味着，只能为 Microsoft Authenticator 应用中的一个工作或学校帐户启用手机登录。

### <a name="intune-mobile-application-management"></a>Intune 移动应用程序管理 

受需要移动应用程序管理 （MAM） 的策略约束的最终用户无法在 Microsoft 身份验证器应用中注册无密码凭据。 

> [!NOTE]
> 设备注册与设备管理或"MDM"不同。 它仅在 Azure AD 目录中将设备 ID 和用户 ID 关联在一起。  

## <a name="next-steps"></a>后续步骤

[什么是无密码？](concept-authentication-passwordless.md)

[了解设备注册](../devices/overview.md#getting-devices-in-azure-ad)

[了解 Azure 多重身份验证](../authentication/howto-mfa-getstarted.md)
