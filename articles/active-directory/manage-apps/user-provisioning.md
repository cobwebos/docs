---
title: Azure AD 中的自动 SaaS 应用用户预配 | Microsoft Docs
description: 介绍可以如何使用 Azure AD 进行自动化设置、取消设置，并不断跨多个第三方 SaaS 应用程序更新用户帐户。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/15/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f661aa67f04de23c7b4871e78d3628c639e7567
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2019
ms.locfileid: "74144545"
---
# <a name="automate-user-provisioning-and-deprovisioning-to-applications-with-azure-active-directory"></a>通过 Azure Active Directory 自动执行用户预配和取消预配

在 Azure Active Directory （Azure AD）中，术语 "**应用设置**" 是指在用户需要访问的云（[SaaS](https://azure.microsoft.com/overview/what-is-saas/)）应用程序中自动创建用户标识和角色。 除了创建用户身份外，自动预配还包括在状态或角色发生更改时维护和删除用户标识。 常见的方案包括将 Azure AD 用户预配到[Dropbox](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial)、 [Salesforce](https://docs.microsoft.com/azure/active-directory/saas-apps/salesforce-provisioning-tutorial)、 [ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-provisioning-tutorial)等应用程序中。

![预配概述关系图](media/user-provisioning/provisioning-overview.png)

此功能可让你：

- **自动预配**：当新用户加入你的团队或组织时，在适当的系统中自动创建新帐户。
- **自动**取消预配：当用户离开团队或组织时，自动停用适当系统中的帐户。
- **同步系统之间的数据：** 确保你的应用和系统中的标识基于目录或人力资源系统中的更改保持最新。
- **预配组：** 将组预配到支持它们的应用程序。
- **控制访问权限：** 监视和审核已预配到应用程序中的人员。
- **无缝部署于棕色现场方案：** 与系统之间的现有标识匹配，并允许轻松集成，即使目标系统中已存在用户也是如此。
- **使用丰富的自定义：** 利用可自定义的属性映射来定义哪些用户数据应从源系统流向目标系统。
- **获取关键事件的警报：** 预配服务针对关键事件提供警报，并允许 Log Analytics 集成，以便你可以在其中定义自定义警报来满足你的业务需求。

## <a name="benefits-of-automatic-provisioning"></a>自动预配的优点

随着现代组织使用的应用程序数量不断增长，IT 管理员可以大规模地进行访问管理。 安全断言标记语言（SAML）或 Open ID Connect （OIDC）等标准允许管理员快速设置单一登录（SSO），但 access 还需要将用户预配到应用程序中。 对于很多管理员而言，设置意味着每周手动创建每个用户帐户或上传 CSV 文件，但这些过程非常耗时、昂贵且容易出错。 已采用 SAML 实时（JIT）等解决方案来自动进行预配，但企业还需要一个解决方案，以便在用户离开组织或不再需要基于角色更改访问某些应用时对其进行取消设置。

使用自动预配的一些常见动机包括：

- 最大程度地提高预配过程的效率和准确性。
- 节省与托管和维护定制开发的预配解决方案和脚本相关的成本。
- 通过在用户离开组织时立即从关键 SaaS 应用中删除用户的标识来保护组织的安全。
- 轻松将大量用户导入特定的 SaaS 应用程序或系统。
- 使用一组策略来确定已预配的用户以及可登录到应用程序的用户。

Azure AD 用户预配可帮助解决这些难题。 若要了解有关客户如何使用 Azure AD 用户预配的详细信息，可以阅读[ASOS 案例研究](https://aka.ms/asoscasestudy)。 以下视频概述了 Azure AD 中的用户预配：

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>可在哪些应用程序和系统中使用 Azure AD 自动用户预配？

Azure AD 功能为许多常用 SaaS 应用和人力资源系统预集成的支持，以及对实现[SCIM 2.0 标准](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)的特定部分的应用的一般性支持。

* **预先集成的应用程序（库 SaaS 应用）** 。 可以在[用户预配的应用程序教程列表](../saas-apps/tutorial-list.md)中找到 Azure AD 支持预集成预配连接器的所有应用程序。 库中列出的预先集成的应用程序通常使用基于 SCIM 2.0 的用户管理 Api 进行设置。 

   ![Salesforce 徽标](media/user-provisioning/gallery-app-logos.png)

   如果要请求新的预配应用程序，可以[请求将应用程序与应用程序库集成](https://docs.microsoft.com/azure/active-directory/develop/howto-app-gallery-listing)。 对于用户设置请求，我们要求应用程序具有与 SCIM 兼容的终结点。 请请求应用程序供应商遵循 SCIM 标准，以便可以将应用程序快速加入我们的平台。

* **支持 SCIM 2.0 的应用程序**。 有关如何一般连接实现基于 SCIM 2.0 的用户管理 Api 的应用程序的信息，请参阅[使用 SCIM 自动将用户和组从 Azure Active Directory 设置到应用程序](use-scim-to-provision-users-and-groups.md)。

## <a name="what-is-scim"></a>什么是 SCIM？

为了帮助自动进行预配和取消预配，应用公开了专有的用户和组 Api。 不过，尝试在多个应用程序中管理用户的任何人都将会告诉你，每个应用都尝试执行相同的简单操作，例如创建或更新用户、将用户添加到组或取消预配用户。 不过，所有这些简单的操作都是以不同的方式实现的，使用不同的终结点路径、指定用户信息的不同方法和不同的架构来表示信息的每个元素。

为了解决这些难题，SCIM 规范提供了一个常见的用户架构，可帮助用户进入、传出和绕过应用。 SCIM 正在成为预配的实际标准，并且与 SAML 或 OpenID Connect 等联合标准结合使用时，为管理员提供了一个用于访问管理的端到端基于标准的解决方案。

有关使用 SCIM 自动将用户和组预配和取消预配到应用程序的详细指南，请参阅[使用 Azure Active Directory 进行 SCIM 用户预配](use-scim-to-provision-users-and-groups.md)。

## <a name="how-does-automatic-provisioning-work"></a>自动化预配的工作原理

**Azure AD 预配服务**通过连接到每个应用程序供应商提供的用户管理 API 终结点，将用户预配到 SaaS 应用和其他系统。 这些用户管理 API 终结点允许 Azure AD 以编程方式创建、更新和删除用户。 对于所选的应用程序，预配服务还可以创建、更新和删除其他标识相关对象，如组和角色。

![Azure AD 预配服务](./media/user-provisioning/provisioning0.PNG)
*图1： Azure AD 预配服务*

![出站用户预配工作流](./media/user-provisioning/provisioning1.PNG)
*图2： "出站" 用户预配工作流从 Azure AD 到热门 SaaS 应用程序*

![入站用户预配工作流](./media/user-provisioning/provisioning2.PNG)
*图3： "入站" 用户预配工作流从常用的人力资本管理（HCM）应用程序到 Azure Active Directory 和 Windows Server Active Directory*

## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>如何设置为自动预配到应用程序？

对于库中列出的预先集成的应用程序，可以使用分步指南设置自动预配。 请参阅[集成库应用的教程列表](https://docs.microsoft.com/azure/active-directory/saas-apps/)。 以下视频演示了如何设置 SalesForce 的自动用户预配。

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

对于支持 SCIM 2.0 的其他应用程序，请按照[SCIM 用户预配 Azure Active Directory](use-scim-to-provision-users-and-groups.md)一文中的步骤进行操作。

## <a name="what-happens-during-provisioning"></a>预配期间会发生什么情况？

如果 Azure AD 是源系统，预配服务将使用 [Azure AD 图形 API 的差异查询功能](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query)来监视用户和组。 预配服务对源系统和目标系统运行初始循环，后跟定期增量循环。

### <a name="initial-cycle"></a>初始周期

启动预配服务时，将运行的第一个同步将：

1. 查询源系统中的所有用户和组，并检索[属性映射](customize-application-attributes.md)中定义的所有属性。
1. 使用配置的任何[分配](assign-user-or-group-access-portal.md)或[基于属性的范围筛选器](define-conditional-rules-for-provisioning-user-accounts.md)筛选返回的用户和组。
1. 当分配用户或在设置范围内时，服务将使用指定的[匹配属性](customize-application-attributes.md#understanding-attribute-mapping-properties)在目标系统中查询匹配的用户。 示例：如果源系统中的 userPrincipal 名称是匹配的属性并映射到目标系统中的 userName，则预配服务会在目标系统中查询与源系统中 userPrincipal 名称值匹配的 userName。
1. 如果在目标系统中找不到匹配的用户，则使用从源系统返回的属性创建该用户。 创建用户帐户后，预配服务将检测并缓存新用户的目标系统 ID，该用户用于运行对该用户的所有未来操作。
1. 如果找到匹配的用户，则使用源系统提供的属性进行更新。 匹配该用户帐户后，预配服务将检测并缓存新用户的目标系统 ID，该用户用于运行对该用户的所有未来操作。
1. 如果属性映射包含 "引用" 属性，则服务会在目标系统上执行其他更新以创建和链接引用的对象。 例如，用户可能在目标系统中有“Manager”属性，该属性链接到目标系统中创建的另一个用户。
1. 在初始周期结束时保存水印，这为后面的增量循环提供开始点。

某些应用程序（如 ServiceNow、G Suite 和 Box）不仅支持预配用户，还支持预配组及其成员。 在这些情况下，如果在[映射](customize-application-attributes.md)中启用了组预配，则预配服务将同步用户和组，然后同步组成员身份。

### <a name="incremental-cycles"></a>增量循环

初始循环后，所有其他循环将执行以下操作：

1. 在源系统中查询自上次存储水印以来已更新的所有用户和组。
1. 使用配置的任何[分配](assign-user-or-group-access-portal.md)或[基于属性的范围筛选器](define-conditional-rules-for-provisioning-user-accounts.md)筛选返回的用户和组。
1. 当分配用户或在设置范围内时，服务将使用指定的[匹配属性](customize-application-attributes.md#understanding-attribute-mapping-properties)在目标系统中查询匹配的用户。
1. 如果在目标系统中找不到匹配的用户，则使用从源系统返回的属性创建该用户。 创建用户帐户后，预配服务将检测并缓存新用户的目标系统 ID，该用户用于运行对该用户的所有未来操作。
1. 如果找到匹配的用户，则使用源系统提供的属性进行更新。 如果这是一个新分配的帐户，则预配服务会检测并缓存新用户的目标系统 ID，该用户用于运行对该用户的所有未来操作。
1. 如果属性映射包含 "引用" 属性，则服务会在目标系统上执行其他更新以创建和链接引用的对象。 例如，用户可能在目标系统中有“Manager”属性，该属性链接到目标系统中创建的另一个用户。
1. 如果从范围中删除了以前在预配范围内的某个用户（包括未分配的用户），该服务会通过更新在目标系统中禁用该用户。
1. 如果在源系统中禁用或软删除了以前在预配范围内的某个用户，该服务会通过更新在目标系统中禁用该用户。
1. 如果在源系统中硬删除了以前在预配范围内的某个用户，该服务会在目标系统中删除该用户。 在 Azure AD 中，将在软删除用户30天后将其硬删除。
1. 在增量循环结束时保留新水印，这为后续增量循环提供起始点。

> [!NOTE]
> 您可以选择通过使用 "[映射](customize-application-attributes.md)" 部分中的 "**目标对象操作**" 复选框来禁用**创建**、**更新**或**删除**操作。 还可以使用“accountEnabled”等字段通过属性映射来控制用于在更新期间禁用用户的逻辑。

预配服务会按[特定于每个应用程序的教程](../saas-apps/tutorial-list.md)中定义的时间间隔，以特定于每个应用程序的时间间隔持续运行回后增量循环，直到发生以下事件之一：

- 使用 Azure 门户或使用相应的图形 API 命令手动停止了该服务 
- 使用 Azure 门户中的 "**清除状态" 和 "重新启动**" 选项，或使用适当的图形 API 命令触发新初始周期。 此操作会清除所有存储的水印，并导致重新评估所有源对象。
- 由于属性映射或范围筛选器发生更改，将触发新的初始周期。 此操作还会清除所有存储的水印，并导致重新评估所有源对象。
- 预配过程将进入隔离区（请参阅下文），因为其错误率较高，并且保持隔离时间超过四周。 在此情况下，该服务会自动禁用。

### <a name="errors-and-retries"></a>错误和重试

如果因目标系统出错而无法在目标系统中添加、更新或删除单个用户，则在下一个同步周期中将重试该操作。 如果用户操作继续失败，则重试频率将开始递减，并逐渐减少到每天只尝试一次。 若要解决此问题，管理员必须检查[设置日志](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)，确定根本原因并采取适当的措施。 常见的失败包括：

- 未在源系统中填充目标系统中所需的某个用户属性
- 在源系统中有一个属性值在目标系统中具有唯一约束的用户，另一个用户记录中存在相同的值

若要解决这些失败，可在源系统中调整受影响用户的属性值，或调整属性映射，以避免冲突。

### <a name="quarantine"></a>隔离

如果对目标系统进行的大部分或全部调用由于错误（例如对于无效的管理员凭据）而持续失败，则设置作业将进入 "隔离" 状态。 如果在 Azure 门户中配置了电子邮件通知，则在[预配摘要报告](check-status-user-account-provisioning.md)和电子邮件中指示此状态。

在隔离时，增量循环的频率会逐渐减少到每天一次。

在修复了所有冲突的错误并启动下一个同步周期后，将从隔离区中删除该设置作业。 如果预配作业在隔离区中保留四周以上，则会禁用预配作业。 在此处了解有关[隔离状态的](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)详细信息。

## <a name="how-long-will-it-take-to-provision-users"></a>预配用户需要多长时间？

性能取决于预配作业是否正在运行初始预配周期或增量循环。 若要详细了解预配的时间以及如何监视预配服务的状态，请参阅[检查用户预配的状态](application-provisioning-when-will-provisioning-finish-specific-user.md)。

## <a name="how-can-i-tell-if-users-are-being-provisioned-properly"></a>如何判断用户预配是否正确？

用户预配服务运行的所有操作都记录在 Azure AD[预配日志（预览版）](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)中。 这包括对源系统和目标系统执行的所有读取和写入操作，以及每个操作期间读取或写入的用户数据。

有关如何读取 Azure 门户中的设置日志的信息，请参阅[预配报告指南](check-status-user-account-provisioning.md)。

## <a name="how-do-i-troubleshoot-issues-with-user-provisioning"></a>如何排查用户预配问题？

有关如何排查自动用户预配问题的基于方案的指导，请参阅[在应用程序中配置和预配用户时出现问题](application-provisioning-config-problem.md)。

## <a name="what-are-the-best-practices-for-rolling-out-automatic-user-provisioning"></a>推出自动用户预配的最佳做法是什么？

> [!VIDEO https://www.youtube.com/embed/MAy8s5WSe3A]

有关向应用程序执行出站用户预配的示例分步部署计划，请参阅[有关用户预配的标识部署指南](https://aka.ms/deploymentplans/userprovisioning)。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-b2b-users-in-azure-ad"></a>SaaS 应用自动用户预配是否适用于 Azure AD 中的 B2B 用户？

是的，可以使用 Azure AD 用户预配服务在 Azure AD 到 SaaS 应用程序中预配 B2B （或来宾）用户。

但是，为了使 B2B 用户使用 Azure AD 登录到 SaaS 应用程序，SaaS 应用程序必须以特定的方式配置其基于 SAML 的单一登录功能。 有关如何配置 SaaS 应用程序以支持 B2B 用户的登录的详细信息，请参阅[为 B2B 协作配置 SaaS 应用]( https://docs.microsoft.com/azure/active-directory/b2b/configure-saas-apps)。

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-dynamic-groups-in-azure-ad"></a>SaaS 应用的自动用户预配是否适用于 Azure AD 中的动态组？

可以。 如果配置为 "仅同步分配的用户和组"，则 Azure AD 用户预配服务可根据用户是否为[动态组](../users-groups-roles/groups-create-rule.md)的成员，在 SaaS 应用程序中预配或取消预配用户。 动态组也适用于“同步所有用户和组”选项。

但是，动态组的使用可能会影响从 Azure AD 到 SaaS 应用程序的端到端用户预配的整体性能。 使用动态组时，请牢记以下注意事项和建议：

- SaaS 应用程序中的用户在动态组中预配或取消预配的速度取决于动态组评估成员身份更改的速度。 有关如何检查动态组的处理状态的信息，请参阅[检查成员身份规则的处理状态](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule)。

- 使用动态组时，必须谨慎考虑用户预配和取消预配，因为丢失成员身份会导致取消预配事件。

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-nested-groups-in-azure-ad"></a>SaaS 应用的自动用户预配是否适用于 Azure AD 中的嵌套组？

不能。 如果配置为 "仅同步分配的用户和组"，则 Azure AD 用户预配服务无法读取或设置嵌套组中的用户。 它只能读取和预配作为显式分配组的直接成员的用户。

这是“应用程序基于组的分配”的限制，该限制还会影响单一登录，请参阅[使用组管理对 SaaS 应用程序的访问](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-saasapps )进行了解。

作为一种解决方法，你应显式分配包含需要预配的用户的组（或中的其他[作用域](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)）。

### <a name="is-provisioning-between-azure-ad-and-a-target-application-using-an-encrypted-channel"></a>使用加密通道在 Azure AD 和目标应用程序之间进行预配？

可以。 我们对服务器目标使用 HTTPS SSL 加密。

## <a name="related-articles"></a>相关文章

- [有关如何集成 SaaS 应用的教程列表](../saas-apps/tutorial-list.md)
- [为用户预配自定义属性映射](customize-application-attributes.md)
- [为属性映射编写表达式](functions-for-customizing-application-data.md)
- [用户预配的作用域筛选器](define-conditional-rules-for-provisioning-user-accounts.md)
- [使用 SCIM 启用从 Azure AD 到应用程序的用户和组的自动预配](use-scim-to-provision-users-and-groups.md)
- [Azure AD 同步 API 概述](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
