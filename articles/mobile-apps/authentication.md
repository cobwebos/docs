---
title: 使用可视化工作室应用中心和 Azure 服务向移动应用添加身份验证
description: 了解帮助设置用户身份验证并使移动应用程序使用社交帐户、Azure 活动目录和自定义身份验证进行身份验证的服务（如 Visual Studio 应用中心）。
author: codemillmatt
ms.assetid: 34a8a070-2222-4faf-9090-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 820005bca008ea3bdb59659c815da3ec36beb0d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241050"
---
# <a name="add-authentication-and-manage-user-identities-in-your-mobile-apps"></a>在移动应用中添加身份验证和管理用户身份

通过查看用户及其在整个应用程序中的行为，开发人员可以通过为用户创建量身定制的体验来更好地吸引用户。 无论您是为组织内的用户构建协作应用程序的应用程序开发人员，还是正在创建下一个社交网络平台，您都需要一种方法来验证用户和管理用户身份。 身份管理服务是移动后端服务最重要的功能之一。

使用以下服务在移动应用中启用用户身份验证。

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[应用中心身份验证](/appcenter/auth/)是一种基于云的身份管理服务，开发人员可以使用该服务对用户进行身份验证和管理用户身份。 应用中心 Auth 还与视觉工作室应用中心的其他部分集成。 开发人员可以使用用户身份查看其他服务中的[用户数据](/appcenter/data/index)，甚至[向用户而不是单个设备发送推送通知](/appcenter/push/push-to-user#setting-user-identity)。 

**主要功能**
- 由 Azure 活动目录 B2C（Azure AD B2C）提供支持。 
    - 企业级。
    - 高可用性。
    - 安全和全球服务。
- 携带您自己的身份以及使用其他常用标识和访问管理提供程序（如 Auth0 和 Firebase）的选项。
- Azure 活动目录支持。
    - 连接现有的 Azure AD 租户。 
    - 启用针对公司域的身份验证。
    - 管理对敏感数据的访问。
- 通过将 Microsoft 身份验证库与可视化工作室应用中心 SDK 包装，实现简单的用户体验和神奇的 SDK 体验。
- 对 iOS、Android、Xamarin 和反应本机平台支持。

**引用**
- [使用可视化工作室应用中心注册](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs) 
- [开始使用应用中心 Auth](/appcenter/auth/)

## <a name="azure-active-directory-b2c"></a>Azure Active Directory B2C
[Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/)是一种企业对使用者 （B2C） 标识管理服务，开发人员可以使用该服务来验证其客户。 此白标服务使开发人员能够自定义和控制用户如何安全地与其 Web、桌面、移动或单页应用程序进行交互。 用户可以使用 Azure AD B2C 注册、登录、重置密码和编辑个人资料。 Azure AD B2C 实现某种形式的 OpenID Connect 和 OAuth 2.0 协议。 

**主要功能**
- 使用首选身份提供商安全地验证客户。
- 管理客户身份和访问权限。
- 获得对Facebook、GitHub、谷歌、LinkedIn、推特、微信和微博等社交媒体的登录支持。
- 使用行业标准协议（如 OpenID Connect 或 SAML）连接到用户帐户，使身份管理在各种平台上成为可能。
- 提供品牌注册和登录体验。
- 轻松与 CRM 数据库、营销分析工具和帐户验证系统集成。
- 捕获客户的登录、首选项和转换数据。

**引用**
- [Azure 门户](https://portal.azure.com/)
- [Azure AD B2C 文档](/azure/active-directory-b2c/)
- [快速入门](/azure/active-directory-b2c/active-directory-b2c-quickstarts-web-app)
- [示例](/azure/active-directory-b2c/code-samples)

## <a name="azure-active-directory"></a>Azure Active Directory
[Azure 活动目录](https://azure.microsoft.com/services/active-directory/)是 Microsoft 基于云的标识和访问管理服务，可帮助员工登录并访问：
- 外部资源，如 Microsoft Office 365、Azure 门户和数千个其他软件作为服务 （SaaS） 应用程序。
- 内部资源，例如公司网络和 Intranet 上的应用，以及由自己的组织开发的任何云应用。

**主要功能**
- 通过将用户连接到所需的应用程序，实现无缝、高度安全的访问。
- 基于用户、位置、设备、数据和应用程序上下文对身份和访问进行全面的身份保护和增强的安全性。
- 用于商业和自定义应用程序（如 Office 365、Salesforce.com 和 Box）的数千个预集成应用。
- 能够大规模管理访问。

**引用**
- [Azure 门户](https://portal.azure.com/)
- [什么是 Azure AD？](/azure/active-directory/fundamentals/active-directory-whatis)
- [Azure Active Directory 入门](/azure/active-directory/fundamentals/active-directory-whatis)
- [快速入门](/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)