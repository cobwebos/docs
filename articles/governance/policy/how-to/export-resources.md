---
title: 导出 Azure Policy 资源
description: 了解如何将 Azure 策略资源导出到 GitHub，如策略定义和策略分配。
ms.date: 09/30/2020
ms.topic: how-to
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 691e0a026c5f4f1a0a68c744ee81b1da8da9e70b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91777082"
---
# <a name="export-azure-policy-resources"></a>导出 Azure Policy 资源

本文提供了有关如何导出现有 Azure 策略资源的信息。 导出资源会很有用，并建议用于备份，但在使用云管理和将 [策略视为代码](../concepts/policy-as-code.md)时，这也是一项重要的一步。 Azure 策略资源可以通过 [Azure 门户](#export-with-azure-portal)、 [Azure CLI](#export-with-azure-cli)、 [Azure PowerShell](#export-with-azure-powershell)和每个受支持的 sdk 进行导出。

## <a name="export-with-azure-portal"></a>导出 Azure 门户

若要从 Azure 门户导出策略定义，请执行以下步骤：

1. 在 Azure 门户中单击“所有服务”，然后搜索并选择“策略”，启动 Azure Policy 服务。 

1. 选择 "Azure 策略" 页左侧的 " **定义** "。

1. 使用 " **导出定义** " 按钮或选择策略定义行中的省略号，然后选择 " **导出定义**"。

1. 选择 " **用 GitHub 登录** " 按钮。 如果尚未使用 GitHub 进行身份验证来授权 Azure 策略导出资源，请在打开的新窗口中查看访问 [Github 操作](https://github.com/features/actions) 所需的操作，并选择 " **授权 AzureGitHubActions** " 以继续执行导出过程。 完成后，新的窗口将自行关闭。

1. 在 " **基本** 信息" 选项卡上，设置以下选项，然后选择页面底部的 " **策略** " 选项卡或 " **下一步：策略** " 按钮。

   - **存储库筛选器**：设置为 _"我的存储库"_ ，以仅查看您拥有的存储库或 _所有存储库_ ，以查看授予 GitHub 操作访问权限的所有用户。
   - **存储库**：设置为要将 Azure 策略资源导出到的存储库。
   - **Branch**：设置存储库中的分支。 使用除默认值以外的分支是在进一步合并到源代码之前验证更新的好方法。
   - **目录**：要将 Azure 策略资源导出到的 _根级别文件夹_ 。 此目录下的子文件夹基于导出的资源进行创建。

1. 在 " **策略** " 选项卡上，通过选择省略号，然后选择管理组、订阅或资源组的组合，将范围设置为 "搜索"。
   
1. 使用 " **添加策略定义 (") ** "按钮搜索要导出的对象的作用域。 在打开的侧窗口中，选择要导出的每个对象。 按搜索框或类型筛选选定内容。 选择要导出的所有对象后，使用页面底部的 " **添加** " 按钮。

1. 对于每个选定的对象，请选择所需的导出选项，例如 _仅定义_ 或 _定义和分配 (s) _ 用于策略定义。 然后选择页面底部的 "查看" 和 " **导出** " 选项卡或 " **下一步：查看** " 和 "导出" 按钮。

   > [!NOTE]
   > 如果选择了 "选项 _定义" 和 "分配 (") _ ，则将导出策略定义添加时筛选器设置的范围内的策略分配。

1. 在 " **查看** " 和 "导出" 选项卡上，检查详细信息，然后使用页面底部的 " **导出** " 按钮。

1. 检查 GitHub 存储库、分支和 _根级别文件夹_ ，以查看所选资源现在是否已导出到源代码管理中。

Azure 策略资源将导出到选定 GitHub 存储库和 _根级别文件夹_中的以下结构：

```text
|
|- <root level folder>/  ________________ # Root level folder set by Directory property
|  |- policies/  ________________________ # Subfolder for policy objects
|     |- <displayName>_<name>____________ # Subfolder based on policy displayName and name properties
|        |- policy.json _________________ # Policy definition
|        |- assign.<displayName>_<name>__ # Each assignment (if selected) based on displayName and name properties
|
```

## <a name="export-with-azure-cli"></a>导出 Azure CLI

每个 Azure 策略定义、计划和分配都可以 [Azure CLI](/cli/azure/install-azure-cli)导出为 JSON。 其中每个命令都使用 **name** 参数来指定要为其获取 JSON 的对象。 **Name**属性通常是_GUID_ ，而不是对象的**displayName** 。

- 定义- [az policy definition show](/cli/azure/policy/definition#az-policy-definition-show)
- 计划- [az policy set-definition show](/cli/azure/policy/set-definition#az-policy-set-definition-show)
- 分配- [az policy 赋值 show](/cli/azure/policy/assignment#az-policy-assignment-show)

下面是一个示例，说明如何获取 **名** 为 _VirtualMachineStorage_的策略定义的 JSON：

```azurecli-interactive
az policy definition show --name 'VirtualMachineStorage'
```

## <a name="export-with-azure-powershell"></a>导出 Azure PowerShell

每个 Azure 策略定义、计划和分配都可以 [Azure PowerShell](/powershell/azure/)导出为 JSON。 其中每个 cmdlet 都使用 **Name** 参数来指定要为其获取 JSON 的对象。 **Name**属性通常是_GUID_ ，而不是对象的**displayName** 。

- 定义- [AzPolicyDefinition](/powershell/module/az.resources/get-azpolicydefinition)
- 计划- [AzPolicySetDefinition](/powershell/module/az.resources/get-azpolicysetdefinition)
- 分配- [AzPolicyAssignment](/powershell/module/az.resources/get-azpolicyassignment)

下面是一个示例，说明如何获取 **名** 为 _VirtualMachineStorage_的策略定义的 JSON：

```azurepowershell-interactive
Get-AzPolicyDefinition -Name 'VirtualMachineStorage'
```

## <a name="next-steps"></a>后续步骤

- 在 [Azure Policy 示例](../samples/index.md)中查看示例。
- 查看 [Azure Policy 定义结构](../concepts/definition-structure.md)。
- 查看[了解策略效果](../concepts/effects.md)。
- 了解如何[以编程方式创建策略](programmatically-create.md)。
- 了解如何[修正不符合的资源](remediate-resources.md)。
- 参阅[使用 Azure 管理组来组织资源](../../management-groups/overview.md)，了解什么是管理组。
