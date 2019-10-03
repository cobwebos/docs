---
title: 查看和管理 Azure 门户中的客户及委派资源
description: 作为使用 Azure 委派资源管理的服务提供商，你可以通过转到 Azure 门户中的“我的客户”，查看所有委派的客户资源和订阅。
author: JnHs
ms.author: jenhayes
ms.service: lighthouse
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: acc90afa258fa7140cd7dfa8711dd64b554df45d
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67810841"
---
# <a name="view-and-manage-customers-and-delegated-resources"></a>查看和管理客户及委派资源

使用 [Azure 委派资源管理](../concepts/azure-delegated-resource-management.md)的服务提供商可以使用 [Azure 门户](https://portal.azure.com)中“我的客户”页来查看委派的客户资源和订阅  。 在此处我们将提到服务提供商和客户，但管理多个租户的企业可以使用相同的过程来整合其管理体验。

若要访问 Azure 门户中“我的客户”页，请选择“所有服务”，然后搜索“我的客户”并选择它    。 还可以通过在 Azure 门户顶部附近的搜索框中输入“我的客户”来找到它。

请记住，“我的客户”页仅显示关于已具有委派订阅或资源组的客户的信息  。 如果你与其他客户（例如[云解决方案提供商](https://docs.microsoft.com/partner-center/csp-overview)）合作，则不会在此看到关于这些客户的信息，除非载入其资源以进行委派资源管理。

> [!NOTE]
> 你的客户可以通过导航到 Azure 门户中的“我的客户”来查看有关服务提供商的信息  。 有关详细信息，请参阅[查看和管理服务提供商](view-manage-service-providers.md)。

## <a name="view-and-manage-customer-details"></a>查看和管理客户详细信息

若要查看客户详细信息，请选择“我的客户”页左侧的“客户”   。

对于每位客户，你将看到客户姓名、客户 ID（租户 ID）以及与用户参与相关联的产品/服务。 在“委派”列中，你将看到委派订阅数和/或委派资源组数  。

可通过页面顶部的筛选器对客户信息进行排序和分组，或者按特定的客户、产品/服务或关键字进行筛选。

可从该页面查看以下信息：

- 若要查看与客户关联的所有订阅、产品/服务和委派，请选择该客户的姓名。
- 若要查看关于产品/服务及其委派的详细信息，请选择产品/服务名称。
- 若要查看关于角色访问权限分配的详细信息，对于委派订阅或资源组，请选择“委派”列中的条目  。

## <a name="view-delegations"></a>查看委派

委派显示已委派的订阅/资源组，以及有权访问该订阅/资源组的用户和权限。 若要查看此信息，请选择“我的客户”页左侧的“委派”   。

可通过页面顶部的筛选器对访问权限分配进行排序和分组，或者按特定的客户、产品/服务或关键字进行筛选。

与每个委派相关联的用户和权限显示在“角色分配”列中  。 可选择每个条目以查看已向其授予访问订阅或资源组的权限的用户、组和服务主体的完整列表。 在此处，可选择特定用户、组或服务主体名称以获取详细信息。

## <a name="work-in-the-context-of-a-delegated-subscription"></a>在委派订阅的上下文中操作

可直接在 Azure 门户中的委派订阅上下文内进行操作，而无需切换正在操作的目录。 为此，请执行以下操作：

1. 选择 Azure 门户顶部附近的“目录 + 订阅”图标  。
2. 在“全局订阅”筛选器中，请确保仅选中该委派订阅的框  。 可以使用“当前 + 委派目录”下拉框来仅显示特定目录中的订阅  。 （请勿使用“切换目录”选项，因为这会更改已登录的目录  。）

如果之后访问支持[跨租户管理体验](../concepts/cross-tenant-management-experience.md)的服务，该服务将默认为所选委派订阅的上下文。 可通过执行上述步骤并选中“全选”框（或选择要处理的一个或多个订阅），来对此进行更改  。

> [!NOTE]
> 如果已向你授予访问一个或多个资源组的权限，而非访问整个订阅的权限，那么你可以选择该资源组所属的订阅。 然后，可在该订阅的上下文中操作，但只能访问委派资源组。

还可通过选择该服务中的订阅或资源组，来访问与服务（支持跨租户管理体验）中的委派订阅或资源组相关的功能。

## <a name="next-steps"></a>后续步骤

- 了解[跨租户管理体验](../concepts/cross-tenant-management-experience.md)。
- 通过转到 Azure 门户中“服务提供商”来了解你的客户如何[查看和管理服务提供商](view-manage-service-providers.md)  。
