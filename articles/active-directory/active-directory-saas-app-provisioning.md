---
title: "Azure AD 中的自动 SaaS 应用用户预配 | Microsoft Docs"
description: "介绍可以如何使用 Azure AD 进行自动化设置、取消设置，并不断跨多个第三方 SaaS 应用程序更新用户帐户。"
services: active-directory
documentationcenter: 
author: asmalser-msft
manager: femila
editor: 
ms.assetid: 58c5fa2d-bb33-4fba-8742-4441adf2cb62
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/15/2017
ms.author: asmalser
ms.openlocfilehash: 3fe57e9c22d04a3557978093ce3fe86613c5c1d6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
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
* 用于帮助 Azure AD 适应组织当前使用的应用和系统配置的自定义选项。
* 可选电子邮件警报，用于设置错误。
* 报表和活动日志，用于帮助进行监视和故障排除。

## <a name="why-use-automated-provisioning"></a>为何要使用自动化预配？
使用此功能的一些常见动机包括：

* 避免与手动设置过程相关的成本、低效和人为错误。
* 避免产生与托管和维护定制开发的预配解决方案和脚本相关的成本
* 通过在用户离开组织时立即删除其在关键 SaaS 应用中的标识，从而保护组织。
* 轻松将大量用户导入特定的 SaaS 应用程序或系统。
* 体验设置解决方案运行为 Azure AD 单一登录定义的相同应用访问策略的便利。


## <a name="how-does-automatic-provisioning-work"></a>自动化预配的工作原理
    
**Azure AD 预配服务**通过连接到由每个应用程序供应商提供的用户管理 API 终结点，将用户预配到 SaaS 应用和其他系统。 这些用户管理 API 终结点允许 Azure AD 以编程方式创建、更新和删除用户。 对于选定的应用程序，预配服务还可以创建、更新和删除其他标识相关的对象，例如组和角色。 

![预配](./media/active-directory-saas-app-provisioning/provisioning0.PNG)
*图 1：Azure AD 预配服务*

![出站预配](./media/active-directory-saas-app-provisioning/provisioning1.PNG)
*图 2：从 Azure AD 到常见 SaaS 应用程序的“出站”用户预配工作流*

![入站预配](./media/active-directory-saas-app-provisioning/provisioning2.PNG)
*图 3：从常见人力资本管理 (HCM) 应用程序到 Azure Active Directory 和 Windows Server Active Directory 的“入站”用户预配工作流*


## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>可在哪些应用程序和系统中使用 Azure AD 自动用户预配？

Azure AD 功能预先集成了对多种常见 SaaS 应用和人力资源系统的支持，以及对实现 [SCIM 2.0 标准](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-scim-provisioning)特定部分的应用的一般性支持。

Azure AD 应用程序库中的所有“特别推荐的”应用均支持自动化用户预配。 [可以在此处查看特别推荐的应用列表。](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1&subcategories=featured)

为了使应用程序支持自动化用户预配，它必须首先提供必要的、允许外部程序自动执行创建、维护和删除用户操作的用户管理终结点。 因此，不是所有的 SaaS 应用都能兼容此功能。 对于不支持用户管理 API 的应用，Azure AD 工程团队能够构建连接到这些应用的设置连接器，并按当前和潜在客户需求设置优先级。 

如需联系 Azure AD 工程团队以请求其他应用程序的预配支持，请通过 [Azure Active Directory 反馈论坛](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests/category/172035-user-provisioning)提交消息。 
    
    
## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>如何设置为自动预配到应用程序？

可以在 **[Azure 门户](https://potal.azure.com)**中开始针对选定的应用程序配置 Azure AD 预配服务。 在“Azure Active Directory”>“企业应用程序”部分中，依次选择“添加”、“所有”，并根据情况添加以下项之一：

* “特色应用程序”部分中的所有应用程序支持自动预配

* 使用“非库应用程序”选项进行定制开发的 SCIM 集成

![库](./media/active-directory-saas-app-provisioning/gallery.png)

在应用程序管理屏幕中的“预配”选项卡上配置预配。

![设置](./media/active-directory-saas-app-provisioning/provisioning_settings0.PNG)


* 必须为 Azure AD 预配服务提供“管理员凭据”，使其能够连接到应用程序提供的用户管理 API。

* 可以配置“属性映射”，用于指定要将源系统（例如 Azure AD）中的哪些字段内容同步到目标系统（例如 ServiceNow）中的哪些字段。 除了用户帐户的预配以外，还可在本部分中选择性地配置组的预配（如果目标应用程序支持这样做）。 使用“匹配的属性”可以选择要将哪些字段用于匹配系统之间的帐户。 使用[表达式](active-directory-saas-writing-expressions-for-attribute-mappings.md)可先修改和转换从源系统检索的值，然后将其写入目标系统。 有关详细信息，请参阅[自定义属性映射](active-directory-saas-customizing-attribute-mappings.md)。

![设置](./media/active-directory-saas-app-provisioning/provisioning_settings1.PNG)

* “范围筛选器”告知预配服务应将源系统中的哪些用户和组预配和/或取消预配到目标系统。 范围筛选器有两个条件会一起评估，确定哪些对象在预配的范围内：

* **根据属性值进行筛选** - 属性映射中的“源对象范围”菜单允许根据特定的属性值进行筛选。 例如，可以指定只能将“Department”属性为“Sales”的用户列入预配范围。

* **根据分配进行筛选** - 使用门户的“预配”>“设置”部分中的“范围”菜单，可以指定是只能将“分配的”用户和组列入预配范围，还是应该预配 Azure AD 目录中的所有用户。 有关“分配的”用户和组的信息，请参阅[在 Azure Active Directory 中将用户或组分配到企业应用](active-directory-coreapps-assign-user-azure-portal.md)。
    
* “设置”控制预配服务针对应用程序执行的操作，包括该操作当前是否正在运行。

* “审核日志”提供 Azure AD 预配服务执行的每项操作的记录。 有关更多详细信息，请参阅[预配报告指南](active-directory-saas-provisioning-reporting.md)。

![设置](./media/active-directory-saas-app-provisioning/audit_logs.PNG)

## <a name="what-happens-during-provisioning"></a>预配期间会发生什么情况？

1. 首次为应用程序启用设置时，将执行以下操作：
   * Azure AD 会尝试将 SaaS 应用中的任何现有用户与其在目录中相应的标识进行匹配。 匹配用户时，*不*自动为其启用单一登录。 为了使用户具有访问该应用程序的权限，必须将其显式分配到 Azure AD 中的应用，可直接分配或通过组成员身份进行分配。
   * 如果已指定应分配到应用程序的用户，并且如果 Azure AD 无法找到这些用户的现有帐户，则 Azure AD 将为它们在应用程序中配置新帐户。
2. 如上所述完成初始同步操作后，Azure AD 会每隔 10 分钟检查以下更改：
   * 如果已将新用户分配到应用程序（直接或通过组成员身份），则会在 SaaS 应用中为其预配新帐户。
   * 如果已删除用户的访问权限，则其在 SaaS 应用中的帐户将标记为禁用（绝不会完全删除用户，这有助于在发生配置错误时避免数据丢失）。
   * 如果用户是最近分配到应用程序并且已在 SaaS 应用中拥有一个帐户，则该帐户将标记为启用，并且如果它们与目录对比已过期，则可能会更新某些用户的属性。
   * 如果已在目录中更改用户的信息（例如电话号码、办公地点），则 SaaS 应用程序中也将更新该信息。


## <a name="frequently-asked-questions"></a>常见问题
**Azure AD 将目录更改写入到 SaaS 应用的频率是多少？**

在完成初始完全同步后，Azure AD 预配服务通常每隔 20 分钟检查更改。 

如果 SaaS 应用返回几个错误（例如无效的管理员凭据），则 Azure AD 将逐渐降低频率到每天一次，直到完成错误修复。 在此情况下，Azure AD 预配作业进入“隔离”状态，并在[预配摘要报告](active-directory-saas-provisioning-reporting.md)中指示此状态。

**预配我的用户需要多长时间？**

完成初始完全同步后，通常会在 20-40 分钟内发生增量更改。 如果尝试预配目录的大部分，时长取决于拥有的用户和组数量。 性能取决于预配服务从源系统读取数据以及将数据写入目标系统时所用的用户管理 API 的性能。 

如果生成了许多错误（记录在[审核日志](active-directory-saas-provisioning-reporting.md)中），并且预配服务已进入“隔离”状态，则性能也会变慢。

**什么是初始完全同步，为何它花费的时间比后续同步要长？**

首次针对给定的应用运行 Azure AD 预配服务时，会创建源目录中用户（有时还包括组）的“快照”。 此快照可让预配服务减少往返访问源和目标 API 的次数，并使后续的“增量”同步的行为更加有效。 

极小型目录中用户的初始完全同步通常可以在几分钟内完成，但对于较大的目录，可能需要几个小时。 包含几十万个用户的企业目录可能需要花费几个小时才能完成初始同步。 但是，在初始同步之后，后续的“增量”同步要快得多。

> [!NOTE]
> 对于支持组和组成员身份预配的应用程序，启用此功能会大幅增加完成完全同步所需的时间。 如果不想要将组名称和组成员身份预配到应用程序，可以在预配配置的[属性映射](active-directory-saas-customizing-attribute-mappings.md)中禁用此功能。

**可以如何跟踪当前预配作业的进度？**

请参阅[预配报告指南](active-directory-saas-provisioning-reporting.md)。

**我如何知道用户无法正确预配？**

所有错误记录在 Azure AD 审核日志中。 有关详细信息，请参阅[预配报告指南](active-directory-saas-provisioning-reporting.md)。

**如何向工程团队提交反馈？**

请通过 [Azure Active Directory 反馈论坛](https://feedback.azure.com/forums/169401-azure-active-directory/)与我们联系。


## <a name="related-articles"></a>相关文章
* [有关 Azure Active Directory 中应用程序管理的文章索引](active-directory-apps-index.md)
* [为用户预配自定义属性映射](active-directory-saas-customizing-attribute-mappings.md)
* [为属性映射编写表达式](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [用于用户预配的作用域筛选器](active-directory-saas-scoping-filters.md)
* [使用 SCIM 启用从 Azure Active Directory 到应用程序的用户和组自动预配](active-directory-scim-provisioning.md)
* [帐户预配通知](active-directory-saas-account-provisioning-notifications.md)
* [有关如何集成 SaaS 应用的教程列表](active-directory-saas-tutorial-list.md)

