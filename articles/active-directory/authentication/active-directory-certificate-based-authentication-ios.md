---
title: iOS 上基于证书的身份验证 - Azure Active Directory
description: 了解受支持的方案以及使用 iOS 设备的解决方案中配置 Azure 活动目录基于证书的身份验证的要求
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/17/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76c5e18a0bf84e96476eafd7ff35398049f1a492
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2020
ms.locfileid: "81639629"
---
# <a name="azure-active-directory-certificate-based-authentication-on-ios"></a>iOS 上 Azure Active Directory 基于证书的身份验证

为了提高安全性，iOS 设备在连接到以下应用程序或服务时，可以使用基于证书的身份验证 （CBA） 使用其设备上的客户端证书对 Azure 活动目录 （Azure AD） 进行身份验证：

* Office 移动应用程序，例如 Microsoft Outlook 和 Microsoft Word
* Exchange ActiveSync (EAS) 客户端

使用证书无需在移动设备上的某些邮件和 Microsoft Office 应用程序中输入用户名和密码组合。

本文详细介绍了在 iOS 设备上配置 CBA 的要求和支持的方案。 适用于 iOS 的 CBA 可在 Azure 公共云、微软政府云、微软云德国和微软 Azure 中国 21Vianet 之间使用。

## <a name="microsoft-mobile-applications-support"></a>Microsoft 移动应用程序支持

| 应用 | 支持 |
| --- | --- |
| Azure 信息保护应用 |![对号，表示支持此应用程序][1] |
| Intune 公司门户 |![对号，表示支持此应用程序][1] |
| Microsoft Teams |![对号，表示支持此应用程序][1] |
| OneNote |![对号，表示支持此应用程序][1] |
| OneDrive |![对号，表示支持此应用程序][1] |
| Outlook |![对号，表示支持此应用程序][1] |
| Power BI |![对号，表示支持此应用程序][1] |
| Skype for Business |![对号，表示支持此应用程序][1] |
| Word/Excel/PowerPoint |![对号，表示支持此应用程序][1] |
| Yammer |![对号，表示支持此应用程序][1] |

## <a name="requirements"></a>要求

要将 CBA 与 iOS 一起使用，请执行以下要求和注意事项：

* 设备操作系统版本必须为 iOS 9 或以上。
* iOS 设备上的 Office 应用程序需要安装 Microsoft Authenticator。
* 必须在包含 ADFS 服务器身份验证 URL 的 macOS 钥匙串中创建标识首选项。 有关详细信息，请参阅在[Mac 上的钥匙串访问中创建标识首选项](https://support.apple.com/guide/keychain-access/create-an-identity-preference-kyca6343b6c9/mac)。

以下活动目录联合服务 （ADFS） 要求和注意事项适用：

* 必须启用 ADFS 服务器进行证书身份验证并使用联合身份验证。
* 证书必须使用增强密钥使用 （EKU） 并在*主题替代名称 （NT 主体名称）* 中包含用户的 UPN。

## <a name="configure-adfs"></a>配置 ADFS

对于 Azure AD 以撤消客户端证书，ADFS 令牌必须具有以下声明。 Azure AD 如果这些声明在 ADFS 令牌（或任何其他 SAML 令牌）中可用，则它们将这些声明添加到刷新令牌中。 当需要验证刷新令牌时，此信息用于检查吊销：

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>`- 添加客户端证书的序列号
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>`- 添加客户端证书颁发者字符串

最佳做法是，您还应使用以下信息更新组织的 ADFS 错误页：

* 在 iOS 上安装 Microsoft 身份验证器的要求。
* 有关如何获取用户证书的说明。

有关详细信息，请参阅自定义[AD FS 登录页](https://technet.microsoft.com/library/dn280950.aspx)。

## <a name="use-modern-authentication-with-office-apps"></a>将现代身份验证与 Office 应用一起使用

启用了新身份验证的某些 Office`prompt=login`应用在其请求中发送到 Azure AD。 默认情况下，Azure AD 将`prompt=login`请求中转换为 ADFS（`wauth=usernamepassworduri`要求 ADFS 执行 U/P Auth）和`wfresh=0`（要求 ADFS 忽略 SSO 状态并执行新的身份验证）。 如果要为这些应用启用基于证书的身份验证，请修改默认的 Azure AD 行为。

要更新默认行为，请将联合域设置中的"*提示登录行为*"设置为 *"已禁用*"。 您可以使用[MSOLDomain联邦设置](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0)cmdlet 执行此任务，如以下示例所示：

```powershell
Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled
```

## <a name="support-for-exchange-activesync-clients"></a>支持 Exchange ActiveSync 客户端

iOS 9 或更高版本支持本机 iOS 邮件客户端。 要确定所有其他 Exchange ActiveSync 应用程序是否支持此功能，请与应用程序开发人员联系。

## <a name="next-steps"></a>后续步骤

要在环境中配置基于证书的身份验证，请参阅[开始使用基于证书的身份验证](active-directory-certificate-based-authentication-get-started.md)获取说明。

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png
