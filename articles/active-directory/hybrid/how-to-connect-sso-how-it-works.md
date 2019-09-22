---
title: Azure AD Connect：无缝单一登录 - 工作原理 | Microsoft Docs
description: 本文介绍 Azure Active Directory 无缝单一登录功能的工作原理。
services: active-directory
keywords: 什么是 Azure AD Connect, 安装 Active Directory, Azure AD 所需的组件, SSO, 单一登录
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/16/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd4743bc38c3b2b4b9495b33535b4b73f48d1372
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2019
ms.locfileid: "71176675"
---
# <a name="azure-active-directory-seamless-single-sign-on-technical-deep-dive"></a>Azure Active Directory 无缝单一登录：技术深入了解

本文从技术层面详细介绍了 Azure Active Directory 无缝单一登录（无缝 SSO）功能的工作原理。

## <a name="how-does-seamless-sso-work"></a>无缝 SSO 的工作原理

本节包含三个部分：

1. 无缝 SSO 功能的设置。
2. 如何使用无缝 SSO 在 Web 浏览器上执行单个用户登录事务。
3. 如何使用无缝 SSO 在本机客户端上执行单个用户登录事务。

### <a name="how-does-set-up-work"></a>如何进行设置？

可使用 Azure AD Connect 启用无缝 SSO，如[此处](how-to-connect-sso-quick-start.md)所示。 启用该功能时，请执行以下步骤：

- 计算机帐户（`AZUREADSSOACC`）在你同步到 Azure AD （使用 Azure AD Connect）的每个 AD 林中的本地 Active Directory （AD）中创建。
- 此外，还会创建一些 Kerberos 服务主体名称（Spn），以便在 Azure AD 登录过程中使用。
- 与 Azure AD 安全共享计算机帐户的 Kerberos 解密密钥。 如果有多个 AD 林，则每个计算机帐户都有其自己的唯一 Kerberos 解密密钥。

>[!IMPORTANT]
> 出于安全原因，需要对计算机帐户进行严格保护。`AZUREADSSOACC` 只有域管理员才能管理计算机帐户。 请确保计算机帐户上的 Kerberos 委派处于禁用状态，并且 Active Directory 中的其他帐户没有对计算机帐户的`AZUREADSSOACC`委派权限。 将计算机帐户存储在组织单位（OU）中，在这种情况下，用户可以放心地删除意外删除，并且只有域管理员才有权访问。 计算机帐户上的 Kerberos 解密密钥也应视为敏感。 我们强烈建议至少每隔 30 天滚动更新一次 `AZUREADSSOACC` 计算机帐户的 [Kerberos 解密密钥](how-to-connect-sso-faq.md)。

完成此设置后，无缝 SSO 的工作方式与使用集成 Windows 身份验证 (IWA) 的任何其他登录方式相同。

### <a name="how-does-sign-in-on-a-web-browser-with-seamless-sso-work"></a>如何使用无缝 SSO 在 Web 浏览器上进行登录？

在 Web 浏览器上进行登录的流程如下所示：

1. 用户尝试从企业网络内部已加入域的企业设备访问 Web 应用程序（例如，Outlook Web 应用 - https://outlook.office365.com/owa/) ）。
2. 如果用户尚未登录，该用户将被重定向到 Azure AD 登录页面。
3. 用户在 Azure AD 登录页键入其用户名。

   >[!NOTE]
   >对于[某些应用程序](./how-to-connect-sso-faq.md)，可跳过步骤 2 和 3。

4. Azure AD 在后台使用 JavaScript，通过“401 未授权”响应质询浏览器，以提供 Kerberos 票证。
5. 然后，浏览器请求从 Active Directory 获取用于 `AZUREADSSOACC` 计算机帐户（表示 Azure AD）的票证。
6. Active Directory 查找计算机帐户，然后将使用计算机帐户机密加密的 Kerberos 票证返回给浏览器。
7. 浏览器将其从 Active Directory 获取的 Kerberos 票证转发给 Azure AD。
8. Azure AD 使用以前共享的密钥对 Kerberos 票证进行解密，其中包括登录到企业设备的用户的标识。
9. 在评估之后，Azure AD 会将一个令牌返回给应用程序，或者要求用户执行其他验证，例如多重身份验证。
10. 如果用户登录成功，则该用户可以访问应用程序。

下图说明了所涉及的所有组件和步骤。

![无缝单一登录 - Web 应用流](./media/how-to-connect-sso-how-it-works/sso2.png)

无缝 SSO 是一项机会型功能，这意味着，如果失败，登录体验将回退到其常规行为 - 即，用户需要输入密码才能登录。

### <a name="how-does-sign-in-on-a-native-client-with-seamless-sso-work"></a>如何使用无缝 SSO 在本地客户端上进行登录？

在本地客户端上进行登录的流程如下所示：

1. 用户尝试从企业网络内部已加入域的企业设备访问本地应用程序（例如，Outlook 客户端）。
2. 如果用户尚未登录，本地应用程序将从设备的 Windows 会话中检索用户的用户名。
3. 应用将用户名发送至 Azure AD，并检索租户的 WS-Trust MEX 终结点。 此 WS-Trust 终结点由无缝 SSO 功能独占使用，并不是 Azure AD 上的 WS-Trust 协议的常规实现。
4. 应用随后查询 WS-Trust MEX 终结点，查看集成身份验证终结点是否可用。 集成身份验证终结点由无缝 SSO 功能独占使用。
5. 如果步骤 4 成功，则颁发 Kerberos 质询。
6. 如果应用还能检索 Kerberos 票证，则该应用会将其转发到 Azure AD 的集成身份验证终结点。
7. Azure AD 解密 Kerberos 票证并对其进行验证。
8. Azure AD 授权用户登录，并向应用颁发 SAML 令牌。
9. 然后应用将 SAML 令牌提交到 Azure AD 的 OAuth2 令牌终结点。
10. Azure AD 验证 SAML 令牌，并向应用颁发指定资源的访问令牌和刷新令牌，以及 id 令牌。
11. 用户获取访问应用资源的权限。

下图说明了所涉及的所有组件和步骤。

![无缝单一登录 - 本机应用流](./media/how-to-connect-sso-how-it-works/sso14.png)

## <a name="next-steps"></a>后续步骤

- [快速入门](how-to-connect-sso-quick-start.md) - 启动并运行 Azure AD 无缝 SSO。
- [**常见问题**](how-to-connect-sso-faq.md) - 常见问题的解答。
- [故障排除](tshoot-connect-sso.md) - 了解如何解决使用此功能时遇到的常见问题。
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - 用于填写新功能请求。
