---
title: 帮助进行标识管理的 Azure 安全功能 | Microsoft Docs
description: " 本文概述了可帮助进行标识管理的核心 Azure 安全功能。 Microsoft 标识和访问管理解决方案可帮助 IT 保护对企业数据中心和云中的应用程序和资源的访问，从而支持附加的验证级别，比如多重身份验证和条件性访问策略。 "
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 5aa0a7ac-8f18-4ede-92a1-ae0dfe585e28
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: 46e4466085200bf9042c4342ea5e6b37fd2fb701
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
---
# <a name="azure-identity-management-security-overview"></a>Azure 标识管理安全概述
Microsoft 标识和访问管理解决方案可帮助 IT 保护对企业数据中心和云中的应用程序和资源的访问，从而支持附加的验证级别，比如多重身份验证和条件性访问策略。 通过高级安全报告、审核和警报来监视可疑活动，有助于减少潜在的安全问题。 [Azure Active Directory Premium](../active-directory/active-directory-editions.md) 向数千个云 (SaaS) 应用提供单一登录，并提供对你在本地运行的 Web 应用的访问。

Azure Active Directory (AD) 在安全方面的益处包括以下能力：

* 为混合企业中的每个用户创建和管理单一 ID，从而保持用户、组和设备同步
* 提供对应用程序（包括数千个预先集成的 SaaS 应用）的单一登录访问
* 通过对本地和云应用强制执行基于规则的多重身份验证，启用应用程序访问安全性
* 通过 Azure AD 应用程序代理预配对本地 Web 应用程序的安全远程访问

本文旨在概述可帮助进行标识管理的核心 Azure 安全功能。 我们还提供了一些文章链接，其中每篇文章都包含每个功能的详细信息，以便可以了解更多内容。  

本文重点介绍以下核心 Azure 标识管理功能︰

* 单一登录
* 反向代理
* 多重身份验证
* 安全监控、警报和基于机器学习的报告
* 消费者标识和访问管理
* 设备注册
* Privileged identity management
* 标识保护
* 混合标识管理

## <a name="single-sign-on"></a>单一登录
单一登录 (SSO) 是指只需使用单个用户帐户登录一次，就能访问展开业务所需的全部应用程序和资源。 登录之后，用户可以访问全部所需的应用程序，而无需再次进行身份验证（例如键入密码）。

许多组织依赖软件即服务 (SaaS) 应用程序（如 Office 365、Box 和 Salesforce）来提高最终用户生产力。 从历史上看，IT 人员需要在每个 SaaS 应用程序中单独创建和更新用户帐户，而用户需要记住每个 SaaS 应用程序的密码。

Azure AD 将本地 Active Directory 环境扩展到云，让用户不仅能够使用主要组织帐户登录到已加入域的设备和公司资源，而且还能登录到完成作业所需的全部 Web 和 SaaS 应用程序。

优势是不仅用户无需管理多组用户名和密码，而且还可根据组织组以及其身为员工的状态，自动预配或取消预配应用程序的访问权限。 Azure AD 引入了安全和访问管理控制，使你能够跨 SaaS 应用程序集中管理用户的访问权限。

了解更多：

* [单一登录概述](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../active-directory/manage-apps/what-is-single-sign-on.md)
* [将 Azure Active Directory 单一登录与 SaaS 应用相集成](../active-directory/active-directory-enterprise-apps-manage-sso.md)

## <a name="reverse-proxy"></a>反向代理
使用 Azure AD 应用程序代理可以在专用网络内部发布本地应用程序（例如 [SharePoint](https://support.office.com/article/What-is-SharePoint-97b915e6-651b-43b2-827d-fb25777f446f?ui=en-US&rs=en-US&ad=US) 站点、[Outlook Web 应用](https://technet.microsoft.com/library/jj657718.aspx)和基于 [IIS](http://www.iis.net/) 的应用），并网络之外的用户提供安全访问。 应用程序代理为许多类型的本地 Web 应用程序和 Azure AD 支持的数以千计的 SaaS 应用程序提供远程访问和单一登录 (SSO)。 员工可以从家中他们自己的设备登录到应用，并通过此基于云的代理进行身份验证。

了解更多：

* [启用 Azure AD 应用程序代理](../active-directory/manage-apps/application-proxy-enable.md)
* [使用 Azure AD 应用程序代理发布应用程序](../active-directory/active-directory-application-proxy-publish.md)
* [使用应用程序代理的单一登录](../active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
* [使用条件性访问](../active-directory/manage-apps/application-proxy-integrate-with-sharepoint-server.md)

## <a name="multi-factor-authentication"></a>多重身份验证
Azure 多重身份验证 (MFA) 是需要使用多个验证方法的身份验证方法，为用户登录和事务额外提供一层重要的安全保障。 MFA 可帮助保护对数据和应用程序的访问，同时满足用户对简单登录过程的需求。 它通过各种验证选项（例如电话、短信、移动应用通知或验证码和第三方 OAuth 令牌）来提供强大的身份验证。

了解更多：

* [多重身份验证](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [什么是 Azure 多重身份验证？](../active-directory/authentication/multi-factor-authentication.md)
* [Azure 多重身份验证的工作原理](../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>安全监控、警报和基于机器学习的报告
安全监控、警报和基于机器学习的报告（它们识别不一致的访问模式）可以帮助保护业务。 可以使用 Azure Active Directory 的访问和使用情况报告来监控你所在组织的目录的完整性和安全性。 使用此信息，目录管理员可以更好地确定哪里可能存在安全风险，以便制定相应的计划来降低风险。

在 Azure 门户中，报告按以下方式分类：

* 异常报告 - 包含我们发现存在异常的登录事件。 我们的目标是让你知道这项活动并使你能够就事件是否可疑做出决定。
* 集成应用程序报告 - 就你所在的组织如何使用云应用程序提供见解。 Azure Active Directory 提供与数千个云应用程序的集成。
* 错误报告 - 指示在为外部应用程序预配帐户时可能发生的错误。
* 用户特定的报告 - 显示特定用户的设备/登录活动数据。
* 活动日志 - 包含过去 24 小时、过去 7 天或过去 30 天内的所有已审核事件的记录，以及组活动更改记录、密码重置和注册活动记录。

了解更多：

* [查看访问和使用情况报告](../active-directory/active-directory-view-access-usage-reports.md)
* [Azure Active Directory Reporting 入门](../active-directory/active-directory-reporting-getting-started.md)
* [Azure Active Directory Reporting 指南](../active-directory/active-directory-reporting-guide.md)

## <a name="consumer-identity-and-access-management"></a>消费者标识和访问管理
Azure Active Directory B2C 是一个高度可用的全局性标识管理服务，该服务适用于面向用户且可通过伸缩来处理数以亿计标识的应用程序。 它可以跨移动平台和 Web 平台进行集成。 用户只需使用现有社交帐户或创建新凭据，即可通过可自定义的体验登录到所有应用程序。

过去，想要在自己的应用程序中注册用户并使用户登录的应用程序开发人员编写了自己的代码。 他们使用本地数据库或系统存储用户名和密码。 Azure Active Directory B2C 通过基于标准的安全平台和大量的可扩展策略，向组织提供一种更好的方式将用户标识管理集成到应用程序中。

使用 Azure Active Directory B2C 时，用户可通过使用他们现有的社交帐户（Facebook、Google、Amazon、LinkedIn）或创建新的凭据（电子邮件地址和密码，或者用户名和密码）在应用程序中注册。

了解更多：

* [什么是 Azure Active Directory B2C？](https://azure.microsoft.com/services/active-directory-b2c/)
* [Azure Active Directory B2C 预览版：在应用程序中注册用户和让用户登录](../active-directory-b2c/active-directory-b2c-overview.md)
* [Azure Active Directory B2C 预览版：应用程序的类型](../active-directory-b2c/active-directory-b2c-apps.md)

## <a name="device-registration"></a>设备注册
Azure AD 设备注册是基于设备的[条件性访问](../active-directory/active-directory-conditional-access-device-registration-overview.md)方案的基础。 在注册设备时，Azure Active Directory 设备注册会为设备提供一个标识，用于在用户登录时对设备进行身份验证。 然后，可以使用已经过身份验证的设备和设备的属性，对云中和本地托管的应用程序实施条件性访问策略。

当与 Intune 之类的移动设备管理 (MDM) 解决方案结合使用时，Azure Active Directory 中的设备属性将使用关于设备的更多信息进行更新。 这会允许创建条件性访问规则，用于从设备强制访问满足安全和合规性标准。

了解更多：

* [Azure Active Directory 设备注册入门](../active-directory/active-directory-conditional-access-device-registration-overview.md)
* [将已加入 Windows 域的设备自动注册到 Azure Active Directory](../active-directory/active-directory-conditional-access-automatic-device-registration.md)
* [对已加入域的 Windows 设备在 Azure Active Directory 中的自动注册进行设置](../active-directory/active-directory-conditional-access-automatic-device-registration-setup.md)

## <a name="privileged-identity-management"></a>Privileged Identity Management
利用 Azure Active Directory (AD) Privileged Identity Management，可以管理、控制和监视特权标识以及对 Azure AD 中和 Office 365 或 Microsoft Intune 等其他 Microsoft Online Services 中资源的访问权限。

用户有时候需要在 Azure 或 Office 365 资源或者其他 SaaS 应用中执行特权操作。 这通常意味着，组织必须授予他们永久的 Azure AD 访问特权。 这会给云中托管的资源不断增大安全风险，因为组织无法充分监视这些用户正在使用管理特权执行哪些操作。 此外，如果有访问特权的用户帐户被泄露，这个缺口可能会影响其总体云安全性。 Azure AD 特权标识管理可帮助解决这一风险。

利用 Azure AD Privileged Identity Management，可以：

* 查看哪些用户是 Azure AD 管理员
* 按需启用对 Office 365 和 Intune 等 Microsoft Online Services 的“实时”管理访问权限
* 获取有关管理员访问历史记录以及管理员分配更改的报告
* 获取有关访问特权角色的警报

了解更多：

* [Azure AD Privileged Identity Management](../active-directory/active-directory-privileged-identity-management-configure.md)
* [Azure AD Privileged Identity Management 中的角色](../active-directory/active-directory-privileged-identity-management-roles.md)
* [Azure AD Privileged Identity Management：如何添加或删除用户角色](../active-directory/active-directory-privileged-identity-management-how-to-add-role-to-user.md)

## <a name="identity-protection"></a>标识保护
Azure AD Identity Protection 是一种安全服务，它提供一个综合试图，你可以在其中查看影响组织标识的风险事件和潜在漏洞。 身份保护利用现有的 Azure Active Directory 异常检测功能（可通过 Azure AD 的异常活动报告得到），并引入了新可以实时检测异常的风险事件类型。

了解更多：

* [Azure Active Directory Identity Protection](../active-directory/active-directory-identityprotection.md)
* [第 9 频道：Azure AD 和标识展示：Identity Protection 预览](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="hybrid-identity-management"></a>混合标识管理
Microsoft 的标识方法跨越本地和云，创建单一用户标识对所有资源进行身份验证和授权，而不考虑其位置。

了解更多：

* [混合标识白皮书](http://download.microsoft.com/download/D/B/A/DBA9E313-B833-48EE-998A-240AA799A8AB/Hybrid_Identity_White_Paper.pdf)
* [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)
* [有关 RBAC 的 Active Directory 团队博客](https://blogs.technet.microsoft.com/ad/)
