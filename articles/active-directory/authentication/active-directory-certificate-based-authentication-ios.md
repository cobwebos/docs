---
title: iOS 上基于证书的身份验证 - Azure Active Directory
description: 了解 iOS 设备的解决方案中配置基于证书的身份验证的支持方案和要求
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: annaba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fd8a0c3688e5056c7941d334da8caee9f21ba82
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81407269"
---
# <a name="azure-active-directory-certificate-based-authentication-on-ios"></a>iOS 上 Azure Active Directory 基于证书的身份验证

连接到以下项时，iOS 设备可以通过基于证书的身份验证 (CBA) 在其设备上使用客户端证书向 Azure Active Directory 进行身份验证：

* Office 移动应用程序，例如 Microsoft Outlook 和 Microsoft Word
* Exchange ActiveSync (EAS) 客户端

如果配置了此功能，就无需在移动设备上的某些邮件和 Microsoft Office 应用程序中输入用户名和密码组合。

本主题为您提供了在 iOS 设备上为 Office 365 企业、商业、教育、美国政府、中国和德国计划中租户用户配置 CBA 的要求和支持的方案。

此功能在 Office 365 US Government Defense 和 Federal 计划中以预览形式提供。

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

设备 OS 版本必须为 iOS 9 及更高版本

必须配置联合服务器。

iOS 设备上的 Office 应用程序需要安装 Microsoft Authenticator。

若要让 Azure Active Directory 吊销客户端证书，ADFS 令牌必须具有以下声明：

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>`（客户端证书的序列号）
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>`（客户端证书颁发者的字符串）

如果 ADFS 令牌（或任何其他 SAML 令牌）具有这些声明，Azure Active Directory 会将这些声明添加到刷新令牌中。 当需要验证刷新令牌时，此信息可用于检查吊销。

最佳做法是，应该使用以下信息更新组织的 ADFS 错误页：

* 在 iOS 设备上安装 Microsoft Authenticator 的要求
* 有关如何获取用户证书的说明。

有关详细信息，请参阅[自定义 AD FS 登录页](https://technet.microsoft.com/library/dn280950.aspx)。

某些 Office 应用（启用了现代身份验证）在其请求中向 Azure AD 发送"*提示\登录*"。 默认情况下，Azure AD 在请求 ADFS 时将"*提示=登录*"转换为"*哇*"（要求 ADFS 执行 U/P Auth）和 *"wfresh_0"（* 要求 ADFS 忽略 SSO 状态并进行新的身份验证）。 如果想要为这些应用启用基于证书的身份验证，需要修改默认 Azure AD 行为。 只需将联合域设置中的"*提示登录行为*"设置为"*已禁用*"。
可使用 [MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) cmdlet 执行此任务：

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`

## <a name="exchange-activesync-clients-support"></a>Exchange ActiveSync 客户端支持

iOS 9 或更高版本支持本机 iOS 邮件客户端。 若要确定其他所有 Exchange ActiveSync 应用程序是否支持此功能，请联系应用程序开发人员。

## <a name="next-steps"></a>后续步骤

如果想要在环境中配置基于证书的身份验证，请参阅 [Android 上基于证书的身份验证入门](../authentication/active-directory-certificate-based-authentication-get-started.md)了解相关说明。

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png
