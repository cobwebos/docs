---
title: 没有为任何用户预配 Azure AD 库应用程序
description: 当在针对用户预配使用 Azure AD 配置的 Azure AD 库应用程序中没有看到用户时，如何解决面对的常见问题
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b60261d63e1bcb75aea9d2e8a6b74902520f391
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522911"
---
# <a name="no-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>没有为任何用户预配 Azure AD 库应用程序
为应用程序配置自动化预配后（包括验证提供给 Azure AD 以连接至该应用的应用凭据是否有效），用户和/或组将预配到应用。 预配取决于以下事情：

-   哪些用户和组已**分配**到应用程序。 请注意，不支持预配嵌套组或 Office 365 组。 有关分配的详细信息，请参阅[在 Azure Active Directory 中向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)。
-   **属性映射**是否已启用，并且是否经过配置，可将有效属性从 Azure AD 同步至应用。 有关属性映射的详细信息，请参阅[为 Azure Active Directory 中的 SaaS 应用程序自定义用户预配属性映射](customize-application-attributes.md)。
-   是否存在根据特定属性值筛选用户的**作用域筛选器**。 有关作用域筛选器的详细信息，请参阅[通过作用域筛选器基于属性预配应用程序](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。
  
如果观察到用户未预配，请参阅 Azure AD 中的[预配日志（预览）。](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) 在日志条目中搜索特定用户。

通过在 **"活动"** 部分中选择**Azure 活动目录**&gt;**企业应用**&gt;**预配日志（预览），** 可以在 Azure 门户中访问预配日志。 您可以根据用户名称或源系统或目标系统中的标识符搜索预配数据。 有关详细信息，请参阅[预配日志（预览）。](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) 

预配日志记录预配服务执行的所有操作，包括为预配范围内的分配用户查询 Azure AD、查询目标应用以了解这些用户的存在、比较用户对象。系统。 然后根据比较结果在目标系统中添加、更新或禁用用户帐户。

## <a name="general-problem-areas-with-provisioning-to-consider"></a>要考虑的有关预配的常规问题范围
以下为常规问题范围列表，如果知道从何处着手，可做一番深入了解。

- [预配服务似乎未启动](#provisioning-service-does-not-appear-to-start)
- [预配日志表示，用户被跳过，并且未预配，即使已分配用户](#provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>预配服务似乎未启动
如果在 Azure 门户的“Azure Active Directory”&gt;“企业应用”“应用程序名称”\] &gt;“预配”部分中将“预配状态”**** 设置为“打开”******&gt; \[**。 但是，在后续重新加载后，该页上不会显示其他状态详细信息，则服务可能正在运行，但尚未完成初始周期。 检查上面描述的**预配日志（预览），** 以确定服务正在执行哪些操作，以及是否有任何错误。

>[!NOTE]
>初始周期可能需要 20 分钟到几个小时，具体取决于 Azure AD 目录的大小和预配范围内的用户数。 初始周期之后的后续同步速度更快，因为预配服务存储的水印，表示初始周期后两个系统的状态。 初始周期可提高后续同步的性能。
>


## <a name="provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>预配日志表示，即使用户已分配，也不会预配用户

当用户在预配日志中显示为"跳过"时，请务必查看日志的 **"步骤**"选项卡以确定原因。 以下为常见的原因和解决方法：

- **已配置作用域筛选器，** **它根据属性值筛选用户**。 有关范围筛选器的详细信息，请参阅[范围筛选器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。
- **该用户“未得到有效授权”。** 如果看到此特定错误消息，是因为存储在 Azure AD 中的用户分配记录出现问题。 要解决此问题，请从应用中取消分配用户（或组），并重新分配。 有关分配的详细信息，请参阅[分配用户或组访问权限](../manage-apps/assign-user-or-group-access-portal.md)。
- **所需的属性丢失或未替用户填写。** 在设置预配时需考虑的重要一点是查看并配置属性映射和工作流，它们可以确定哪些用户（或组）属性将从 Azure AD 流向应用程序。 此配置包括设置用于在两个系统之间唯一地标识和匹配用户/组的“匹配属性”。 有关此重要过程的详细信息，请参阅[为 Azure Active Directory 中的 SaaS 应用程序自定义用户预配属性映射](customize-application-attributes.md)。
- **适用于组的属性映射：** 除预配成员的名称和详细信息外，还预配组名称和组的详细信息（前提是某些应用程序支持这样做）。 您可以通过启用或禁用**预配**选项卡中显示的组对象的**映射**来启用或禁用此功能。如果启用了预配组，请确保查看属性映射，以确保为"匹配 ID"使用适当的字段。 此匹配 ID 可以是显示名称或电子邮件别名。 如果匹配属性为空，或没有为 Azure AD 中的某个组填写时，该组及组成员将不会进行预配。

## <a name="next-steps"></a>后续步骤

[Azure AD Connect 同步：了解声明性预配](../hybrid/concept-azure-ad-connect-sync-declarative-provisioning.md)
