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
ms.date: 09/15/2017
ms.author: asmalser
ms.openlocfilehash: 72f796f0a4522b66feb55b827b02a83dcfdd3a01
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2018
ms.locfileid: "34069884"
---
# <a name="automate-user-provisioning-and-deprovisioning-to-saas-applications-with-azure-active-directory"></a>Azure Active Directory SaaS 应用程序的自动化用户预配和取消预配
## <a name="what-is-automated-user-provisioning-for-saas-apps"></a>什么是 SaaS 应用的自动化用户预配？
Azure Active Directory (Azure AD) 允许用户自动创建、维护和删除云 ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) 应用程序（如 Dropbox、Salesforce、ServiceNow 等）中的用户标识。

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

有关 Azure AD 中支持预先集成预配连接器的所有应用程序的列表，请参阅[支持用户预配的应用程序列表教程](active-directory-saas-tutorial-list.md)。

有关如何在应用程序中添加 Azure AD 用户预配支持的信息，请参阅[使用 SCIM 在应用程序中自动预配 Azure Active Directory 中的用户和组](active-directory-scim-provisioning.md)。

如需联系 Azure AD 工程团队以请求其他应用程序的预配支持，请通过 [Azure Active Directory 反馈论坛](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests/filters/new?category_id=172035)提交消息。    

> [!NOTE]
> 为了使应用程序支持自动化用户预配，它必须首先提供必要的、允许外部程序自动执行创建、维护和删除用户操作的用户管理 API。 因此，不是所有的 SaaS 应用都能兼容此功能。 对于不支持用户管理 API 的应用，Azure AD 工程团队能够构建连接到这些应用的设置连接器，并按当前和潜在客户需求设置优先级。 
    
    
## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>如何设置为自动预配到应用程序？

可以在 **[Azure 门户](https://portal.azure.com)** 中开始针对选定的应用程序配置 Azure AD 预配服务。 在“Azure Active Directory”>“企业应用程序”部分中，依次选择“添加”、“所有”，并根据情况添加以下项之一：

* “特色应用程序”部分中的所有应用程序支持自动预配。 有关其他应用程序，请参阅[支持用户预配的应用程序列表教程](active-directory-saas-tutorial-list.md)。

* 使用“非库应用程序”选项进行定制开发的 SCIM 集成

![库](./media/active-directory-saas-app-provisioning/gallery.png)

在应用程序管理屏幕中的“预配”选项卡上配置预配。

![设置](./media/active-directory-saas-app-provisioning/provisioning_settings0.PNG)


* 必须为 Azure AD 预配服务提供“管理员凭据”，使其能够连接到应用程序提供的用户管理 API。 还可以在此部分中启用电子邮件通知，以便在凭据失败，或者预配作业进入[隔离区](#quarantine)时发送通知。

* 可以配置“属性映射”，用于指定要将源系统（例如 Azure AD）中的哪些字段内容同步到目标系统（例如 ServiceNow）中的哪些字段。 除了用户帐户的预配以外，还可在本部分中选择性地配置组的预配（如果目标应用程序支持这样做）。 使用“匹配的属性”可以选择要将哪些字段用于匹配系统之间的帐户。 使用 [表达式](active-directory-saas-writing-expressions-for-attribute-mappings.md)”可先修改和转换从源系统检索的值，然后将其写入目标系统。 有关详细信息，请参阅[自定义属性映射](active-directory-saas-customizing-attribute-mappings.md)。

![设置](./media/active-directory-saas-app-provisioning/provisioning_settings1.PNG)

* “范围筛选器”告知预配服务应将源系统中的哪些用户和组预配和/或取消预配到目标系统。 范围筛选器有两个条件会一起评估，确定哪些对象在预配的范围内：

    * **根据属性值进行筛选** - 属性映射中的“源对象范围”菜单允许根据特定的属性值进行筛选。 例如，可以指定只能将“Department”属性为“Sales”的用户列入预配范围。 有关详细信息，请参阅[使用范围筛选器](active-directory-saas-scoping-filters.md)。

    * **根据分配进行筛选** - 使用门户的“预配”>“设置”部分中的“范围”菜单，可以指定是只能将“分配的”用户和组列入预配范围，还是应该预配 Azure AD 目录中的所有用户。 有关“分配的”用户和组的信息，请参阅[在 Azure Active Directory 中将用户或组分配到企业应用](active-directory-coreapps-assign-user-azure-portal.md)。
    
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
2. 使用配置的任何[分配](active-directory-coreapps-assign-user-azure-portal.md)或[基于属性的范围筛选器](active-directory-saas-scoping-filters.md)筛选返回的用户和组。
3. 找到要分配的用户，或者在预配范围中找到某个用户后，服务将使用指定的[匹配属性](active-directory-saas-customizing-attribute-mappings.md#understanding-attribute-mapping-properties)在目标系统中查询匹配的用户。 示例：如果源系统中的 userPrincipal 名称是匹配的属性并映射到目标系统中的 userName，则预配服务会在目标系统中查询与源系统中 userPrincipal 名称值匹配的 userName。
4. 如果在目标系统中未找到匹配的用户，则使用从源系统返回的属性创建该用户。
5. 如果找到匹配的用户，则使用源系统提供的属性更新该用户。
6. 如果属性映射包含“reference”属性，该服务将在目标系统中执行附加的更新，以创建并链接引用对象。 例如，用户可能在目标系统中有“Manager”属性，该属性链接到目标系统中创建的另一个用户。
7. 在初始同步结束时保留水印，以便为后续的增量同步提供起点。

某些应用程序（例如 ServiceNow、Google Apps 和 Box）不仅支持预配用户，而且还支持预配组及其成员。 在这种情况下，如果在[映射](active-directory-saas-customizing-attribute-mappings.md)中启用了组预配，则预配服务会同步用户和组，然后同步组成员身份。 

### <a name="incremental-syncs"></a>增量同步
初始同步后，所有的后续同步是：

1. 在源系统中查询自上次存储水印以来已更新的所有用户和组。
2. 使用配置的任何[分配](active-directory-coreapps-assign-user-azure-portal.md)或[基于属性的范围筛选器](active-directory-saas-scoping-filters.md)筛选返回的用户和组。
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

预配服务会根据[特定于每个应用程序的教程](active-directory-saas-tutorial-list.md)中定义的间隔，持续无限期地运行后端到后端的增量同步，直到发生以下事件之一：

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


## <a name="frequently-asked-questions"></a>常见问题

**预配我的用户需要多长时间？**

根据预配作业执行的是初始同步还是增量同步，性能会有所不同。

完成初始同步所需的时间直接取决于源系统中的用户、组和组成员身份数量。 包含几百个对象的极小型源系统只需几分钟就能完成初始同步。 但是，包含几十万甚至几百万个组合对象的源系统将需要更长时间才能完成初始同步。

完成增量同步所需的时间取决于在该同步周期中检测到的更改数量。 如果检测到 5,000 个以下的用户或组成员身份更改，则同步周期通常在 40 分钟内即可完成。 

请注意，整体性能取决于源系统和目标系统。 某些目标系统在大规模同步操作期间实施请求速率限制，这可能会影响性能，而这些系统的预建 Azure AD 预配连接器会考虑此因素。

如果生成了许多错误（记录在[审核日志](active-directory-saas-provisioning-reporting.md)中），并且预配服务已进入“隔离”状态，则性能也会变慢。

**如何提高同步的性能？**

大部分性能问题是在对包含大量组和组成员身份的系统执行初始同步期间发生的。

如果不需要同步组或组成员身份，则可通过以下方式大幅提升同步性能：

1. 将“预配”>“设置”>“范围”菜单设置为“全部同步”，而不是同步分配的用户和组。
2. 使用[范围筛选器](active-directory-saas-scoping-filters.md)而不是分配来筛选预配用户的列表。

> [!NOTE]
> 对于支持预配组名称和组属性的应用程序（例如 ServiceNow 和 Google Apps），禁用此功能还会减少完成初始同步所需的时间。 如果不想要将组名称和组成员身份预配到应用程序，可以在预配配置的[属性映射](active-directory-saas-customizing-attribute-mappings.md)中禁用此功能。

**可以如何跟踪当前预配作业的进度？**

请参阅[预配报告指南](active-directory-saas-provisioning-reporting.md)。

**我如何知道用户无法正确预配？**

所有错误记录在 Azure AD 审核日志中。 有关详细信息，请参阅[预配报告指南](active-directory-saas-provisioning-reporting.md)。

**如何构建支持预配服务的应用程序？**

请参阅[使用 SCIM 将 Azure Active Directory 中的用户和组自动预配到应用程序](https://docs.microsoft.com/azure/active-directory/active-directory-scim-provisioning)。

**如何向工程团队提交反馈？**

请通过 [Azure Active Directory 反馈论坛](https://feedback.azure.com/forums/169401-azure-active-directory/)与我们联系。


## <a name="related-articles"></a>相关文章
* [有关如何集成 SaaS 应用的教程列表](active-directory-saas-tutorial-list.md)
* [为用户预配自定义属性映射](active-directory-saas-customizing-attribute-mappings.md)
* [为属性映射编写表达式](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [用于用户预配的作用域筛选器](active-directory-saas-scoping-filters.md)
* [使用 SCIM 启用从 Azure Active Directory 到应用程序的用户和组自动预配](active-directory-scim-provisioning.md)
* [Azure AD 同步 API 概述](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
* [应用程序的出站用户预配的分步部署计划](https://aka.ms/userprovisioningdeploymentplan)

