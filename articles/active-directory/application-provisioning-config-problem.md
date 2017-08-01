---
title: "为 Azure AD 库应用程序配置用户预配时遇到的问题 | Microsoft Docs"
description: "当为在 Azure AD 应用程序库中列出的某个应用程序配置用户预配时，如何解决面对的常见问题"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: e15784a662b1691774eb3d9cc8b3fbe66ac67385
ms.contentlocale: zh-cn
ms.lasthandoff: 04/18/2017

---

# <a name="problem-configuring-user-provisioning-to-an-azure-ad-gallery-application"></a>为 Azure AD 库应用程序配置用户预配时遇到的问题

为应用配置[自动化用户预配](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning)时（在受支持的情况下），需要按照特定说明操作，从而为要自动化预配的应用程序做好准备。 然后可使用 Azure 门户配置预配服务，将用户帐户同步到应用程序。

应始终先查找特定于设置应用程序预配的设置教程。 然后根据这些步骤操作，配置应用和 Azure AD 以创建预配连接。 可以在[有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)找到相应的应用教程列表。

## <a name="how-to-see-if-provisioning-is-working"></a>如何查看预配是否有效 

配置服务后，可通过两个地方了解服务操作的大多数情况：

-   **审核日志** - 预配审核日志记录由预配服务执行的全部操作，包括查询 Azure AD 以找到预配作用域中分配的用户。 查询目标应用以验证是否存在这些用户以及比较系统之间的用户对象。 然后根据比较结果在目标系统中添加、更新或禁用用户帐户。 可在 Azure 门户中访问预配审核日志，具体位置在**“Azure Active Directory”&gt;“企业应用”&gt;“应用程序名称”\[\]“审核日志”&gt;**选项卡。 在“帐户预配”类别上筛选日志，以仅查看该应用的预配事件。

-   **预配状态** - 在“服务设置”屏幕底部的**“Azure Active Directory”&gt;“企业应用”&gt;“应用程序名称”\[\]“预配”&gt;**部分中，可以看到为给定应用最后一次运行的预配的摘要。 此部分总结有多少用户（和/或组）在两个系统之间同步，以及是否出现任何错误。 错误的详细信息显示在审核日志中。 请注意，预配状态保持未填充状态，直至在 Azure AD 和应用之间完成一个完整的初始同步。

## <a name="general-problem-areas-with-provisioning-to-consider"></a>要考虑的有关预配的常规问题范围

以下为常规问题范围列表，如果知道从何处着手，可做一番深入了解。

* [预配服务似乎未启动](#provisioning-service-does-not-appear-to-start)
* [因应用凭据无效而无法保存配置](#can’t-save-configuration-due-to-app-credentials-not-working)
* [即使用户已分配，审核日志还是显示他们已被“跳过”且未预配](#audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>预配服务似乎未启动

如果在 Azure 门户的“Azure Active Directory”**&gt;“企业应用”&gt;“应用程序名称”\[\]“预配”&gt;**部分中将“预配状态”设置为“打开”。 但是在后续重新加载后该页面未显示其他状态详细信息。 有可能是因为该服务正在运行，但尚未完成初始同步。 检查上述**审核日志**以确定服务正在执行哪些操作，以及是否出现任何错误。

>[!NOTE]
>初始同步可能会持续 20 分钟至几小时，具体取决于 Azure AD 目录的大小与作用域中的预配用户数量。 在初始同步之后同步速度会变快，因为在初始同步后，预配服务已存储代表两个系统状态的水印，这提高了后续同步的性能。
>
>

## <a name="cant-save-configuration-due-to-app-credentials-not-working"></a>因应用凭据不工作而无法保存配置

为使预配工作，Azure AD 需要有效凭据才可连接至该应用提供的用户管理 API。 如果这些凭据不起作用，或不知道它们是什么，请查看上述此应用的设置教程。

## <a name="audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>即使用户已分配，审核日志还是显示他们已被跳过且未预配

当用户在审核日志中显示为“已跳过”时，请务必阅读日志消息中的延伸详细信息，以确定原因。 以下为常见的原因和解决方法：

-   **已配置作用域筛选器**，**它根据属性值筛选用户**。 有关作用域筛选器的详细信息，请参阅 <https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters>。

-   **该用户“未得到有效授权”。** 如果看到此特定错误消息，是因为存储在 Azure AD 中的用户分配记录出现问题。 若要解决此问题，请从应用中取消分配用户（或组），然后重新分配。 有关分配的详细信息，请参阅 <https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal>。

-   **所需的属性丢失或未替用户填写。** 在设置预配时需考虑的重要一点是查看并配置属性映射和工作流，它们可以确定哪些用户（或组）属性将从 Azure AD 流向应用程序。 这包括设置用于唯一标识和匹配两个系统之间用户/组的“匹配属性”。 有关此重要过程的详细信息，请访问 <https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings>。

   * **适用于组的属性映射：**除预配成员的名称和详细信息外，还预配组名称和组的详细信息（前提是某些应用程序支持这样做）。 通过启用或禁用在“预配”选项卡中显示的组对象的“映射”，可启用或禁用此功能。 如果启用预配组，请务必查看属性映射以确保相应字段用于“匹配 ID”。 这可以是显示名称或电子邮件别名，因为在匹配属性为空，或没有为 Azure AD 中的某个组填写时，组及组成员将无法进行预配。

#<a name="next-steps"></a>后续步骤
[Azure Active Directory SaaS 应用程序的自动化用户预配和取消预配](active-directory-saas-app-provisioning.md)

