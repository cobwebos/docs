---
title: 通过 Visual Studio App Center 和 Azure 服务向移动应用添加身份验证
description: 了解 App Center 的服务，这些服务可帮助设置用户身份验证，并使移动应用程序能够使用社交帐户、Azure Active Directory 和自定义身份验证进行身份验证。
author: elamalani
ms.assetid: 34a8a070-2222-4faf-9090-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 8885f6cd91179f4dee6456277d47581f68c21723
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795752"
---
# <a name="add-authentication-and-manage-user-identities-in-your-mobile-apps"></a>在移动应用中添加身份验证和管理用户标识

在应用程序中拥有用户及其行为的视图使开发人员可以通过为用户提供定制体验来更好地与用户合作。 无论你是应用程序开发人员为组织内的用户构建协作应用程序，还是要创建下一社交网络平台，你都需要一种方法来对用户进行身份验证和管理用户标识。 使用标识管理服务是移动后端服务的最重要功能之一。

使用以下服务在移动应用中启用用户身份验证。

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center](/appcenter/auth/) authentication 是一项基于云的标识管理服务，它使开发人员能够对用户进行身份验证和管理用户标识。 App Center authentication 还与 App Center 的其他部分集成，使开发人员能够利用用户标识来查看其他服务中的[用户数据](/appcenter/data/index)，甚至[将推送通知发送给用户，而不是将其发送到单个设备](/appcenter/push/push-to-user#app-center-auth-set-identity)。 

**关键功能**
- **支持的 Azure Active Directory B2C （Azure AD B2C）** 
    - 企业级。
    - 高可用性。
    - 安全和全局服务。
- **自带标识**并使用其他常用的标识和访问管理提供程序（如 Auth0 和 Firebase）。
- **AAD 支持** 
    - 连接现有 AAD 租户。 
    - 对公司域启用身份验证。
    - 管理对敏感数据的访问。
- **简单的用户体验**和神奇 sdk 体验，方法是使用 App Center SDK 包装 MSAL 库。
- **平台支持**-IOS、Android、Xamarin、响应本机。

**参考**
- [注册 App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs) 
- [App Center Auth 入门](/appcenter/auth/)

## <a name="azure-active-directory-b2c"></a>Azure Active Directory B2C
[Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/)是一种企业对消费者标识管理服务，使开发人员能够对其客户进行身份验证。 利用这一空白服务，开发人员可以自定义和控制用户如何与 web、桌面、移动或单页应用程序进行安全交互。 用户可以使用 Azure AD B2C 注册、登录、重置密码和编辑个人资料。 Azure AD B2C 实现某种形式的 OpenID Connect 和 OAuth 2.0 协议。 

**关键功能**
- 安全地通过其首选标识提供者对客户进行身份验证。
- **客户标识和访问管理**。
- 支持社交登录，如 Facebook、GitHub、Google、LinkedIn、Twitter、WeChat、Weibo。
- 使用 OpenID Connect 或 SAML 等**行业标准协议**连接到用户帐户，以便能够在各种平台上进行标识管理。
- 提供品牌注册和登录体验。
- 轻松与 CRM 数据库、营销分析工具和帐户验证系统集成。
- 捕获客户的登录名、首选项和转换数据。

**参考**
- [Azure 门户](https://portal.azure.com/)
- [Azure AD B2C 文档](/azure/active-directory-b2c/)
- [快速入门](/azure/active-directory-b2c/active-directory-b2c-quickstarts-web-app)
- [示例](/azure/active-directory-b2c/code-samples)

## <a name="azure-active-directory"></a>Azure Active Directory
[Azure Active Directory （Azure AD）](https://azure.microsoft.com/services/active-directory/)是 Microsoft 的基于云的标识和访问管理服务，可帮助你的员工登录和访问。
- 外部资源，例如 Microsoft Office 365、Azure 门户以及成千上万的其他 SaaS 应用程序。
- 内部资源，例如公司网络和 Intranet 上的应用，以及由自己的组织开发的任何云应用。

**关键功能**
- 通过将用户连接到他们所需的应用程序 **，实现无缝且高度安全的访问**。
- **全面的标识保护**和基于用户、位置、设备、数据和应用程序上下文的访问的增强安全性。
- **数千个预先集成的应用**程序，例如 Office 365、Salesforce.com 和 Box。
- **大规模管理访问权限**。

**参考**
- [Azure 门户](https://portal.azure.com/)
- [什么是 Azure AD？](/azure/active-directory/fundamentals/active-directory-whatis)
- [Azure Active Directory 入门](/azure/active-directory/fundamentals/active-directory-whatis)
- [快速入门](/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)