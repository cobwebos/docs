---
title: Azure AD 中的自动 SaaS 应用用户预配 | Microsoft Docs
description: 介绍可以如何使用 Azure AD 进行自动化设置、取消设置，并不断跨多个第三方 SaaS 应用程序更新用户帐户。
services: active-directory
documentationcenter: ''
author: asmalser-msft
manager: mtillman
editor: ''
ms.assetid: 58c5fa2d-bb33-4fba-8742-4441adf2cb62
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/26/2018
ms.author: asmalser
ms.openlocfilehash: c7a18132a797bd7411487c233fc41647cc20dfb4
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37025135"
---
# <a name="automate-user-provisioning-and-deprovisioning-to-saas-applications-with-azure-active-directory"></a>Azure Active Directory SaaS 应用程序的自动化用户预配和取消预配
## <a name="what-is-automated-user-provisioning-for-saas-apps"></a>什么是 SaaS 应用的自动化用户预配？
Azure Active Directory (Azure AD) 允许用户自动创建、维护和删除云 ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) 应用程序（如 Dropbox、Salesforce、ServiceNow 等）中的用户标识。

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

**以下是此功能操作的一些示例：**

* 在新人加入团队或组织时，在适当的系统中为他们自动创建新帐户。
* 在新人离开团队或组织时，在适当的系统中自动停用其帐户。
* 确保应用和系统中的标识基于目录中的更改或人力资源系统保持最新。
* 将非用户对象（例如组）预配到支持它们的应用程序。

**自动化用户预配还包括以下功能：**

* 在源与目标系统之间匹配现有标识的功能。
* 可自定义的属性映射，定义应将哪些用户数据从源系统流向目标系统。
* 可选电子邮件警报，用于预配错误
* 报表和活动日志，用于帮助进行监视和故障排除。

## <a name="why-use-automated-provisioning"></a>为何要使用自动化预配？
使用此功能的一些常见动机包括：

* 避免与手动预配过程相关的成本、低效和人为错误。
* 避免产生与托管和维护定制开发的预配解决方案和脚本相关的成本
* 通过在用户离开组织时立即删除其在关键 SaaS 应用中的标识，从而保护组织。
* 轻松将大量用户导入特定的 SaaS 应用程序或系统。
* 享用一组策略，确定已预配哪些用户，以及哪些用户可登录应用。

## <a name="how-does-automatic-provisioning-work"></a>自动化预配的工作原理
    
**Azure AD 预配服务**通过连接到由每个应用程序供应商提供的用户管理 API 终结点，将用户预配到 SaaS 应用和其他系统。 这些用户管理 API 终结点允许 Azure AD 以编程方式创建、更新和删除用户。 对于选定的应用程序，预配服务还可以创建、更新和删除其他标识相关的对象，例如组和角色。 

![预配](./media/active-directory-saas-app-provisioning/provisioning0.PNG)
*图 1：Azure AD 预配服务*

![出站预配](./media/active-directory-saas-app-provisioning/provisioning1.PNG)
*图 2：从 Azure AD 到常见 SaaS 应用程序的“出站”用户预配工作流*

![入站预配](./media/active-directory-saas-app-provisioning/provisioning2.PNG)
*图 3：从常见人力资本管理 (HCM) 应用程序到 Azure Active Directory 和 Windows Server Active Directory 的“入站”用户预配工作流*


## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>可在哪些应用程序和系统中使用 Azure AD 自动用户预配？

Azure AD 功能预先集成了对多种常见 SaaS 应用和人力资源系统的支持，以及对实现 SCIM 2.0 标准特定部分的应用的一般性支持。

### <a name="pre-integrated-applications"></a>预先集成的应用程序
有关 Azure AD 中支持预先集成预配连接器的所有应用程序的列表，请参阅[支持用户预配的应用程序列表教程](saas-apps/tutorial-list.md)。

如需联系 Azure AD 工程团队以请求其他应用程序的预配支持，请通过 [Azure Active Directory 反馈论坛](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests/filters/new?category_id=172035)提交消息。

> [!NOTE]
> 为了使应用程序支持自动化用户预配，它必须首先提供必要的、允许外部程序自动执行创建、维护和删除用户操作的用户管理 API。 因此，不是所有的 SaaS 应用都能兼容此功能。 对于不支持用户管理 API 的应用，Azure AD 工程团队能够构建连接到这些应用的设置连接器，并按当前和潜在客户需求设置优先级。 

### <a name="connecting-applications-that-support-scim-20"></a>连接支持 SCIM 2.0 的应用程序
若要了解如何常规连接实现基于 SCIM. 2.0 的用户管理 API 的应用程序，请参阅[使用 SCIM 自动将用户和组从 Azure Active Directory 预配到应用程序](manage-apps/use-scim-to-provision-users-and-groups.md)。

    
## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>如何设置为自动预配到应用程序？

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

可以在 **[Azure 门户](https://portal.azure.com)** 中开始针对选定的应用程序配置 Azure AD 预配服务。 在“Azure Active Directory”>“企业应用程序”部分中，依次选择“添加”、“所有”，并根据情况添加以下项之一：

* “特色应用程序”部分中的所有应用程序支持自动预配。 有关其他应用程序，请参阅[支持用户预配的应用程序列表教程](saas-apps/tutorial-list.md)。

* 使用“非库应用程序”选项进行定制开发的 SCIM 集成

![库](./media/active-directory-saas-app-provisioning/gallery.png)

在应用程序管理屏幕中的“预配”选项卡上配置预配。

![设置](./media/active-directory-saas-app-provisioning/provisioning_settings0.PNG)


* 必须为 Azure AD 预配服务提供“管理员凭据”，使其能够连接到应用程序提供的用户管理 API。 还可以在此部分中启用电子邮件通知，以便在凭据失败，或者预配作业进入[隔离区](#quarantine)时发送通知。

* 可以配置“属性映射”，用于指定要将源系统（例如 Azure AD）中的哪些字段内容同步到目标系统（例如 ServiceNow）中的哪些字段。 除了用户帐户的预配以外，还可在本部分中选择性地配置组的预配（如果目标应用程序支持这样做）。 使用“匹配的属性”可以选择要将哪些字段用于匹配系统之间的帐户。 使用 [表达式](active-directory-saas-writing-expressions-for-attribute-mappings.md)”可先修改和转换从源系统检索的值，然后将其写入目标系统。 有关详细信息，请参阅[自定义属性映射](active-directory-saas-customizing-attribute-mappings.md)。

![设置](./media/active-directory-saas-app-provisioning/provisioning_settings1.PNG)

* “范围筛选器”告知预配服务应将源系统中的哪些用户和组预配和/或取消预配到目标系统。 范围筛选器有两个条件会一起评估，确定哪些对象在预配的范围内：

    * **根据属性值进行筛选** - 属性映射中的“源对象范围”菜单允许根据特定的属性值进行筛选。 例如，可以指定只能将“Department”属性为“Sales”的用户列入预配范围。 有关详细信息，请参阅[使用范围筛选器](active-directory-saas-scoping-filters.md)。

    * **根据分配进行筛选** - 使用门户的“预配”>“设置”部分中的“范围”菜单，可以指定是只能将“分配的”用户和组列入预配范围，还是应该预配 Azure AD 目录中的所有用户。 有关“分配的”用户和组的信息，请参阅[在 Azure Active Directory 中将用户或组分配到企业应用](manage-apps/assign-user-or-group-access-portal.md)。
    
* “设置”控制预配服务针对应用程序执行的操作，包括该操作当前是否正在运行。

* “审核日志”提供 Azure AD 预配服务执行的每项操作的记录。 有关更多详细信息，请参阅[预配报告指南](active-directory-saas-provisioning-reporting.md)。

![设置](./media/active-directory-saas-app-provisioning/audit_logs.PNG)

> [!NOTE]
> 还可以使用 [Microsoft 图形 API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview) 配置和管理 Azure AD 用户预配服务。


## <a name="what-happens-during-provisioning"></a>预配期间会发生什么情况？

如果 Azure AD 是源系统，预配服务将使用 [Azure AD 图形 API 的差异查询功能](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query)来监视用户和组。 预配服务针对源系统和目标系统运行初始同步，然后运行定期的增量同步。 

### <a name="initial-sync"></a>初始同步
当预配服务启动时，执行的第一次同步是：

1. 查询源系统中的所有用户和组，并检索[属性映射](active-directory-saas-customizing-attribute-mappings.md)中定义的所有属性。
2. 使用配置的任何[分配](manage-apps/assign-user-or-group-access-portal.md)或[基于属性的范围筛选器](active-directory-saas-scoping-filters.md)筛选返回的用户和组。
3. 找到要分配的用户，或者在预配范围中找到某个用户后，服务将使用指定的[匹配属性](active-directory-saas-customizing-attribute-mappings.md#understanding-attribute-mapping-properties)在目标系统中查询匹配的用户。 示例：如果源系统中的 userPrincipal 名称是匹配的属性并映射到目标系统中的 userName，则预配服务会在目标系统中查询与源系统中 userPrincipal 名称值匹配的 userName。
4. 如果在目标系统中未找到匹配的用户，则使用从源系统返回的属性创建该用户。
5. 如果找到匹配的用户，则使用源系统提供的属性更新该用户。
6. 如果属性映射包含“reference”属性，该服务将在目标系统中执行附加的更新，以创建并链接引用对象。 例如，用户可能在目标系统中有“Manager”属性，该属性链接到目标系统中创建的另一个用户。
7. 在初始同步结束时保留水印，以便为后续的增量同步提供起点。

某些应用程序（例如 ServiceNow、Google Apps 和 Box）不仅支持预配用户，而且还支持预配组及其成员。 在这种情况下，如果在[映射](active-directory-saas-customizing-attribute-mappings.md)中启用了组预配，则预配服务会同步用户和组，然后同步组成员身份。 

### <a name="incremental-syncs"></a>增量同步
初始同步后，所有的后续同步是：

1. 在源系统中查询自上次存储水印以来已更新的所有用户和组。
2. 使用配置的任何[分配](manage-apps/assign-user-or-group-access-portal.md)或[基于属性的范围筛选器](active-directory-saas-scoping-filters.md)筛选返回的用户和组。
3. 找到要分配的用户，或者在预配范围中找到某个用户后，服务将使用指定的[匹配属性](active-directory-saas-customizing-attribute-mappings.md#understanding-attribute-mapping-properties)在目标系统中查询匹配的用户。
4. 如果在目标系统中未找到匹配的用户，则使用从源系统返回的属性创建该用户。
5. 如果找到匹配的用户，则使用源系统提供的属性更新该用户。
6. 如果属性映射包含“reference”属性，该服务将在目标系统中执行附加的更新，以创建并链接引用对象。 例如，用户可能在目标系统中有“Manager”属性，该属性链接到目标系统中创建的另一个用户。
7. 如果从范围中删除了以前在预配范围内的某个用户（包括未分配的用户），该服务会通过更新在目标系统中禁用该用户。
8. 如果在源系统中禁用或软删除了以前在预配范围内的某个用户，该服务会通过更新在目标系统中禁用该用户。
9. 如果在源系统中硬删除了以前在预配范围内的某个用户，该服务会在目标系统中删除该用户。 在 Azure AD 中，软删除用户 30 天后会将其硬删除。
10. 在增量同步结束时保留新水印，以便为后续的增量同步提供起点。

>[!NOTE]
> 可以根据需要，使用[属性映射](active-directory-saas-customizing-attribute-mappings.md)部分中的“目标对象操作”复选框来禁用创建、更新或删除操作。 还可以使用“accountEnabled”等字段通过属性映射来控制用于在更新期间禁用用户的逻辑。

预配服务会根据[特定于每个应用程序的教程](saas-apps/tutorial-list.md)中定义的间隔，持续无限期地运行后端到后端的增量同步，直到发生以下事件之一：

* 使用 Azure 门户或使用相应的图形 API 命令手动停止了该服务 
* 在 Azure 门户中使用“清除状态并重启”选项，或使用相应的图形 API 命令触发了新的初始同步。 这会清除所有存储的水印，并导致重新评估所有源对象。
* 由于属性映射或范围筛选器发生更改而触发了新的初始同步。 这也会清除所有存储的水印，并导致重新评估所有源对象。
* 错误率较高导致预配进程进入隔离区（参阅下文），并保留在隔离区四周以上。 在此情况下，该服务会自动禁用。

### <a name="errors-and-retries"></a>错误和重试 
如果由于目标系统中出错而无法在目标系统中添加、更新或删除单个用户，则会在下一个同步周期重试该操作。 如果用户操作继续失败，则重试频率将开始递减，并逐渐减少到每天只尝试一次。 若要解决失败，管理员需要检查[审核日志](active-directory-saas-provisioning-reporting.md)中的“process escrow”（进程保管）事件，以确定根本原因并采取适当的措施。 常见的失败包括：

* 未在源系统中填充目标系统中所需的某个用户属性
* 源系统中存在一个在目标系统中具有唯一约束的用户属性值，但另一条用户记录中存在相同的值

若要解决这些失败，可在源系统中调整受影响用户的属性值，或调整属性映射，以避免冲突。   

### <a name="quarantine"></a>隔离
如果某个错误（例如管理员凭据无效）导致针对目标系统发出的大部分或所有调用持续失败，则预配作业会进入“隔离”状态。 [预配摘要报告](active-directory-saas-provisioning-reporting.md)中会指示此状态；如果在 Azure 门户中配置了电子邮件通知，则会通过电子邮件告知此状态。 

进入隔离区后，增量同步的频率会逐渐减少为每天一次。 

解决所有造成问题的错误后，将从隔离区中删除预配作业，然后，下一个同步周期开始。 如果预配作业在隔离区中保留四周以上，则会禁用预配作业。


## <a name="how-long-will-it-take-to-provision-users"></a>预配用户需要多长时间？

性能取决于预配作业执行的是初始同步还是增量同步，如上一部分中所述。

对于**初始同步**，作业时间取决于各种因素，包括预配范围中用户和组的数目，以及源系统中用户和组的总数。 本部分中的下文中汇总了影响初始同步性能的因素的完整列表。

对于**增量同步**，作业时间取决于在该同步周期中检测到的更改数量。 如果有少于 5000 个用户或组成员身份更改，则作业可以在单个增量同步周期内完成。 

下表总结了常见的预配方案的同步时间。 在这些方案中，源系统是 Azure AD，目标系统是 SaaS 应用程序。 同步时间是通过对 SaaS 应用程序 ServiceNow、Workplace、Salesforce 和 Google Apps 的同步作业进行统计分析得出的。


| 作用域配置 | 作用域中的用户、组和成员 | 初始同步时间 | 增量同步时间 |
| -------- | -------- | -------- | -------- |
| 仅同步已分配的用户和组 |  < 1,000 |  < 30 分钟 | < 30 分钟 |
| 仅同步已分配的用户和组 |  1,000 - 10,000 | 142 - 708 分钟 | < 30 分钟 |
| 仅同步已分配的用户和组 |   10,000 - 100,000 | 1,170 - 2,340 分钟 | < 30 分钟 |
| 同步 Azure AD 中的所有用户和组 |  < 1,000 | < 30 分钟  | < 30 分钟 |
| 同步 Azure AD 中的所有用户和组 |  1,000 - 10,000 | < 30 - 120 分钟 | < 30 分钟 |
| 同步 Azure AD 中的所有用户和组 |  10,000 - 100,000  | 713 - 1,425 分钟 | < 30 分钟 |
| 同步 Azure AD 中的所有用户|  < 1,000  | < 30 分钟 | < 30 分钟 |
| 同步 Azure AD 中的所有用户 | 1,000 - 10,000  | 43 - 86 分钟 | < 30 分钟 |


对于“仅同步已分配的用户和组”配置，可以使用以下公式来确定大概的最小和最大预计**初始同步**时间：

    Minimum minutes =  0.01 x [Number of assigned users, groups, and group members]
    Maximum minutes = 0.08 x [Number of assigned users, groups, and group members] 
    
影响**初始同步**的完成时间的因素汇总：

* 预配范围中用户和组的总数

* 源系统 (Azure AD) 中存在的用户、组和组成员的总数

* 预配范围中的用户与目标应用程序中的现有用户匹配，还是需要首次创建。 要首次为其创建所有用户的同步作业花费的时间大约是所有用户都与现有用户匹配的同步作业花费的时间的“两倍”。

* [审核日志](active-directory-saas-provisioning-reporting.md)中的错误数。 如果有许多错误，并且预配服务已进入“隔离”状态，则性能较低   

* 目标系统实现的请求速率限制。 某些目标系统实现了在大型同步操作期间可能会影响性能的请求速率限制。 在这些情况下，太快地接收太多请求的应用可能会拖慢其响应速率或关闭连接。 为提高性能，连接器需要进行调整，以不高于应用可以应对的处理速率的速率来向应用发送请求。 Microsoft 构建的预配连接器进行此调整。 

* 已分配的组的数量和大小。 同步已分配的组比同步用户花费的时间要长。 已分配的组的数量和大小都会影响性能。 如果应用程序[为组对象同步启用了映射](active-directory-saas-customizing-attribute-mappings.md#editing-group-attribute-mappings)，则除了用户之外，还会同步组名称和成员身份等组属性。 这些额外的同步比仅同步用户对象需要花费更长的时间。


##<a name="how-can-i-tell-if-users-are-being-provisioned-properly"></a>如何判断用户预配是否正确？

Azure AD 审核日志中记录了用户预配服务执行的所有操作。 这包括对源和目标系统执行的所有读写操作，以及在每次操作期间读取或写入的用户数据。

若要了解如何在 Azure 门户中读取审核日志，请参阅[预配报告指南](active-directory-saas-provisioning-reporting.md)。


##<a name="how-do-i-troubleshoot-issues-with-user-provisioning"></a>如何排查用户预配问题？

有关如何排查自动用户预配问题的基于方案的指导，请参阅[在应用程序中配置和预配用户时出现问题](active-directory-application-provisioning-content-map.md)。


##<a name="what-are-the-best-practices-for-rolling-out-automatic-user-provisioning"></a>推出自动用户预配的最佳做法是什么？

> [!VIDEO https://www.youtube.com/embed/MAy8s5WSe3A]

有关向应用程序执行出站用户预配的分步部署计划，请参阅[有关用户预配的标识部署指南](https://aka.ms/userprovisioningdeploymentplan)/


## <a name="related-articles"></a>相关文章
* [有关如何集成 SaaS 应用的教程列表](saas-apps/tutorial-list.md)
* [为用户预配自定义属性映射](active-directory-saas-customizing-attribute-mappings.md)
* [为属性映射编写表达式](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [用于用户预配的作用域筛选器](active-directory-saas-scoping-filters.md)
* [使用 SCIM 启用从 Azure Active Directory 到应用程序的用户和组自动预配](manage-apps/use-scim-to-provision-users-and-groups.md)
* [Azure AD 同步 API 概述](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
