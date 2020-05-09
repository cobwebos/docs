---
title: 了解 Azure AD 预配的工作方式 |Microsoft Docs
description: 了解 Azure AD 预配的工作原理
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 12/10/2019
ms.author: mimart
ms.reviewer: arvinh
ms.openlocfilehash: 7ee685da3492b6915a687151beea3e82e46185de
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82593720"
---
# <a name="how-provisioning-works"></a>预配工作原理

自动设置是指在用户需要访问的云应用程序中创建用户标识和角色。 除了创建用户标识外，自动预配还包括在状态或角色发生更改时维护和删除用户标识。 在开始部署之前，你可以查看本文，了解 Azure AD 预配的工作原理以及如何获取配置建议。 

**Azure AD 预配服务**通过连接到应用程序供应商提供的跨域标识管理（SCIM）2.0 用户管理 API 终结点的系统，将用户预配到 SaaS 应用和其他系统。 此 SCIM 端点允许 Azure AD 以编程方式创建、更新和删除用户。 对于所选的应用程序，预配服务还可以创建、更新和删除其他标识相关对象，如组和角色。 用于在 Azure AD 和应用程序之间预配的通道使用 HTTPS TLS 1.2 加密进行加密。


![Azure AD 预配](./media/how-provisioning-works/provisioning0.PNG)
服务*图1： Azure AD 预配服务*

![出站用户预](./media/how-provisioning-works/provisioning1.PNG)
配工作流*图2： "出站" 用户预配工作流从 Azure AD 到热门 SaaS 应用程序*

![入站用户预](./media/how-provisioning-works/provisioning2.PNG)
配工作流*图3： "入站" 用户预配工作流从常用的人力资本管理（HCM）应用程序到 Azure Active Directory 和 Windows Server Active Directory*

## <a name="provisioning-using-scim-20"></a>使用 SCIM 2.0 进行预配

Azure AD 预配服务使用[SCIM 2.0 协议](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/bg-p/IdentityStandards)进行自动预配。 该服务将连接到应用程序的 SCIM 终结点，并使用 SCIM 用户对象架构和 REST Api 来自动预配和取消预配用户和组。 Azure AD 库中的大多数应用程序都提供了基于 SCIM 的预配连接器。 生成适用于 Azure AD 的应用时，开发人员可以使用 SCIM 2.0 用户管理 API 构建一个 SCIM 终结点，该终结点集成了用于预配的 Azure AD。 有关详细信息，请参阅[生成 SCIM 终结点和配置用户设置](../app-provisioning/use-scim-to-provision-users-and-groups.md)。

若要为当前没有应用的应用请求自动 Azure AD 预配连接器，请填写[Azure Active Directory 应用程序请求](https://aka.ms/aadapprequest)。

## <a name="authorization"></a>授权

需要提供凭据才能 Azure AD 连接到应用程序的用户管理 API。 为应用程序配置自动用户预配时，需要输入有效凭据。 可以通过参考应用教程来查找应用程序的凭据类型和要求。 在 Azure 门户中，你将能够通过 Azure AD 尝试使用提供的凭据连接到应用的预配应用来测试凭据。

如果也为应用程序配置了基于 SAML 的单一登录，Azure AD 的内部、每个应用程序的存储限制为1024字节。 此限制包括与应用程序的单个实例相关联的所有证书、机密令牌、凭据和相关配置数据（也称为 Azure AD 中的服务主体记录）。 如果配置了基于 SAML 的单一登录，用于对 SAML 令牌进行签名的证书通常会占用超过50% 的空间。 用户预配期间输入的任何其他项目（机密令牌、Uri、通知电子邮件地址、用户名和密码）可能会超出存储限制。 有关详细信息，请参阅[配置用户预配时保存管理员凭据的问题](../manage-apps/application-provisioning-config-problem-storage-limit.md)。

## <a name="mapping-attributes"></a>映射属性

启用第三方 SaaS 应用程序的用户预配时，Azure 门户通过属性映射控制其属性值。 设置或更新用户帐户时，映射会确定在 Azure AD 和目标应用程序之间流动的用户属性。

Azure AD 用户对象与每个 SaaS 应用的用户对象之间存在预先配置的一组属性和属性映射。 某些应用管理其他类型的对象，以及用户（如组）。

设置预配时，必须查看和配置属性映射和工作流，以定义哪些用户（或组）属性从 Azure AD 传输到应用程序。 查看并配置匹配属性（**使用此属性匹配对象**），该属性用于唯一标识和匹配两个系统之间的用户/组。

可以根据业务需求自定义默认的属性映射。 因此，你可以更改或删除现有的属性映射，或者创建新的属性映射。 有关详细信息，请参阅为[SaaS 应用程序自定义用户预配属性映射](../manage-apps/customize-application-attributes.md)。

将预配配置到 SaaS 应用程序时，表达式映射是可指定的属性映射类型之一。 对于这些映射，你必须编写一个类似脚本的表达式，以便将用户的数据转换为 SaaS 应用程序更易接受的格式。 有关详细信息，请参阅[编写属性映射的表达式](functions-for-customizing-application-data.md)。

## <a name="scoping"></a>Scoping 
### <a name="assignment-based-scoping"></a>基于分配的作用域

对于从 Azure AD 到 SaaS 应用程序的出站预配，依赖于[用户或组分配](../manage-apps/assign-user-or-group-access-portal.md)是确定哪些用户处于预配范围内的最常见方法。 由于用户分配还用于启用单一登录，因此，可以使用相同的方法来管理访问和设置。 基于分配的作用域不适用于入站预配方案，例如 Workday 和 Successfactors。

* **组合.** 使用 Azure AD Premium 许可计划，可以使用组来分配对 SaaS 应用程序的访问权限。 然后，当设置作用域设置为**仅同步分配的用户和组**时，Azure AD 预配服务将基于用户是否是分配给该应用程序的组的成员来预配或取消预配用户。 除非应用程序支持组对象，否则不会预配组对象本身。 确保分配给应用程序的组的 "SecurityEnabled" 属性设置为 "False"。

* **动态组。** Azure AD 用户预配服务可以读取和预配[动态组](../users-groups-roles/groups-create-rule.md)中的用户。 请牢记以下注意事项和建议：

  * 动态组会影响从 Azure AD 到 SaaS 应用程序的端到端设置性能。

  * 在 SaaS 应用程序中预配或取消预配动态组中用户的速度，取决于动态组计算成员身份更改的速度。 有关如何检查动态组的处理状态的信息，请参阅[检查成员身份规则的处理状态](../users-groups-roles/groups-create-rule.md)。

  * 当用户失去动态组中的成员身份时，会将其视为取消预配事件。 为动态组创建规则时，请考虑这种情况。

* **嵌套组。** Azure AD 用户预配服务无法读取或设置嵌套组中的用户。 服务只能读取和预配作为显式分配的组的直接成员的用户。 "对应用程序进行基于组的分配" 的限制也会影响单一登录（请参阅[使用组管理对 SaaS 应用程序的访问权限](../users-groups-roles/groups-saasapps.md)）。 相反，直接在包含需要预配的用户的组中分配或以其他方式指定[范围](define-conditional-rules-for-provisioning-user-accounts.md)。

### <a name="attribute-based-scoping"></a>基于属性的作用域 

可以使用范围筛选器定义基于属性的规则，这些规则确定将哪些用户预配到应用程序。 此方法通常用于从 HCM 应用程序入站预配到 Azure AD 和 Active Directory。 在每个 Azure AD 用户预配连接器的属性映射过程中，配置范围筛选器。 有关配置基于属性的范围筛选器的详细信息，请参阅[通过范围筛选器进行基于属性的应用程序设置](define-conditional-rules-for-provisioning-user-accounts.md)。

### <a name="b2b-guest-users"></a>B2B （来宾）用户

可以使用 Azure AD 用户预配服务将 Azure AD 中的 B2B （或来宾）用户预配到 SaaS 应用程序。 但是，为了使 B2B 用户使用 Azure AD 登录到 SaaS 应用程序，SaaS 应用程序必须以特定的方式配置其基于 SAML 的单一登录功能。 有关如何配置 SaaS 应用程序以支持 B2B 用户的登录的详细信息，请参阅[为 B2B 协作配置 SaaS 应用](../b2b/configure-saas-apps.md)。

请注意，来宾用户的 userPrincipalName 通常存储为 "alias # EXT #@domain.com"。 如果在属性映射中包含 userPrincipalName 作为源属性，则会从 userPrincipalName 中去除 #EXT #。 如果要求 #EXT # 出现，请将 userPrincipalName 替换为 originalUserPrincipalName 作为 source 属性。 

## <a name="provisioning-cycles-initial-and-incremental"></a>预配周期：初始和增量

当 Azure AD 是源系统时，预配服务使用 "[使用增量查询" 跟踪 Microsoft Graph 数据中的更改](https://docs.microsoft.com/graph/delta-query-overview)，以监视用户和组。 预配服务对源系统和目标系统运行初始循环，后跟定期增量循环。

### <a name="initial-cycle"></a>初始周期

启动预配服务时，第一个周期将：

1. 查询源系统中的所有用户和组，并检索[属性映射](customize-application-attributes.md)中定义的所有属性。

2. 使用配置的任何[分配](../manage-apps/assign-user-or-group-access-portal.md)或[基于属性的范围筛选器](define-conditional-rules-for-provisioning-user-accounts.md)筛选返回的用户和组。

3. 当分配用户或在设置范围内时，服务将使用指定的[匹配属性](customize-application-attributes.md#understanding-attribute-mapping-properties)在目标系统中查询匹配的用户。 示例：如果源系统中的 userPrincipal 名称是匹配的属性并映射到目标系统中的 userName，则预配服务会在目标系统中查询与源系统中 userPrincipal 名称值匹配的 userName。

4. 如果在目标系统中找不到匹配的用户，则使用从源系统返回的属性创建该用户。 创建用户帐户后，预配服务将检测并缓存新用户的目标系统 ID。 此 ID 用于在该用户上运行所有未来操作。

5. 如果找到匹配的用户，则使用源系统提供的属性进行更新。 用户帐户匹配后，预配服务会检测并缓存新用户的目标系统 ID。 此 ID 用于在该用户上运行所有未来操作。

6. 如果属性映射包含 "引用" 属性，则服务会在目标系统上执行其他更新以创建和链接引用的对象。 例如，用户可能在目标系统中有“Manager”属性，该属性链接到目标系统中创建的另一个用户。

7. 在初始周期结束时保存水印，这为后面的增量循环提供开始点。

某些应用程序（如 ServiceNow、G Suite 和 Box）不仅支持预配用户，还支持预配组及其成员。 在这些情况下，如果在[映射](customize-application-attributes.md)中启用了组预配，则预配服务将同步用户和组，然后同步组成员身份。

### <a name="incremental-cycles"></a>增量循环

初始循环后，所有其他循环将执行以下操作：

1. 在源系统中查询自上次存储水印以来已更新的所有用户和组。

2. 使用配置的任何[分配](../manage-apps/assign-user-or-group-access-portal.md)或[基于属性的范围筛选器](define-conditional-rules-for-provisioning-user-accounts.md)筛选返回的用户和组。

3. 当分配用户或在设置范围内时，服务将使用指定的[匹配属性](customize-application-attributes.md#understanding-attribute-mapping-properties)在目标系统中查询匹配的用户。

4. 如果在目标系统中找不到匹配的用户，则使用从源系统返回的属性创建该用户。 创建用户帐户后，预配服务将检测并缓存新用户的目标系统 ID。 此 ID 用于在该用户上运行所有未来操作。

5. 如果找到匹配的用户，则使用源系统提供的属性进行更新。 如果这是一个新分配的帐户，则预配服务会检测并缓存新用户的目标系统 ID。 此 ID 用于在该用户上运行所有未来操作。

6. 如果属性映射包含 "引用" 属性，则服务会在目标系统上执行其他更新以创建和链接引用的对象。 例如，用户可能在目标系统中有“Manager”属性，该属性链接到目标系统中创建的另一个用户。

7. 如果从作用域中删除了以前在预配范围内的用户，包括未分配的用户，则该服务会通过更新在目标系统中禁用该用户。

8. 如果在源系统中禁用或软删除了以前在预配范围内的某个用户，该服务会通过更新在目标系统中禁用该用户。

9. 如果在源系统中硬删除了以前在预配范围内的某个用户，该服务会在目标系统中删除该用户。 在 Azure AD 中，将在软删除用户30天后将其硬删除。

10. 在增量循环结束时保留新水印，这为后续增量循环提供起始点。

> [!NOTE]
> 您可以选择通过使用 "[映射](customize-application-attributes.md)" 部分中的 "**目标对象操作**" 复选框来禁用**创建**、**更新**或**删除**操作。 还可以使用“accountEnabled”等字段通过属性映射来控制用于在更新期间禁用用户的逻辑。

预配服务会按[特定于每个应用程序的教程](../saas-apps/tutorial-list.md)中定义的时间间隔，无限期地继续运行后向后增量循环。 增量循环将继续，直到发生以下事件之一：

- 使用 Azure 门户或使用适当的 Microsoft Graph API 命令手动停止了该服务。
- 使用 Azure 门户中的 "**清除状态" 和 "重新启动**" 选项，或使用适当的 Microsoft Graph API 命令触发新的初始周期。 此操作会清除所有存储的水印，并导致重新评估所有源对象。
- 由于属性映射或范围筛选器发生更改，将触发新的初始周期。 此操作还会清除所有存储的水印，并导致重新评估所有源对象。
- 预配过程将进入隔离区（请参阅下文），因为其错误率较高，并且保持隔离时间超过四周。 在此情况下，该服务会自动禁用。

### <a name="errors-and-retries"></a>错误和重试

如果目标系统中的错误阻止在目标系统中添加、更新或删除单个用户，则会在下一个同步周期重试该操作。 如果用户操作继续失败，则重试频率将开始递减，并逐渐减少到每天只尝试一次。 若要解决此问题，管理员必须检查[设置日志](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)，确定根本原因并采取适当的措施。 常见的失败包括：

- 未在源系统中填充目标系统中所需的某个用户属性
- 在源系统中有一个属性值在目标系统中具有唯一约束的用户，另一个用户记录中存在相同的值

通过调整源系统中受影响用户的属性值，或者通过调整属性映射来解决这些问题，使它们不会导致冲突。

### <a name="quarantine"></a>隔离

如果对目标系统进行的大部分或全部调用由于错误（例如管理凭据无效）而一直失败，则设置作业将进入 "隔离" 状态。 如果在 Azure 门户中配置了电子邮件通知，则在[预配摘要报告](../manage-apps/check-status-user-account-provisioning.md)和电子邮件中指示此状态。

在隔离时，增量循环的频率会逐渐减少到每天一次。

在修复了所有冲突的错误并启动下一个同步周期后，预配作业会退出隔离。 如果预配作业在隔离区中保留四周以上，则会禁用预配作业。 在此处了解有关[隔离状态的](../manage-apps/application-provisioning-quarantine-status.md)详细信息。

### <a name="how-long-provisioning-takes"></a>预配花费多长时间

性能取决于预配作业是否正在运行初始预配周期或增量循环。 若要详细了解预配的时间以及如何监视预配服务的状态，请参阅[检查用户预配的状态](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md)。

### <a name="how-to-tell-if-users-are-being-provisioned-properly"></a>如何判断是否已正确预配用户

用户预配服务运行的所有操作都记录在 Azure AD[预配日志（预览版）](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)中。 日志包括对源系统和目标系统执行的所有读取和写入操作，以及每个操作期间读取或写入的用户数据。 有关如何读取 Azure 门户中的设置日志的信息，请参阅[预配报告指南](../manage-apps/check-status-user-account-provisioning.md)。

## <a name="de-provisioning"></a>取消预配

Azure AD 预配服务使源和目标系统保持同步，因为用户不应再进行访问。 

当应用程序 suupports 软删除（更新请求具有 active = false）并发生以下任何事件时，Azure AD 预配服务将软删除应用程序中的用户：

* 用户帐户在 Azure AD 中被删除
*   未从应用程序中取消分配用户
*   用户不再符合范围筛选器并超出范围
    * 默认情况下，Azure AD 预配服务软删除或禁用超出范围的用户。 如果要重写此默认行为，可以设置一个标志来[跳过超出范围的删除](../app-provisioning/skip-out-of-scope-deletions.md)操作。
*   AccountEnabled 属性设置为 False

如果发生上述四个事件之一，并且目标应用程序不支持软删除，则预配服务将发送删除请求，以从应用程序中永久删除该用户。 

在 Azure AD 中删除用户30天后，将从租户中永久删除这些用户。 此时，预配服务将发送删除请求，以在应用程序中永久删除该用户。 在30天内的任何时间，你都可以[手动删除用户](../fundamentals/active-directory-users-restore.md)，这会向应用程序发送删除请求。

如果在属性映射中看到属性 IsSoftDeleted，则使用该属性来确定用户的状态，以及是否发送含 active = false 的更新请求以软删除用户。 

## <a name="next-steps"></a>后续步骤

[规划自动用户预配部署](../app-provisioning/plan-auto-user-provisioning.md)

[为库应用配置预配](../manage-apps/configure-automatic-user-provisioning-portal.md)

[创建自己的应用时生成 SCIM 终结点并配置预配](../app-provisioning/use-scim-to-provision-users-and-groups.md)

[排查在应用程序中配置和预配用户的问题](../manage-apps/application-provisioning-config-problem.md)。
