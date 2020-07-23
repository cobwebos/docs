---
title: 使用 Azure 门户添加或删除 Azure 角色分配 - Azure RBAC
description: 了解如何使用 Azure 门户和 Azure 基于角色的访问控制 (Azure RBAC) 向用户、组、服务主体或托管标识授予对 Azure 资源的访问权限。
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 06/24/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 76f4f39e7def192b8cb97c37aefc9f67d82ad4be
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85362208"
---
# <a name="add-or-remove-azure-role-assignments-using-the-azure-portal"></a>使用 Azure 门户添加或删除 Azure 角色分配

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] 本文介绍如何使用 Azure 门户分配角色。

如需在 Azure Active Directory 中分配管理员角色，请参阅[在 Azure Active Directory 中查看和分配管理员角色](../active-directory/users-groups-roles/directory-manage-roles-portal.md)。

## <a name="prerequisites"></a>先决条件

若要添加或删除角色分配，必须拥有以下权限：

- `Microsoft.Authorization/roleAssignments/write` 和 `Microsoft.Authorization/roleAssignments/delete` 权限，例如[用户访问管理员](built-in-roles.md#user-access-administrator)或[所有者](built-in-roles.md#owner)

## <a name="access-control-iam"></a>访问控制 (IAM)

**访问控制（IAM）** 是通常用于分配角色以授予对 Azure 资源的访问权限的页面。 该功能也称为标识和访问管理，会显示在 Azure 门户中的多个位置。 下面显示了订阅的访问控制（IAM）页的示例。

![订阅的“访问控制(IAM)”页](./media/role-assignments-portal/access-control-subscription.png)

若要在访问控制（IAM）页中实现最有效的功能，如果您在尝试分配角色时可以回答以下三个问题，则会很有帮助：

1. **谁需要访问权限？**

    谁指的是用户、组、服务主体或托管标识。 这也称为“安全主体”。

1. **他们需要什么角色？**

    权限组合成角色。 可以从包含多个[内置角色](built-in-roles.md)的列表中进行选择，也可以使用自己的自定义角色。

1. **他们在何处需要访问权限？**

    “何处”是指访问权限应用到的资源集。 “何处”可以是管理组、订阅、资源组或单个资源，例如存储帐户。 这称为“范围”。

## <a name="add-a-role-assignment"></a>添加角色分配

在 Azure RBAC 中，若要授予对 Azure 资源的访问权限，请添加角色分配。 遵循以下步骤分配角色。

1. 在 Azure 门户中单击“所有服务”，然后选择要授予访问权限的范围。 例如，可以选择“管理组”、“订阅”、“资源组”或某个资源  。

1. 单击该范围的特定资源。

1. 单击“访问控制(IAM)”。

1. 单击“角色分配”选项卡以查看在此范围内的角色分配。

    ![“访问控制(IAM)”和“角色分配”选项卡](./media/role-assignments-portal/role-assignments.png)

1. 单击“添加” > “角色分配”。 

   如果没有分配角色的权限，则将禁用“添加角色分配”选项。

   ![添加角色分配菜单](./media/shared/add-role-assignment-menu.png)

    此时会打开“添加角色分配”窗格。

   ![“添加角色分配”窗格](./media/role-assignments-portal/add-role-assignment.png)

1. 在“角色”下拉列表中选择一个角色，例如“虚拟机参与者”。 

1. 在“选择”列表中，选择用户、组、服务主体或托管标识。 如果没有在列表中看到安全主体，则可在“选择”框中键入相应内容，以便在目录中搜索显示名称、电子邮件地址和对象标识符。

1. 单击“保存”以分配该角色。

   片刻之后，会在所选范围内为安全主体分配角色。

    ![添加角色分配 - 保存](./media/role-assignments-portal/add-role-assignment-save.png)

## <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>将用户分配为订阅的管理员

若要使某个用户成为 Azure 订阅的管理员，请在订阅范围内为其分配[所有者](built-in-roles.md#owner)角色。 “所有者”角色授予用户对订阅中所有资源的完全访问权限，包括将访问权限授予其他用户的权限。 这些步骤与任何其他角色分配是相同的。

1. 在 Azure 门户中，依次单击“所有服务”、“订阅” 。

1. 单击要授予访问权限的订阅。

1. 单击“访问控制(IAM)”。

1. 单击“角色分配”选项卡以查看此订阅的角色分配。

    ![“访问控制(IAM)”和“角色分配”选项卡](./media/role-assignments-portal/role-assignments.png)

1. 单击“添加” > “角色分配”。 

   如果没有分配角色的权限，则将禁用“添加角色分配”选项。

   ![添加角色分配菜单](./media/shared/add-role-assignment-menu.png)

    此时会打开“添加角色分配”窗格。

   ![“添加角色分配”窗格](./media/role-assignments-portal/add-role-assignment.png)

1. 在“角色”下拉列表中，选择“所有者”角色。

1. 在“选择”列表中，选择一个用户。 如果没有在列表中看到用户，则可在“选择”框中键入相应内容，以便在目录中搜索显示名称和电子邮件地址。

1. 单击“保存”以分配该角色。

   片刻之后，会在订阅范围为用户分配“所有者”角色。

## <a name="add-a-role-assignment-for-a-managed-identity-preview"></a>为托管标识（预览）添加角色分配

如本文前面所述，可以使用**访问控制（IAM）** 页添加托管标识的角色分配。 使用访问控制（IAM）页时，请从范围开始，然后选择托管标识和角色。 本部分介绍了一种添加托管标识的角色分配的替代方法。 使用这些步骤，从托管标识开始，然后选择范围和角色。

> [!IMPORTANT]
> 使用这些备用步骤添加托管标识的角色分配目前处于预览阶段。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

### <a name="system-assigned-managed-identity"></a>系统分配的托管标识

按照以下步骤，通过托管标识将角色分配给系统分配的托管标识。

1. 在 Azure 门户中，打开系统分配的托管标识。

1. 在左侧菜单中，单击“标识”。

    ![系统分配的托管标识](./media/shared/identity-system-assigned.png)

1. 在 "**权限**" 下，单击 " **Azure 角色分配**"。

    如果已将角色分配给所选系统分配的托管标识，则会看到角色分配的列表。 此列表包括你有权读取的所有角色分配。

    ![系统分配的托管标识的角色分配](./media/shared/role-assignments-system-assigned.png)

1. 若要更改订阅，请单击 "**订阅**" 列表。

1. 单击 "**添加角色分配（预览）**"。

1. 使用下拉列表选择角色分配适用的资源集，如**订阅**、**资源组**或资源。

    如果没有所选作用域的角色分配写入权限，则将显示内联消息。 

1. 在“角色”下拉列表中选择一个角色，例如“虚拟机参与者”。 

   ![“添加角色分配”窗格](./media/role-assignments-portal/add-role-assignment-with-scope.png)

1. 单击“保存”以分配该角色。

   几分钟后，将为托管标识分配所选作用域中的角色。

### <a name="user-assigned-managed-identity"></a>用户分配的托管标识

按照以下步骤，通过托管标识将角色分配给用户分配的托管标识。

1. 在 Azure 门户中，打开用户分配的托管标识。

1. 在左侧菜单中，单击 " **Azure 角色分配**"。

    如果已将角色分配给所选的用户分配的托管标识，则会看到角色分配的列表。 此列表包括你有权读取的所有角色分配。

    ![系统分配的托管标识的角色分配](./media/shared/role-assignments-user-assigned.png)

1. 若要更改订阅，请单击 "**订阅**" 列表。

1. 单击 "**添加角色分配（预览）**"。

1. 使用下拉列表选择角色分配适用的资源集，如**订阅**、**资源组**或资源。

    如果没有所选作用域的角色分配写入权限，则将显示内联消息。 

1. 在“角色”下拉列表中选择一个角色，例如“虚拟机参与者”。 

   ![“添加角色分配”窗格](./media/role-assignments-portal/add-role-assignment-with-scope.png)

1. 单击“保存”以分配该角色。

   几分钟后，将为托管标识分配所选作用域中的角色。

## <a name="remove-a-role-assignment"></a>删除角色分配

在 Azure RBAC 中，若要从 Azure 资源删除访问权限，请删除角色分配。 通过以下步骤删除角色分配。

1. 在要删除访问权限的范围（例如管理组、订阅、资源组或资源）内打开“访问控制(IAM)”。

1. 单击“角色分配”选项卡以查看此订阅的所有角色分配。

1. 在角色分配列表中，在需删除其角色分配的安全主体旁边添加复选标记。

   ![“删除角色分配”消息](./media/role-assignments-portal/remove-role-assignment-select.png)

1. 单击“删除”。

   ![“删除角色分配”消息](./media/role-assignments-portal/remove-role-assignment.png)

1. 在显示的“删除角色分配”消息中，单击“是”。

    此时会显示一条消息，指出无法删除继承的角色分配，而你正在尝试删除子范围的角色分配。 应在角色的分配范围打开“访问控制(IAM)”，然后重试。 在正确的范围打开“访问控制(IAM)”的快捷方法是查看“范围”列，然后单击“(继承)”旁边的链接。 

   ![“删除角色分配”消息](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>后续步骤

- [使用 Azure 门户列出 Azure 角色分配](role-assignments-list-portal.md)
- [教程：使用 Azure 门户授予用户对 Azure 资源的访问权限](quickstart-assign-role-user-portal.md)
- [排查 Azure RBAC 问题](troubleshooting.md)
- [使用 Azure 管理组来组织资源](../governance/management-groups/overview.md)
