---
title: 规划 Azure Active Directory 的自动用户预配部署
description: 用于计划和执行自动用户预配的指南
services: active-directory
author: martincoetzer
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 10/17/2019
ms.author: martinco
ms.reviewer: arvindha, celested
ms.openlocfilehash: 4f391a2fce669c2684ac5001e3659247adfeb4f6
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82593533"
---
# <a name="plan-an-automatic-user-provisioning-deployment"></a>规划自动用户预配部署

许多组织依赖软件即服务（SaaS）应用程序（如 ServiceNow、Zscaler 和可供最终用户使用的时差）。 过去的 IT 人员依赖手动预配方法，例如上传 CSV 文件或使用自定义脚本来安全管理每个 SaaS 应用程序中的用户标识。 这些过程容易出错、不安全且难以管理。

Azure Active Directory （Azure AD）自动用户预配可通过安全地根据业务规则在 SaaS 应用程序中自动创建、维护和删除用户标识，从而简化了此过程。 利用这种自动化，你可以在仅限云的混合环境和混合环境中有效地扩展你的标识管理系统，同时扩展它们在基于云的解决方案上的依赖关系。

请参阅[利用 Azure Active Directory 自动执行用户预配和取消预配到 SaaS 应用程序](../app-provisioning/user-provisioning.md)，以更好地了解功能。

## <a name="learn"></a>了解

用户预配为正在进行的标识管理创建了基础，并增强了依赖于权威标识数据的业务流程的质量。

### <a name="key-benefits"></a>主要优点

启用自动用户预配的主要好处包括：

* **提高了工作效率**。 你可以使用单个用户预配管理界面跨 SaaS 应用程序管理用户标识。 此接口具有一组设置策略。

* **管理风险**。 可以通过基于定义角色和/或访问权限的员工状态或组成员身份来自动执行更改，从而提高安全性。

* **满足合规性和监管要求**。 Azure AD 支持每个用户预配请求的本机审核日志。 在源系统和目标系统中执行请求。 这使你可以从单个屏幕跟踪对应用程序的访问权限。

* **降低成本**。 自动用户预配通过避免与手动预配相关的低效和人为错误，降低了成本。 它减少了自定义开发的用户预配解决方案、脚本和审核日志的需求。

### <a name="licensing"></a>授权

Azure AD 使用应用程序库菜单中提供的模板为任何应用程序提供自助集成。 有关许可证要求的完整列表，请参阅[Azure AD 授权 "页](https://azure.microsoft.com/pricing/details/active-directory/)。

#### <a name="application-licensing"></a>应用程序授权

需要为要自动预配的应用程序提供适当的许可证。 与应用程序所有者讨论，分配给应用程序的用户是否具有适用于其应用程序角色的适当许可证。 如果 Azure AD 基于角色管理自动预配，则 Azure AD 中分配的角色必须与应用程序许可证一致。 在应用程序中拥有的许可证不正确可能会导致在用户的预配/更新过程中出现错误。

### <a name="terms"></a>术语

本文使用以下术语：

* CRUD 操作-对用户帐户执行的操作：创建、读取、更新、删除。

* 单一登录（SSO）-用户能够登录一次并访问启用了 SSO 的所有应用程序。 在用户预配的上下文中，SSO 是用户只有单个帐户才能访问使用自动用户预配的所有系统的结果。

* 源系统-Azure AD 设置的用户的存储库。 Azure AD 是大多数预先集成的预配连接器的源系统。 但是，云应用程序（例如 SAP、Workday 和 AWS）有一些例外情况。 例如，请参阅[从 Workday 到 AD 的用户预配](../saas-apps/workday-inbound-tutorial.md)。

* 目标系统-Azure AD 预配到的用户的存储库。 目标系统通常是 SaaS 应用程序，如 ServiceNow、Zscaler 和时差。 目标系统也可以是本地系统，例如 AD。

* [用于跨域标识管理（SCIM）的系统](https://aka.ms/scimoverview)-一种开放标准，允许用户预配的自动化。 SCIM 在标识提供程序（如 Microsoft）与服务提供商（如 Salesforce 或其他需要用户标识信息的 SaaS 应用程序）之间传达用户标识数据。

### <a name="training-resources"></a>培训资源

| 资源| 链接和说明 |
| - | - |
| 点播网络研讨会| [利用 Azure AD 管理企业应用程序](https://info.microsoft.com/CO-AZUREPLAT-WBNR-FY18-03Mar-06-ManageYourEnterpriseApplicationsOption1-MCW0004438_02OnDemandRegistration-ForminBody.html)<br>了解 Azure AD 如何帮助你为企业 SaaS 应用程序实现 SSO，并了解控制访问的最佳做法。 |
| 视频| [什么是活动 Azure 目录中的用户预配？](https://youtu.be/_ZjARPpI6NI) <br> [如何在 Active Azure Directory 中部署用户预配？](https://youtu.be/pKzyts6kfrw) <br> [将 Salesforce 与 Azure AD 集成：如何实现用户预配](https://azure.microsoft.com/resources/videos/integrating-salesforce-with-azure-ad-how-to-automate-user-provisioning/) |
| 在线课程| SkillUp Online：[管理标识](https://skillup.online/courses/course-v1:Microsoft+AZ-100.5+2018_T3/about) <br> 了解如何将 Azure AD 与多个 SaaS 应用程序集成，并确保用户对这些应用程序的访问权限。 |
| 书籍| [针对 Web 应用程序的 Azure Active Directory 新式身份验证（开发人员参考）第一版](https://www.amazon.com/Authentication-Directory-Applications-Developer-Reference/dp/0735696942/ref=sr_1_fkmr0_1?keywords=Azure+multifactor+authentication&qid=1550168894&s=gateway&sr=8-1-fkmr0)。  <br> 这是为这些新环境构建 Active Directory 身份验证解决方案的权威深入指南。 |
| 教程| 请参阅[有关如何将 SaaS 应用与 Azure AD 集成的教程的列表](../saas-apps/tutorial-list.md)。 |
| 常见问题解答| [有关自动](../app-provisioning/user-provisioning.md)用户预配的常见问题 |

### <a name="solution-architectures"></a>解决方案体系结构

Azure AD 预配服务通过连接到每个应用程序供应商提供的用户管理 API 终结点，将用户预配到 SaaS 应用和其他系统。 这些用户管理 API 终结点允许 Azure AD 以编程方式创建、更新和删除用户。

#### <a name="automatic-user-provisioning-for-hybrid-enterprises"></a>混合企业的自动用户预配

在此示例中，用户和或组是在连接到本地目录的 HR 数据库中创建的。 Azure AD 预配服务可管理目标 SaaS 应用程序的自动用户预配。

 ![用户预配](./media/plan-auto-user-provisioning/hybridprovisioning.png)

**工作流的说明：**

1. 用户/组是在本地 HR 应用程序/系统（例如 SAP）中创建的。 

1. **Azure AD Connect 代理**将从本地 AD 到 Azure AD 的标识（用户和组）的计划同步运行。

1. **Azure AD 预配服务**开始针对源系统和目标系统的[初始循环](../app-provisioning/user-provisioning.md)。 

1. **Azure AD 预配服务**在源系统中查询自初始周期以来发生更改的任何用户和组，并推送[增量循环](../app-provisioning/user-provisioning.md)中的更改。

#### <a name="automatic-user-provisioning-for-cloud-only-enterprises"></a>仅限云的企业的自动用户预配

在此示例中，用户创建发生在 Azure AD 中，Azure AD 预配服务管理目标（SaaS）应用程序的自动用户预配。

![图片 2](./media/plan-auto-user-provisioning/cloudprovisioning.png)

**工作流的说明：**

1. 用户/组是在 Azure AD 中创建的。

1. **Azure AD 预配服务**开始针对源系统和目标系统的[初始循环](../app-provisioning/user-provisioning.md)。 

1. **Azure AD 预配服务**会查询自初始周期以来更新的任何用户和组的源系统，并执行任何[增量周期](../app-provisioning/user-provisioning.md)。

#### <a name="automatic-user-provisioning-for-cloud-hr-applications"></a>云 HR 应用程序的自动用户预配 

在此示例中，用户和或组在云 HR 应用程序（如 Workday 和 SuccessFactors）中创建。 Azure AD 预配服务和 Azure AD Connect 预配代理将用户数据从云 HR 应用租户预配到 AD 中。 在 AD 中更新帐户后，该帐户将通过 Azure AD Connect 与 Azure AD 同步，并且电子邮件地址和用户名属性可以写回到云 HR 应用租户。

![图片 2](./media/plan-auto-user-provisioning/workdayprovisioning.png)

1.  **Hr 团队**在 cloud HR app 租户中执行事务。
2.  **Azure AD 预配服务**运行来自 cloud HR 应用租户的计划周期，并标识需要处理以便与 AD 同步的更改。
3.  **Azure AD 预配服务**使用包含 AD 帐户创建/更新/启用/禁用操作的请求负载调用 Azure AD Connect 预配代理。
4.  **Azure AD Connect 预配代理**使用服务帐户来管理 AD 帐户数据。
5.  **Azure AD Connect**运行增量同步以获取 AD 中的更新。
6.  **AD**更新与 Azure AD 同步。 
7.  **Azure AD 预配服务**回写电子邮件属性和用户名从 Azure AD 到云 HR 应用租户。

## <a name="plan-the-deployment-project"></a>规划部署项目

考虑你的组织需要确定在你的环境中部署用户设置的策略。

### <a name="engage-the-right-stakeholders"></a>吸引适当的利益干系人

当技术项目失败时，通常是由于对影响、结果和责任的预期不匹配而导致的。 若要避免这些问题，请[确保参与到正确的利益干系人](https://aka.ms/deploymentplans)，并通过记录利益干系人及其项目输入和责任来充分了解项目中的利益干系人角色。

### <a name="plan-communications"></a>规划沟通

沟通对于任何新服务的成功至关重要。 主动与用户交流他们的体验将如何更改，何时会发生更改，以及在遇到问题时如何获取支持。

### <a name="plan-a-pilot"></a>规划试点

建议自动用户预配的初始配置在测试环境中包含一小部分用户，然后将其扩展到生产中的所有用户。 请参阅运行试验的[最佳实践](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot)。

#### <a name="best-practices-for-a-pilot"></a>试验的最佳实践  

通过试验，你可以在为每个人部署功能之前，对小型组进行测试。 确保在测试过程中，组织内的每个用例都经过全面测试。

在第一轮中，面向 IT、可用性和其他可测试和提供反馈的适当用户。 使用此反馈，进一步开发您向用户发送的通信和说明，并深入了解支持人员可能会看到的问题类型。

通过增加目标组的范围，将推出扩大到较大的用户组。 这可以通过[动态组成员身份](../users-groups-roles/groups-dynamic-membership.md)来完成，或手动将用户添加到目标组。

## <a name="plan-application-connections-and-administration"></a>规划应用程序连接和管理

使用 Azure AD 门户查看和管理所有支持预配的应用程序。 请参阅[在门户中查找应用](../app-provisioning/configure-automatic-user-provisioning-portal.md)。

### <a name="determine-the-type-of-connector-to-use"></a>确定要使用的连接器类型

启用和配置自动化预配所需的实际步骤因具体应用程序而异。 如果要自动预配的应用程序列在[Azure AD SaaS 应用库](../saas-apps/tutorial-list.md)中，则应选择[应用特定的集成教程](../saas-apps/tutorial-list.md)来配置其预先集成的用户预配连接器。

否则，请执行以下步骤：

1. 为预先集成的用户预配连接器[创建请求](../develop/howto-app-gallery-listing.md)。 我们的团队将与你和应用程序开发人员合作，以将你的应用程序载入到平台（如果它支持 SCIM）。

1. 使用应用的[BYOA SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md)通用用户预配支持。 这是 Azure AD 在没有预先集成的预配连接器的情况下将用户预配到应用程序时所必需的。

1. 如果应用程序能够利用 BYOA SCIM 连接器，请参阅[BYOA SCIM integration 教程](../app-provisioning/use-scim-to-provision-users-and-groups.md)，为应用程序配置 BYOA SCIM 连接器。

有关详细信息，请参阅[可以使用哪些应用程序和系统 Azure AD 自动用户预配？](../app-provisioning/user-provisioning.md)

### <a name="collect-information-to-authorize-application-access"></a>收集信息以授权应用程序访问

设置自动用户预配是每个应用程序的过程。 对于每个应用程序，你需要提供[管理员凭据](../app-provisioning/configure-automatic-user-provisioning-portal.md)以连接到目标系统的用户管理终结点。

下图显示了所需的管理员凭据的一个版本：

![用于管理用户帐户预配设置的 "设置" 屏幕](./media/plan-auto-user-provisioning/userprovisioning-admincredentials.png)

虽然某些应用程序需要管理员用户名和密码，但其他应用程序可能需要持有人令牌。

## <a name="plan-user-and-group-provisioning"></a>规划用户和组预配

如果为企业应用启用用户预配，则[Azure 门户](https://portal.azure.com/)通过属性映射控制其属性值。

### <a name="determine-operations-for-each-saas-app"></a>确定每个 SaaS 应用的操作

每个应用程序都有唯一的用户或组属性，这些属性必须映射到 Azure AD 中的属性。 应用程序只能提供 CRUD 操作的子集。

对于每个应用程序，记录以下信息：

* 要对目标系统的用户和或组对象执行的 CRUD 预配操作。 例如，每个 SaaS 应用程序业务所有者可能不希望进行所有可能的操作。

* 源系统中的可用属性

* 目标系统中的可用属性

* 系统之间的属性映射。

### <a name="choose-which-users-and-groups-to-provision"></a>选择要设置的用户和组

实现自动用户预配之前，必须确定要设置到应用程序的用户和组。

* 使用[范围筛选器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)定义基于属性的规则，这些规则确定将哪些用户预配到应用程序。

* 接下来，根据需要使用[用户和组分配](../manage-apps/assign-user-or-group-access-portal.md)来进行其他筛选。

### <a name="define-user-and-group-attribute-mapping"></a>定义用户和组属性映射

若要实现自动用户预配，你需要定义应用程序所需的用户和组属性。 Azure AD 用户对象与每个 SaaS 应用程序的用户对象之间存在预先配置的一组属性和[属性映射](../app-provisioning/configure-automatic-user-provisioning-portal.md)。 并非所有 SaaS 应用都支持组属性。

Azure AD 通过直接的特性到属性映射、提供常量值或[为属性映射编写表达式](../app-provisioning/functions-for-customizing-application-data.md)来支持。 这种灵活性使你可以精细控制将在目标系统的属性中填充的内容。 你可以使用[MICROSOFT GRAPH API](../app-provisioning/export-import-provisioning-configuration.md)和图形资源管理器将用户预配属性映射和架构导出到 JSON 文件，然后将其重新导入 Azure AD。

有关详细信息，请参阅[在 Azure Active Directory 中为 SaaS 应用程序自定义用户预配属性映射](../app-provisioning/customize-application-attributes.md)。

### <a name="special-considerations-for-user-provisioning"></a>用户预配的特殊注意事项

请考虑以下各项，以减少部署后出现的问题：

* 确保用于映射源和目标应用程序之间的用户/组对象的属性具有复原能力。 如果属性更改（例如，用户移动到公司的其他部分），则不会导致无法正确设置用户/组。

* 应用程序可能具有特定的限制和/或要求，用户预配需要满足这些要求才能正常工作。 例如，可宽延时间截断某些属性的值。 请参阅特定于每个应用程序的[自动用户预配教程](../saas-apps/tutorial-list.md)。

* 确认源系统和目标系统之间的架构一致性。 常见问题包括 UPN 或邮件不匹配之类的属性。 例如，在应用程序中，UPN *john_smith@contoso.com* Azure AD 设置为，它是*jsmith@contoso.com*。 有关详细信息，请参阅[用户和组架构参考](../app-provisioning/use-scim-to-provision-users-and-groups.md)。

## <a name="plan-testing-and-security"></a>规划测试和安全

在部署的每个阶段，请确保测试结果是否符合预期，并审核预配周期。

### <a name="plan-testing"></a>规划测试

为应用程序配置了自动用户预配后，你将运行测试用例来验证此解决方案是否符合组织的要求。

| 方案| 预期结果 |
| - | - |
| 用户已添加到分配给目标系统的组 | 在目标系统中设置用户对象。 <br>用户可以登录目标系统并执行所需的操作。 |
| 已将用户从分配给目标系统的组中删除 | 在目标系统中取消预配用户对象。<br>用户无法登录到目标系统。 |
| 用户信息通过任何方法 Azure AD 更新 | 增量循环后，已更新的用户属性会反映在目标系统中 |
| 用户超出范围 | 已禁用或删除用户对象。 <br>注意：对于[Workday 设置](skip-out-of-scope-deletions.md)，将重写此行为。 |

### <a name="plan-security"></a>规划安全性

作为部署的一部分，通常需要安全审查。 如果需要安全检查，请参阅许多 Azure AD[白皮书](https://www.microsoft.com/download/details.aspx?id=36391)，其中概述了标识即服务。

### <a name="plan-rollback"></a>规划回滚

如果自动用户预配实现在生产环境中不能按预期方式工作，以下回滚步骤可帮助你恢复到以前已知的良好状态：

1. 查看[预配摘要报告](../app-provisioning/check-status-user-account-provisioning.md)和[预配日志](../app-provisioning/check-status-user-account-provisioning.md#provisioning-logs-preview)，以确定对受影响的用户和/或组执行了哪些错误操作。

1. 使用设置审核日志来确定受影响的用户和/或组的最近一次正确状态。 另请查看源系统（Azure AD 或 AD）。

1. 与应用程序所有者合作，使用上一次已知的良好状态值更新直接受应用程序影响的用户和/或组。

## <a name="deploy-automatic-user-provisioning-service"></a>部署自动用户预配服务

选择符合解决方案要求的步骤。

### <a name="prepare-for-the-initial-cycle"></a>准备初始周期

第一次运行 Azure AD 预配服务时，针对源系统和目标系统的初始周期会创建每个目标系统的所有用户对象的快照。

为应用程序启用自动预配时，初始周期可能需要20分钟到几小时。 持续时间取决于 Azure AD 目录的大小以及用于预配的范围内的用户数量。 请参阅[如何提高预配性能](../app-provisioning/application-provisioning-when-will-provisioning-finish.md)。

预配服务在初始周期后存储两个系统的状态，从而提高后续增量循环的性能。

### <a name="configure-automatic-user-provisioning"></a>配置自动用户预配

使用[Azure 门户](https://portal.azure.com/)管理自动用户帐户预配，并对支持它的应用程序取消预配。 按照[如何实现设置应用程序的自动预配](../app-provisioning/user-provisioning.md)中的步骤操作？

还可以使用 [Microsoft 图形 API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview) 配置和管理 Azure AD 用户预配服务。

## <a name="manage-automatic-user-provisioning"></a>管理自动用户预配

现在，你已部署，你需要管理解决方案。

### <a name="monitor-user-provisioning-operation-health"></a>监视用户预配操作运行状况

在成功完成[初始循环](../app-provisioning/user-provisioning.md)后，Azure AD 预配服务将按特定于每个应用程序的间隔无限期地运行增量更新，直到发生以下事件之一：

* 手动停止了该服务，并使用[Azure 门户](https://portal.azure.com/)或使用适当的[Microsoft Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)命令触发了新的初始周期。

* 新初始周期由属性映射或范围筛选器中的更改触发。

* 预配过程会因为较高的错误率而进入隔离状态，并且在超过四周后会自动禁用。

若要查看这些事件以及预配服务执行的所有其他活动，请参阅 Azure AD[设置日志](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)。

若要了解预配周期所需的时间和监视预配作业的进度，可以[检查用户预配的状态](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)。

### <a name="gain-insights-from-reports"></a>从报表获取见解

Azure AD 可以通过审核日志和报告，[进一步深入](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)了解你的组织的用户预配使用情况和运行状况。

管理员应检查预配摘要报告，以监视预配作业的操作运行状况。 预配服务执行的所有活动都记录在 Azure AD 审核日志中。 请参阅[教程：有关自动用户帐户预配的报告](../app-provisioning/check-status-user-account-provisioning.md)。

建议你假设所有权，并使用这些报表来满足组织的要求。 Azure AD 将大多数审核数据保留30天。

### <a name="troubleshoot"></a>疑难解答

请参阅以下链接，以解决在预配期间可能会打开的任何问题：

* [为 Azure AD 库应用程序配置用户预配时遇到的问题](../app-provisioning/application-provisioning-config-problem.md)

* [将属性从本地 Active Directory 同步到 Azure AD 以便预配到应用程序](../app-provisioning/user-provisioning-sync-attributes-for-mapping.md)

* [用户预配到 Azure AD 库应用程序需要数小时或更长时间](../app-provisioning/application-provisioning-when-will-provisioning-finish.md)

* [在为 Azure Active Directory 库应用程序配置用户预配时保存管理员凭据时出现问题](../app-provisioning/application-provisioning-config-problem-storage-limit.md)

* [没有为任何用户预配 Azure AD 库应用程序](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)

* [预配到 Azure AD 库应用程序的用户组错误](../app-provisioning/application-provisioning-config-problem-wrong-users-provisioned.md)

### <a name="helpful-documentation"></a>有用的文档

* [为属性映射编写表达式](../app-provisioning/functions-for-customizing-application-data.md)

* [Azure AD 同步 API 概述](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)

* [跳过删除超出范围的用户帐户](skip-out-of-scope-deletions.md)

* [Azure AD Connect 预配代理：版本发布历史记录](provisioning-agent-release-version-history.md)

#### <a name="resources"></a>资源

* [提供产品反馈](https://feedback.azure.com/forums/169401-azure-active-directory)

* [随时了解 Azure AD 的新增功能](https://azure.microsoft.com/updates/?product=active-directory)

* [论坛 Azure AD 堆栈溢出](https://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="next-steps"></a>后续步骤
* [配置自动用户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)

* [使用 Microsoft Graph API 导出或导入预配配置](../app-provisioning/export-import-provisioning-configuration.md)

* [在 Azure Active directory 中编写属性映射的表达式](../app-provisioning/functions-for-customizing-application-data.md)
