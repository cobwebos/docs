---
title: 如何与 Azure Active Directory 集成 | Microsoft Docs
description: 介绍与 Azure Active Directory 集成的好处与相关资源的指南。
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: d13bba54-96bd-4b81-bee9-c8025ffa1648
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: celested
ms.reviewer: bryanla
ms.custom: aaddev
ms.openlocfilehash: be7dec07597b0a82633d330a72274a94dbb9bf67
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2018
---
# <a name="integrating-with-azure-active-directory"></a>与 Azure Active Directory 集成
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Azure Active Directory 为组织的云应用程序提供企业级标识管理。 Azure AD 集成可以简化用户登录体验，并帮助应用程序符合 IT 策略。

## <a name="how-to-integrate"></a>如何集成
应用程序可通过多种方式与 Azure AD 集成。 请根据应用程序，利用其中的一个或多个方案。

### <a name="support-azure-ad-as-a-way-to-sign-in-to-your-application"></a>支持使用 Azure AD 作为登录应用程序的方式
**减少登录阻碍并降低支持成本。** 如果使用 Azure AD 登录应用程序，用户不需要记住更多的名称和密码。 作为开发人员，可以减少要存储和保护的密码。 无需重置忘记的密码，单凭这一点就能节省不少的精力。 使用 Azure AD 可以登录世界上最热门的一些云应用程序，包括 Office 365 和 Microsoft Azure。 Azure AD 包含了来自数百万家组织的几亿个用户，用户很可能已经登录到了 Azure AD。 深入了解如何[添加 Azure AD 登录支持](active-directory-authentication-scenarios.md)。

**简化应用程序的注册过程。**  在注册应用程序期间，Azure AD 可以发送有关用户的基本信息，以便可以预先填充注册表单，或者完全清除表单。 用户可以使用其 Azure AD 帐户，通过社交媒体和移动应用程序中常见的许可体验注册应用程序。 任何用户都可以注册和登录与 Azure AD 集成的应用程序，而无需 IT 人员的参与。 深入了解如何[注册应用程序进行 Azure AD 帐户登录](../../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md)。

### <a name="browse-for-users-manage-user-provisioning-and-control-access-to-your-application"></a>浏览用户，管理用户设置，以及控制对应用程序的访问
**浏览目录中的用户。**  在邀请其他人或授予访问权限时，可以使用 Graph API 来帮助用户搜索和浏览其组织中的其他人员，而无需键入电子邮件地址。 用户可以使用熟悉的通讯簿样式界面进行浏览，包括查看组织层次结构的详细信息。 深入了解[图形 API](active-directory-graph-api.md)。

**重复使用客户正在管理的 Active Directory 组和通讯组列表。**  Azure AD 包含客户已用于电子邮件分发和管理访问权限的组。 使用 Graph API 时，可以重复使用这些组，而无需要求客户在应用程序中创建并管理一系列不同的组。 还可以在登录令牌中向应用程序发送组信息。 深入了解[图形 API](active-directory-graph-api.md)。

**使用 Azure AD 控制有权访问应用程序的用户。**  Azure AD 中的管理员和应用程序所有者可以将应用程序访问权限分配给特定的用户和组。 使用 Graph API，可以读取此列表并使用它来控制资源的设置和取消设置，以及应用程序中的访问权限。

**利用 Azure AD 实现基于角色的访问控制。**  管理员和应用程序所有者可以向在 Azure AD 中注册应用程序时定义的角色分配用户和组。 角色信息会在登录令牌中发送到应用程序，并可使用 Graph API 来读取。 深入了解如何[使用 Azure AD 进行授权](http://blogs.technet.com/b/ad/archive/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles.aspx)。

### <a name="get-access-to-users-profile-calendar-email-contacts-files-and-more"></a>获取对用户配置文件、日历、电子邮件、联系人、文件等的访问权限
**Azure AD 是一款面向 Office 365 和其他 Microsoft 业务服务的授权服务器。**  如果支持使用 Azure AD 登录到应用程序，或者支持将当前用户帐户链接到使用 OAuth 2.0 的 Azure AD 用户帐户，则可以请求对用户配置文件、日历、电子邮件、联系人、文件和其他信息的读取和写入访问权限。 可以顺利地将事件写入用户日历，以及在其 OneDrive 中读取或写入文件。 深入了解如何[访问 Office 365 API](https://msdn.microsoft.com/office/office365/howto/platform-development-overview)。

### <a name="promote-your-application-in-the-azure-and-office-365-marketplaces"></a>在 Azure 和 Office 365 Marketplace 中推广应用程序
**向数百万个已使用 Azure AD 的组织推广应用程序。**  搜索和浏览这些应用商店的用户已在使用一个或多个云服务，这让他们成为合格的云服务客户。 深入了解如何在 [Azure Marketplace](https://azure.microsoft.com/marketplace/partner-program/) 中推广应用程序。

**用户注册应用程序后，该程序会在其 Azure AD 访问面板和 Office 365 应用启动器中显示。**  之后，用户能够快速轻松地返回应用程序，可提高用户参与度。 深入了解 [Azure AD 访问面板](../active-directory-saas-access-panel-introduction.md)。

### <a name="secure-device-to-service-and-service-to-service-communication"></a>保护设备与服务之间以及服务与服务之间的通信
**利用 Azure AD 进行服务和设备的标识管理，减少需编写的代码并使 IT 人员能够管理访问权限。**  服务和设备可以使用 OAuth 从 Azure AD 获取令牌，并使用这些令牌来访问 Web API。 使用 Azure AD 可以避免编写复杂的身份验证代码。 由于服务和设备的标识存储在 Azure AD 中，IT 人员可以在一个位置管理密钥和吊销，而无需单独在应用程序中执行此操作。

## <a name="benefits-of-integration"></a>集成的好处
与 Azure AD 集成带来的好处是无需编写额外的代码。

### <a name="integration-with-enterprise-identity-management"></a>与企业标识管理集成
**帮助应用程序符合 IT 策略。**  组织可将其企业标识管理系统与 Azure AD 集成，这样，在员工离开组织后，他们会自动失去对应用程序的访问权限，而不需要 IT 人员采取额外的措施。 IT 人员可以控制谁可以访问应用程序，并确定需要哪些访问策略（例如多重身份验证），这就减少了为遵守复杂的企业策略而要编写的代码量。 Azure AD 为管理员提供详细的审核日志，其中记录了哪些人登录了应用程序，使 IT 人员可以跟踪使用情况。

**Azure AD 已将 Active Directory 扩展到云中，便于应用程序与 AD 集成。**  世界各地的许多组织都在使用 Active Directory 作为首要登录和标识管理系统，并要求它们的应用程序使用 AD。 与 Azure AD 集成可将应用与 Active Directory 相集成。

### <a name="advanced-security-features"></a>高级安全功能
**多重身份验证。**  Azure AD 提供本机多重身份验证。 IT 管理员可以要求访问应用程序之前经过多重身份验证，因此，无需编写此项支持的代码。 深入了解[多重身份验证](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)。

**异常登录检测。**  Azure AD 每天要处理十亿次以上的登录，同时，使用机器学习算法来检测可疑活动，并通知 IT 管理员可能存在的问题。 通过支持 Azure AD 登录，应用程序将从这种保护中受益。 深入了解如何[查看 Azure Active Directory 访问报告](../active-directory-view-access-usage-reports.md)。

**条件性访问。**  除了多重身份验证以外，管理员可以要求用户在登录应用程序之前满足特定的条件。 可设置的条件包括客户端设备的 IP 地址范围、指定的组中的成员资格，以及用于访问的设备的状态。 深入了解 [Azure Active Directory 条件访问](../active-directory-conditional-access-azure-portal.md)。

### <a name="easy-development"></a>易于开发
**行业标准协议。**  Microsoft 承诺支持行业标准。 Azure AD 支持 SAML 2.0、OpenID Connect 1.0、OAuth 2.0 和 WS-Federation 1.2 身份验证协议。 Graph API 符合 OData 4.0 规范。 如果应用程序已支持使用 SAML 2.0 或 OpenID Connect 1.0 进行联合登录，则可以直接添加对 Azure AD 的支持。 深入了解 [Azure AD 支持的身份验证协议](active-directory-authentication-protocols.md)。

**开放源代码库。**  Microsoft 为主流语言和平台提供完全受支持的开放源代码库以加速开发。 这些源代码已获 Apache 2.0 的授权，可以在项目中任意衍生和改写。 深入了解 [Azure AD 身份验证库](active-directory-authentication-libraries.md)。

### <a name="worldwide-presence-and-high-availability"></a>全球存在和高可用性
**全球数据库中均已部署 Azure AD，且受到全天候的管理和监视。**  Azure AD 是一款面向 Microsoft Azure 和 Office 365 的标识管理系统，已在全球 28 个数据中心进行部署。 我们保证至少将目录数据复制到三个数据中心。 全局负载均衡器确保用户访问包含其数据的最靠近 Azure AD 副本，如果检测到问题，会自动将请求重新路由到其他数据中心。

## <a name="next-steps"></a>后续步骤
[开始编写代码](active-directory-developers-guide.md#get-started)。

[使用 Azure AD 登录用户](active-directory-authentication-scenarios.md)

