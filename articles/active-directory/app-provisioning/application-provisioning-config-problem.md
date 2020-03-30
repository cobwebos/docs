---
title: 配置用户预配到 Azure AD 库应用时遇到问题
description: 当为在 Azure AD 应用程序库中列出的某个应用程序配置用户预配时，如何解决面对的常见问题
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
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3955b96e4a7edfc79a229d927523bdd4473409d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522758"
---
# <a name="problem-configuring-user-provisioning-to-an-azure-ad-gallery-application"></a>为 Azure AD 库应用程序配置用户预配时遇到的问题

为应用配置[自动化用户预配](user-provisioning.md)时（在受支持的情况下），需要按照特定说明操作，从而为要自动化预配的应用程序做好准备。 然后可使用 Azure 门户配置预配服务，将用户帐户同步到应用程序。

应始终先查找特定于设置应用程序预配的设置教程。 然后根据这些步骤操作，配置应用和 Azure AD 以创建预配连接。 可以在[有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](../saas-apps/tutorial-list.md)找到相应的应用教程列表。

## <a name="how-to-see-if-provisioning-is-working"></a>如何查看预配是否有效 

配置服务后，可通过两个地方了解服务操作的大多数情况：

-   **预配日志（预览）** -[预配日志](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)记录预配服务执行的所有操作，包括查询 Azure AD 以指定范围内的预配用户。 查询目标应用以验证是否存在这些用户以及比较系统之间的用户对象。 然后根据比较结果在目标系统中添加、更新或禁用用户帐户。 通过在 **"活动"** 部分中选择**Azure 活动目录**&gt;**企业应用**&gt;**预配日志（预览），** 可以在 Azure 门户中访问预配日志。

-   **当前状态 |** 在服务设置下屏幕底部的**Azure 活动&gt;目录企业应用&gt;\[\]&gt;应用程序名称预配**部分中可以看到给定应用的最后一次预配运行摘要。 "当前状态"部分显示预配周期是否已开始预配用户帐户。 您可以查看周期的进度，查看已预配的用户和组数，并查看创建了多少个角色。 如果有任何错误，可以在 [预配日志 （.. ） 中找到详细信息。/报告监视/概念-预配-logs.md？上下文_azure/主动目录/管理应用/上下文/管理应用-上下文）。

## <a name="general-problem-areas-with-provisioning-to-consider"></a>要考虑的有关预配的常规问题范围

以下为常规问题范围列表，如果知道从何处着手，可做一番深入了解。

* [预配服务似乎未启动](#provisioning-service-does-not-appear-to-start)
* 因应用凭据不工作而无法保存配置
* [预配日志表示用户被"跳过"，未预配，即使已分配用户](#provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>预配服务似乎未启动

如果在 Azure 门户的“Azure Active Directory”&gt;“企业应用”“应用程序名称”\] &gt;“预配”部分中将“预配状态”**** 设置为“打开”******&gt; \[**。 但是在后续重新加载后该页面未显示其他状态详细信息。 服务可能正在运行，但尚未完成初始周期。 检查上面描述的**预配日志**，以确定服务正在执行哪些操作，以及是否有任何错误。

>[!NOTE]
>初始周期可能需要 20 分钟到几个小时，具体取决于 Azure AD 目录的大小和预配范围内的用户数。 初始周期之后的后续同步速度更快，因为预配服务存储的水印，表示初始周期后两个系统的状态，从而提高后续同步的性能。
>
>

## <a name="cant-save-configuration-due-to-app-credentials-not-working"></a>因应用凭据不工作而无法保存配置

为使预配工作，Azure AD 需要有效凭据才可连接至该应用提供的用户管理 API。 如果这些凭据不起作用，或不知道它们是什么，请查看前面所述的此应用的设置教程。

## <a name="provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>预配日志表示，即使用户已分配，也不会预配用户

当用户在预配日志中显示为"跳过"时，请务必阅读日志消息中的扩展详细信息以确定原因。 以下为常见的原因和解决方法：

- **已配置作用域筛选器，** **它根据属性值筛选用户**。 有关详细信息，请参阅[使用范围筛选器的基于属性的应用程序预配](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。

- **该用户“未得到有效授权”。** 如果看到此特定错误消息，是因为存储在 Azure AD 中的用户分配记录出现问题。 要解决此问题，请从应用中取消分配用户（或组），并重新分配。 有关详细信息，请参阅[将用户或组分配给企业应用](../manage-apps/assign-user-or-group-access-portal.md)。

- **所需的属性丢失或未替用户填写。** 在设置预配时需考虑的重要一点是查看并配置属性映射和工作流，它们可以确定哪些用户（或组）属性将从 Azure AD 流向应用程序。 这包括设置用于唯一标识和匹配两个系统之间用户/组的“匹配属性”。 有关此重要过程的详细信息，请参阅[自定义用户预配属性映射](../app-provisioning/customize-application-attributes.md)。

  * **适用于组的属性映射：** 除预配成员的名称和详细信息外，还预配组名称和组的详细信息（前提是某些应用程序支持这样做）。 您可以通过启用或禁用**预配**选项卡中显示的组对象的**映射**来启用或禁用此功能。如果启用了预配组，请确保查看属性映射，以确保为"匹配 ID"使用适当的字段。 这可以是显示名称或电子邮件别名，因为在匹配属性为空，或没有为 Azure AD 中的某个组填写时，组及组成员将无法进行预配。

## <a name="next-steps"></a>后续步骤
[Azure Active Directory SaaS 应用程序的自动化用户设置和取消设置](user-provisioning.md)
