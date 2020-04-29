---
title: 使用 Azure AD 管理对应用的访问 | Microsoft Docs
description: 介绍 Azure Active Directory 如何使组织能够指定每个用户有权访问的应用。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 489b15423add03d69070bc32057af97396a85309
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "79409060"
---
# <a name="managing-access-to-apps"></a>管理对应用的访问

将应用集成到组织的标识系统之后，进行中的访问管理、使用评估和报告将持续成为一项挑战。 在许多情况下，IT 管理员或支持人员需要对应用访问管理持续采取主动措施。 有时，分配由一般或分部 IT 小组执行。 分配决策往往由业务决策人委派，在 IT 人员进行分配之前需要其批准。  其他组织会投资来与现有自动化标识与访问管理系统集成，例如基于角色的访问控制 (RBAC) 或基于属性的访问控制 (ABAC)。 集成与规则开发往往是针对性的且费用高昂。 对任一管理方式进行监视或报告本身是一项独立、昂贵且复杂的投资。

## <a name="how-does-azure-active-directory-help"></a>Azure Active Directory 有何帮助？

Azure AD 针对已配置的应用程序支持广泛的访问管理，使组织能够轻松实现正确的访问策略，范围包括自动的基于分配的策略（ABAC 或 RBAC 方案）到委派和纳入管理员管理。 有了 Azure AD，可以轻松地实现复杂策略，结合单个应用程序的多个管理模型，甚至在具有相同受众的应用程序之间重复使用管理规则。

借助 Azure AD，使用和分配报告可完全集成，使管理员能够轻松报告分配状态、分配错误甚至使用情况。

### <a name="assigning-users-and-groups-to-an-app"></a>向应用程序分配用户和组

Azure AD 的应用程序分配着重于两种主要分配模式：

* **单独分配**：具有目录全局管理员权限的 IT 管理员可以选择单个用户帐户并授予这些用户访问应用程序的权限。

* **基于组的分配（需要 Azure AD Premium P1 或 P2）** 具有目录全局管理员权限的 IT 管理员可以将组分配给应用程序。 特定用户的访问权限取决于用户尝试访问应用程序时是否为组的成员。 换而言之，管理员可以有效地创建分配规则，指明“分配组的任何现有成员都有权访问应用程序”。 使用此分配选项，管理员可以受益于 Azure AD 组管理选项，包括[基于属性的动态组](../fundamentals/active-directory-groups-create-azure-portal.md)、外部系统组（例如，本地 Active Directory 或 Workday）、管理员管理的组或自助管理组。 可以轻松地将单个组分配给多个应用程序，确保具有分配关联性的应用程序可以共享分配规则，从而降低总体管理复杂性。 请注意，目前不支持使用嵌套的组成员身份来对应用程序进行基于组的分配。

管理员可以使用这两种分配模式实现任何所需的分配管理方案。

### <a name="requiring-user-assignment-for-an-app"></a>需要为应用程序分配用户

对于某些类型的应用程序，你可以选择[要求将用户分配到应用程序](assign-user-or-group-access-portal.md#configure-an-application-to-require-user-assignment)。 这样一来，你就可以防止每个人在你显式分配给该应用程序的用户进行登录。 以下类型的应用程序支持此选项：

* 为联合单一登录（SSO）配置了基于 SAML 的身份验证的应用程序
* 使用 Azure Active Directory 预身份验证的应用程序代理应用程序
* 在用户或管理员同意该应用程序后，在 Azure AD 应用程序平台上构建的应用程序使用 OAuth 2.0/OpenID Connect 身份验证。某些企业应用程序提供对允许登录的用户的更多控制。

当*不需要*用户分配时，未分配的用户将在其 "我的应用" 访问面板上看到该应用程序，但它们仍可以登录到应用程序本身（也称为 SP 启动的登录），也可以使用应用程序的 "**属性**" 页中的 "**用户访问 URL** " （也称为 IDP 发起的登录）。

对于某些应用程序，要求用户分配的选项在应用程序的属性中不可用。 在这些情况下，可以使用 PowerShell 设置服务主体上的 appRoleAssignmentRequired 属性。

### <a name="determining-the-user-experience-for-accessing-apps"></a>确定访问应用的用户体验

Azure AD 提供[多种可自定义的方式将应用程序部署](end-user-experiences.md)到组织中的最终用户：

* Azure AD 我的应用访问面板
* Office 365 应用程序启动器
* 直接登录联合应用（服务 pr）
* 联合、基于密码或现有的应用的深层链接

可以确定分配到企业应用的用户是否可以在访问面板和 Office 365 应用程序启动器中看到它。

## <a name="example-complex-application-assignment-with-azure-ad"></a>示例：带 Azure AD 的复杂应用程序分配
以 Salesforce 之类的应用程序为例。 在许多组织中，Salesforce 主要由营销和销售团队使用。 通常，营销团队成员对 Salesforce 拥有较高的访问权限，而销售团队的访问权限则受限。 在许多情况下，大范围的信息工作者对应用程序的访问权限会受到限制。 这些规则存在的例外使情况变得复杂。 营销或销售领导团队通常有特权授予用户访问权限，或独立于这些常规规则更改其角色。

使用 Azure AD 可将 Salesforce 等应用程序预先配置为支持单一登录 (SSO) 和自动化预配。 配置应用程序后，管理员可以执行一次性的操作来创建和分配相应的组。 在本示例中，管理员可以执行以下分配：

* 可以定义[动态组](../fundamentals/active-directory-groups-create-azure-portal.md)，自动代表营销和销售团队的所有成员使用部门或角色之类的属性：
  
  * 为营销组的所有成员分配 Salesforce 中的“营销”角色
  * 为销售组的所有成员分配 Salesforce 中的“销售”角色。 进一步细化可以使用代表分配有不同 Salesforce 角色的区域销售团队的多个组。

* 若要启用例外机制，可为每个角色创建自助组。 例如，可以将“Salesforce 营销例外”组创建为自助服务组。 可以为该组分配 Salesforce 营销角色，而营销领导团队可成为所有者。 营销领导团队的成员可以添加或删除用户、设置加入策略甚至批准或拒绝单个用户的加入请求。 信息工作者只需有相应经验即可支持该机制，不需要对所有者或成员进行专门培训。

在此情况下，所有分配的用户会自动预配到 Salesforce，因为当他们添加到不同组时，他们的角色分配会在 Salesforce 中更新。 用户可以通过 Microsoft 应用程序访问面板、Office Web 客户端甚至通过浏览到其组织的 Salesforce 登录页来发现和访问 Salesforce。 管理员可以使用 Azure AD 报告轻松查看使用情况和分配状态。

管理员可以使用[Azure AD 条件访问](../active-directory-conditional-access-azure-portal.md)来设置特定角色的访问策略。 这些策略可能包括是否允许从企业环境外部访问、多重身份验证或设备要求，以在各种情况下实现访问。

## <a name="access-to-microsoft-applications"></a>访问 Microsoft 应用程序

Microsoft 应用程序（如 Office 365 Exchange、SharePoint、Yammer 等）的分配和管理与集成到 Azure AD 以进行单一登录的第三方 SaaS 应用程序或其他应用程序略有不同。

用户访问 Microsoft 发布的应用程序的方法主要有三种。

- 对于 Office 365 或其他付费套件中的应用程序，可以通过**许可证分配**直接向用户的用户帐户授予访问权限，也可以使用基于组的许可证分配功能通过组授予用户访问权限。
- 对于 Microsoft 或第三方免费发布以便任何人使用的应用程序，可以通过[用户同意](configure-user-consent.md)向用户授予访问权限。 这意味着他们使用其 Azure AD 工作或学校帐户登录到应用程序，并允许它访问其帐户上一些受限制的数据集。
- 对于 Microsoft 或第三方免费发布以便任何人使用的应用程序，也可以通过[管理员同意](manage-consent-requests.md)向用户授予访问权限。 这意味着管理员已确定组织中的所有人都可以使用此应用程序，因此他们使用全局管理员帐户可以登录到应用程序并向组织中所有人授予访问权限。

某些应用程序将这些方法结合起来。 例如，某些 Microsoft 应用程序是 Office 365 订阅的一部分，但仍需要同意。

用户可以通过其 Office 365 门户访问 Office 365 应用程序。 你还可以在 "我的应用" 访问面板中显示或隐藏 Office 365 应用程序，并在目录的**用户设置**中使用[office 365 的 "可见性" 切换](hide-application-from-user-portal.md)。 

与企业应用一样，你可以通过 Azure 门户[将用户分配](assign-user-or-group-access-portal.md)到某些 Microsoft 应用程序，或者，如果 "门户" 选项不可用，则可以使用 PowerShell。

## <a name="next-steps"></a>后续步骤
* [使用条件性访问保护应用](../active-directory-conditional-access-azure-portal.md)
* [自助服务组管理/SSAA](../users-groups-roles/groups-self-service-management.md)
