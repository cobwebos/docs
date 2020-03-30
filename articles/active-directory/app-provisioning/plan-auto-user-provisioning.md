---
title: 规划 Azure 活动目录的自动用户预配部署
description: 规划和执行自动用户预配指南
services: active-directory
author: martincoetzer
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 10/17/2019
ms.author: martinco
ms.reviewer: arvindha
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d2f284fddfc49632e467adbf5877856b40a81dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522404"
---
# <a name="plan-an-automatic-user-provisioning-deployment"></a>规划自动用户预配部署

许多组织依赖软件即服务 （SaaS） 应用程序（如 ServiceNow、Zscaler 和 Slack）来提高最终用户的工作效率。 从历史上看，IT 人员一直依赖手动预配方法，例如上载 CSV 文件或使用自定义脚本安全地管理每个 SaaS 应用程序中的用户身份。 这些进程容易出错、不安全且难以管理。

Azure 活动目录 （Azure AD） 自动用户预配通过基于业务规则安全地自动化 SaaS 应用程序中的用户标识的创建、维护和删除，简化了此过程。 这种自动化使您能够在扩展对基于云的解决方案的依赖时，在仅云和混合环境中有效地扩展身份管理系统。

请参阅[使用 Azure 活动目录自动预配和取消预配到 SaaS 应用程序](../app-provisioning/user-provisioning.md)以更好地了解功能。

## <a name="learn"></a>了解

用户预配为持续的身份治理奠定了基础，并提高了依赖于权威标识数据的业务流程的质量。

### <a name="key-benefits"></a>主要优点

启用自动用户预配的主要优点是：

* **提高生产力**。 您可以使用单个用户预配管理界面跨 SaaS 应用程序管理用户身份。 此接口具有一组预配策略。

* **管理风险**。 您可以通过根据定义角色和/或访问权限的员工状态或组成员身份自动进行更改来提高安全性。

* **解决合规性和治理**问题。 Azure AD 支持每个用户预配请求的本机审核日志。 请求在源系统和目标系统中执行。 这使您能够跟踪谁可以从单个屏幕访问应用程序。

* **降低成本**。 自动用户预配通过避免与手动预配相关的效率低下和人为错误来降低成本。 它减少了对自定义开发的用户预配解决方案、脚本和审核日志的需求。

### <a name="licensing"></a>授权

Azure AD 使用应用程序库菜单中提供的模板提供任何应用程序的自助服务集成。 有关许可证要求的完整列表，请参阅[Azure AD 许可页](https://azure.microsoft.com/pricing/details/active-directory/)。

#### <a name="application-licensing"></a>应用程序许可

您需要为要自动预配的应用程序提供适当的许可证。 与应用程序所有者讨论分配给应用程序的用户是否为其应用程序角色具有适当的许可证。 如果 Azure AD 根据角色管理自动预配，则 Azure AD 中分配的角色必须与应用程序许可证保持一致。 应用程序中拥有的许可证不正确可能会导致用户预配/更新期间出错。

### <a name="terms"></a>术语

本文使用以下术语：

* CRUD 操作 - 对用户帐户执行的操作：创建、读取、更新、删除。

* 单一登录 （SSO） - 用户登录一次并访问所有启用 SSO 的应用程序的能力。 在用户预配上下文中，SSO 是用户拥有单个帐户以访问使用自动用户预配的所有系统的结果。

* 源系统 - Azure AD 提供的用户的存储库。 Azure AD 是大多数预集成预配连接器的源系统。 但是，云应用程序（如 SAP、工作日和 AWS）也有一些例外。 例如，请参阅[从工作日到 AD 的用户预配](../saas-apps/workday-inbound-tutorial.md)。

* 目标系统 - Azure AD 规定的用户存储库。 目标系统通常是 SaaS 应用程序，如服务现在、Zscaler 和 Slack。 目标系统也可以是本地系统，如AD。

* [跨域身份管理系统 （SCIM）](https://aka.ms/scimoverview) - 允许用户预配自动化的开放标准。 SCIM 在标识提供程序（如 Microsoft）和 Salesforce 或其他需要用户标识信息的 SaaS 应用之间通信用户标识数据。

### <a name="training-resources"></a>培训资源

| 资源| 链接和描述 |
| - | - |
| 点播网络研讨会| [使用 Azure AD 管理企业应用程序](https://info.microsoft.com/CO-AZUREPLAT-WBNR-FY18-03Mar-06-ManageYourEnterpriseApplicationsOption1-MCW0004438_02OnDemandRegistration-ForminBody.html)<br>了解 Azure AD 如何帮助您实现企业 SaaS 应用程序的 SSO 以及控制访问的最佳做法。 |
| 视频| [活动 Azure 目录中的用户预配是什么？](https://youtu.be/_ZjARPpI6NI) <br> [如何在活动 Azure 目录中部署用户预配？](https://youtu.be/pKzyts6kfrw) <br> [将销售人员与 Azure AD 集成：如何自动调配用户](https://azure.microsoft.com/resources/videos/integrating-salesforce-with-azure-ad-how-to-automate-user-provisioning/) |
| 在线课程| 在线技能：[管理身份](https://skillup.online/courses/course-v1:Microsoft+AZ-100.5+2018_T3/about) <br> 了解如何将 Azure AD 与许多 SaaS 应用程序集成，以及如何保护用户对这些应用程序的访问。 |
| 书籍| [Web 应用程序 Azure 活动目录（开发人员参考）第一版的现代身份验证](https://www.amazon.com/Authentication-Directory-Applications-Developer-Reference/dp/0735696942/ref=sr_1_fkmr0_1?keywords=Azure+multifactor+authentication&qid=1550168894&s=gateway&sr=8-1-fkmr0)。  <br> 这是为这些新环境构建 Active Directory 身份验证解决方案的权威深度指南。 |
| 教程| 请参阅[有关如何将 SaaS 应用与 Azure AD 集成的教程列表](../saas-apps/tutorial-list.md)。 |
| FAQ| 有关自动用户预配的[常见问题](../app-provisioning/user-provisioning.md) |

### <a name="solution-architectures"></a>解决方案体系结构

Azure AD 预配服务通过连接到每个应用程序供应商提供的用户管理 API 终结点，将用户预配到 SaaS 应用和其他系统。 这些用户管理 API 终结点允许 Azure AD 以编程方式创建、更新和删除用户。

#### <a name="automatic-user-provisioning-for-hybrid-enterprises"></a>混合动力企业的自动用户配置

在此示例中，用户和/或组是在连接到本地目录的 HR 数据库中创建的。 Azure AD 预配服务管理对目标 SaaS 应用程序的自动用户预配。

 ![用户预配](./media/plan-auto-user-provisioning/hybridprovisioning.png)

**工作流描述：**

1. 用户/组是在本地 HR 应用程序/系统（如 SAP）中创建的。 

1. **Azure AD Connect 代理**运行从本地 AD 到 Azure AD 的标识（用户和组）的计划同步。

1. **Azure AD 预配服务**针对源系统和目标系统开始[初始周期](../app-provisioning/user-provisioning.md)。 

1. **Azure AD 预配服务**查询自初始周期以来更改的任何用户和组的源系统，并在[增量周期](../app-provisioning/user-provisioning.md)中推送更改。

#### <a name="automatic-user-provisioning-for-cloud-only-enterprises"></a>仅限云的企业自动用户预配

在此示例中，用户创建发生在 Azure AD 中，Azure AD 预配服务管理对目标 （SaaS） 应用程序的自动用户预配。

![图片 2](./media/plan-auto-user-provisioning/cloudprovisioning.png)

**工作流描述：**

1. 用户/组在 Azure AD 中创建。

1. **Azure AD 预配服务**针对源系统和目标系统开始[初始周期](../app-provisioning/user-provisioning.md)。 

1. **Azure AD 预配服务**查询自初始周期以来更新的任何用户和组的源系统，并执行任何[增量周期](../app-provisioning/user-provisioning.md)。

#### <a name="automatic-user-provisioning-for-cloud-hr-applications"></a>云 HR 应用程序的自动用户配置 

在此示例中，用户和/或组是在云 HR 应用程序中（如工作日和成功因素）中创建的。 Azure AD 预配服务和 Azure AD 连接预配代理将云 HR 应用租户的用户数据预配到 AD 中。 在 AD 中更新帐户后，它将通过 Azure AD 连接与 Azure AD 同步，电子邮件地址和用户名属性可以写回云 HR 应用租户。

![图片 2](./media/plan-auto-user-provisioning/workdayprovisioning.png)

1.  **HR 团队**在云 HR 应用租户中执行事务。
2.  **Azure AD 预配服务**从云 HR 应用租户运行计划周期，并标识需要处理的更改才能与 AD 同步。
3.  **Azure AD 预配服务**调用 Azure AD 连接预配代理，其请求有效负载包含 AD 帐户创建/更新/启用/禁用操作。
4.  **Azure AD 连接预配代理**使用服务帐户来管理 AD 帐户数据。
5.  **Azure AD 连接**运行增量同步以在 AD 中提取更新。
6.  **AD**更新与 Azure AD 同步。 
7.  **Azure AD 预配服务**将电子邮件属性和用户名从 Azure AD 写入云 HR 应用租户。

## <a name="plan-the-deployment-project"></a>规划部署项目

考虑组织需要确定在环境中部署用户预配的策略。

### <a name="engage-the-right-stakeholders"></a>吸引适当的利益干系人

当技术项目失败时，通常是由于对影响、结果和责任的期望不匹配。 为了避免这些陷阱，[确保您与合适的利益相关者接洽](https://aka.ms/deploymentplans)，并通过记录利益相关者及其项目投入和问责制，充分理解项目中的利益相关者角色。

### <a name="plan-communications"></a>规划沟通

沟通对于任何新服务的成功至关重要。 主动与用户沟通他们的体验将如何改变、何时更改，以及如果他们遇到问题如何获得支持。

### <a name="plan-a-pilot"></a>规划试点

我们建议自动用户预配的初始配置在具有少量用户的测试环境中，然后再将其扩展到生产中的所有用户。 请参阅运行试验的[最佳做法](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot)。

#### <a name="best-practices-for-a-pilot"></a>飞行员的最佳做法  

通过试验，您可以在为每个人部署功能之前使用一小群进行测试。 确保作为测试的一部分，对组织中的每个用例都进行了全面测试。

在第一波中，针对 IT、可用性和其他可以测试和提供反馈的适当用户。 使用此反馈可进一步开发您发送给用户的通信和说明，并深入了解支持人员可能看到的问题类型。

通过扩大目标组的范围，将推出范围扩大到更大的用户组。 这可以通过[动态组成员身份](../users-groups-roles/groups-dynamic-membership.md)或手动将用户添加到目标组来实现。

## <a name="plan-application-connections-and-administration"></a>规划应用程序连接和管理

使用 Azure AD 门户查看和管理支持预配的所有应用程序。 请参阅[在门户中查找应用](../app-provisioning/configure-automatic-user-provisioning-portal.md)。

### <a name="determine-the-type-of-connector-to-use"></a>确定要使用的连接器类型

启用和配置自动化预配所需的实际步骤因具体应用程序而异。 如果要自动预配的应用程序在[Azure AD SaaS 应用库中](../saas-apps/tutorial-list.md)列出，则应选择[特定于应用的集成教程](../saas-apps/tutorial-list.md)来配置其预集成的用户预配连接器。

如果没有，请按照以下步骤操作：

1. [创建](../develop/howto-app-gallery-listing.md)预集成用户预配连接器的请求。 如果应用程序支持 SCIM，我们的团队将与您和应用程序开发人员合作，将您的应用程序连接到我们的平台。

1. 使用[BYOA SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md)通用用户预配支持。 这是 Azure AD 在没有预集成预配连接器的情况下向应用预配用户的要求。

1. 如果应用程序能够利用 BYOA SCIM 连接器，请参阅[BYOA SCIM 集成教程](../app-provisioning/use-scim-to-provision-users-and-groups.md)，为应用程序配置 BYOA SCIM 连接器。

有关详细信息，请参阅[哪些应用程序和系统可以与 Azure AD 自动用户预配一起使用？](../app-provisioning/user-provisioning.md)

### <a name="collect-information-to-authorize-application-access"></a>收集信息以授权应用程序访问

设置自动用户预配是每个应用程序的过程。 对于每个应用程序，您需要提供[管理员凭据](../app-provisioning/configure-automatic-user-provisioning-portal.md)才能连接到目标系统的用户管理终结点。

下图显示了所需管理员凭据的一个版本：

![预配屏幕以管理用户帐户预配设置](./media/plan-auto-user-provisioning/userprovisioning-admincredentials.png)

虽然某些应用程序需要管理员用户名和密码，但其他应用程序可能需要无记名令牌。

## <a name="plan-user-and-group-provisioning"></a>计划用户和组预配

如果为企业应用启用用户预配[，Azure 门户](https://portal.azure.com/)将通过属性映射控制其属性值。

### <a name="determine-operations-for-each-saas-app"></a>确定每个 SaaS 应用的操作

每个应用程序可能具有唯一的用户或组属性，这些属性必须映射到 Azure AD 中的属性。 应用程序可能只有 CRUD 操作的子集可用。

对于每个应用程序，记录以下信息：

* 要对目标系统的用户和/或组对象执行 CRUD 预配操作。 例如，每个 SaaS 应用业务所有者可能不希望所有可能的操作。

* 源系统中可用的属性

* 目标系统中可用的属性

* 系统之间的属性映射。

### <a name="choose-which-users-and-groups-to-provision"></a>选择要预配的用户和组

在实现自动用户预配之前，必须确定要预配到应用程序的用户和组。

* 使用[范围筛选器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)定义基于属性的规则，这些规则确定哪些用户预配到应用程序。

* 接下来，根据需要使用[用户和组分配](../manage-apps/assign-user-or-group-access-portal.md)进行其他筛选。

### <a name="define-user-and-group-attribute-mapping"></a>定义用户和组属性映射

要实现自动用户预配，您需要定义应用程序所需的用户和组属性。 Azure AD 用户对象和每个 SaaS 应用程序的用户对象之间有一组预先配置的属性和[属性映射](../app-provisioning/configure-automatic-user-provisioning-portal.md)。 并非所有 SaaS 应用都启用组属性。

Azure AD 支持直接的属性到属性映射、提供常量值或[编写属性映射表达式](../app-provisioning/functions-for-customizing-application-data.md)。 这种灵活性使您能够精细地控制目标系统属性中将填充的内容。 您可以使用[Microsoft 图形 API](../app-provisioning/export-import-provisioning-configuration.md)和图形资源管理器将用户预配属性映射和架构导出到 JSON 文件并将其导入 Azure AD。

有关详细信息，请参阅在[Azure 活动目录中为 SaaS 应用程序自定义用户预配属性映射](../app-provisioning/customize-application-attributes.md)。

### <a name="special-considerations-for-user-provisioning"></a>用户预配的特殊注意事项

请考虑以下事项，以减少部署后的问题：

* 确保用于在源应用程序和目标应用程序之间映射用户/组对象的属性具有弹性。 如果属性发生更改（例如，用户移动到公司的不同部分），它们不应导致用户/组预配不正确。

* 应用程序可能具有特定的限制和/或要求，需要满足用户预配才能正常工作。 例如，Slack 会截截某些属性的值。 请参阅特定于每个应用程序的[自动用户预配教程](../saas-apps/tutorial-list.md)。

* 确认源系统和目标系统之间的架构一致性。 常见问题包括诸如 UPN 或邮件不匹配等属性。 例如，Azure AD 中的 UPN*john_smith@contoso.com*设置为 app 中，它是*jsmith@contoso.com*。 有关详细信息，请参阅[用户和组架构引用](../app-provisioning/use-scim-to-provision-users-and-groups.md)。

## <a name="plan-testing-and-security"></a>计划测试和安全

在部署的每个阶段，确保测试结果与预期结果一样，并审核预配周期。

### <a name="plan-testing"></a>计划测试

为应用程序配置自动用户预配后，将运行测试用例以验证此解决方案是否满足组织的要求。

| 方案| 预期结果 |
| - | - |
| 用户将添加到分配给目标系统的组 | 用户对象在目标系统中预配。 <br>用户可以登录到目标系统并执行所需的操作。 |
| 用户从分配给目标系统的组中删除 | 用户对象在目标系统中取消预配。<br>用户无法登录到目标系统。 |
| 用户信息在 Azure AD 中由任何方法更新 | 更新的用户属性在增量循环后反映在目标系统中 |
| 用户范围外 | 用户对象被禁用或删除。 <br>注意： 此行为将被覆盖为[工作日预配](skip-out-of-scope-deletions.md)。 |

### <a name="plan-security"></a>规划安全性

在部署中通常需要进行安全审查。 如果需要安全审核，请参阅许多 Azure AD 白皮书，这些[白皮书](https://www.microsoft.com/download/details.aspx?id=36391)提供了作为服务的身份概述。

### <a name="plan-rollback"></a>计划回滚

如果自动用户预配实现在生产环境中无法正常工作，以下回滚步骤可以帮助您恢复到以前的已知良好状态：

1. 查看[预配摘要报告](../app-provisioning/check-status-user-account-provisioning.md)和[预配日志](../app-provisioning/check-status-user-account-provisioning.md#provisioning-logs-preview)，以确定受影响的用户和/或组发生了哪些不正确的操作。

1. 使用预配审核日志确定受影响的用户和/或组的最后已知良好状态。 还要查看源系统（Azure AD 或 AD）。

1. 与应用程序所有者合作，使用最后一个已知良好的状态值更新应用程序中直接受影响的用户和/或组。

## <a name="deploy-automatic-user-provisioning-service"></a>部署自动用户预配服务

选择与您的解决方案要求一致的步骤。

### <a name="prepare-for-the-initial-cycle"></a>准备初始周期

当 Azure AD 预配服务首次运行时，针对源系统和目标系统的初始周期将为每个目标系统创建所有用户对象的快照。

为应用程序启用自动预配时，初始周期可能需要 20 分钟到几个小时。 持续时间取决于 Azure AD 目录的大小和预配范围内的用户数。 请参阅[如何提高预配性能](../app-provisioning/application-provisioning-when-will-provisioning-finish.md)。

预配服务在初始周期后存储两个系统的状态，从而提高后续增量周期的性能。

### <a name="configure-automatic-user-provisioning"></a>配置自动用户预配

使用[Azure 门户](https://portal.azure.com/)管理支持它的应用程序的自动用户帐户预配和取消预配。 按照[如何设置应用程序自动预配中的步骤操作？](../app-provisioning/user-provisioning.md)

还可以使用 [Microsoft 图形 API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview) 配置和管理 Azure AD 用户预配服务。

## <a name="manage-automatic-user-provisioning"></a>管理自动用户预配

部署后，需要管理解决方案。

### <a name="monitor-user-provisioning-operation-health"></a>监视用户预配操作运行状况

成功[的初始周期](../app-provisioning/user-provisioning.md)后，Azure AD 预配服务将无限期地以特定于每个应用程序的时间间隔运行增量更新，直到发生以下事件之一：

* 该服务将手动停止，并使用[Azure 门户](https://portal.azure.com/)或使用相应的 Microsoft[图形 API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)命令触发新的初始周期。

* 新的初始周期由属性映射或范围筛选器的更改触发。

* 预配过程由于错误率很高而进入隔离状态，并在隔离中停留超过四周，然后将自动禁用。

要查看这些事件以及预配服务执行的所有其他活动，请参阅 Azure AD[预配日志](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)。

要了解预配周期需要多长时间并监视预配作业的进度，可以[检查用户预配的状态](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)。

### <a name="gain-insights-from-reports"></a>从报告中获取见解

Azure AD 可通过审核日志和报告提供有关组织用户预配使用情况和操作运行状况[的其他见解](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)。

管理员应检查预配摘要报告，以监视预配作业的操作运行状况。 预配服务执行的所有活动都记录在 Azure AD 审核日志中。 请参阅[教程：报告自动用户帐户预配](../app-provisioning/check-status-user-account-provisioning.md)。

我们建议您根据满足组织要求的节奏承担这些报表的所有权并使用这些报告。 Azure AD 保留大多数审核数据 30 天。

### <a name="troubleshoot"></a>疑难解答

请参阅以下链接，以排除预配期间可能出现的任何问题：

* [为 Azure AD 库应用程序配置用户预配时遇到的问题](../app-provisioning/application-provisioning-config-problem.md)

* [将属性从本地活动目录同步到 Azure AD 以预配到应用程序](../app-provisioning/user-provisioning-sync-attributes-for-mapping.md)

* [用户预配到 Azure AD 库应用程序需要数小时或更长时间](../app-provisioning/application-provisioning-when-will-provisioning-finish.md)

* [在将用户预配配置配置为 Azure 活动目录库应用程序时保存管理员凭据时出现问题](../app-provisioning/application-provisioning-config-problem-storage-limit.md)

* [没有为任何用户预配 Azure AD 库应用程序](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)

* [预配到 Azure AD 库应用程序的用户组错误](../app-provisioning/application-provisioning-config-problem-wrong-users-provisioned.md)

### <a name="helpful-documentation"></a>有用的文档

* [为属性映射编写表达式](../app-provisioning/functions-for-customizing-application-data.md)

* [Azure AD 同步 API 概述](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)

* [跳过删除超出范围的用户帐户](skip-out-of-scope-deletions.md)

* [Azure AD 连接预配代理：版本发布历史记录](provisioning-agent-release-version-history.md)

#### <a name="resources"></a>资源

* [提供产品反馈](https://feedback.azure.com/forums/169401-azure-active-directory)

* [了解 Azure AD 的新增功能](https://azure.microsoft.com/updates/?product=active-directory)

* [堆栈溢出 Azure AD 论坛](https://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="next-steps"></a>后续步骤
* [配置自动用户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)

* [使用 Microsoft 图形 API 导出或导入预配配置](../app-provisioning/export-import-provisioning-configuration.md)

* [为 Azure 活动目录中的属性映射编写表达式](../app-provisioning/functions-for-customizing-application-data.md)
