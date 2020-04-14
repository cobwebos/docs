---
title: 创建管理组以组织资源 - Azure 治理
description: 了解如何使用门户、Azure PowerShell 和 Azure CLI 创建 Azure 管理组以管理多个资源。
ms.date: 12/18/2019
ms.topic: conceptual
ms.openlocfilehash: a06679432d795b5b7854af8dc66b468841978e9c
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273182"
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>创建用来组织和管理资源的管理组

管理组是一些容器，可以帮助跨多个订阅管理访问权限、策略和符合性。 可以创建这些容器来构建可以与 [Azure Policy](../policy/overview.md) 和 [Azure 基于角色的访问控制](../../role-based-access-control/overview.md)配合使用的有效且高效的层次结构。 若要详细了解管理组，请参阅[使用 Azure 管理组整理资源](overview.md)。

在目录中创建的第一个管理组可能需要最多 15 分钟才能完成。 一些进程会首次运行以在 Azure 中为目录设置管理组服务。 在进程完成后将显示通知。 有关详细信息，请参阅[管理组的初始设置](./overview.md#initial-setup-of-management-groups)。 

## <a name="create-a-management-group"></a>创建管理组

租户中的任何 Azure AD 用户可以创建管理组，而无需分配给该用户的管理组写入权限。  此新管理组将是根管理组的子组，创建者将获得"所有者"角色分配。 管理组服务允许此功能，因此在根级别不需要角色分配。 创建根管理组时，没有用户有权访问该组。  为了避免找到 Azure AD 全局管理员开始使用管理组的障碍，我们允许在根级别创建初始管理组。      

可以使用门户、[资源管理器模板](../../azure-resource-manager/templates/deploy-to-tenant.md#create-management-group)、PowerShell 或 Azure CLI 创建管理组。

### <a name="create-in-portal"></a>在门户中创建

1. 登录到[Azure 门户](https://portal.azure.com)。

1. 选择 **"所有服务** > **管理 + 治理**"。

1. 选择**成本管理和计费**

1. 在“成本管理 + 计费 - 管理组”页上，选择“管理组”****

1. 选择“+ 添加管理组”。****

   ![管理组操作页](./media/main.png)

1. 填写管理组 ID 字段。

   - ****“管理组 ID”是用来在此管理组上提交命令的目录唯一标识符。 此标识符一旦创建便无法再编辑，因为它用来在整个 Azure 系统中标识这个组。 [根管理组](overview.md#root-management-group-for-each-directory)是自动创建的，其 ID 为 Azure Active Directory ID。 对于所有其他管理组，请分配唯一的 ID。
   - 显示名称字段是在 Azure 门户中显示的名称。 创建管理组时，单独的显示名称是一个可选字段，并且可以随时更改。  

   ![用于创建新管理组的“选项”窗格](./media/create_context_menu.png)  

1. 选择“保存”。 

### <a name="create-in-powershell"></a>在 PowerShell 中创建

在 PowerShell 中，使用 [New-AzManagementGroup](/powershell/module/az.resources/new-azmanagementgroup) cmdlet 创建新的管理组。

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso'
```

**GroupName** 是要创建的唯一标识符。 此 ID 由其他命令用来引用此组，并且以后无法更改。

如果希望管理组在 Azure 门户中显示其他名称，则添加 **"显示名称"** 参数。 例如，若要创建 GroupName 为 Contoso 且显示名称为“Contoso Group”的管理组，请使用以下 cmdlet：

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso' -DisplayName 'Contoso Group'
```

在上述示例中，新的管理组是在根管理组下创建的。 若要指定一个不同的管理组作为父级，请使用 **ParentId** 参数。

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName Contoso
New-AzManagementGroup -GroupName 'ContosoSubGroup' -ParentId $parentGroup.id
```

### <a name="create-in-azure-cli"></a>在 Azure CLI 中创建

在 Azure CLI 中，使用 [az account management-group create](/cli/azure/account/management-group?view=azure-cli-latest#az-account-management-group-create) 命令创建新的管理组。

```azurecli-interactive
az account management-group create --name Contoso
```

**name** 是要创建的唯一标识符。 此 ID 由其他命令用来引用此组，并且以后无法更改。

如果希望管理组在 Azure 门户中显示一个不同的名称，请添加 **display-name** 参数。 例如，若要创建 GroupName 为 Contoso 且显示名称为“Contoso Group”的管理组，请使用以下命令：

```azurecli-interactive
az account management-group create --name Contoso --display-name 'Contoso Group'
```

在上述示例中，新的管理组是在根管理组下创建的。 若要指定一个不同的管理组作为父级，请使用 **parent** 参数并提供父组的名称。

```azurecli-interactive
az account management-group create --name ContosoSubGroup --parent Contoso
```

## <a name="next-steps"></a>后续步骤

若要了解有关管理组的详细信息，请参阅：

- [创建用于整理 Azure 资源的管理组](create.md)
- [如何更改、删除或管理管理组](manage.md)
- [在 Azure PowerShell 资源模块中查看管理组](/powershell/module/az.resources#resources)
- [在 REST API 中查看管理组](/rest/api/resources/managementgroups)
- [在 Azure CLI 中查看管理组](/cli/azure/account/management-group)
