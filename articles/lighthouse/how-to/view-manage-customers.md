---
title: 查看和管理客户及委派资源
description: 作为使用 Azure 委派资源管理的服务提供商，你可以通过转到 Azure 门户中的“我的客户”，查看所有委派的客户资源和订阅。
ms.date: 01/22/2020
ms.topic: conceptual
ms.openlocfilehash: 0d4b3187066754e8a549f029623762df539b30b1
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76543420"
---
# <a name="view-and-manage-customers-and-delegated-resources"></a>查看和管理客户及委派资源

使用 [Azure 委派资源管理](../concepts/azure-delegated-resource-management.md)的服务提供商可以使用 [Azure 门户](https://portal.azure.com)中“我的客户”页来查看委派的客户资源和订阅。 在此处我们将提到服务提供商和客户，但管理多个租户的企业可以使用相同的过程来整合其管理体验。

若要访问 Azure 门户中“我的客户”页，请选择“所有服务”，然后搜索“我的客户”并选择它。 还可以通过在 Azure 门户顶部附近的搜索框中输入“我的客户”来找到它。

请记住，"**我的客户**" 页的 "顶级**客户**" 部分仅显示有关已委派订阅或资源组的客户的信息。 如果你与其他客户（例如通过[云解决方案提供商计划](https://docs.microsoft.com/partner-center/csp-overview)）合作，则在 "**客户**" 部分中将不会显示有关这些客户的信息，除非你已将其资源载入 Azure 委派的资源管理。

在此页上，名为 "**云解决方案提供商（预览版）** " 的单独部分会显示已[签署 Microsoft 客户协议（MCA）](https://docs.microsoft.com/partner-center/confirm-customer-agreement)且处于[Azure 计划下](https://docs.microsoft.com/partner-center/azure-plan-get-started)的 CSP 客户的帐单信息和资源。 有关详细信息，请参阅 [Microsoft 合作伙伴协议计费帐户入门](../../billing/mpa-overview.md)。 请注意，此部分中将显示此类 CSP 客户，无论是否还载入它们进行 Azure 委派的资源管理。 同样，CSP 客户无需显示在 "**我的客户**的**云解决方案提供商（预览版）** " 部分中，即可将它们加入 Azure 委托资源管理。

> [!NOTE]
> 你的客户可以通过导航到 Azure 门户中的“我的客户”来查看有关服务提供商的信息。 有关详细信息，请参阅[查看和管理服务提供商](view-manage-service-providers.md)。

## <a name="view-and-manage-customer-details"></a>查看和管理客户详细信息

若要查看客户详细信息，请选择“我的客户”页左侧的“客户”。

对于每位客户，你将看到客户姓名、客户 ID（租户 ID）以及与用户参与相关联的产品/服务。 在“委派”列中，你将看到委派订阅数和/或委派资源组数。

> [!IMPORTANT]
> 若要查看委派，必须在加入过程中向用户授予[读者](../../role-based-access-control/built-in-roles.md#reader)角色（或其他内置角色，其中包括读者访问权限）。

可通过页面顶部的筛选器对客户信息进行排序和分组，或者按特定的客户、产品/服务或关键字进行筛选。

可从该页面查看以下信息：

- 若要查看与客户关联的所有订阅、产品/服务和委派，请选择该客户的姓名。
- 若要查看关于产品/服务及其委派的详细信息，请选择产品/服务名称。
- 若要查看关于角色分配的更多详细信息，对于委托订阅或资源组，请选择“委托”列中的条目。

## <a name="view-and-manage-delegations"></a>查看和管理委派

委派显示已委派的订阅/资源组，以及有权访问该订阅/资源组的用户和权限。 若要查看此信息，请选择“我的客户”页左侧的“委派”。

可通过页面顶部的筛选器对访问权限分配进行排序和分组，或者按特定的客户、产品/服务或关键字进行筛选。

### <a name="view-role-assignments"></a>查看角色分配

与每个委派相关联的用户和权限显示在“角色分配”列中。 可选择每个条目以查看已向其授予访问订阅或资源组的权限的用户、组和服务主体的完整列表。 在此处，可选择特定用户、组或服务主体名称以获取详细信息。

### <a name="remove-delegations"></a>删除委派

如果在为 Azure 委派的资源管理人员加入客户时包含具有[托管服务注册分配](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role)的用户，则这些用户可以通过选择在该委托的行中显示的 "垃圾桶" 图标来删除委派。 当他们执行此操作时，服务提供商的租户中的任何用户都将无法访问之前已委派的资源。


## <a name="work-in-the-context-of-a-delegated-subscription"></a>在委派订阅的上下文中操作

可直接在 Azure 门户中的委派订阅上下文内进行操作，而无需切换正在操作的目录。 为此，请执行以下操作：

1. 选择 Azure 门户顶部附近的“目录 + 订阅”图标。
2. 在“全局订阅”筛选器中，请确保仅选中该委派订阅的框。 可以使用“当前 + 委派目录”下拉框来仅显示特定目录中的订阅。 （请勿使用“切换目录”选项，因为这会更改已登录的目录。）

如果之后访问支持[跨租户管理体验](../concepts/cross-tenant-management-experience.md)的服务，该服务将默认为所选委派订阅的上下文。 可通过执行上述步骤并选中“全选”框（或选择要处理的一个或多个订阅），来对此进行更改。

> [!NOTE]
> 如果已向你授予访问一个或多个资源组的权限，而非访问整个订阅的权限，那么你可以选择该资源组所属的订阅。 然后，可在该订阅的上下文中操作，但只能访问委派资源组。

还可通过选择该服务中的订阅或资源组，来访问与服务（支持跨租户管理体验）中的委派订阅或资源组相关的功能。

## <a name="next-steps"></a>后续步骤

- 了解[跨租户管理体验](../concepts/cross-tenant-management-experience.md)。
- 通过转到 Azure 门户中“服务提供商”来了解你的客户如何[查看和管理服务提供商](view-manage-service-providers.md)。
