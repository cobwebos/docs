---
title: 使用 Azure 门户创建 Azure 自定义角色 - Azure RBAC
description: 了解如何使用 Azure 门户和 Azure 基于角色的访问控制 (Azure RBAC) 创建 Azure 自定义角色。 这包括如何列出、创建、更新和删除自定义角色。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/13/2020
ms.author: rolyon
ms.openlocfilehash: 91d2605dddd6107d09e635969f5e5d98c2a02d60
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86511715"
---
# <a name="create-or-update-azure-custom-roles-using-the-azure-portal"></a>使用 Azure 门户创建 Azure 自定义角色

如果 [Azure 内置角色](built-in-roles.md)不能满足组织的具体需求，你可以创建自己的 Azure 自定义角色。 与内置角色一样，可将自定义角色分配到订阅和资源组范围内的用户、组与服务主体。 自定义角色存储在 Azure Active Directory (Azure AD) 目录中，可以在订阅之间共享。 每个目录最多可以有 5000 个自定义角色。 可以使用 Azure 门户、Azure PowerShell、Azure CLI 或 REST API 创建自定义角色。 本文介绍如何使用 Azure 门户创建自定义角色。

## <a name="prerequisites"></a>先决条件

若要创建自定义角色，需要具备以下条件：

- 有权创建自定义角色，例如[所有者](built-in-roles.md#owner)或[用户访问管理员](built-in-roles.md#user-access-administrator)

## <a name="step-1-determine-the-permissions-you-need"></a>步骤 1：确定所需的权限

Azure 具有数千个权限，你可将这些权限包含在自定义角色中。 可通过以下四种方法来确定要添加到自定义角色的权限：

| 方法 | 说明 |
| --- | --- |
| 查看现有角色 | 可以查看现有的角色，以了解正在使用哪些权限。 有关详细信息，请参阅 [Azure 内置角色](built-in-roles.md)。 |
| 按关键字搜索权限 | 使用 Azure 门户创建自定义角色时，可以按关键字搜索权限。 例如，可以搜索“虚拟机”或“计费”权限。  有关此搜索功能的介绍，请参阅后面的[步骤 4：](#step-4-permissions)权限”。 |
| 下载所有权限 | 使用 Azure 门户创建自定义角色时，可将所有权限作为 CSV 文件下载，然后搜索此文件。 在“添加权限”窗格中，单击“下载所有权限”按钮下载所有权限。  有关“添加权限”窗格的详细信息，请参阅[步骤 4：](#step-4-permissions)权限”。 |
| 查看文档中的权限 | 可以在 [Azure 资源管理器资源提供程序操作](resource-provider-operations.md)中查看可用的权限。 |

## <a name="step-2-choose-how-to-start"></a>步骤 2：选择如何开始

可通过三种方法开始创建自定义角色。 可以克隆现有角色、从头开始创建或者从 JSON 文件开始。 最简单的方法是找到拥有大部分所需权限的现有角色，然后根据自己的方案克隆并修改该角色。 

### <a name="clone-a-role"></a>克隆角色

如果现有角色并非恰好拥有你所需的全部权限，你可以克隆该角色，然后修改权限。 遵循以下步骤开始克隆角色。

1. 在 Azure 门户中，打开你希望可在其中分配自定义角色的订阅或资源组，然后打开“访问控制(IAM)”。

    以下屏幕截图显示了针对订阅打开的“访问控制(IAM)”页。

    ![订阅的“访问控制(IAM)”页](./media/custom-roles-portal/access-control-subscription.png)

1. 单击“角色”选项卡以查看包含所有内置角色和自定义角色的列表。

1. 搜索要克隆的角色，例如“计费读取者”角色。

1. 单击行尾的省略号 ( **...** )，然后单击“克隆”。

    ![“克隆”上下文菜单](./media/custom-roles-portal/clone-menu.png)

    此时会打开自定义角色编辑器，其中已选择“克隆角色”选项。

1. 转到[步骤 3：基本信息](#step-3-basics)。

### <a name="start-from-scratch"></a>从头开始

如果需要，可以遵循以下步骤从头开始创建自定义角色。

1. 在 Azure 门户中，打开你希望可在其中分配自定义角色的订阅或资源组，然后打开“访问控制(IAM)”。

1. 依次单击“添加”、“添加自定义角色”。 

    ![“添加自定义角色”菜单](./media/custom-roles-portal/add-custom-role-menu.png)

    此时会打开自定义角色编辑器，其中已选择“从头开始”选项。

1. 转到[步骤 3：基本信息](#step-3-basics)。

### <a name="start-from-json"></a>从 JSON 开始

如果需要，可以在 JSON 文件中指定大部分自定义角色值。 可以在自定义角色编辑器中打开该文件，进行其他更改，然后创建自定义角色。 遵循以下步骤从 JSON 文件开始创建。

1. 创建采用以下格式的 JSON 文件：

    ```json
    {
        "properties": {
            "roleName": "",
            "description": "",
            "assignableScopes": [],
            "permissions": [
                {
                    "actions": [],
                    "notActions": [],
                    "dataActions": [],
                    "notDataActions": []
                }
            ]
        }
    }
    ```

1. 在该 JSON 文件中指定各个属性的值。 下面是添加了一些值的示例。 有关不同属性的信息，请参阅[了解 Azure 角色定义](role-definitions.md)。

    ```json
    {
        "properties": {
            "roleName": "Billing Reader Plus",
            "description": "Read billing data and download invoices",
            "assignableScopes": [
                "/subscriptions/11111111-1111-1111-1111-111111111111"
            ],
            "permissions": [
                {
                    "actions": [
                        "Microsoft.Authorization/*/read",
                        "Microsoft.Billing/*/read",
                        "Microsoft.Commerce/*/read",
                        "Microsoft.Consumption/*/read",
                        "Microsoft.Management/managementGroups/read",
                        "Microsoft.CostManagement/*/read",
                        "Microsoft.Support/*"
                    ],
                    "notActions": [],
                    "dataActions": [],
                    "notDataActions": []
                }
            ]
        }
    }
    ```
    
1. 在 Azure 门户中，打开“访问控制(IAM)”页。

1. 依次单击“添加”、“添加自定义角色”。 

    ![“添加自定义角色”菜单](./media/custom-roles-portal/add-custom-role-menu.png)

    此时会打开自定义角色编辑器。

1. 在“基本信息”选项卡上的“基线权限”中，选择“从 JSON 开始”。 

1. 在“选择文件”框的旁边，单击文件夹按钮打开“打开”对话框。

1. 选择你的 JSON 文件，然后单击“打开”。

1. 转到[步骤 3：基本信息](#step-3-basics)。

## <a name="step-3-basics"></a>步骤 3：基础知识

在“基本信息”选项卡上，指定自定义角色的名称、说明和基线权限。

1. 在“自定义角色名称”框中，指定自定义角色的名称。 该名称必须在 Azure AD 目录中唯一。 该名称可以包含字母、数字、空格和特殊字符。

1. 在“说明”框中，指定自定义角色的可选说明。 此说明将用作该自定义角色的工具提示。

    此时应该已根据上一步骤设置了“基线权限”选项，不过可对其进行更改。

    ![已指定了值的“基本信息”选项卡](./media/custom-roles-portal/basics-values.png)

## <a name="step-4-permissions"></a>步骤 4：权限

在“权限”选项卡上，指定自定义角色的权限。 根据你是克隆了角色还是从 JSON 开始创建，“权限”选项卡可能已列出了一些权限。

![创建自定义角色时的“权限”选项卡](./media/custom-roles-portal/permissions.png)

### <a name="add-or-remove-permissions"></a>添加或删除权限

遵循以下步骤为自定义角色添加或删除权限。

1. 若要添加权限，请单击“添加权限”打开“添加权限”窗格。

    此窗格将列出所有可用权限，这些权限以卡片格式分组成不同的类别。 每个类别代表一个资源提供程序（提供 Azure 资源的服务）。

1. 在“搜索权限”框中，键入一个字符串以搜索权限。 例如，搜索“发票”可以查找与发票相关的权限。

    系统会根据搜索字符串显示资源提供程序卡片的列表。 有关资源提供程序到 Azure 服务的映射列表，请参阅 [Azure 服务的资源提供程序](../azure-resource-manager/management/azure-services-resource-providers.md)。

    ![包含资源提供程序的“添加权限”窗格](./media/custom-roles-portal/add-permissions-provider.png)

1. 单击可能包含你要添加到自定义角色的权限的资源提供程序卡片，例如“Microsoft 计费”。

    系统会根据搜索字符串显示该资源提供程序的管理权限列表。

    ![“添加权限”列表](./media/custom-roles-portal/add-permissions-list.png)

1. 若要查找适用于数据平面的权限，请单击“数据操作”。 否则，请将操作切换开关保持设置为“操作”，以列出适用于管理平面的权限。 有关管理平面与数据平面之间的差异的详细信息，请参阅[管理和数据操作](role-definitions.md#management-and-data-operations)。

1. 如有需要，请更新搜索字符串以进一步具体化搜索。

1. 找到要添加到自定义角色的一个或多个权限后，请在这些权限旁边添加复选标记。 例如，在“其他:下载发票”旁边添加复选标记可添加用于下载发票的权限。

1. 单击“添加”，以将权限添加到权限列表。

    权限将作为 `Actions` 或 `DataActions` 添加。

    ![已添加权限](./media/custom-roles-portal/permissions-list-add.png)

1. 若要删除权限，请单击行尾的删除图标。 在此示例中，由于用户不需要获得创建支持票证的功能，因此可以删除 `Microsoft.Support/*` 权限。

### <a name="add-wildcard-permissions"></a>添加通配符权限

根据所选的开始创建角色的方式，权限列表中可能存在包含通配符 (`*`) 的权限。 通配符（ `*` ）将权限扩展到与所提供的操作字符串匹配的所有内容。 例如，以下通配符字符串添加了与 Azure 成本管理和导出相关的所有权限。 此通配符权限还包括将来可能要添加的任何导出权限。

```
Microsoft.CostManagement/exports/*
```

不能使用“添加权限”窗格添加新的通配符权限。 若要添加通配符权限，必须使用“JSON”选项卡手动添加。有关详细信息，请参阅[步骤 6：JSON](#step-6-json)。

### <a name="exclude-permissions"></a>排除权限

如果角色拥有通配符 (`*`) 权限，而你想要从该通配符权限中排除或减去特定的权限，你可以排除权限。 例如，假设你有以下通配符权限：

```
Microsoft.CostManagement/exports/*
```

如果你不希望允许删除导出权限，可以排除以下删除权限：

```
Microsoft.CostManagement/exports/delete
```

排除某个权限时，它将作为 `NotActions` 或 `NotDataActions` 添加。 有效的管理权限是通过累加所有 `Actions`，然后减去所有 `NotActions` 计算得出的。 有效的数据权限是通过累加所有 `DataActions`，然后减去所有 `NotDataActions` 计算得出的。

> [!NOTE]
> 排除权限不同于“拒绝”。 排除权限只是一种从通配符权限中减去权限的简便方法。

1. 若要从允许的通配符权限中排除或减去权限，请单击“排除权限”打开“排除权限”窗格。

    在此窗格中，指定要排除或减去的管理权限或数据权限。

1. 找到一个或多个要排除的权限后，在这些权限旁边添加复选标记，然后单击“添加”按钮。

    ![“排除权限”窗格 - 已选定权限](./media/custom-roles-portal/exclude-permissions-select.png)

    权限将作为 `NotActions` 或 `NotDataActions` 添加。

    ![已排除权限](./media/custom-roles-portal/exclude-permissions-list-add.png)

## <a name="step-5-assignable-scopes"></a>步骤 5：可分配范围

在“可分配范围”选项卡上，指定自定义角色在何处可供分配，例如订阅或资源组。 根据所选的开始创建角色的方式，此选项卡可能会列出你打开“访问控制(IAM)”页时所处的范围。 不支持将可分配范围设置为根范围（“/”）。 目前无法将管理组添加为可分配范围。

1. 单击“添加可分配范围”打开“添加可分配范围”窗格。

    ![“可分配范围”选项卡](./media/custom-roles-portal/assignable-scopes.png)

1. 单击要使用的一个或多个范围（通常是你的订阅）。

    ![添加可分配范围](./media/custom-roles-portal/add-assignable-scopes.png)

1. 单击“添加”按钮添加可分配范围。

## <a name="step-6-json"></a>步骤 6：JSON

在“JSON”选项卡上，可以看到 JSON 格式的自定义角色。 如果需要，可以直接编辑 JSON。 若要添加通配符 (`*`) 权限，必须使用此选项卡。

1. 若要编辑 JSON，请单击“编辑”。

    ![显示自定义角色的“JSON”选项卡](./media/custom-roles-portal/json.png)

1. 对 JSON 进行更改。

    如果 JSON 格式不正确，在竖槽中会显示红色的锯齿线和指示器。

1. 完成编辑后，单击“保存”。

## <a name="step-7-review--create"></a>步骤 7：查看 + 创建

在“查看 + 创建”选项卡上，可以查看自定义角色设置。

1. 查看自定义角色设置。

    ![“查看 + 创建”选项卡](./media/custom-roles-portal/review-create.png)

1. 单击“创建”以创建自定义角色。

    片刻之后，会出现一个消息框，指出已成功创建自定义角色。

    ![“创建自定义角色”消息](./media/custom-roles-portal/custom-role-success.png)

    如果检测到任何错误，将显示一条消息。

    ![“查看 + 创建”中的错误](./media/custom-roles-portal/review-create-error.png)

1. 在“角色”列表中查看新的自定义角色。 如果未看到你的自定义角色，请单击“刷新”。

     自定义角色可能需要几分钟的时间才能显示在每个位置。

## <a name="list-custom-roles"></a>列出自定义角色

遵循以下步骤查看自定义角色。

1. 打开订阅或资源组，然后打开“访问控制(IAM)”。

1. 单击“角色”选项卡以查看包含所有内置角色和自定义角色的列表。

1. 在“类型”列表中选择“CustomRole”，以便仅查看你的自定义角色。 

    如果你刚刚创建了自定义角色，但在列表中未看到它，请单击“刷新”。

    ![自定义角色列表](./media/custom-roles-portal/custom-role-list.png)

## <a name="update-a-custom-role"></a>更新自定义角色

1. 如本文前面所述，打开自定义角色列表。

1. 单击要更新的自定义角色对应的省略号 ( **...** )，然后单击“编辑”。 请注意，无法更新内置角色。

    该自定义角色将在编辑器中打开。

    ![自定义角色菜单](./media/custom-roles-portal/edit-menu.png)

1. 使用不同的选项卡更新自定义角色。

1. 完成更改后，单击“查看 + 创建”选项卡查看所做的更改。

1. 单击“更新”按钮以更新自定义角色。

## <a name="delete-a-custom-role"></a>删除自定义角色

1. 如本文前面所述，打开自定义角色列表。

1. 删除使用自定义角色的任何角色分配。

1. 单击要删除的自定义角色对应的省略号 ( **...** )，然后单击“删除”。

    ![自定义角色菜单](./media/custom-roles-portal/delete-menu.png)

    可能需要几分钟的时间才能完全删除你的自定义角色。

## <a name="next-steps"></a>后续步骤

- [教程：使用 Azure PowerShell 创建 Azure 自定义角色](tutorial-custom-role-powershell.md)
- [Azure 自定义角色](custom-roles.md)
- [Azure 资源管理器资源提供程序操作](resource-provider-operations.md)
