---
title: Azure AD Connect：无缝单一登录 - 工作原理 | Microsoft Docs
description: 本文介绍 Azure Active Directory 无缝单一登录功能的工作原理。
services: active-directory
keywords: 什么是 Azure AD Connect, 安装 Active Directory, Azure AD 所需的组件, SSO, 单一登录
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2018
ms.author: billmath
ms.openlocfilehash: 9d17a4038f2171b74c8ba1dbc21e8335e6893691
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2018
---
# <a name="azure-active-directory-seamless-single-sign-on-technical-deep-dive"></a>Azure Active Directory 无缝单一登录：深入技术探究

本文从技术层面详细介绍了 Azure Active Directory 无缝单一登录（无缝 SSO）功能的工作原理。

## <a name="how-does-seamless-sso-work"></a>无缝 SSO 的工作原理

本节包含三个部分：
1. 无缝 SSO 功能的设置。
2. 如何使用无缝 SSO 在 Web 浏览器上执行单个用户登录事务。
3. 如何使用无缝 SSO 在本机客户端上执行单个用户登录事务。

### <a name="how-does-set-up-work"></a>如何进行设置？

可使用 Azure AD Connect 启用无缝 SSO，如[此处](active-directory-aadconnect-sso-quick-start.md)所示。 启用该功能时，请执行以下步骤：
- 在本地 Active Directory (AD) 中创建一个名为 `AZUREADSSOACC` 的计算机帐户（表示 Azure AD）。
- 与 Azure AD 安全共享计算机帐户的 Kerberos 解密密钥。
- 此外，创建两个 Kerberos 服务主体名称 (SPN) 来表示 Azure AD 登录期间使用的两个 URL。

>[!NOTE]
> 在与 Azure AD 同步（使用 Azure AD Connect）且要为其用户启用无缝 SSO 的每个 AD 林中创建计算机帐户和 Kerberos SPN。 将 `AZUREADSSOACC` 计算机帐户移动到在其中存储其他计算机帐户的组织单位 (OU)，确保以相同的方式对该帐户进行管理，并确保它不会被删除。

>[!IMPORTANT]
>我们强烈建议至少每隔 30 天滚动更新一次 `AZUREADSSOACC` 计算机帐户的 [Kerberos 解密密钥](active-directory-aadconnect-sso-faq.md#how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account)。

完成此设置后，无缝 SSO 的工作方式与使用集成 Windows 身份验证 (IWA) 的任何其他登录方式相同。

### <a name="how-does-sign-in-on-a-web-browser-with-seamless-sso-work"></a>如何使用无缝 SSO 在 Web 浏览器上进行登录？

在 Web 浏览器上进行登录的流程如下所示：

1. 用户尝试从企业网络内部已加入域的企业设备访问 Web 应用程序（例如，Outlook Web App - https://outlook.office365.com/owa/）。
2. 如果用户尚未登录，该用户将被重定向到 Azure AD 登录页面。
3. 用户在 Azure AD 登录页键入其用户名。

  >[!NOTE]
  >对于[某些应用程序](./active-directory-aadconnect-sso-faq.md#what-applications-take-advantage-of-domainhint-or-loginhint-parameter-capability-of-seamless-sso)，可跳过步骤 2 和 3。

4. Azure AD 在后台使用 JavaScript，通过“401 未授权”响应质询浏览器，以提供 Kerberos 票证。
5. 然后，浏览器请求从 Active Directory 获取用于 `AZUREADSSOACC` 计算机帐户（表示 Azure AD）的票证。
6. Active Directory 查找计算机帐户，然后将使用计算机帐户机密加密的 Kerberos 票证返回给浏览器。
7. 浏览器将其从 Active Directory 获取的 Kerberos 票证转发给 Azure AD。
8. Azure AD 使用以前共享的密钥对 Kerberos 票证进行解密，其中包括登录到企业设备的用户的标识。
9. 在评估之后，Azure AD 会将一个令牌返回给应用程序，或者要求用户执行其他验证，例如多重身份验证。
10. 如果用户登录成功，则该用户可以访问应用程序。

下图说明了所涉及的所有组件和步骤。

![无缝单一登录 - Web 应用流](./media/active-directory-aadconnect-sso/sso2.png)

无缝 SSO 是一项机会型功能，这意味着，如果失败，登录体验将回退到其常规行为 - 即，用户需要输入密码才能登录。

### <a name="how-does-sign-in-on-a-native-client-with-seamless-sso-work"></a>如何使用无缝 SSO 在本地客户端上进行登录？

在本地客户端上进行登录的流程如下所示：

1. 用户尝试从企业网络内部已加入域的企业设备访问本地应用程序（例如，Outlook 客户端）。
2. 如果用户尚未登录，本地应用程序将从设备的 Windows 会话中检索用户的用户名。
3. 应用将用户名发送至 Azure AD，并检索租户的 WS-Trust MEX 终结点。
4. 应用随后查询 WS-Trust MEX 终结点，查看集成身份验证终结点是否可用。
5. 如果步骤 4 成功，则颁发 Kerberos 质询。
6. 如果应用还能检索 Kerberos 票证，则该应用会将其转发到 Azure AD 的集成身份验证终结点。
7. Azure AD 解密 Kerberos 票证并对其进行验证。
8. Azure AD 授权用户登录，并向应用颁发 SAML 令牌。
9. 然后应用将 SAML 令牌提交到 Azure AD 的 OAuth2 令牌终结点。
10. Azure AD 验证 SAML 令牌，并向应用颁发指定资源的访问令牌和刷新令牌，以及 id 令牌。
11. 用户获取访问应用资源的权限。

下图说明了所涉及的所有组件和步骤。

![无缝单一登录 - 本机应用流](./media/active-directory-aadconnect-sso/sso14.png)

## <a name="next-steps"></a>后续步骤

- [快速入门](active-directory-aadconnect-sso-quick-start.md) - 启动并运行 Azure AD 无缝 SSO。
- [**常见问题**](active-directory-aadconnect-sso-faq.md) - 常见问题解答。
- [故障排除](active-directory-aadconnect-troubleshoot-sso.md) - 了解如何解决使用此功能时遇到的常见问题。
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - 用于填写新功能请求。
