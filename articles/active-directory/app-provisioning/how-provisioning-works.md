---
title: 了解 Azure AD 预配的工作原理 ›微软文档
description: 了解 Azure AD 预配的工作原理
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 241d90981ed9ba54d253e6c22c00f9e5a9197863
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80884879"
---
# <a name="how-provisioning-works"></a>预配工作原理

自动预配是指在用户需要访问的云应用程序中创建用户身份和角色。 除了创建用户标识外，自动预配还包括在状态或角色发生更改时维护和删除用户标识。 在开始部署之前，可以查看本文以了解 Azure AD 预配的工作原理并获取配置建议。 

**Azure AD 预配服务**通过连接到应用程序供应商提供的跨域标识管理系统 （SCIM） 2.0 用户管理 API 终结点，将用户预配到 SaaS 应用和其他系统。 此 SCIM 终结点允许 Azure AD 以编程方式创建、更新和删除用户。 对于选定的应用程序，预配服务还可以创建、更新和删除其他与标识相关的对象，如组和角色。 用于在 Azure AD 和应用程序之间预配的通道使用 HTTPS TLS 加密进行加密。


![Azure AD 预配](./media/how-provisioning-works/provisioning0.PNG)
服务*图 1：Azure AD 预配服务*

![出站用户预配工作流](./media/how-provisioning-works/provisioning1.PNG)
*图 2："出站"用户预配工作流从 Azure AD 到流行的 SaaS 应用程序*

![入站用户预配工作流](./media/how-provisioning-works/provisioning2.PNG)
*图 3："入站"用户预配工作流，从流行的人力资本管理 （HCM） 应用程序到 Azure 活动目录和 Windows 服务器活动目录*

## <a name="provisioning-using-scim-20"></a>使用 SCIM 2.0 进行预配

Azure AD 预配服务使用[SCIM 2.0 协议](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/bg-p/IdentityStandards)进行自动预配。 该服务连接到应用程序的 SCIM 终结点，并使用 SCIM 用户对象架构和 REST API 自动预配和取消预配用户和组。 Azure AD 库中的大多数应用程序都提供了基于 SCIM 的预配连接器。 为 Azure AD 构建应用时，开发人员可以使用 SCIM 2.0 用户管理 API 构建集成 Azure AD 进行预配的 SCIM 终结点。 有关详细信息，请参阅[生成 SCIM 终结点并配置用户预配](../app-provisioning/use-scim-to-provision-users-and-groups.md)。

要为当前没有应用的应用请求自动 Azure AD 预配连接器，请填写[Azure 活动目录应用程序请求](https://aka.ms/aadapprequest)。

## <a name="authorization"></a>授权

Azure AD 需要凭据才能连接到应用程序的用户管理 API。 在为应用程序配置自动用户预配时，需要输入有效的凭据。 通过引用应用教程，可以找到应用程序的凭据类型和要求。 在 Azure 门户中，您可以通过让 Azure AD 尝试使用提供的凭据连接到应用的预配应用来测试凭据。

如果为应用程序也配置了基于 SAML 的单登录，则 Azure AD 的内部每个应用程序存储限制为 1024 字节。 此限制包括与应用程序的单个实例（也称为 Azure AD 中的服务主体记录）关联的所有证书、密钥令牌、凭据和相关配置数据。 配置基于 SAML 的单登录时，用于对 SAML 令牌进行签名的证书通常会占用超过 50% 的空间。 您在用户预配设置期间输入的任何其他项目（秘密令牌、URI、通知电子邮件地址、用户名和密码）都可能超过存储限制。 有关详细信息，请参阅[在配置用户预配时保存管理员凭据时出现问题](../manage-apps/application-provisioning-config-problem-storage-limit.md)。

## <a name="mapping-attributes"></a>映射属性

为第三方 SaaS 应用程序启用用户预配时，Azure 门户将通过属性映射控制其属性值。 映射确定在预配或更新用户帐户时在 Azure AD 和目标应用程序之间流动的用户属性。

Azure AD 用户对象和每个 SaaS 应用的用户对象之间有一组预先配置的属性和属性映射。 某些应用与"用户"一起管理其他类型的对象，如"组"。

设置预配时，请务必查看和配置属性映射和工作流，这些映射和工作流定义哪些用户（或组）属性从 Azure AD 流向应用程序。 查看并配置匹配属性 （**使用此属性匹配对象**），该属性用于在两个系统之间唯一标识和匹配用户/组。

可以根据业务需求自定义默认的属性映射。 因此，您可以更改或删除现有的属性映射，或创建新的属性映射。 有关详细信息，请参阅[为 SaaS 应用程序自定义用户预配属性映射](../manage-apps/customize-application-attributes.md)。

将预配配置到 SaaS 应用程序时，表达式映射是可指定的属性映射类型之一。 对于这些映射，必须编写类似于脚本的表达式，该表达式允许您将用户的数据转换为 SaaS 应用程序更可接受的格式。 有关详细信息，请参阅[为属性映射编写表达式](functions-for-customizing-application-data.md)。

## <a name="scoping"></a>Scoping 
### <a name="assignment-based-scoping"></a>基于分配的范围

对于从 Azure AD 到 SaaS 应用程序的出站预配，依赖[用户或组分配](../manage-apps/assign-user-or-group-access-portal.md)是确定哪些用户处于预配范围内的最常见方法。 由于用户分配也用于启用单一登录，因此相同的方法可用于管理访问和预配。 基于分配的范围范围不适用于入站预配方案，如工作日和成功因素。

* **组。** 使用 Azure AD 高级许可证计划，可以使用组分配对 SaaS 应用程序的访问权限。 然后，当预配范围设置为**仅同步分配的用户和组**时，Azure AD 预配服务将根据用户是否为分配给应用程序的组的成员预配或取消预配。 除非应用程序支持组对象，否则不会预配组对象本身。 确保分配给应用程序的组将属性"安全启用"设置为"False"。

* **动态组。** Azure AD 用户预配服务可以在[动态组中](../users-groups-roles/groups-create-rule.md)读取和预配用户。 请记住以下注意事项和建议：

  * 动态组可能会影响从 Azure AD 到 SaaS 应用程序的端到端预配的性能。

  * 动态组中的用户在 SaaS 应用程序中的预配或取消预配的速度取决于动态组评估成员身份更改的速度。 有关如何检查动态组的处理状态的信息，请参阅[检查成员资格规则的处理状态](../users-groups-roles/groups-create-rule.md)。

  * 当用户失去动态组中的成员身份时，它被视为取消预配事件。 在为动态组创建规则时，请考虑此方案。

* **嵌套组。** Azure AD 用户预配服务无法读取或预配嵌套组中的用户。 该服务只能读取和预配显式分配的组的直接成员的用户。 "基于组的应用程序分配"的这种限制还会影响单一登录（请参阅[使用组来管理对 SaaS 应用程序的访问](../users-groups-roles/groups-saasapps.md)）。 相反，直接在包含需要预配的用户的组中分配或以其他方式分配[作用域](define-conditional-rules-for-provisioning-user-accounts.md)。

### <a name="attribute-based-scoping"></a>基于属性的范围 

可以使用范围筛选器定义基于属性的规则，这些规则确定哪些用户预配到应用程序。 此方法通常用于从 HCM 应用程序到 Azure AD 和活动目录的入站预配。 在每个 Azure AD 用户预配连接器的属性映射过程中，配置范围筛选器。 有关配置基于属性的范围筛选器的详细信息，请参阅[使用范围筛选器的基于属性的应用程序预配](define-conditional-rules-for-provisioning-user-accounts.md)。

### <a name="b2b-guest-users"></a>B2B（来宾）用户

可以使用 Azure AD 用户预配服务将 Azure AD 中的 B2B（或来宾）用户预配到 SaaS 应用程序。 但是，对于 B2B 用户使用 Azure AD 登录到 SaaS 应用程序，SaaS 应用程序必须以特定方式配置其基于 SAML 的单登录功能。 有关如何配置 SaaS 应用程序以支持 B2B 用户的登录的详细信息，请参阅[为 B2B 协作配置 SaaS 应用](../b2b/configure-saas-apps.md)。

请注意，来宾用户的用户主要名称通常存储为"别名_EXT"。"。@domain.com 当用户主体名称作为源属性包含在属性映射中时，#EXT* 将从用户主要名称中剥离。 如果需要存在#EXT*，请将用户主体名称替换为原始用户主体名称作为源属性。 

## <a name="provisioning-cycles-initial-and-incremental"></a>预配周期：初始和增量

当 Azure AD 是源系统时，预配服务使用[使用增量查询来跟踪 Microsoft 图形数据中的更改](https://docs.microsoft.com/graph/delta-query-overview)来监视用户和组。 预配服务针对源系统和目标系统运行初始周期，然后是周期性增量周期。

### <a name="initial-cycle"></a>初始周期

启动预配服务时，第一个循环将：

1. 查询源系统中的所有用户和组，并检索[属性映射](customize-application-attributes.md)中定义的所有属性。

2. 使用配置的任何[分配](../manage-apps/assign-user-or-group-access-portal.md)或[基于属性的范围筛选器](define-conditional-rules-for-provisioning-user-accounts.md)筛选返回的用户和组。

3. 分配用户或处于预配范围时，服务将使用指定的[匹配属性](customize-application-attributes.md#understanding-attribute-mapping-properties)查询匹配用户的目标系统。 示例：如果源系统中的 userPrincipal 名称是匹配的属性并映射到目标系统中的 userName，则预配服务会在目标系统中查询与源系统中 userPrincipal 名称值匹配的 userName。

4. 如果在目标系统中找不到匹配的用户，则使用从源系统返回的属性创建匹配用户。 创建用户帐户后，预配服务将检测并缓存新用户的目标系统的 ID。 此 ID 用于运行该用户的所有将来操作。

5. 如果找到匹配的用户，则使用源系统提供的属性进行更新。 匹配用户帐户后，预配服务将检测并缓存新用户的目标系统的 ID。 此 ID 用于运行该用户的所有将来操作。

6. 如果属性映射包含"引用"属性，则服务在目标系统上执行其他更新以创建和链接引用的对象。 例如，用户可能在目标系统中有“Manager”属性，该属性链接到目标系统中创建的另一个用户。

7. 在初始周期结束时保留水印，为以后的增量周期提供起点。

某些应用程序（如 ServiceNow、G Suite 和 Box）不仅支持预配用户，还支持预配组及其成员。 在这些情况下，如果在[映射](customize-application-attributes.md)中启用了组预配，则预配服务将同步用户和组，然后同步组成员身份。

### <a name="incremental-cycles"></a>增量周期

初始周期后，所有其他周期将：

1. 在源系统中查询自上次存储水印以来已更新的所有用户和组。

2. 使用配置的任何[分配](../manage-apps/assign-user-or-group-access-portal.md)或[基于属性的范围筛选器](define-conditional-rules-for-provisioning-user-accounts.md)筛选返回的用户和组。

3. 分配用户或处于预配范围时，服务将使用指定的[匹配属性](customize-application-attributes.md#understanding-attribute-mapping-properties)查询匹配用户的目标系统。

4. 如果在目标系统中找不到匹配的用户，则使用从源系统返回的属性创建匹配用户。 创建用户帐户后，预配服务将检测并缓存新用户的目标系统的 ID。 此 ID 用于运行该用户的所有将来操作。

5. 如果找到匹配的用户，则使用源系统提供的属性进行更新。 如果是匹配的新分配的帐户，则预配服务将检测并缓存新用户的目标系统的 ID。 此 ID 用于运行该用户的所有将来操作。

6. 如果属性映射包含"引用"属性，则服务在目标系统上执行其他更新以创建和链接引用的对象。 例如，用户可能在目标系统中有“Manager”属性，该属性链接到目标系统中创建的另一个用户。

7. 如果以前处于预配范围的用户从作用域中删除（包括未分配），则服务将通过更新禁用目标系统中的用户。

8. 如果在源系统中禁用或软删除了以前在预配范围内的某个用户，该服务会通过更新在目标系统中禁用该用户。

9. 如果在源系统中硬删除了以前在预配范围内的某个用户，该服务会在目标系统中删除该用户。 在 Azure AD 中，用户在软删除 30 天后将硬删除。

10. 在增量周期结束时保留一个新的水印，为以后的增量周期提供起点。

> [!NOTE]
> 通过使用["映射](customize-application-attributes.md)"部分中**的目标对象操作**复选框，可以选择禁用 **"创建**、**更新****"和"删除**"操作。 还可以使用“accountEnabled”等字段通过属性映射来控制用于在更新期间禁用用户的逻辑。

预配服务继续无限期地连续运行增量周期，其间隔为[特定于每个应用程序的教程](../saas-apps/tutorial-list.md)中。 增量循环将继续，直到发生以下事件之一：

- 使用 Azure 门户或使用相应的 Microsoft 图形 API 命令手动停止该服务。
- 使用 Azure 门户中的 **"清除"状态和重新启动**选项或使用相应的 Microsoft 图形 API 命令触发新的初始周期。 此操作清除任何存储的水印，并会导致再次评估所有源对象。
- 由于属性映射或范围筛选器的更改，将触发新的初始周期。 此操作还会清除任何存储的水印，并会导致再次评估所有源对象。
- 由于错误率高，预配过程进入隔离区（见下文），并在隔离中停留超过四周。 在此情况下，该服务会自动禁用。

### <a name="errors-and-retries"></a>错误和重试

如果目标系统中的错误阻止在目标系统中添加、更新或删除单个用户，则在下一个同步周期中重试该操作。 如果用户操作继续失败，则重试频率将开始递减，并逐渐减少到每天只尝试一次。 要解决故障，管理员必须检查[预配日志](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)以确定根本原因并采取相应的操作。 常见的失败包括：

- 未在源系统中填充目标系统中所需的某个用户属性
- 源系统中具有属性值的用户，该属性值在目标系统中存在唯一约束，并且其他用户记录中存在相同的值

通过调整源系统中受影响用户的属性值或调整属性映射以使其不会导致冲突来解决这些失败。

### <a name="quarantine"></a>隔离

如果针对目标系统进行的大多数或所有调用始终由于错误（例如无效的管理员凭据）而失败，则预配作业进入"隔离"状态。 如果在 Azure 门户中配置了电子邮件通知，则此状态在[预配摘要报表](../manage-apps/check-status-user-account-provisioning.md)中指示，并通过电子邮件指示。

在隔离时，增量循环的频率逐渐降低为每天一次。

修复所有违规错误并启动下一个同步周期后，预配作业将退出隔离区。 如果预配作业在隔离区中保留四周以上，则会禁用预配作业。 在此处了解有关隔离状态的更多[信息](../manage-apps/application-provisioning-quarantine-status.md)。

### <a name="how-long-provisioning-takes"></a>预配花费多长时间

性能取决于预配作业是运行初始预配周期还是增量周期。 有关预配需要多长时间以及如何监视预配服务的状态的详细信息，请参阅[检查用户预配的状态](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md)。

### <a name="how-to-tell-if-users-are-being-provisioned-properly"></a>如何判断用户是否配置正确

用户预配服务运行的所有操作都记录在 Azure AD[预配日志（预览）](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)中。 日志包括对源和目标系统进行的所有读取和写入操作，以及在每个操作期间读取或写入的用户数据。 有关如何读取 Azure 门户中的预配日志的信息，请参阅[预配报告指南](../manage-apps/check-status-user-account-provisioning.md)。

## <a name="de-provisioning"></a>去预配

Azure AD 预配服务通过在用户不应再具有访问权限时取消预配帐户来使源和目标系统保持同步。 

当应用程序支持软删除（使用活动 = false 更新请求）和发生以下任一事件时，Azure AD 预配服务将软删除应用程序中的用户：

* 用户帐户在 Azure AD 中被删除
*   用户从应用程序取消分配
*   用户不再满足范围筛选器并超出范围
    * 默认情况下，Azure AD 预配服务软删除或禁用超出范围的用户。 如果要重写此默认行为，可以设置一个标志以[跳过范围外删除](../app-provisioning/skip-out-of-scope-deletions.md)。
*   启用帐户的属性设置为"False"

如果发生上述四个事件之一，并且目标应用程序不支持软删除，则预配服务将发送 DELETE 请求以永久从应用中删除用户。 

在 Azure AD 中删除用户 30 天后，他们将从租户中永久删除。 此时，预配服务将发送 DELETE 请求以永久删除应用程序中的用户。 在 30 天窗口期间的任何时间，您可以[手动永久删除用户](../fundamentals/active-directory-users-restore.md)，该用户会向应用程序发送删除请求。

如果在属性映射中看到属性 IsSoftDelete，则用于确定用户的状态以及是否发送具有活动 = false 的更新请求以软删除用户。 

## <a name="next-steps"></a>后续步骤

[规划自动用户预配部署](../app-provisioning/plan-auto-user-provisioning.md)

[为库应用配置预配](../manage-apps/configure-automatic-user-provisioning-portal.md)

[在创建自己的应用时构建 SCIM 终结点并配置预配](../app-provisioning/use-scim-to-provision-users-and-groups.md)

[在将用户配置和预配到应用程序时排除问题](../manage-apps/application-provisioning-config-problem.md)。
