---
title: 配置 Azure AD 库应用的用户预配时出现问题
description: 当为在 Azure AD 应用程序库中列出的某个应用程序配置用户预配时，如何解决面对的常见问题
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: mimart
ms.reviewer: asteen
ms.openlocfilehash: e6fb5fbecc9b2917f4fde2d1ccb76d6962a0af18
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82593958"
---
# <a name="problem-configuring-user-provisioning-to-an-azure-ad-gallery-application"></a>为 Azure AD 库应用程序配置用户预配时遇到的问题

为应用配置[自动化用户预配](user-provisioning.md)时（在受支持的情况下），需要按照特定说明操作，从而为要自动化预配的应用程序做好准备。 然后可使用 Azure 门户配置预配服务，将用户帐户同步到应用程序。

应始终先查找特定于设置应用程序预配的设置教程。 然后根据这些步骤操作，配置应用和 Azure AD 以创建预配连接。 可以在[有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](../saas-apps/tutorial-list.md)找到相应的应用教程列表。

## <a name="how-to-see-if-provisioning-is-working"></a>如何查看预配是否有效 

配置服务后，可通过两个地方了解服务操作的大多数情况：

-   **预配日志（预览版）** –[预配日志](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)记录预配服务执行的所有操作，包括查询在预配范围内分配的用户 Azure AD。 查询目标应用以验证是否存在这些用户以及比较系统之间的用户对象。 然后根据比较结果在目标系统中添加、更新或禁用用户帐户。 可以通过在 "**活动**" 部分中选择 " **Azure Active Directory** &gt; **企业应用** &gt; **预配日志（预览版）** " 来访问 Azure 门户中的设置日志。

-   **当前状态–** 可在屏幕底部的 "服务设置" 下的 " ** &gt; Azure Active Directory Enterprise &gt; \[Apps 应用程序名称\] &gt;预配**" 部分中查看给定应用的最后一次设置运行的摘要。 "当前状态" 部分显示预配周期是否已开始预配用户帐户。 您可以查看周期的进度，查看已设置的用户和组的数量，并查看已创建的角色数。 如果有任何错误，则可在 [设置日志（.。/reports-monitoring/concept-provisioning-logs.md？ context = azure/active directory/管理-应用/上下文/管理-应用-上下文）。

## <a name="general-problem-areas-with-provisioning-to-consider"></a>要考虑的有关预配的常规问题范围

以下为常规问题范围列表，如果知道从何处着手，可做一番深入了解。

* [预配服务似乎未启动](#provisioning-service-does-not-appear-to-start)
* 因应用凭据不工作而无法保存配置
* [预配日志指出用户已被 "跳过" 且未预配，即使它们已被分配](#provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>预配服务似乎未启动

如果在 Azure 门户的“Azure Active Directory”&gt;“企业应用”“应用程序名称”\] &gt;“预配”部分中将“预配状态”**** 设置为“打开”******&gt; \[**。 但是在后续重新加载后该页面未显示其他状态详细信息。 可能是服务正在运行，但尚未完成初始循环。 检查上述**预配日志**，确定服务正在执行哪些操作，以及是否有任何错误。

>[!NOTE]
>初始周期可能需要20分钟到几小时的时间，具体取决于 Azure AD 目录的大小和预配范围内的用户数量。 初始循环后的后续同步速度更快，因为预配服务在初始周期后存储表示这两个系统状态的水印，从而提高后续同步的性能。
>
>

## <a name="cant-save-configuration-due-to-app-credentials-not-working"></a>因应用凭据不工作而无法保存配置

为使预配工作，Azure AD 需要有效凭据才可连接至该应用提供的用户管理 API。 如果这些凭据不起作用，或不知道它们是什么，请查看前面所述的此应用的设置教程。

## <a name="provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>预配日志指出用户被跳过且未预配，即使它们已被分配

当用户在预配日志中显示为 "已跳过" 时，请务必阅读日志消息中的扩展详细信息以确定原因。 以下为常见的原因和解决方法：

- **已配置作用域筛选器，** **它根据属性值筛选用户**。 有关详细信息，请参阅[通过范围筛选器进行基于属性的应用程序设置](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。

- **该用户“未得到有效授权”。** 如果看到此特定错误消息，是因为存储在 Azure AD 中的用户分配记录出现问题。 要解决此问题，请从应用中取消分配用户（或组），并重新分配。 有关详细信息，请参阅向[企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)。

- **所需的属性丢失或未替用户填写。** 在设置预配时需考虑的重要一点是查看并配置属性映射和工作流，它们可以确定哪些用户（或组）属性将从 Azure AD 流向应用程序。 这包括设置用于唯一标识和匹配两个系统之间用户/组的“匹配属性”。 有关此重要过程的详细信息，请参阅[自定义用户预配属性映射](../app-provisioning/customize-application-attributes.md)。

  * **适用于组的属性映射：** 除预配成员的名称和详细信息外，还预配组名称和组的详细信息（前提是某些应用程序支持这样做）。 可以启用或禁用 "**预配**" 选项卡中显示的组对象**映射**来启用或禁用此功能。如果启用了预配组，请务必查看属性映射以确保 "匹配 ID" 使用合适的字段。 这可以是显示名称或电子邮件别名，因为在匹配属性为空，或没有为 Azure AD 中的某个组填写时，组及组成员将无法进行预配。

## <a name="next-steps"></a>后续步骤
[Azure Active Directory SaaS 应用程序的自动化用户设置和取消设置](user-provisioning.md)
