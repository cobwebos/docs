---
title: 使用 Azure 门户（预览） 创建或更新 Azure 自定义角色 - Azure RBAC
description: 了解如何使用 Azure 门户为基于 Azure 角色的访问控制 （Azure RBAC） 创建 Azure 自定义角色。 这包括如何列出、创建、更新和删除自定义角色。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/26/2020
ms.author: rolyon
ms.openlocfilehash: 3204cdf51f3f37588f684f801a811f569b337d13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77674865"
---
# <a name="create-or-update-azure-custom-roles-using-the-azure-portal-preview"></a>使用 Azure 门户创建或更新 Azure 自定义角色（预览）

> [!IMPORTANT]
> 使用 Azure 门户的 Azure 自定义角色当前处于公共预览中。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

如果[Azure 内置角色](built-in-roles.md)不能满足组织的特定需求，则可以创建自己的 Azure 自定义角色。 与内置角色一样，您可以将自定义角色分配给订阅和资源组作用域中的用户、组和服务主体。 自定义角色存储在 Azure Active Directory (Azure AD) 目录中，可以在订阅之间共享。 每个目录最多可以有 5000 个自定义角色。 可以使用 Azure 门户、Azure PowerShell、Azure CLI 或 REST API 创建自定义角色。 本文介绍如何使用 Azure 门户（当前预览版）创建自定义角色。

## <a name="prerequisites"></a>先决条件

若要创建自定义角色，需要具备以下条件：

- 有权创建自定义角色，例如[所有者](built-in-roles.md#owner)或[用户访问管理员](built-in-roles.md#user-access-administrator)

## <a name="step-1-determine-the-permissions-you-need"></a>第 1 步：确定所需的权限

Azure 具有数千个权限，您可以将其包含在自定义角色中。 以下是确定要添加到自定义角色的权限的四种方法：

| 方法 | 描述 |
| --- | --- |
| 查看现有角色 | 您可以查看现有角色，查看正在使用的权限。 有关详细信息，请参阅[Azure 内置角色](built-in-roles.md)。 |
| 按关键字搜索权限 | 使用 Azure 门户创建自定义角色时，可以按关键字搜索权限。 例如，您可以搜索*虚拟机*或*计费*权限。 此搜索功能在[步骤 4： 权限](#step-4-permissions)中稍后描述。 |
| 下载所有权限 | 使用 Azure 门户创建自定义角色时，可以将所有权限下载为 CSV 文件，然后搜索此文件。 在"**添加权限"** 窗格中，单击"**下载所有权限**"按钮以下载所有权限。 有关"添加权限"窗格的详细信息，请参阅[步骤 4：权限](#step-4-permissions)。 |
| 查看文档中的权限 | 您可以在[Azure 资源管理器资源管理器资源管理器操作](resource-provider-operations.md)中查看可用权限。 |

## <a name="step-2-choose-how-to-start"></a>第 2 步：选择如何开始

有三种方法可以开始创建自定义角色。 您可以克隆现有角色、从头开始或从 JSON 文件开始。 最简单的方法是查找具有您所需的大部分权限的现有角色，然后为您的方案克隆和修改它。 

### <a name="clone-a-role"></a>克隆角色

如果现有角色没有所需的权限，则可以克隆它，然后修改权限。 按照以下步骤开始克隆角色。

1. 在 Azure 门户中，打开希望自定义角色可分配的订阅或资源组，然后打开**访问控件 （IAM）。**

    以下屏幕截图显示了为订阅打开的访问控制 （IAM） 页面。

    ![订阅的访问控制 （IAM） 页](./media/custom-roles-portal/access-control-subscription.png)

1. 单击“角色”选项卡以查看包含所有内置角色和自定义角色的列表****。

1. 搜索要克隆的角色，如计费阅读器角色。

1. 在行的末尾，单击省略号 （**...），** 然后单击 **"克隆**"。

    ![克隆上下文菜单](./media/custom-roles-portal/clone-menu.png)

    这将打开自定义角色编辑器，并选择 **"克隆"角色**选项。

1. 继续[步骤 3：基础知识](#step-3-basics)。

### <a name="start-from-scratch"></a>从头开始

如果您愿意，可以按照以下步骤从头开始自定义角色。

1. 在 Azure 门户中，打开希望自定义角色可分配的订阅或资源组，然后打开**访问控件 （IAM）。**

1. 单击 **"添加**"，然后单击"**添加自定义角色（预览）"。**

    ![添加自定义角色菜单](./media/custom-roles-portal/add-custom-role-menu.png)

    这将打开自定义角色编辑器，并选择"**从头开始**"选项。

1. 继续[步骤 3：基础知识](#step-3-basics)。

### <a name="start-from-json"></a>从 JSON 开始

如果您愿意，可以在 JSON 文件中指定大多数自定义角色值。 您可以在自定义角色编辑器中打开该文件，进行其他更改，然后创建自定义角色。 按照以下步骤从 JSON 文件开始。

1. 创建具有以下格式的 JSON 文件：

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

1. 在 JSON 文件中，指定各种属性的值。 下面是添加了一些值的示例。 有关不同属性的信息，请参阅[了解角色定义](role-definitions.md)。

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
    
1. 在 Azure 门户中，打开**访问控件 （IAM）** 页。

1. 单击 **"添加**"，然后单击"**添加自定义角色（预览）"。**

    ![添加自定义角色菜单](./media/custom-roles-portal/add-custom-role-menu.png)

    这将打开自定义角色编辑器。

1. 在"基本"选项卡上，在 **"基线"权限**中，选择**从 JSON 开始**。

1. 在"选择文件框"旁边，单击文件夹按钮以打开"打开"对话框。

1. 选择您的 JSON 文件，然后单击 **"打开**"。

1. 继续[步骤 3：基础知识](#step-3-basics)。

## <a name="step-3-basics"></a>第 3 步：基础知识

在 **"基本"** 选项卡上，您可以指定自定义角色的名称、说明和基线权限。

1. 在 **"自定义角色名称"** 框中，为自定义角色指定名称。 该名称对于 Azure AD 目录必须是唯一的。 名称可以包括字母、数字、空格和特殊字符。

1. 在 **"描述"** 框中，为自定义角色指定可选说明。 这将成为自定义角色的工具提示。

    应根据上一步设置 **"基线权限**"选项，但您可以更改。

    ![指定值的基础知识选项卡](./media/custom-roles-portal/basics-values.png)

## <a name="step-4-permissions"></a>第 4 步：权限

在 **"权限"** 选项卡上，您可以指定自定义角色的权限。 根据是克隆角色还是从 JSON 开始，"权限"选项卡可能已经列出了一些权限。

![创建自定义角色的权限选项卡](./media/custom-roles-portal/permissions.png)

### <a name="add-or-remove-permissions"></a>添加或删除权限

按照以下步骤添加或删除自定义角色的权限。

1. 要添加权限，请单击"**添加权限**"以打开"添加权限"窗格。

    此窗格列出以卡片格式分组到不同类别的所有可用权限。 每个类别表示*一个资源提供程序*，它是提供 Azure 资源的服务。

1. 在 **"搜索权限**"框中，键入字符串以搜索权限。 例如，搜索*发票*以查找与发票相关的权限。

    资源提供程序卡的列表将根据您的搜索字符串显示。 有关资源提供程序如何映射到 Azure 服务的列表，请参阅[Azure 服务的资源提供程序](../azure-resource-manager/management/azure-services-resource-providers.md)。

    ![使用资源提供程序添加权限窗格](./media/custom-roles-portal/add-permissions-provider.png)

1. 单击可能具有要添加到自定义角色的权限的资源提供商卡片，例如**Microsoft 计费**。

    根据搜索字符串显示该资源提供程序的管理权限列表。

    ![添加权限列表](./media/custom-roles-portal/add-permissions-list.png)

1. 如果要查找应用于数据平面的权限，请单击"**数据操作**"。 否则，将操作切换设置为 **"操作"** 以列出应用于管理平面的权限。 有关详细信息，请参阅管理平面和数据平面之间的差异，请参阅[管理和数据操作](role-definitions.md#management-and-data-operations)。

1. 如有必要，请更新搜索字符串以进一步优化搜索。

1. 找到要添加到自定义角色的一个或多个权限后，请在权限旁边添加复选标记。 例如，在 **"其他：下载发票"** 旁边添加复选标记以添加下载发票的权限。

1. 单击"**添加**"可将权限添加到权限列表中。

    权限将添加为`Actions`或 。 `DataActions`

    ![已添加权限](./media/custom-roles-portal/permissions-list-add.png)

1. 要删除权限，请单击行末尾的删除图标。 在此示例中，由于用户不需要创建支持票证的能力，因此可以删除`Microsoft.Support/*`权限。

### <a name="add-wildcard-permissions"></a>添加通配符权限

根据选择开始的方式，在权限列表中可能具有通配符 （\*） 的权限。 通配符\*（ ） 将权限扩展到与您提供的字符串匹配的所有内容。 例如，假设您要添加与 Azure 成本管理和导出相关的所有权限。 您可以添加所有这些权限：

```
Microsoft.CostManagement/exports/action
Microsoft.CostManagement/exports/read
Microsoft.CostManagement/exports/write
Microsoft.CostManagement/exports/delete
Microsoft.CostManagement/exports/run/action
```

您可以添加通配符权限，而不是添加所有这些权限。 例如，以下通配符权限等效于前五个权限。 这还包括将来可能添加的任何导出权限。

```
Microsoft.CostManagement/exports/*
```

如果要添加新的通配符权限，则无法使用"**添加权限"** 窗格添加该权限。 要添加通配符权限，必须使用**JSON**选项卡手动添加它。有关详细信息，请参阅步骤[6：JSON](#step-6-json)。

### <a name="exclude-permissions"></a>排除权限

如果您的角色具有通配符 （\*） 权限，并且希望从该通配符权限中排除或减去特定权限，则可以排除这些权限。 例如，假设您具有以下通配符权限：

```
Microsoft.CostManagement/exports/*
```

如果不想允许删除导出，可以排除以下删除权限：

```
Microsoft.CostManagement/exports/delete
```

排除权限时，将将其添加为`NotActions`或`NotDataActions`。 有效的管理权限是通过添加所有 ，`Actions`然后减去 所有 。 `NotActions` 通过添加所有 ，`DataActions`然后减去 所有 ，`NotDataActions`计算有效的数据权限。

> [!NOTE]
> 排除权限与拒绝不同。 排除权限只是从通配符权限中减去权限的便捷方法。

1. 要从允许的通配符权限中排除或减去权限，请单击"**排除权限**"以打开"排除权限"窗格。

    在此窗格中，指定排除或减去的管理或数据权限。

1. 找到要排除的一个或多个权限后，请在权限旁边添加复选标记，然后单击"**添加**"按钮。

    ![排除权限窗格 - 选择权限](./media/custom-roles-portal/exclude-permissions-select.png)

    权限将添加为`NotActions`或`NotDataActions`。

    ![排除权限](./media/custom-roles-portal/exclude-permissions-list-add.png)

## <a name="step-5-assignable-scopes"></a>第 5 步：可分配作用域

在"**可分配作用域"** 选项卡上，可以指定自定义角色可用于分配的位置，例如订阅或资源组。 根据您选择的开始方式，此选项卡可能会列出打开访问控件 （IAM） 页的范围。 不支持将可分配的范围设置为根作用域（"/"）。 对于此预览，不能将管理组添加为可分配作用域。

1. 单击"**添加可分配作用域**"以打开"添加可分配作用域"窗格。

    ![可分配作用域选项卡](./media/custom-roles-portal/assignable-scopes.png)

1. 单击要使用的一个或多个作用域，通常是您的订阅。

    ![添加可分配作用域](./media/custom-roles-portal/add-assignable-scopes.png)

1. 单击"**添加**"按钮可添加可分配作用域。

## <a name="step-6-json"></a>第 6 步：JSON

在 **"JSON"** 选项卡上，您可以看到在 JSON 中格式化的自定义角色。 如果需要，可以直接编辑 JSON。 如果要添加通配符 （\*） 权限，则必须使用此选项卡。

1. 要编辑 JSON，请单击"**编辑**"。

    ![显示自定义角色的 JSON 选项卡](./media/custom-roles-portal/json.png)

1. 对 JSON 进行更改。

    如果 JSON 的格式不正确，您将在垂直排水沟中看到一条红色锯齿线和一个指示器。

1. 完成编辑后，单击"**保存**"。

## <a name="step-7-review--create"></a>第 7 步：查看 + 创建

在 **"审阅 + 创建"** 选项卡上，您可以查看自定义角色设置。

1. 查看自定义角色设置。

    ![“查看 + 创建”选项卡](./media/custom-roles-portal/review-create.png)

1. 单击"**创建**"以创建自定义角色。

    几分钟后，将显示一个消息框，指示您的自定义角色已成功创建。

    ![创建自定义角色消息](./media/custom-roles-portal/custom-role-success.png)

    如果检测到任何错误，将显示一条消息。

    ![查看 = 创建错误](./media/custom-roles-portal/review-create-error.png)

1. 在 **"角色**"列表中查看新的自定义角色。 如果看不到自定义角色，请单击"**刷新**"。

     自定义角色可能需要几分钟时间才能显示到所有位置。

## <a name="list-custom-roles"></a>列出自定义角色

按照以下步骤查看自定义角色。

1. 打开订阅或资源组，然后打开**访问控制 （IAM）。**

1. 单击“角色”选项卡以查看包含所有内置角色和自定义角色的列表****。

1. 在 **"类型"** 列表中，选择 **"自定义角色"** 以仅查看自定义角色。

    如果您刚刚创建了自定义角色，但列表中未看到它，请单击"**刷新**"。

    ![自定义角色列表](./media/custom-roles-portal/custom-role-list.png)

## <a name="update-a-custom-role"></a>更新自定义角色

1. 如本文前面所述，打开自定义角色列表。

1. 单击要更新的自定义角色的省略号 （**...），** 然后单击"**编辑**"。 请注意，您无法更新内置角色。

    自定义角色在编辑器中打开。

    ![自定义角色菜单](./media/custom-roles-portal/edit-menu.png)

1. 使用不同的选项卡更新自定义角色。

1. 完成更改后，单击"审阅 +**创建**"选项卡以查看更改。

1. 单击 **"更新**"按钮以更新自定义角色。

## <a name="delete-a-custom-role"></a>删除自定义角色

1. 如本文前面所述，打开自定义角色列表。

1. 删除使用自定义角色的任何角色分配。

1. 单击要删除的自定义角色的省略号 （**...），** 然后单击"**删除**"。

    ![自定义角色菜单](./media/custom-roles-portal/delete-menu.png)

    完全删除自定义角色可能需要几分钟时间。

## <a name="next-steps"></a>后续步骤

- [教程：使用 Azure PowerShell 创建自定义角色](tutorial-custom-role-powershell.md)
- [Azure 中的自定义角色](custom-roles.md)
- [Azure 资源管理器资源管理器提供程序操作](resource-provider-operations.md)
