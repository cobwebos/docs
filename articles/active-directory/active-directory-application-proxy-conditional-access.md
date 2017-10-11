---
title: "对本地应用的条件访问 - Azure AD | Microsoft Docs"
description: "介绍如何设置使用 Azure AD 应用程序代理发布远程访问的应用程序的条件性访问。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 2e97722b-eb4e-4078-b607-9fed210d8a0f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro; oldportal
ms.openlocfilehash: 463946256f9e335fa6d98fc904835e5c3dc2725e
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2017
---
# <a name="working-with-conditional-access-in-azure-ad-application-proxy"></a>使用 Azure AD 应用程序代理中的条件访问

>[!NOTE]
>本文适用于将要停用的 Azure 经典门户。 我们建议你使用 [Azure 门户](https://portal.azure.com)。 在 Azure 门户中，应用程序代理应用具有与任何其他 SaaS 应用相同的条件访问功能。 若要了解有关条件访问的详细信息，请参阅 [Azure Active Directory 中的条件访问入门](active-directory-conditional-access-azure-portal-get-started.md)。

可以配置访问规则，以便授予对使用应用程序代理发布的应用程序的条件性访问权限。 这样，便可以：

* 基于应用程序要求多重身份验证
* 仅当用户未工作时要求多重身份验证
* 当用户未工作时阻止其访问应用程序

这些规则可以应用于所有用户和组，或仅应用于特定用户和组。 默认情况下，规则将应用于有权访问应用程序的所有用户。 但是规则也可以只适用于属于指定安全组成员的用户。  

当用户访问使用 OAuth 2.0、OpenID Connect、SAML 或 WS-Federation 的联合应用程序时，则会计算访问规则。 此外，当刷新令牌用于获取访问令牌时，则会使用 OAuth 2.0 和 OpenID Connect 计算访问规则。

## <a name="conditional-access-prerequisites"></a>条件性访问先决条件
* Azure Active Directory Premium 订阅
* 联合或托管的 Azure Active Directory 租户
* 联合租户需要多重身份验证 (MFA)  
    ![配置访问规则 - 需要多重身份验证](./media/active-directory-application-proxy-conditional-access/application-proxy-conditional-access.png)

## <a name="configure-per-application-multi-factor-authentication"></a>配置每应用程序多重身份验证
1. 在 Azure 经典门户中，以管理员身份进行登录。
2. 转到 Active Directory，并选择要在其中启用应用程序代理的目录。
3. 单击“应用程序”，并向下滚动到“访问规则”部分。 只有使用应用程序代理（该代理使用联合身份验证）发布的应用程序才会显示“访问规则”部分。
4. 通过选择“打开”“启用访问规则”来启用规则。
5. 指定向其应用规则的用户和组。 使用“添加组”按钮选择访问规则会应用于的一个或多个组。 此对话框还可以用于删除所选组。  当选择的规则应用于组时，仅会对属于指定安全组之一的用户强制执行访问规则。  

   * 若要从规则中显式排除安全组，请选中“除外”并指定一个或多个组。 属于“例外”列表中的组成员的用户不需要执行多重身份验证。  
   * 如果已使用每用户多重身份验证功能配置用户，则此设置的优先级高于应用程序的多重身份验证规则。 已配置为每用户多重身份验证的用户将需要执行多重身份验证，即使他们已从应用程序的多重身份验证规则中免除。 了解有关[多重身份验证和每个用户设置](../multi-factor-authentication/multi-factor-authentication.md)的详细信息。
6. 选择要设置的访问规则：

   * **需要多重身份验证**：向其应用访问规则的用户需要完成多重身份验证，才能访问规则应用到的应用程序。
   * **不工作时要求多重身份验证**：尝试从受信任的 IP 地址访问应用程序的用户不必执行多重身份验证。 可以在多重身份验证设置页上配置受信任的 IP 地址范围。
   * **不工作时阻止访问**：尝试从公司网络外部访问应用程序的用户不能访问应用程序。

## <a name="configuring-mfa-for-federation-services"></a>为联合服务配置 MFA
对于联合租户，多重身份验证 (MFA) 可能通过 Azure Active Directory 或本地 AD FS 服务器执行。 默认情况下，MFA 会在 Azure Active Directory 托管的任何页上进行。 若要配置本地 MFA，请运行 Windows PowerShell 并使用 –SupportsMFA 属性设置 Azure AD 模块。

以下示例演示如何在 contoso.com 租户上使用 [Set-MsolDomainFederationSettings cmdlet](https://msdn.microsoft.com/library/azure/dn194088.aspx) 启用本地 MFA：`Set-MsolDomainFederationSettings -DomainName contoso.com -SupportsMFA $true `

除了设置此标志之外，必须配置联合租户 AD FS 实例，以执行多重身份验证。 请按照[在本地部署 Microsoft Azure 多重身份验证](../multi-factor-authentication/multi-factor-authentication-get-started-server.md)中的说明执行操作。

## <a name="see-also"></a>另请参阅
* [使用声明感知应用程序](active-directory-application-proxy-claims-aware-apps.md)
* [使用应用程序代理发布应用程序](active-directory-application-proxy-publish.md)
* [启用单一登录](active-directory-application-proxy-sso-using-kcd.md)
* 
            [使用自己的域名发布应用程序](active-directory-application-proxy-custom-domains.md)

有关最新新闻和更新，请参阅 [应用程序代理博客](http://blogs.technet.com/b/applicationproxyblog/)
