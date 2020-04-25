---
title: iOS 上基于证书的身份验证 - Azure Active Directory
description: 了解针对在 iOS 设备的解决方案中配置基于证书的身份验证的支持方案和要求 Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/17/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ede7ddb81bae69d92983e787e779ee9d410bd87
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2020
ms.locfileid: "82144070"
---
# <a name="azure-active-directory-certificate-based-authentication-on-ios"></a>iOS 上 Azure Active Directory 基于证书的身份验证

为了提高安全性，iOS 设备在连接到以下应用程序或服务时，可以使用基于证书的身份验证（CBA）在其设备上使用客户端证书进行身份验证 Azure Active Directory （Azure AD）：

* Office 移动应用程序，例如 Microsoft Outlook 和 Microsoft Word
* Exchange ActiveSync (EAS) 客户端

使用证书，无需在移动设备上的某些邮件和 Microsoft Office 应用程序中输入用户名和密码组合。

本文详细介绍了在 iOS 设备上配置 CBA 时的要求和支持的方案。 CBA for iOS 可跨 Azure 公有云、Microsoft 政府云、Microsoft 云德国和 Microsoft Azure 中国世纪互联。

## <a name="microsoft-mobile-applications-support"></a>Microsoft 移动应用程序支持

| 应用 | 支持 |
| --- | --- |
| Azure 信息保护应用 |![对号，表示支持此应用程序][1] |
| Intune 公司门户 |![对号，表示支持此应用程序][1] |
| Microsoft Teams |![对号，表示支持此应用程序][1] |
| Office （移动） |![对号，表示支持此应用程序][1] |
| OneNote |![对号，表示支持此应用程序][1] |
| OneDrive |![对号，表示支持此应用程序][1] |
| Outlook |![对号，表示支持此应用程序][1] |
| Power BI |![对号，表示支持此应用程序][1] |
| Skype for Business |![对号，表示支持此应用程序][1] |
| Word/Excel/PowerPoint |![对号，表示支持此应用程序][1] |
| Yammer |![对号，表示支持此应用程序][1] |

## <a name="requirements"></a>要求

若要将 CBA 与 iOS 配合使用，请遵循以下要求和注意事项：

* 设备 OS 版本必须为 iOS 9 或更高版本。
* iOS 设备上的 Office 应用程序需要安装 Microsoft Authenticator。
* 必须在 macOS 密钥链中创建一个标识首选项，其中包括 ADFS 服务器的身份验证 URL。 有关详细信息，请参阅[在 Mac 上的密钥链访问中创建标识首选项](https://support.apple.com/guide/keychain-access/create-an-identity-preference-kyca6343b6c9/mac)。

以下 Active Directory 联合身份验证服务（ADFS）要求和注意事项如下：

* 必须为 ADFS 服务器启用证书身份验证，并使用联合身份验证。
* 证书需要使用增强型密钥使用（EKU），并在 "*使用者备用名称" （NT 主体名称）* 中包含用户的 UPN。

## <a name="configure-adfs"></a>配置 ADFS

对于 Azure AD 要吊销客户端证书，ADFS 令牌必须具有以下声明。 如果在 ADFS 令牌（或任何其他 SAML 令牌）中可用，Azure AD 会将这些声明添加到刷新令牌中。 当需要验证刷新令牌时，将使用此信息来检查吊销：

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>`-添加客户端证书的序列号
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>`-添加客户端证书的颁发者的字符串

作为最佳做法，还应使用以下信息更新组织的 ADFS 错误页：

* 在 iOS 上安装 Microsoft Authenticator 的要求。
* 有关如何获取用户证书的说明。

有关详细信息，请参阅[自定义 AD FS 登录页](https://technet.microsoft.com/library/dn280950.aspx)。

## <a name="use-modern-authentication-with-office-apps"></a>将新式身份验证用于 Office 应用

某些启用了新式验证的 Office 应用`prompt=login`会在其请求中发送到 Azure AD。 默认情况下，Azure AD `prompt=login`会将请求转换为`wauth=usernamepassworduri` adfs （要求 Adfs 执行 U/P 身份验证）和`wfresh=0` （要求 adfs 忽略 SSO 状态并执行全新的身份验证）。 如果要为这些应用启用基于证书的身份验证，请修改默认 Azure AD 行为。

若要更新默认行为，请将联盟域设置中的 "*PromptLoginBehavior*" 设置为 "*已禁用*"。 可以使用[set-msoldomainfederationsettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) cmdlet 执行此任务，如以下示例中所示：

```powershell
Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled
```

## <a name="support-for-exchange-activesync-clients"></a>支持 Exchange ActiveSync 客户端

iOS 9 或更高版本支持本机 iOS 邮件客户端。 若要确定所有其他 Exchange ActiveSync 应用程序是否支持此功能，请与应用程序开发人员联系。

## <a name="next-steps"></a>后续步骤

若要在环境中配置基于证书的身份验证，请参阅有关说明的[基于证书的身份验证入门](active-directory-certificate-based-authentication-get-started.md)。

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png
