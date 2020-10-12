---
title: 使用 Azure 门户列出 Azure 角色分配 - Azure RBAC
description: 了解如何使用 Azure 门户和 Azure 基于角色的访问控制 (Azure RBAC) 来确定用户、组、服务主体和托管标识有权访问的资源。
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 06/24/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: f7d56ecc7fc6bd850fced33c2c1cf20902bb2df4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85361827"
---
# <a name="list-azure-role-assignments-using-the-azure-portal"></a>使用 Azure 门户列出 Azure 角色分配

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] 本文介绍如何使用 Azure 门户列出角色分配。

> [!NOTE]
> 如果你的组织对使用 [Azure 委托资源管理](../lighthouse/concepts/azure-delegated-resource-management.md)的服务提供商具有外包管理功能，则此处将不会显示该服务提供商授权的角色分配。

## <a name="list-role-assignments-for-a-user-or-group"></a>列出用户或组的角色分配

要查看分配给订阅中的用户或组的角色，最简单的方法是使用“Azure 角色分配”窗格。

1. 在 Azure 门户的“Azure 门户”菜单中，选择“所有服务”。

1. 选择“Azure Active Directory”，然后选择“用户”或“组”。

1. 单击要列出其角色分配的用户或组。

1. 单击“Azure 角色分配”。

    随即将显示各种范围中分配给所选用户或组的角色列表，如管理组、订阅、资源组或资源。 此列表包括你有权读取的所有角色分配。

    ![用户的角色分配](./media/role-assignments-list-portal/azure-role-assignments-user.png)    

1. 要更改订阅，请单击“订阅”列表。

## <a name="list-owners-of-a-subscription"></a>列出订阅的所有者

已分配订阅的[所有者](built-in-roles.md#owner)角色的用户可以管理订阅中的所有内容。 按照以下步骤列出订阅的所有者。

1. 在 Azure 门户中，依次单击“所有服务”、“订阅” 。

1. 单击要列出其所有者的订阅。

1. 单击“访问控制(IAM)”。

1. 单击“角色分配”选项卡以查看此订阅的所有角色分配。

1. 滚动到“所有者”部分，以查看已分配此订阅的“所有者”角色的所有用户。

   ![“订阅访问控制 - 角色分配”选项卡](./media/role-assignments-list-portal/access-control-role-assignments-subscription.png)

## <a name="list-role-assignments-at-a-scope"></a>列出某个范围内的角色分配

1. 在 Azure 门户中单击“所有服务”，然后选择范围。 例如，可以选择“管理组”、“订阅”、“资源组”或某个资源  。

1. 单击特定的资源。

1. 单击“访问控制(IAM)”。

1. 单击“角色分配”选项卡以查看在此范围内的所有角色分配。

   ![“访问控制”-“角色分配”选项卡](./media/role-assignments-list-portal/access-control-role-assignments.png)

   在“角色分配”选项卡上，可以看到谁有权访问此范围。 请注意，有些角色的权限范围已划归到**此资源**，还有一些角色是从另一范围 **(继承的)** 。 访问权限可以专门分配给此资源，也可以从父作用域的分配继承。

## <a name="list-role-assignments-for-a-user-at-a-scope"></a>列出某个范围内某个角色的角色分配

若要列出某个用户、组、服务主体或托管标识的访问权限，请列出其角色分配。 按照以下步骤列出特定范围内单个用户、组、服务主体或托管标识的角色分配。

1. 在 Azure 门户中单击“所有服务”，然后选择范围。 例如，可以选择“管理组”、“订阅”、“资源组”或某个资源  。

1. 单击特定的资源。

1. 单击“访问控制(IAM)”。

1. 单击“检查访问权限”选项卡。

    ![“访问控制”-“检查访问权限”选项卡](./media/role-assignments-list-portal/access-control-check-access.png)

1. 在“查找”列表中，选择要检查访问权限的安全主体类型。

1. 在搜索框中，输入字符串以在目录中搜索显示名称、电子邮件地址或对象标识符。

    ![“检查访问权限”选择列表](./media/role-assignments-list-portal/check-access-select.png)

1. 单击安全主体以打开“分配”窗格。

    ![分配窗格](./media/role-assignments-list-portal/check-access-assignments.png)

    在此窗格中，可以看到分配给所选安全主体和范围的角色。 如果此范围内有任何拒绝分配或继承到此范围的角色，则会将其列出。

## <a name="list-role-assignments-for-a-managed-identity"></a>列出托管标识的角色分配

如之前所述，可以通过使用“访问控制(IAM)”边栏选项卡来列出特定范围内系统分配的托管标识和用户分配的托管标识的角色分配。 本部分介绍如何列出仅托管标识的角色分配。

### <a name="system-assigned-managed-identity"></a>系统分配的托管标识

1. 在 Azure 门户中，打开系统分配的托管标识。

1. 在左侧菜单中，单击“标识”。

    ![系统分配的托管标识](./media/shared/identity-system-assigned.png)

1. 在“权限”下，单击“Azure 角色分配” 。

    随即将显示各种范围中分配给所选系统分配的托管标识的角色列表，如管理组、订阅、资源组或资源。 此列表包括你有权读取的所有角色分配。

    ![系统分配的托管标识的角色分配](./media/shared/role-assignments-system-assigned.png)

1. 若要更改订阅，请单击“订阅”列表。

### <a name="user-assigned-managed-identity"></a>用户分配的托管标识

1. 在 Azure 门户中，打开用户分配的托管标识。

1. 单击“Azure 角色分配”。

    随即将显示各种范围中分配给所选角色分配的托管标识的角色列表，如管理组、订阅、资源组或资源。 此列表包括你有权读取的所有角色分配。

    ![系统分配的托管标识的角色分配](./media/shared/role-assignments-user-assigned.png)

1. 若要更改订阅，请单击“订阅”列表。

## <a name="list-number-of-role-assignments"></a>列出角色分配数

每个订阅中最多可以包含 **2000** 个角色分配。 此限制包括订阅、资源组和资源范围内的角色分配。 为了帮助你跟踪此限制，“角色分配”选项卡包含一个图表，其中列出了当前订阅的角色分配数。

![访问控制 - 角色分配数目图表](./media/role-assignments-list-portal/access-control-role-assignments-chart.png)

如果在快要到达最大数量时尝试添加更多角色分配，则会在“添加角色分配”窗格中看到一条警告。 有关可减少角色分配数的方法，请参阅 [Azure RBAC 故障排除](troubleshooting.md#azure-role-assignments-limit)。

![访问控制 - 添加角色分配警告](./media/role-assignments-list-portal/add-role-assignment-warning.png)

## <a name="download-role-assignments-preview"></a>下载角色分配（预览版）

可以使用 CSV 或 JSON 格式下载某一范围内的角色分配。 如果你在迁移订阅时需要检查电子表格中的列表或进行清点，这会很有帮助。

> [!IMPORTANT]
> 下载目前以公共预览版提供的角色分配。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

下载角色分配时，应记住以下条件：

- 如果你没有读取目录的权限，Directory Readers 角色、DisplayName、SignInName 和 ObjectType 等列将为空。
- 未包含已删除了安全主体的角色分配。
- 未包含已授予给经典管理员的访问权限。

按照以下步骤下载某一范围内的角色分配。

1. 在 Azure 门户中，单击“所有服务”，然后选择要下载角色分配的范围。 例如，可以选择“管理组”、“订阅”、“资源组”或某个资源  。

1. 单击特定的资源。

1. 单击“访问控制(IAM)”。

1. 单击“下载角色分配(预览版)”以打开“下载角色分配”窗格。

    ![访问控制 - 下载角色分配](./media/role-assignments-list-portal/download-role-assignments.png)

1. 使用这些复选框来选择要包含在下载文件中的角色分配。

    - **继承** - 包含当前范围的继承角色分配。
    - **当前范围** - 包含当前范围的角色分配。
    - **子级** - 包含当前范围下各级别的角色分配。 对于管理组范围，此复选框处于禁用状态。

1. 选择文件格式，可以是逗号分隔值 (CSV) 或 JavaScript 对象表示法 (JSON)。

1. 指定文件名称。

1. 单击“开始”以开始下载。

    下面展示了每个文件格式的输出示例。

    ![按 CSV 格式下载角色分配](./media/role-assignments-list-portal/download-role-assignments-csv.png)

    ![按 CSV 格式下载角色分配](./media/role-assignments-list-portal/download-role-assignments-json.png)

## <a name="next-steps"></a>后续步骤

- [使用 Azure 门户添加或删除 Azure 角色分配](role-assignments-portal.md)
- [排查 Azure RBAC 问题](troubleshooting.md)
