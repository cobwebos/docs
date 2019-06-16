---
title: 列表拒绝使用 Azure 门户的 Azure 资源的分配 |Microsoft Docs
description: 了解如何列出用户、 组、 服务主体和管理的标识已被拒绝访问在特定范围内使用 Azure 门户的特定 Azure 资源操作。
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
ms.openlocfilehash: 21ffb1a2539a2e724a91dd3b2818270a5e573ef8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67127494"
---
# <a name="list-deny-assignments-for-azure-resources-using-the-azure-portal"></a>列表拒绝使用 Azure 门户的 Azure 资源的分配

即使角色分配向用户授予了访问权限，[拒绝分配](deny-assignments.md)也会阻止用户执行特定的 Azure 资源操作。 本文介绍如何列出拒绝使用 Azure 门户的分配。

> [!NOTE]
> 您不能直接创建您自己拒绝分配。 了解如何拒绝创建分配时，请参阅[拒绝分配](deny-assignments.md)。

## <a name="prerequisites"></a>必备组件

若要获取有关拒绝分配的信息，必须具有：

- `Microsoft.Authorization/denyAssignments/read` 在大多数中包含的权限[Azure 资源的内置角色](built-in-roles.md)。

## <a name="list-deny-assignments"></a>列表拒绝分配

请按照这些步骤，列出拒绝订阅或管理组作用域分配。

1. 在 Azure 门户中单击“所有服务”  ，然后选择“管理组”  或“订阅”。 

1. 单击你想要列出的订阅的管理组。

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

到列表，有关拒绝分配的其他详细信息，请执行以下步骤。

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

    | 操作类型 | 描述 |
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

* [了解 Azure 资源的拒绝分配](deny-assignments.md)
* [列表拒绝使用 Azure PowerShell 的 Azure 资源的分配](deny-assignments-powershell.md)
