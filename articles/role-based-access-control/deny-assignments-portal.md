---
title: 使用 Azure 门户列出 Azure 拒绝分配-Azure RBAC
description: 了解如何使用 Azure 门户和 Azure 基于角色的访问控制（Azure RBAC）来列出已拒绝使用特定范围内的特定 Azure 资源操作的用户、组、服务主体和管理标识。
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
ms.date: 06/10/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 97c03d417f8bf123de3332142344f292de00e3b2
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2020
ms.locfileid: "82734104"
---
# <a name="list-azure-deny-assignments-using-the-azure-portal"></a>使用 Azure 门户列出 Azure 拒绝分配

[Azure 拒绝分配](deny-assignments.md)会阻止用户执行特定的 Azure 资源操作，即使角色分配授予访问权限。 本文介绍如何使用 Azure 门户列出拒绝分配。

> [!NOTE]
> 不能直接创建自己的拒绝分配。 有关如何创建拒绝分配的信息，请参阅[Azure 拒绝分配](deny-assignments.md)。

## <a name="prerequisites"></a>必备条件

如要获取拒绝分配的相关信息，必须具有：

- `Microsoft.Authorization/denyAssignments/read`权限包括在大多数[Azure 内置角色](built-in-roles.md)中。

## <a name="list-deny-assignments"></a>列出拒绝分配

请执行以下步骤，在订阅或管理组范围列出拒绝分配。

1. 在 Azure 门户中单击“所有服务”  ，然后选择“管理组”  或“订阅”。 

1. 单击要列出的管理组或订阅。

1. 单击“访问控制(IAM)”  。

1. 单击“拒绝分配”选项卡（或单击“查看拒绝分配”磁贴上的“视图”按钮）   。

    如果此范围内有任何拒绝分配或继承到此范围的角色，则会将其列出。

    ![访问控制 -“拒绝分配”选项卡](./media/deny-assignments-portal/access-control-deny-assignments.png)

1. 若要显示其他列，请单击“编辑列”。 

    ![拒绝分配 - 列](./media/deny-assignments-portal/deny-assignments-columns.png)

    |  |  |
    | --- | --- |
    | **名称** | 拒绝分配的名称。 |
    | **主体类型** | 用户、组、系统定义的组或服务主体。 |
    | **拒绝**  | 包括在拒绝分配中的安全主体的名称。 |
    | **Id** | 拒绝分配的唯一标识符。 |
    | **排除的主体** | 是否有排除在拒绝分配之外的安全主体。 |
    | **不适用于儿童** | 是否已将拒绝分配继承到子范围。 |
    | **受系统保护** | 拒绝分配是否由 Azure 托管。 当前始终为“是”。 |
    | **范围** | 管理组、订阅、资源组或资源。 |

1. 将一个复选标记添加到任何已启用的项，然后单击“确定”以显示所选列。 

## <a name="list-details-about-a-deny-assignment"></a>列出有关拒绝分配的详细信息

执行以下步骤，以便列出有关拒绝分配的更多详细信息。

1. 按照上一部分的说明打开“拒绝分配”窗格。 

1. 单击拒绝分配名称，打开“用户”边栏选项卡。 

    ![拒绝分配 - 用户](./media/deny-assignments-portal/deny-assignment-users.png)

    “用户”边栏选项卡包括下述两个部分。 

    |  |  |
    | --- | --- |
    | **拒绝分配适用于**  | 拒绝分配应用到的安全主体。 |
    | **拒绝分配排除** | 排除在拒绝分配之外的安全主体。 |

    **系统定义的主体**代表 Azure AD 目录中的所有用户、组、服务主体和托管标识。

1. 若要查看已拒绝权限的列表，请单击“已拒绝权限”。 

    ![拒绝分配 - 已拒绝权限](./media/deny-assignments-portal/deny-assignment-denied-permissions.png)

    | 操作类型 | 说明 |
    | --- | --- |
    | **操作**  | 已拒绝管理操作。 |
    | **不操作** | 从已拒绝管理操作中排除的管理操作。 |
    | **DataActions**  | 已拒绝数据操作。 |
    | **NotDataActions** | 从已拒绝数据操作中排除的数据操作。 |

    就上一屏幕截图中显示的示例来说，下面是有效的权限：

    - 数据平面上的所有存储操作都已拒绝，计算操作除外。

1. 若要查看拒绝分配的属性，请单击“属性”。 

    ![拒绝分配 - 属性](./media/deny-assignments-portal/deny-assignment-properties.png)

    在“属性”边栏选项卡上，可以看到拒绝分配名称、ID、说明和范围。  “不适用于儿童”开关指示是否已将拒绝分配继承到子范围。  “受系统保护”开关指示此拒绝分配是否由 Azure 托管。  当前，所有情况下此项均为“是”。 

## <a name="next-steps"></a>后续步骤

* [了解 Azure 拒绝分配](deny-assignments.md)
* [使用 Azure PowerShell 列出 Azure 拒绝分配](deny-assignments-powershell.md)
