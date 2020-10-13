---
title: 如何与 Microsoft 标识平台集成 |Microsoft
titleSuffix: Microsoft identity platform
description: 了解将应用程序与 Microsoft 标识平台集成的优点，并获取诸如简化登录、标识管理、多重身份验证和访问控制等功能的资源。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/01/2020
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, seoapril2019
ms.openlocfilehash: e9724bb2e5377f84904dc77c33375c39f3077dd4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91631403"
---
# <a name="integrating-with-microsoft-identity-platform"></a>与 Microsoft 标识平台集成

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

在本文中，你将了解将应用程序与 Microsoft 标识平台集成的优点，并获取用于集成的资源。 Microsoft 标识平台和 Azure Active Directory (AD) 为组织提供用于云应用程序的企业级标识管理。 Microsoft 标识平台集成向用户提供简化的登录体验，并有助于应用程序符合 IT 策略。

## <a name="how-to-integrate"></a>如何集成

应用程序与 Microsoft 标识平台集成有多种方式。 请根据应用程序，利用其中的一个或多个方案。

### <a name="support-microsoft-identity-platform-as-a-way-to-sign-in-to-your-application"></a>支持 Microsoft 标识平台作为登录应用程序的方式

**减少登录阻碍并降低支持成本。** 通过使用 Microsoft 标识平台登录应用程序，用户就不需要再多记住一套名称和密码。 作为开发人员，可以减少要存储和保护的密码。 无需重置忘记的密码，单凭这一点就能节省不少的精力。 Microsoft 标识平台为某些世界上最受欢迎的云应用程序（包括 Microsoft 365 和 Microsoft Azure）进行登录。 Microsoft 标识平台拥有来自数百万家组织的上亿名用户，你的用户很有可能已经登录到了该平台。 请详细了解如何[添加对 Microsoft 标识平台登录的支持](./authentication-vs-authorization.md)。

**简化应用程序的注册过程。**  在注册应用程序的过程中，Microsoft 标识平台可以发送有关用户的基本信息，以便你可以预先填写注册表单或完全清除注册表单中的信息。 用户可以使用其 Azure AD 帐户，通过社交媒体和移动应用程序中常见的许可体验注册应用程序。 无需 IT 人员支持，任何用户都可以注册并登录到与 Microsoft 标识平台集成的应用程序。 深入了解如何[注册应用程序进行 Azure AD 帐户登录](../../app-service/configure-authentication-provider-aad.md)。

### <a name="browse-for-users-manage-user-provisioning-and-control-access-to-your-application"></a>浏览用户，管理用户预配，以及控制对应用程序的访问

**浏览目录中的用户。**  在邀请其他人或授予访问权限时，可以使用 Microsoft Graph API 来帮助用户搜索和浏览其组织中的其他人员，而无需用户键入电子邮件地址。 用户可以使用熟悉的通讯簿样式界面进行浏览，包括查看组织层次结构的详细信息。 详细了解 [Microsoft Graph API](/graph/overview)。

**重复使用客户正在管理的 Active Directory 组和通讯组列表。**  Azure AD 包含客户已用于电子邮件分发和管理访问权限的组。 使用 Microsoft Graph API 时，可以重复使用这些组，而无需要求客户在应用程序中创建并管理一系列不同的组。 还可以在登录令牌中向应用程序发送组信息。 详细了解 [Microsoft Graph API](/graph/overview)。

使用 Microsoft 标识平台来控制哪一用户有权访问你的应用程序。  Azure AD 中的管理员和应用程序所有者可以将应用程序访问权限分配给特定的用户和组。 使用 Microsoft Graph API，可以读取此列表并使用它来控制资源的预配和取消预配，以及应用程序中的访问权限。

将 Microsoft 标识平台用于基于角色的访问控制。  管理员和应用程序所有者可以向你在 Microsoft 标识平台中注册应用程序时定义的角色分配用户和组。 角色信息会在登录令牌中发送到应用程序，并可使用 Microsoft Graph API 来读取。 详细了解如何[使用 Microsoft 标识平台进行授权](https://cloudblogs.microsoft.com/enterprisemobility/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles/)。

### <a name="get-access-to-users-profile-calendar-email-contacts-files-and-more"></a>获取对用户配置文件、日历、电子邮件、联系人、文件等的访问权限

**Microsoft 标识平台是用于 Microsoft 365 和其他 Microsoft 业务服务的授权服务器。**  如果支持使用 Microsoft 标识平台登录到应用程序，或者支持将当前用户帐户链接到使用 OAuth 2.0 的 Azure AD 用户帐户，则可以请求获得用户配置文件、日历、电子邮件、联系人、文件和其他信息的读写访问权限。 可以顺利地将事件写入用户日历，以及在其 OneDrive 中读取或写入文件。 详细了解 [Microsoft 365 api](/graph/overview)。

### <a name="promote-your-application-in-the-azure-and-microsoft-365-marketplaces"></a>在 Azure 中升级应用程序并 Microsoft 365 市场

**向数百万个已使用 Azure AD 的组织推广应用程序。**  搜索和浏览这些市场的用户已在使用一个或多个云服务，这让他们成为合格的云服务客户。 深入了解如何在 [Azure 市场](https://azure.microsoft.com/marketplace/partner-program/)中推广应用程序。

**当用户注册应用程序时，它将显示在其 Azure AD 访问面板和 Microsoft 365 应用程序启动器中。**  之后，用户能够快速轻松地返回应用程序，可提高用户参与度。 深入了解 [Azure AD 访问面板](../user-help/my-apps-portal-end-user-access.md)。

### <a name="secure-device-to-service-and-service-to-service-communication"></a>保护设备与服务之间以及服务与服务之间的通信

使用 Microsoft 标识平台对服务和设备进行标识管理，可减少需要编写的代码并让 IT 人员能够管理访问权限。  服务和设备可以使用 OAuth 从 Microsoft 标识平台获取令牌，并可以使用这些令牌来访问 Web API。 使用 Microsoft 标识平台可以避免编写复杂的身份验证代码。 由于服务和设备的标识存储在 Azure AD 中，IT 人员可以在一个位置管理密钥和吊销，而无需单独在应用程序中执行此操作。

## <a name="benefits-of-integration"></a>集成的好处

与 Microsoft 标识平台集成带来的好处是不需要再另外编写代码。

### <a name="integration-with-enterprise-identity-management"></a>与企业标识管理集成

**帮助应用程序符合 IT 策略。**  组织将其企业标识管理系统与 Microsoft 标识平台集成后，如果有人离开组织，他们将自动失去访问应用程序的权限，不需要 IT 人员再另外采取措施。 IT 人员可以控制谁可以访问应用程序，并确定需要哪些访问策略（例如多重身份验证），这就减少了为遵守复杂的企业策略而要编写的代码量。 Azure AD 为管理员提供详细的审核日志，其中记录了哪些人登录了应用程序，使 IT 人员可以跟踪使用情况。

**Azure AD 已将 Active Directory 扩展到云中，便于应用程序与 AD 集成。**  世界各地的许多组织都在使用 Active Directory 作为首要登录和标识管理系统，并要求它们的应用程序使用 AD。 与 Azure AD 集成可将应用与 Active Directory 相集成。

### <a name="advanced-security-features"></a>高级安全功能

**多重身份验证。**  Microsoft 标识平台提供本机多重身份验证。 IT 管理员可以要求访问应用程序之前经过多重身份验证，因此，无需编写此项支持的代码。 深入了解[多重身份验证](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)。

**异常登录检测。**  Microsoft 标识平台每天要处理十亿次以上的登录，同时使用机器学习算法来检测可疑活动并通知 IT 管理员可能存在的问题。 通过支持 Microsoft 标识平台登录，应用程序可以受益于这种防护机制。 深入了解如何[查看 Azure Active Directory 访问报告](../reports-monitoring/overview-reports.md)。

**条件访问。**  除了多重身份验证以外，管理员可以要求用户在登录应用程序之前满足特定的条件。 可设置的条件包括客户端设备的 IP 地址范围、指定的组中的成员资格，以及用于访问的设备的状态。 详细了解 [Azure Active Directory 条件访问](../conditional-access/overview.md)。

### <a name="easy-development"></a>易于开发

**行业标准协议。**  Microsoft 承诺支持行业标准。 Microsoft 标识平台支持行业标准协议 OAuth 2.0 和 OpenID Connect 1.0。 详细了解 [Microsoft 标识平台身份验证协议](active-directory-v2-protocols.md)。

**开放源代码库。**  Microsoft 为主流语言和平台提供完全受支持的开放源代码库以加速开发。 这些源代码已获 Apache 2.0 的授权，可以在项目中任意衍生和改写。 详细了解 [Microsoft 身份验证库 (MSAL)](reference-v2-libraries.md)。

### <a name="worldwide-presence-and-high-availability"></a>全球存在和高可用性

**全球数据库中均已部署 Azure AD，且受到全天候的管理和监视。**  Azure AD 是 Microsoft Azure 和 Microsoft 365 的标识管理系统，并在世界各地的28个数据中心内进行部署。 我们保证至少将目录数据复制到三个数据中心。 全局负载均衡器确保用户访问包含其数据的最靠近 Azure AD 副本，如果检测到问题，会自动将请求重新路由到其他数据中心。

## <a name="next-steps"></a>后续步骤

[开始编写代码](v2-overview.md#getting-started)。

[使用 Microsoft 标识平台将用户登录](./authentication-vs-authorization.md)