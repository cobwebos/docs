---
title: 分配或删除许可证 - Azure Active Directory | Microsoft Docs
description: 关于如何对用户或组分配或删除 Azure Active Directory 许可证的说明。
services: active-directory
author: msaburnley
manager: daveba
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 869713967a6a89611ab2ea9ca7b7516f090a5f92
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2019
ms.locfileid: "72034270"
---
# <a name="assign-or-remove-licenses-in-the-azure-active-directory-portal"></a>在 Azure Active Directory 门户中分配或删除许可证

许多 Azure Active Directory （Azure AD）服务要求您为该服务的每个用户或组（以及相关成员）提供许可证。 只有具有有效许可证的用户才能访问和使用 Azure AD 的许可的服务。

## <a name="available-license-plans"></a>可用的许可证计划

Azure AD 服务有若干可用的许可计划，包括：

- Azure AD Free

- Azure AD Premium P1

- Azure AD Premium P2

有关每个许可证计划和关联的许可详细信息的具体信息，请参阅["需要哪些许可证？"](https://azure.microsoft.com/pricing/details/active-directory/)。

并非所有 Microsoft 服务都可在所有位置使用。 必须先为所有成员指定“使用位置”，然后才能将许可证分配给组。 可以在 Azure AD 的“Azure Active Directory”&gt;“用户”&gt;“配置文件”&gt;“设置”区域中设置此值。 未指定其使用位置的任何用户都将继承 Azure AD 组织的位置。

## <a name="view-license-plans-and-plan-details"></a>查看许可证计划和计划详细信息

您可以查看可用的服务计划，包括各个许可证，检查待定过期日期，并查看可用分配数。

### <a name="to-find-your-service-plan-and-plan-details"></a>查找服务计划和计划详细信息

1. 使用 Azure AD 组织中的许可证管理员帐户登录到[Azure 门户](https://portal.azure.com/)。

1. 选择“Azure Active Directory”，然后选择“许可证”。

    !["许可证" 页，包含购买的服务数量和分配的许可证](media/license-users-groups/license-details-blade.png)

1. 选择 "**购买**" 链接以查看 "**产品**" 页，并查看许可证计划的 "**已分配**"、"**可用**" 和 "即将**过期**" 数字。

    ![服务页面，包含服务许可证计划和关联的许可证信息](media/license-users-groups/license-products-blade-with-products.png)

1. 选择计划名称以查看其许可的用户和组。

## <a name="assign-licenses-to-users-or-groups"></a>向用户或组分配许可证

确保使用已许可 Azure AD 服务的任何人都具有相应的许可证。 你可以将许可权限添加到用户或整个组。

### <a name="to-assign-a-license-to-a-user"></a>向用户分配许可证

1. 在 "**产品**" 页上，选择要分配给用户的许可证计划的名称。

    ![服务页，具有突出显示的服务许可证计划](media/license-users-groups/license-products-blade-with-product-highlight.png)

1. 在 "许可计划概述" 页上，选择 "**分配**"。

    !["服务" 页，具有突出显示的分配选项](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

1. 在“分配”页上，选择“用户和组”，然后搜索并选择向其分配许可证的用户。

    ![“分配许可证”页，突出显示了搜索和“选择”选项](media/license-users-groups/assign-license-blade-with-highlight.png)

1. 选择“分配选项”，确保打开了相应的许可证选项，然后选择“确定”。

    ![许可证选项页，其中包含许可证计划中的所有可用选项](media/license-users-groups/license-option-blade-assignments.png)

    “分配许可证”页会进行更新，以显示选择了用户并且配置了分配。

    > [!NOTE]
    > 并非所有 Microsoft 服务都可在所有位置使用。 必须先指定“使用位置”，然后才能将许可证分配给用户。 可以在 Azure AD 的“Azure Active Directory”&gt;“用户”&gt;“配置文件”&gt;“设置”区域中设置此值。 未指定其使用位置的任何用户都将继承 Azure AD 组织的位置。

1. 选择“分配”。

    用户会添加到已许可用户列表，并有权访问所包含的 Azure AD 服务。

### <a name="to-assign-a-license-to-a-group"></a>将许可证分配到组

1. 在 "**产品**" 页上，选择要分配给用户的许可证计划的名称。

    ![产品边栏选项卡，具有突出显示的产品许可证计划](media/license-users-groups/license-products-blade-with-product-highlight.png)

1. 在“Azure Active Directory Premium 计划 2”页上，选择“分配”。

    ![“产品”页，突出显示了“分配”选项](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

1. 在“分配”页上，选择“用户和组”，然后搜索并选择向其分配许可证的组。

    ![“分配许可证”页，突出显示了搜索和“选择”选项](media/license-users-groups/assign-group-license-blade-with-highlight.png)

1. 选择“分配选项”，确保打开了相应的许可证选项，然后选择“确定”。

    ![许可证选项页，其中包含许可证计划中的所有可用选项](media/license-users-groups/license-option-blade-group-assignments.png)

    “分配许可证”页会进行更新，以显示选择了用户并且配置了分配。

1. 选择“分配”。

    组会添加到已许可组的列表，所有成员都有权访问所包含的 Azure AD 服务。

## <a name="remove-a-license"></a>删除许可证

你可以从用户的 Azure AD "用户" 页、组分配的 "组概述" 页或从 "Azure AD**许可证**" 页中删除许可证，以查看许可证的用户和组。

### <a name="to-remove-a-license-from-a-user"></a>从用户删除许可证

1. 在服务计划的 "**许可用户**" 页上，选择不应再有许可证的用户。 例如，Alain Charon。

1. 选择“删除许可证”。

    ![“许可的用户”页，突出显示了“删除许可证”选项](media/license-users-groups/license-products-user-blade-with-remove-option-highlight.png)

> [!IMPORTANT]
> 不能直接删除用户从组继承的许可证。 而是必须从继承许可证的组删除用户。

### <a name="to-remove-a-license-from-a-group"></a>从组删除许可证

1. 在许可计划的 "**许可组**" 页上，选择不应再有许可证的组。

1. 选择“删除许可证”。

    ![“许可的组”页，突出显示了“删除许可证”选项](media/license-users-groups/license-products-group-blade-with-remove-option-highlight.png)

## <a name="next-steps"></a>后续步骤

分配了许可证之后，可以执行以下过程：

- [识别和解决许可证分配问题](../users-groups-roles/licensing-groups-resolve-problems.md)

- [将许可的用户添加到组以进行许可](../users-groups-roles/licensing-groups-migrate-users.md)

- [在 Azure Active Directory 中使用组管理许可的方案、限制和已知问题](../users-groups-roles/licensing-group-advanced.md)

- [添加或更改配置文件信息](active-directory-users-profile-azure-portal.md)
