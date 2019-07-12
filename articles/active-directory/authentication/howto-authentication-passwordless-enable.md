---
title: 配置 Azure Active Directory （预览版） 的无密码登录
description: 启用无密码登录到 Azure AD 使用 FIDO2 安全密钥或 Microsoft 验证器应用 （预览版）
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ba2545467ebfbd032408aeee25b82b92a628f2a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712067"
---
# <a name="enable-passwordless-sign-in-for-azure-ad-preview"></a>为 Azure AD （预览版） 启用无密码登录

## <a name="requirements"></a>要求

* Azure 多重身份验证
* 结合使用注册预览版
* FIDO2 安全密钥预览版需要兼容 FIDO2 安全密钥
* WebAuthN 需要 Windows 10 1809年或更高版本上的 Microsoft Edge
* 在基于的 Windows 符号则需要 Azure AD 的 FIDO2 已加入 Windows 10 1809年或更高版本

## <a name="prepare-devices-for-preview"></a>准备设备进行预览

您将使用试验的设备必须运行 Windows 10 版本 1809年或更高版本。 最佳的体验是 Windows 10 1903年或更高版本上。

## <a name="enable-security-keys-for-windows-sign-in"></a>启用安全密钥以进行 Windows 登录

组织可以选择使用一个或多个以下方法来启用安全密钥用于 Windows 登录。

### <a name="enable-credential-provider-via-intune"></a>启用通过 Intune 的凭据提供程序

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 浏览到**Microsoft Intune** > **设备注册** > **Windows 注册** > **Windows Hello有关业务** > **属性**。
1. 下**设置**设置**使用安全密钥进行登录**到**已启用**。

配置的安全密钥以进行登录，不依赖于配置 Windows hello 企业版。

#### <a name="enable-targeted-intune-deployment"></a>启用 Intune 的目标的部署

若要针对特定设备组以启用凭据提供程序，使用 Intune 通过以下自定义设置。 

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 浏览到**Microsoft Intune** > **设备配置** > **配置文件** > **创建配置文件**.
1. 使用以下设置来配置新的配置文件
   1. 姓名：Windows 登录的安全密钥
   1. 说明:能够 FIDO 安全密钥以在 Windows 登录过程中使用
   1. 平台:Windows 10 及更高版本
   1. 平台类型：自定义
   1. 自定义 OMA-URI 设置：
      1. 姓名：为 Windows 登录启用 FIDO 安全密钥
      1. OMA URI:./Device/Vendor/MSFT/PassportForWork/SecurityKey/UseSecurityKeyForSignin
      1. 数据类型：Integer
      1. 值：1 
1. 可以将此策略分配到特定用户、 设备或组。 可一文中找到更多信息[分配 Microsoft Intune 中的用户和设备配置文件](https://docs.microsoft.com/intune/device-profile-assign)。

![创建 Intune 自定义设备配置策略](./media/howto-authentication-passwordless-enable/intune-custom-profile.png)

### <a name="enable-credential-provider-via-provisioning-package"></a>启用凭据提供程序通过预配包

对于不由 Intune 管理的设备，可以安装预配包启用的功能。 可以从安装在 Windows 配置设计器应用[Microsoft Store](https://www.microsoft.com/store/apps/9nblggh4tx22)。

1. 启动 Windows 配置设计器。
1. 选择**文件** > **新项目**。
1. 为你的项目提供一个名称并记下的路径创建项目。
1. 选择“**下一步**”。
1. 将保留**预配包**作为所选**选定项目工作流**，然后选择**下一步**。
1. 选择**所有的 Windows 桌面版本**下**选择要查看和配置的设置**，然后选择**下一步**。
1. 选择“完成”。 
1. 在新创建的项目，浏览到**运行时设置** > **WindowsHelloForBusiness** > **SecurityKeys**  > **UseSecurityKeyForSignIn**。
1. 设置**UseSecurityKeyForSignIn**到**启用**。
1. 选择**导出** > **预配包**
1. 保留中的默认值**构建**下的窗口**描述预配包**，然后选择**下一步**。
1. 保留中的默认值**构建**下的窗口**选择预配包的安全详细信息**，然后选择**下一步**。
1. 记下或在更改了路径**构建**下的 windows**选择预配包的保存位置**，然后选择**下一步**。
1. 选择**构建**上**生成预配包**页。
1. 将创建两个文件 （ppkg 和 cat） 保存到其中您可以将它们应用于计算机更高版本的位置。
1. 遵循本文中的指导[预配包应用](https://docs.microsoft.com/windows/configuration/provisioning-packages/provisioning-apply-package)，将应用创建的预配包。

## <a name="obtain-fido2-security-keys"></a>获取 FIDO2 安全密钥

请参阅以下文章中的部分 FIDO2 安全密钥[什么是无密码？](concept-authentication-passwordless.md)有关支持的键和制造商详细信息。

> [!NOTE]
> 如果您购买并计划使用 NFC 基于安全密钥需要在支持的 NFC 读取器。

## <a name="enable-passwordless-authentication-methods"></a>启用无密码身份验证方法

### <a name="enable-the-combined-registration-experience"></a>启用组合的注册体验

FIDO2 安全密钥的注册功能依赖于组合的注册预览版。 请按照以下步骤来启用组合的注册预览版。

1. 登录到 [Azure 门户](https://portal.azure.com)
1. 浏览到**Azure Active Directory** > **用户设置**
   1. 单击**管理的设置的访问面板中预览功能**
   1. 下**用户可以使用预览功能来注册和管理增强的安全信息-** 。
      1. 选择**选定**在预览中选择一组将加入的用户。
      1. 或选择**所有**若要启用你的目录中的每个人。
1. 单击“保存” 

### <a name="enable-new-passwordless-authentication-methods"></a>启用新的无密码身份验证方法

1. 登录到 [Azure 门户](https://portal.azure.com)
1. 浏览到**Azure Active Directory** > **身份验证方法** > **身份验证方法策略 （预览）**
1. 每个下**方法**，选择以下选项
   1. **启用**-Yes 或 No
   1. **目标**-所有用户还是选择用户
1. **保存**每个方法

> [!WARNING]
> "密钥限制策略"尚不能使用 FIDO2。 此功能将提供正式发布前，请不要从默认值更改这些策略。

> [!NOTE]
> 无需选择启用这两个无密码的方法 （如果你想要预览只有一个无密码的方法，可以启用该方法只有）。 我们鼓励您尝试这两种方法，因为它们都有其自己的好处。

## <a name="user-registration-and-management-of-fido2-security-keys"></a>用户注册和管理 FIDO2 安全密钥

1. 浏览到 [https://myprofile.microsoft.com](https://myprofile.microsoft.com)
1. 在如果尚未登录
1. 单击**安全信息**
   1. 如果用户已注册的至少一种 Azure 多重身份验证方法，它们可以立即注册 FIDO2 安全密钥。
   1. 如果他们没有至少一个已注册的 Azure 多重身份验证方法，它们必须添加一个。
1. 通过单击添加 FIDO2 安全密钥**将方法添加**，然后选择**安全密钥**
1. 选择**USB 设备**或**NFC 设备**
1. 已准备就绪，然后选择你的密钥**下一步**
1. 一个框，此时将出现并要求您创建或输入 PIN 的安全密钥，然后你的密钥执行所需的手势生物识别或触摸。
1. 你将返回到组合的注册体验，要求提供你的令牌有意义的名称，因此如果您有多个可以确定哪一个。 单击“下一步”  。
1. 单击**完成**以完成过程

### <a name="manage-security-key-biometric-pin-or-reset-security-key"></a>管理安全密钥生物识别，PIN，或重置的安全密钥

* Windows 10 版本 1809
   * 附带的软件供应商的安全密钥是必需的
* Windows 10 1903 或更高版本
   * 用户可以打开**Windows 设置**在其设备上 >**帐户** > **安全密钥**
   * 用户可以更改其 pin 码、 更新生物识别，或重置其安全密钥

## <a name="user-registration-and-management-of-microsoft-authenticator-app"></a>用户注册和管理 Microsoft Authenticator 应用

若要配置 Microsoft Authenticator 应用手机登录，请按照本文中的指导[登录到你的帐户使用 Microsoft Authenticator 应用](../user-help/user-help-auth-app-sign-in.md)。

## <a name="sign-in-with-passwordless-credentials"></a>使用无密码的凭据登录

### <a name="sign-in-at-the-lock-screen"></a>在锁定屏幕登录

在用户下面的示例 Bala Sandhu 具有已预配其 FIDO2 安全密钥。 Bala 可以从 Windows 10 锁定屏幕中选择安全密钥凭据提供程序，并插入安全密钥以登录到 Windows。

![在 Windows 10 锁定屏幕登录的安全密钥](./media/howto-authentication-passwordless-enable/fido2-windows-10-1903-sign-in-lock-screen.png)

### <a name="sign-in-on-the-web"></a>在 web 上登录

在用户下面的示例有已预配其 FIDO2 安全密钥。 用户可以选择使用 Windows 10 1809年或更高版本上的 Microsoft Edge 浏览器内其 FIDO2 安全密钥登录在 web 上。

![Microsoft Edge 中的安全密钥登录](./media/howto-authentication-passwordless-enable/fido2-windows-10-1903-edge-sign-in.png)

有关使用 Microsoft Authenticator 应用登录信息，请参阅本文中，[登录到你的帐户使用 Microsoft Authenticator 应用](../user-help/user-help-auth-app-sign-in.md)。

## <a name="known-issues"></a>已知问题

### <a name="fido2-security-keys"></a>FIDO2 安全密钥

#### <a name="security-key-provisioning"></a>安全密钥预配

预配和取消预配的安全密钥的管理员不是在公共预览版中可用。

#### <a name="hybrid-azure-ad-join"></a>混合 Azure AD 加入

使用管理的凭据的用户依赖于 WIA SSO 喜欢 FIDO2 安全密钥或无密码登录 Microsoft Authenticator 应用需要混合加入 Windows 10 上以利用 SSO。 但是，安全密钥只适用于 Azure Active Directory 已加入计算机现在。 我们建议您仅试用 FIDO2 Windows 锁定屏幕纯 Azure Active Directory 已加入的计算机上的安全密钥。 此限制不适用于 web。

#### <a name="upn-changes"></a>UPN 更改

我们正在努力支持允许混合 AADJ UPN 更改和 AADJ 设备的功能。 如果用户的 UPN 发生更改，不再可以修改 FIDO2 安全密钥，若要为此帐户。 因此，唯一的方法是将设备重置和用户必须重新注册。

### <a name="authenticator-app"></a>Authenticator 应用

#### <a name="ad-fs-integration"></a>AD FS 集成

用户启用 Microsoft Authenticator 无密码凭据后，该用户的身份验证始终默认为发送审批通知。 此逻辑可以防止混合租户中的用户定向到 ADFS 的登录验证而无需采取其他步骤，用户单击"改为使用你的密码。" 此过程还会绕过任何本地条件访问策略和直通身份验证流。 此过程的例外是 login_hint 是否指定，用户将自动转发到 AD FS，并绕过使用无密码的凭据的选项。

#### <a name="azure-mfa-server"></a>Azure MFA 服务器

通过组织的本地 Azure MFA 服务器启用了 MFA 的最终用户仍可以创建和使用凭据中的单个无密码的电话登录。 如果用户尝试使用凭据升级 Microsoft Authenticator 的多个安装（5 个以上），则此项更改可能导致错误。  

#### <a name="device-registration"></a>设备注册

创建这种新式强凭据的先决条件之一是，该凭据所在的设备已注册到单个用户的 Azure AD 租户中。 由于设备注册的限制，一个设备只能注册到一个租户中。 此限制意味着 Microsoft Authenticator 应用中的只有一个工作或学校帐户，可以启用电话登录。

## <a name="next-steps"></a>后续步骤

[了解设备注册](../devices/overview.md)

[了解 Azure 多重身份验证](../authentication/howto-mfa-getstarted.md)
