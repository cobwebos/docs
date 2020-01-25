---
title: 使用 Azure RBAC 和 Azure 门户列出角色分配
description: 了解如何使用 Azure 基于角色的访问控制（RBAC）和 Azure 门户来确定用户、组、服务主体或托管标识有权访问哪些资源。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/23/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 099cf74dd27f39a4289397d5178511125d9ebf6f
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720690"
---
# <a name="list-role-assignments-using-azure-rbac-and-the-azure-portal"></a>使用 Azure RBAC 和 Azure 门户列出角色分配

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] 本文介绍了如何使用 Azure 门户列出角色分配。

> [!NOTE]
> 如果你的组织对使用[Azure 委托资源管理](../lighthouse/concepts/azure-delegated-resource-management.md)的服务提供商具有外包管理功能，则此处将不会显示该服务提供商授权的角色分配。

## <a name="list-role-assignments-for-a-user-or-group"></a>列出用户或组的角色分配

若要查看分配给订阅中的用户或组的角色，最简单的方法是使用 " **Azure 资源**" 窗格。

1. 在 Azure 门户中，单击 "**所有服务**"，然后选择 "**用户**或**组**"。

1. 单击要列出其角色分配的用户或组。

1. **Azure 리소스**를 클릭합니다.

    你会在管理组、订阅、资源组或资源等不同范围内看到分配给所选用户或组的角色的列表。 此列表包含您有权读取的所有角色分配。

    ![사용자에 대한 역할 할당](./media/role-assignments-list-portal/azure-resources-user.png)    

1. 若要更改订阅，请单击 "**订阅**" 列表。

## <a name="list-owners-of-a-subscription"></a>列出订阅的所有者

已分配订阅的 "[所有者](built-in-roles.md#owner)" 角色的用户可以管理订阅中的所有内容。 按照以下步骤列出订阅的所有者。

1. Azure Portal에서 **모든 서비스**, **구독**을 차례로 클릭합니다.

1. 单击要列出其所有者的订阅。

1. **액세스 제어(IAM)** 를 클릭합니다.

1. **역할 할당** 탭을 클릭하여 이 구독의 모든 역할 할당을 봅니다.

1. 滚动到 "**所有者**" 部分，查看为此订阅分配了 "所有者" 角色的所有用户。

   !["订阅访问控制-角色分配" 选项卡](./media/role-assignments-list-portal/access-control-role-assignments-subscription.png)

## <a name="list-role-assignments-at-a-scope"></a>列出范围内的角色分配

1. 在 Azure 门户中，单击 "**所有服务**"，然后选择范围。 예를 들어 **관리 그룹**, **구독**, **리소스 그룹** 또는 리소스를 선택할 수 있습니다.

1. 单击特定资源。

1. **액세스 제어(IAM)** 를 클릭합니다.

1. **역할 할당** 탭을 클릭하여 이 범위의 모든 역할 할당을 봅니다.

   ![액세스 제어 - 역할 할당 탭](./media/role-assignments-list-portal/access-control-role-assignments.png)

   역할 할당 탭에서 이 범위에서 액세스 권한이 있는 사용자를 볼 수 있습니다. 일부 역할의 범위는 **이 리소스**이지만 나머지는 다른 범위에서 **(상속)** 됩니다. 액세스 권한은 이 리소스에 특정적으로 할당되거나 부모 범위에 대한 할당에서 상속됩니다.

## <a name="list-role-assignments-for-a-user-at-a-scope"></a>列出某个用户在某个范围内的角色分配

若要列出用户、组、服务主体或托管标识的访问权限，请列出其角色分配。 按照以下步骤列出特定范围内单个用户、组、服务主体或托管标识的角色分配。

1. 在 Azure 门户中，单击 "**所有服务**"，然后选择范围。 예를 들어 **관리 그룹**, **구독**, **리소스 그룹** 또는 리소스를 선택할 수 있습니다.

1. 单击特定资源。

1. **액세스 제어(IAM)** 를 클릭합니다.

1. **액세스 권한 확인** 탭을 클릭합니다.

    ![액세스 제어 - 액세스 권한 확인 탭](./media/role-assignments-list-portal/access-control-check-access.png)

1. **찾기** 목록에서 액세스 권한을 확인하려는 보안 주체의 유형을 선택합니다.

1. 검색 상자에서 표시 이름, 이메일 주소 또는 개체 ID에 대한 디렉터리를 검색할 문자열을 입력합니다.

    ![액세스 권한 확인 선택 목록](./media/role-assignments-list-portal/check-access-select.png)

1. 보안 주체를 클릭하여 **할당** 창을 엽니다.

    ![할당 창](./media/role-assignments-list-portal/check-access-assignments.png)

    이 창에서 선택한 보안 주체 및 범위에 할당된 역할을 볼 수 있습니다. 이 범위에 거부 할당이 있거나 이 범위에 상속된 거부 할당이 있으면 나열됩니다.

## <a name="list-role-assignments-for-a-system-assigned-managed-identity"></a>为系统分配的托管标识列出角色分配

1. 在 Azure 门户中，打开一个系统分配的托管标识。

1. 在左侧菜单中，单击 "**标识**"。

    ![시스템 할당 관리 ID](./media/role-assignments-list-portal/identity-system-assigned.png)

1. 在 "**角色分配**" 下，单击 **"显示分配给此托管标识的 Azure RBAC 角色"** 。

    在管理组、订阅、资源组或资源等不同范围内，你会看到分配给所选系统分配的托管标识的角色的列表。 此列表包含您有权读取的所有角色分配。

    ![系统分配的托管标识的角色分配](./media/role-assignments-list-portal/azure-resources-system-assigned.png)

## <a name="list-role-assignments-for-a-user-assigned-managed-identity"></a>列出用户分配的托管标识的角色分配

1. 在 Azure 门户中，打开一个系统分配的托管标识。

1. **Azure 리소스**를 클릭합니다.

    在管理组、订阅、资源组或资源等不同范围内，你会看到分配给所选用户分配的托管标识的角色的列表。 此列表包含您有权读取的所有角色分配。

    ![系统分配的托管标识的角色分配](./media/role-assignments-list-portal/azure-resources-user-assigned.png)

1. 若要更改订阅，请单击 "**订阅**" 列表。

## <a name="list-number-of-role-assignments"></a>列出角色分配数

每个订阅最多可以有**2000**个角色分配。 为了帮助您跟踪这些限制，"**角色分配**" 选项卡包括列出当前角色分配数的图表。

![访问控制-角色分配的数目图表](./media/role-assignments-list-portal/access-control-role-assignments-chart.png)

如果要接近最大数量，并尝试添加更多角色分配，则在 "**添加角色分配**" 窗格中将显示一条警告。 可以通过删除不再需要的角色分配，或将角色分配给组而不是单个用户来减少角色分配的数量。

![访问控制-添加角色分配警告](./media/role-assignments-list-portal/add-role-assignment-warning.png)

## <a name="next-steps"></a>다음 단계

- [使用 Azure RBAC 和 Azure 门户添加或删除角色分配](role-assignments-portal.md)
- [Azure 리소스에 대한 RBAC 문제 해결](troubleshooting.md)
