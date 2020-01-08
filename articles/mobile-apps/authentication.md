---
title: 通过 Visual Studio App Center 和 Azure 服务向移动应用添加身份验证
description: 了解 Visual Studio App Center 的服务，这些服务可帮助设置用户身份验证，并使移动应用程序能够使用社交帐户、Azure Active Directory 和自定义身份验证进行身份验证。
author: elamalani
ms.assetid: 34a8a070-2222-4faf-9090-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: bc7d8d4aaec6ebe27a0f8d2ecc11ca408266f7ef
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453243"
---
# <a name="add-authentication-and-manage-user-identities-in-your-mobile-apps"></a>在移动应用中添加身份验证和管理用户标识

在应用程序中拥有用户及其行为的视图使开发人员可以通过为用户提供定制体验来更好地与用户合作。 无论你是为组织内的用户构建协作应用程序的应用程序开发人员，还是在创建下一社交网络平台，你都需要一种方法来对用户进行身份验证和管理用户标识。 标识管理服务是移动后端服务的最重要的功能之一。

使用以下服务在移动应用中启用用户身份验证。

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center](/appcenter/auth/) authentication 是一项基于云的标识管理服务，开发人员可以使用它来对用户进行身份验证和管理用户标识。 App Center 身份验证还与 Visual Studio App Center 的其他部分集成。 开发人员可以使用用户标识来查看其他服务中的[用户数据](/appcenter/data/index)，甚至可以将[推送通知发送给用户，而不是单独的设备](/appcenter/push/push-to-user#app-center-auth-set-identity)。 

**主要功能**
- 支持的 Azure Active Directory B2C （Azure AD B2C）。 
    - 企业级。
    - 高可用性。
    - 安全和全局服务。
- 使用你自己的标识和选项来使用其他常用的标识和访问管理提供程序，例如 Auth0 和 Firebase。
- Azure Active Directory 支持。
    - 连接现有 Azure AD 租户。 
    - 对公司域启用身份验证。
    - 管理对敏感数据的访问。
- 通过使用 Visual Studio App Center SDK 包装 Microsoft 身份验证库，简单的用户体验和神奇 SDK 体验。
- 适用于 iOS、Android、Xamarin 和响应本机的平台支持。

**参考**
- [注册 Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs) 
- [App Center Auth 入门](/appcenter/auth/)

## <a name="azure-active-directory-b2c"></a>Azure Active Directory B2C
[Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/)是一种企业对消费者（B2C）标识管理服务，开发人员可以使用该服务对其客户进行身份验证。 利用这一空白服务，开发人员可以自定义和控制用户如何与 web、桌面、移动或单页应用程序进行安全交互。 用户可以使用 Azure AD B2C 注册、登录、重置密码和编辑个人资料。 Azure AD B2C 实现某种形式的 OpenID Connect 和 OAuth 2.0 协议。 

**主要功能**
- 安全地通过其首选标识提供者对客户进行身份验证。
- 管理客户标识和访问权限。
- 获取对社交媒体（如 Facebook、GitHub、Google、LinkedIn、Twitter、WeChat 和 Weibo）的登录支持。
- 使用行业标准协议（例如 OpenID Connect 或 SAML）连接到用户帐户，以便在各种平台上实现标识管理。
- 提供品牌注册和登录体验。
- 轻松与 CRM 数据库、营销分析工具和帐户验证系统集成。
- 捕获客户的登录、首选项和转换数据。

**参考**
- [Azure 门户](https://portal.azure.com/)
- [Azure AD B2C 文档](/azure/active-directory-b2c/)
- [快速入门](/azure/active-directory-b2c/active-directory-b2c-quickstarts-web-app)
- [示例](/azure/active-directory-b2c/code-samples)

## <a name="azure-active-directory"></a>Azure Active Directory
[Azure Active Directory](https://azure.microsoft.com/services/active-directory/)是 Microsoft 的基于云的标识和访问管理服务，可帮助员工登录并获取访问权限：
- 外部资源，例如 Microsoft Office 365、Azure 门户和上千个其他软件即服务（SaaS）应用程序。
- 内部资源，例如公司网络和 Intranet 上的应用，以及由自己的组织开发的任何云应用。

**主要功能**
- 通过将用户连接到他们所需的应用程序，实现无缝且高度安全的访问。
- 全面的标识保护和基于用户、位置、设备、数据和应用程序上下文的访问的增强安全性。
- 适用于商用和自定义应用程序（如 Office 365、Salesforce.com 和 Box）的数千个预先集成的应用程序。
- 能够大规模管理访问权限。

**参考**
- [Azure 门户](https://portal.azure.com/)
- [什么是 Azure AD？](/azure/active-directory/fundamentals/active-directory-whatis)
- [Azure Active Directory 入门](/azure/active-directory/fundamentals/active-directory-whatis)
- [快速入门](/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)