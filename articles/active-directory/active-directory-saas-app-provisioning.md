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
ms.date: 02/09/2016
ms.author: asmalser
translationtype: Human Translation
ms.sourcegitcommit: 6b77e338e1c7f0f79ea3c25b0b073296f7de0dcf
ms.openlocfilehash: 1bee9573e23e1c814626d19a3df1eb7ab12486ab


---
# <a name="automate-user-provisioning-and-deprovisioning-to-saas-applications-with-azure-active-directory"></a>Azure Active Directory SaaS 应用程序的自动化用户设置和取消设置
## <a name="what-is-automated-user-provisioning-for-saas-apps"></a>什么是 SaaS 应用的自动化用户设置？
Azure Active Directory (Azure AD) 允许用户自动创建、维护和删除云 ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) 应用程序（如 Dropbox、Salesforce、ServiceNow 等）中的用户标识。

**以下是此功能操作的一些示例：**

* 在正确的 SaaS 应用中为新人（加入团队时）自动创建新帐户。
* 当人员不可避免离开团队时，从 SaaS 应用中自动停用其帐户。
* 确保你的 SaaS 应用中的标识基于目录中的更改保持最新。
* 设置非用户对象，例如组以及支持它们的 SaaS 应用。

**自动化用户预配还包括以下功能：**

* 匹配 Azure AD 和 SaaS 应用之间现有标识的能力。
* 用于帮助 Azure AD 适应组织当前使用的 SaaS 应用配置的自定义选项。
* 可选电子邮件警报，用于设置错误。
* 报表和活动日志，用于帮助进行监视和故障排除。

## <a name="why-use-automated-provisioning"></a>为什么要使用自动化设置？
使用此功能的一些常见动机包括：

* 避免与手动设置过程相关的成本、低效和人为错误。
* 通过在用户离开组织时立即删除其在关键 SaaS 应用中的标识，从而保护你的组织。
* 轻松将大量用户导入特定的 SaaS 应用程序。
* 体验设置解决方案运行为 Azure AD 单一登录定义的相同应用访问策略的便利。

## <a name="frequently-asked-questions"></a>常见问题
**Azure AD 将目录更改写入到 SaaS 应用的频率是多少？**

Azure AD 每五到十分钟进行一次更改检查。 如果 SaaS 应用返回几个错误（例如无效的管理员凭据），则 Azure AD 将逐渐降低频率到每天一次，直到完成错误修复。

**预配我的用户需要多长时间？**

增量更改将立即发生，但如果你尝试设置目录的大部分，则时长取决于所拥有的用户和组数量。 较小目录只需几分钟、中等规模目录可能需要数分钟，而非常大的目录可能需要几小时。

**可以如何跟踪当前预配作业的进度？**

可以查看目录中“报表”部分下的“帐户设置报表”。 另一种方法是，访问正在设置的 SaaS 应用程序的仪表板选项卡，并查找页面底部附近的“集成状态”部分。

**我如何知道用户无法正确预配？**

在设置配置向导结束时，存在订阅电子邮件通知获取设置失败的选项。 你可以查看“设置错误报表”以了解设置失败的用户以及原因。

**Azure AD 是否可以将更改从 SaaS 应用重新写回目录？**

对于多数 SaaS 应用而言，设置仅支持出站处理，这意味着用户可以从目录写入到应用程序，而无法从应用程序将更改写回目录。 然而，对于 [Workday](https://msdn.microsoft.com/library/azure/dn762434.aspx) 而言，预配仅支持入站处理，这意味着用户可从 Workday 导入到目录，同样，目录中的更改执行操作无法写回到 Workday。

**如何向工程团队提交反馈？**

请通过 [Azure Active Directory 反馈论坛](https://feedback.azure.com/forums/169401-azure-active-directory/)与我们联系。

## <a name="how-does-automated-provisioning-work"></a>自动化设置是如何工作的？
Azure AD 通过连接到由每个应用程序供应商所提供的设置终结点设置 SaaS 应用的用户。 这些终结点允许 Azure AD 以编程方式创建、更新和删除用户。 下面是 Azure AD 自动化设置执行的不同步骤的简要概述。

1. 首次为应用程序启用设置时，将执行以下操作：
   * Azure AD 会尝试将 SaaS 应用中的任何现有用户与其在目录中相应的标识进行匹配。 匹配用户时，*不*自动为其启用单一登录。 为了使用户具有访问该应用程序的权限，必须将其显式分配到 Azure AD 中的应用，可直接分配或通过组成员身份进行分配。
   * 如果已指定应分配到应用程序的用户，并且如果 Azure AD 无法找到这些用户的现有帐户，则 Azure AD 将为它们在应用程序中配置新帐户。
2. 如上所述完成初始同步操作后，Azure AD 将每隔 10 分钟检查一次以下更改：
   * 如果已将新用户分配到应用程序（直接或通过组成员身份），则将在 SaaS 应用中为其设置新帐户。
   * 如果已删除用户的访问权限，则其在 SaaS 应用中的帐户将标记为禁用（绝不会完全删除用户，这有助于在发生配置错误时避免数据丢失）。
   * 如果用户是最近分配到应用程序并且已在 SaaS 应用中拥有一个帐户，则该帐户将标记为启用，并且如果它们与目录对比已过期，则可能会更新某些用户的属性。
   * 如果已在目录中更改用户的信息（如电话号码、办公地点，等等），则 SaaS 应用程序中也将更新该信息。

有关特性如何在 Azure AD 和 SaaS 应用之间映射的更多详细信息，请参阅[自定义特性映射](active-directory-saas-customizing-attribute-mappings.md)一文。

## <a name="list-of-apps-that-support-automated-user-provisioning"></a>支持自动化用户设置的应用列表
单击应用以查看如何为其配置自动化设置的教程：

* [Box](http://go.microsoft.com/fwlink/?LinkId=286016)
* [Citrix GoToMeeting](http://go.microsoft.com/fwlink/?LinkId=309580)
* [Concur](http://go.microsoft.com/fwlink/?LinkId=309575)
* [Docusign](http://go.microsoft.com/fwlink/?LinkId=403254)
* [Dropbox for Business](http://go.microsoft.com/fwlink/?LinkId=309581)
* [Google Apps](http://go.microsoft.com/fwlink/?LinkId=309577)
* [Jive](http://go.microsoft.com/fwlink/?LinkId=309591)
* [Salesforce](http://go.microsoft.com/fwlink/?LinkId=286017)
* [Salesforce Sandbox](http://go.microsoft.com/fwlink/?LinkId=327869)
* [ServiceNow](http://go.microsoft.com/fwlink/?LinkId=309587)
* [Workday](http://go.microsoft.com/fwlink/?LinkId=690250)（入站预配）

为了使应用程序支持自动化用户设置，它必须首先提供必要的、允许外部程序自动执行创建、维护和删除用户操作的终结点。 因此，不是所有的 SaaS 应用都能兼容此功能。 对于不支持此功能的应用，Azure AD 工程团队将能够构建连接到这些应用的设置连接器，并按当前和潜在客户需求设置优先级。

如需联系 Azure AD 工程团队以请求其他应用程序的预配支持，请通过 [Azure Active Directory 反馈论坛](https://feedback.azure.com/forums/169401-azure-active-directory/)提交消息。

## <a name="related-articles"></a>相关文章
* [有关 Azure Active Directory 中应用程序管理的文章索引](active-directory-apps-index.md)
* [为用户预配自定义属性映射](active-directory-saas-customizing-attribute-mappings.md)
* [为属性映射编写表达式](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [用于用户预配的作用域筛选器](active-directory-saas-scoping-filters.md)
* [使用 SCIM 启用从 Azure Active Directory 到应用程序的用户和组自动预配](active-directory-scim-provisioning.md)
* [帐户预配通知](active-directory-saas-account-provisioning-notifications.md)
* [有关如何集成 SaaS 应用的教程列表](active-directory-saas-tutorial-list.md)




<!--HONumber=Dec16_HO5-->


