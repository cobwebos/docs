---
title: 无密码登录 Microsoft Authenticator 应用-Azure Active Directory
description: 使用 Microsoft Authenticator 应用 () 预览启用无密码登录 Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 09/29/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 053a489993c31344b96e83253c88eed93b27b145
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91964819"
---
# <a name="enable-passwordless-sign-in-with-the-microsoft-authenticator-app-preview"></a>使用 Microsoft Authenticator 应用 (预览版启用无密码登录) 

使用 Microsoft Authenticator 应用可以登录到任何 Azure AD 帐户，且无需输入密码。 类似于 [Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-identity-verification) 的技术，Microsoft Authenticator 使用基于密钥的身份验证来启用绑定到设备的用户凭据，并使用生物识别特征或 PIN。 此身份验证方法可用于任何设备平台（包括移动设备）以及与 Microsoft 身份验证库集成的任何应用程序或网站。

![要求用户批准登录的浏览器登录示例](./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png)

输入用户名后，从 Microsoft Authenticator 应用启用手机登录的用户将看到一条消息，要求他们在其应用中点击一个数字，而不是在输入用户名之后看到密码提示。 若要完成应用中的登录过程，用户必须与号码匹配，选择 " **批准**"，然后提供其 PIN 或生物识别。

## <a name="prerequisites"></a>先决条件

若要将无密码 phone 登录与 Microsoft Authenticator 应用一起使用，必须满足以下先决条件：

- Azure 多重身份验证，允许将推送通知作为验证方法。
- 已在运行 iOS 8.0 或更高版本或者 Android 6.0 或更高版本的设备上安装最新版本的 Microsoft Authenticator。

> [!NOTE]
> 如果你使用 Azure AD PowerShell 启用 Microsoft Authenticator 应用无密码登录预览，则会为你的整个目录启用此功能。 如果使用此新方法启用，则它将取代 PowerShell 策略。 建议你通过新的 *身份验证方法* 菜单为租户中的所有用户启用，否则不在新策略中的用户将无法在没有密码的情况下进行登录。

## <a name="enable-passwordless-authentication-methods"></a>启用无密码身份验证方法

若要在 Azure AD 中使用无密码 authentication，首先要启用合并的注册体验，并使用户能够使用密码更少的方法。

### <a name="enable-the-combined-registration-experience"></a>启用组合注册体验

无密码身份验证方法的注册功能依赖于组合注册功能。 若要让用户自行完成组合注册，请按照步骤 [启用组合的安全信息注册](howto-registration-mfa-sspr-combined.md)。

### <a name="enable-passwordless-phone-sign-in-authentication-methods"></a>启用无密码手机登录身份验证方法

Azure AD 允许选择在登录过程中可使用的身份验证方法。 然后，用户注册他们想要使用的方法。

若要为无密码 phone 登录启用身份验证方法，请完成以下步骤：

1. 使用*全局管理员*帐户登录到[Azure 门户](https://portal.azure.com)。
1. 搜索并选择 " *Azure Active Directory*"，然后浏览到 "**安全**  >  **身份验证方法**" "  >  **身份验证方法" " (预览") **
1. 在 " **无密码手机登录**" 下，选择以下选项：
   1. **启用** -是或否
   1. **目标** -所有用户或选择用户
1. 若要应用新策略，请选择 " **保存**"。

## <a name="user-registration-and-management-of-microsoft-authenticator-app"></a>Microsoft Authenticator 应用的用户注册和管理

使用可在 Azure AD 中使用的无密码 authentication 方法，用户现在必须使用以下步骤为无密码 authentication 方法注册自己：

1. 浏览到 [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) 。
1. 登录，然后通过选择 " **添加方法 > 验证器应用**" 添加验证器应用，然后选择 " **添加**"。
1. 按照说明在设备上安装和配置 Microsoft Authenticator 应用。
1. 选择 **"完成"** 以完成身份验证器配置。
1. 在 **Microsoft Authenticator** 应用中，从注册帐户的下拉菜单中选择 " **启用手机登录** "。
1. 按照应用中的说明完成注册无密码 phone 登录的帐户。

组织可以指导其用户 [通过手机登录，而不是通过密码](../user-help/user-help-auth-app-sign-in.md) 来帮助配置 Microsoft Authenticator 应用并启用手机登录。

> [!NOTE]
> 策略不允许使用手机登录的用户无法再在 Microsoft Authenticator 应用中启用它。  

## <a name="sign-in-with-passwordless-credential"></a>用无密码凭据登录

如果管理员已启用其租户，并且用户已更新其 Microsoft Authenticator 应用程序以启用手机登录，则用户可以开始使用无密码登录。

若要开始使用手机登录，请在登录页面键入用户名并选择 " **下一步**" 后，用户可能需要选择 **其他登录方式**，然后 **批准 Microsoft Authenticator 应用上的请求**。 然后，会向用户显示一个数字，并在 Microsoft Authenticator 应用程序中提示用户选择适当的数字进行身份验证，而不是使用其密码。 用户使用无密码手机登录后，系统会提示他们再次使用该登录，直到他们使用选择其他方法。

![使用 Microsoft Authenticator 应用程序的浏览器登录示例](./media/howto-authentication-passwordless-phone/web-sign-in-microsoft-authenticator-app.png)

## <a name="known-issues"></a>已知问题

当前预览体验存在以下已知问题。

### <a name="not-seeing-option-for-passwordless-phone-sign-in"></a>无密码手机登录的 "不查看" 选项

如果用户有一个无人参与的无密码手机登录验证处于挂起状态，并尝试再次登录，则用户可能只会看到输入密码的选项。 打开 Microsoft Authenticator 并响应任何通知提示以继续使用无密码手机登录。

### <a name="federated-accounts"></a>联合帐户

用户启用任何无密码凭据后，Azure AD 登录名将停止使用 login_hint 以加速用户使用联合登录位置。 此逻辑可防止混合租户中的用户被定向到 AD FS 进行登录验证，而无需用户执行额外步骤来单击 "改用密码"。

### <a name="azure-mfa-server"></a>Azure MFA 服务器

通过组织的本地 Azure MFA 服务器启用 MFA 的最终用户仍可以创建和使用单个无密码手机登录凭据。 如果用户尝试使用凭据升级 Microsoft Authenticator 的多个安装（5 个以上），则此项更改可能导致错误。  

### <a name="device-registration"></a>设备注册

创建此新的强凭据的先决条件之一是，安装 Microsoft Authenticator 应用的设备还必须在 Azure AD 租户内注册到单个用户。 由于当前设备注册限制，只能在单个租户中注册设备。 此项限制意味着，只能为 Microsoft Authenticator 应用中的一个工作或学校帐户启用手机登录。

> [!NOTE]
> 设备注册不同于设备管理或 "MDM"。 它仅将设备 ID 和用户 ID 在 Azure AD 目录中一起关联。  

## <a name="next-steps"></a>后续步骤

若要了解 Azure AD 身份验证和无密码方法，请参阅以下文章：

* [了解无密码 authentication 的工作原理](concept-authentication-passwordless.md)
* [了解设备注册](../devices/overview.md#getting-devices-in-azure-ad)
* [了解 Azure 多重身份验证](../authentication/howto-mfa-getstarted.md)
