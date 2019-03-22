---
title: SAML 单一登录的本地应用程序与 Azure Active Directory 应用程序代理 （预览版） |Microsoft Docs
description: 了解如何提供单一登录，以便在本地通过 SAML 身份验证进行保护的应用程序代理发布应用程序。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: celested
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 415b33dce42945c40aedd996d4dcfa5c6b987b44
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/21/2019
ms.locfileid: "58336212"
---
# <a name="saml-single-sign-on-for-on-premises-applications-with-application-proxy-preview"></a>SAML 单一登录对应用程序代理 （预览版） 的本地应用程序

可以向本地应用程序的 SAML 身份验证进行保护，并提供对这些应用程序通过应用程序代理远程访问提供单一登录 (SSO)。 SAML 单一登录，Azure Active Directory (Azure AD) 进行身份验证对应用程序使用用户的 Azure AD 帐户。 Azure AD 通过连接协议将登录信息传递给应用程序。 此外可以将用户映射到特定的应用程序角色根据在 SAML 声明中定义的规则。 除了 SAML SSO 的应用程序代理，从而您的用户将具有外部访问应用程序和无缝 SSO 体验。

应用程序必须能够使用颁发的 SAML 令牌**Azure Active Directory**。 此配置不适用于使用本地标识提供程序的应用程序。 这些方案中，我们建议查看[资源迁移到 Azure AD 应用程序](migration-resources.md)。

SAML SSO 与应用程序代理也适用于 SAML 令牌加密功能。 有关详细信息，请参阅[配置 Azure AD SAML 令牌加密](howto-saml-token-encryption.md)。

## <a name="publish-the-on-premises-application-with-application-proxy"></a>发布的本地应用程序使用应用程序代理

可以为本地应用程序提供 SSO 之前，请确保已启用应用程序代理，并且已安装连接器。 请参阅[在 Azure AD 中添加的本地应用程序的应用程序代理远程访问](application-proxy-add-on-premises-application.md)若要了解如何。

当你即将完成本教程，请记住以下：

* 在本教程中发布你的应用程序根据的说明。 请务必选择**Azure Active Directory**作为**预身份验证**方法为应用程序 (步骤 4[添加到 Azure AD 的本地应用](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad
))。
* 复制**外部 URL**应用程序。
* 最佳做法是，将自定义域，只要有可能用于优化的用户体验。 详细了解如何[使用的 Azure AD 应用程序代理中的自定义域](application-proxy-configure-custom-domain.md)。
* 将至少一个用户添加到应用程序，并确保测试帐户有权访问的本地应用程序。

## <a name="set-up-saml-sso"></a>设置 SAML SSO

1. 在 Azure 门户中，选择**Azure Active Directory > 企业应用程序**从列表中选择的应用程序。
1. 从应用程序的**概述**页上，选择**单一登录**。
1. 选择**SAML**作为单一登录方法。
1. 在**设置了单一登录使用 SAML**页上，编辑**基本 SAML 配置**数据并按照中的步骤[Enter 基本 SAML 配置](configure-single-sign-on-non-gallery-applications.md#saml-based-single-sign-on)若要配置基于 SAML 的应用程序的身份验证。

   * 请确保**回复 URL**根匹配或已下的路径**外部 URL**在 Azure AD 中添加用于通过应用程序代理远程访问的本地应用程序。

     ![输入基本 SAML 配置数据](./media/application-proxy-configure-single-sign-on-on-premises-apps/basic-saml-configuration.png)

    > [!NOTE]
    > 如果后端应用程序期望**回复 URL**是内部 URL，你将需要在用户的设备上安装我的应用安全登录扩展。 此扩展会自动重定向到相应的应用程序代理服务。 若要安装扩展，请参阅[我的应用安全登录扩展](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension)。

## <a name="test-your-app"></a>测试应用

完成所有这些步骤后，应用应会启动并运行。 若要测试应用程序：

1. 打开浏览器并导航到发布应用时创建的外部 URL。 
1. 使用分配给应用的测试帐户登录。

## <a name="next-steps"></a>后续步骤

- [Azure AD 应用程序代理如何提供单一登录？](application-proxy-single-sign-on.md)
- [排查应用程序代理问题](application-proxy-troubleshoot.md)
