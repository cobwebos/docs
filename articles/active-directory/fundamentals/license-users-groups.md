---
title: 分配或删除许可证 - Azure Active Directory | Microsoft Docs
description: 关于如何对用户或组分配或删除 Azure Active Directory 许可证的说明。
services: active-directory
author: ajburnle
manager: daveba
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
ms.date: 09/06/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: c69f2ce964d9171197771d783358dcef2ca60726
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/02/2020
ms.locfileid: "89319519"
---
# <a name="assign-or-remove-licenses-in-the-azure-active-directory-portal"></a>在 Azure Active Directory 门户中分配或删除许可证

许多 Azure Active Directory (Azure AD) 服务要求针对该服务向每个用户或组（以及关联成员）授予许可。 只有具有活动许可证的用户才能访问和使用符合该要求的已许可 Azure AD 服务。 许可证按租户应用，不能转让给其他租户。 

## <a name="available-license-plans"></a>可用许可计划

有多个许可计划可用于 Azure AD 服务，其中包括：

- Azure AD Free

- Azure AD Premium P1

- Azure AD Premium P2

有关每个许可计划的特定信息和关联许可详细信息，请参阅[需要哪个许可证？](https://azure.microsoft.com/pricing/details/active-directory/)。 若要注册 Azure AD Premium 许可计划，请参阅[此文](./active-directory-get-started-premium.md)。

并非所有 Microsoft 服务都可在所有位置使用。 必须先为所有成员指定“使用位置”  ，然后才能将许可证分配给组。 可以在 Azure AD 的“Azure Active Directory”&gt;“用户”&gt;“配置文件”&gt;“设置”  区域中设置此值。 未指定使用位置的任何用户都会继承 Azure AD 组织的位置。

## <a name="view-license-plans-and-plan-details"></a>查看许可计划和计划详细信息

可以查看可用服务计划（包括各个许可证）、检查即将到来的到期日期，以及查看可用分配数。

### <a name="to-find-your-service-plan-and-plan-details"></a>查找服务计划和计划详细信息的步骤

1. 使用 Azure AD 组织中的许可证管理员帐户登录到 [Azure 门户](https://portal.azure.com/)。

1. 选择“Azure Active Directory”，然后选择“许可证”   。

    ![“许可证”页，显示已购买服务和已分配许可证数量](media/license-users-groups/license-details-blade.png)

1. 选择“已购买项”  链接可查看“产品”  页，以及查看许可计划的“已分配”  数、“可用”数  和“即将过期”  数。

    ![“服务”页，包含服务许可计划和关联的许可证信息](media/license-users-groups/license-products-blade-with-products.png)

1. 选择计划名称可查看其已许可的用户和组。

## <a name="assign-licenses-to-users-or-groups"></a>向用户或组分配许可证

确保使用已许可 Azure AD 服务的任何人都具有相应的许可证。 可将许可权添加到用户或整个组。

### <a name="to-assign-a-license-to-a-user"></a>向用户分配许可证的步骤

1. 在“产品”  页上，选择要分配给用户的许可计划的名称。

    ![“服务”页，其中突出显示了服务许可计划](media/license-users-groups/license-products-blade-with-product-highlight.png)

1. 在“许可计划概览”页上，选择“分配”。 

    ![“服务”页，突出显示了“分配”选项](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

1. 在“分配”  页上，选择“用户和组”  ，然后搜索并选择向其分配许可证的用户。

    ![“分配许可证”页，突出显示了搜索和“选择”选项](media/license-users-groups/assign-license-blade-with-highlight.png)

1. 选择“分配选项”  ，确保打开了相应的许可证选项，然后选择“确定”  。

    ![“许可证选项”页，显示许可计划中的所有可用选项](media/license-users-groups/license-option-blade-assignments.png)

    “分配许可证”  页会进行更新，以显示选择了用户并且配置了分配。

    > [!NOTE]
    > 并非所有 Microsoft 服务都可在所有位置使用。 必须先指定“使用位置”  ，然后才能将许可证分配给用户。 可以在 Azure AD 的“Azure Active Directory”&gt;“用户”&gt;“配置文件”&gt;“设置”  区域中设置此值。 未指定使用位置的任何用户都会继承 Azure AD 组织的位置。

1. 选择“分配”。 

    用户会添加到已许可用户列表，并有权访问所包含的 Azure AD 服务。
    > [!NOTE]
    > 也可以从用户的“许可证”  页将许可证直接分配给用户。 如果用户有一个通过组成员关系分配的许可证，并且你希望直接将相同的许可证分配给该用户，那么只能从步骤 1 中提到的“产品”  页进行分配。

### <a name="to-assign-a-license-to-a-group"></a>向组分配许可证的步骤

1. 在“产品”  页上，选择要分配给用户的许可计划的名称。

    ![“产品”边栏选项卡，突出显示了产品许可计划](media/license-users-groups/license-products-blade-with-product-highlight.png)

1. 在“Azure Active Directory Premium 计划 2”  页上，选择“分配”  。

    ![“产品”页，突出显示了“分配”选项](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

1. 在“分配”  页上，选择“用户和组”  ，然后搜索并选择向其分配许可证的组。

    ![“分配许可证”页，突出显示了搜索和“选择”选项](media/license-users-groups/assign-group-license-blade-with-highlight.png)

1. 选择“分配选项”  ，确保打开了相应的许可证选项，然后选择“确定”  。

    ![“许可证选项”页，显示许可计划中的所有可用选项](media/license-users-groups/license-option-blade-group-assignments.png)

    “分配许可证”  页会进行更新，以显示选择了用户并且配置了分配。

1. 选择“分配”。 

    组会添加到已许可组的列表，所有成员都有权访问所包含的 Azure AD 服务。

## <a name="remove-a-license"></a>删除许可证

可以从用户的 Azure AD 用户页删除许可证、从组分配的组概览页进行删除，也可以从 Azure AD 的“许可证”页开始查看许可证的用户和组。 

### <a name="to-remove-a-license-from-a-user"></a>从用户删除许可证的步骤

1. 在服务计划的“许可的用户”  页上，选择应不再具有许可证的用户。 例如，_Alain Charon_。

1. 选择“删除许可证”  。

    ![“许可的用户”页，突出显示了“删除许可证”选项](media/license-users-groups/license-products-user-blade-with-remove-option-highlight.png)

> [!IMPORTANT]
> 无法直接删除用户从组继承的许可证。 而是必须从继承许可证的组删除用户。

### <a name="to-remove-a-license-from-a-group"></a>从组删除许可证

1. 在许可计划的“许可的组”  页上，选择应不再具有许可证的组。

1. 选择“删除许可证”  。

    ![“许可的组”页，突出显示了“删除许可证”选项](media/license-users-groups/license-products-group-blade-with-remove-option-highlight.png)
    
    > [!NOTE]
    > 当同步到 Azure AD 的本地用户帐户超出了同步的范围，或者当同步被删除时，该用户将在 Azure AD 中被软删除。 发生这种情况时，直接分配给用户或通过基于组的许可分配给用户的许可证将标记为“已挂起”  ，而不是“已删除”  。

## <a name="next-steps"></a>后续步骤

分配了许可证之后，可以执行以下过程：

- [识别和解决许可证分配问题](../users-groups-roles/licensing-groups-resolve-problems.md)

- [将许可的用户添加到组以进行许可](../users-groups-roles/licensing-groups-migrate-users.md)

- [在 Azure Active Directory 中使用组管理许可的方案、限制和已知问题](../users-groups-roles/licensing-group-advanced.md)

- [添加或更改个人资料信息](active-directory-users-profile-azure-portal.md)