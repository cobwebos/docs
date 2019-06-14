---
title: 如何使用 PowerShell 在 Azure VM 上配置 Azure 资源的托管标识
description: 分步说明如何使用 PowerShell 在 Azure VM 上配置 Azure 资源的托管标识。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/27/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f76fef3d5e6515e9d546c709ace0a4a533c0a45
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66112693"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-powershell"></a>使用 PowerShell 在 Azure VM 上配置 Azure 资源的托管标识

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Azure 资源的托管标识在 Azure Active Directory 中为 Azure 服务提供了一个自动托管标识。 此标识可用于通过支持 Azure AD 身份验证的任何服务的身份验证，这样就无需在代码中插入凭据了。 

在本文中，你将了解如何在 Azure VM 上使用 PowerShell 执行 Azure 资源的托管标识的以下操作。

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>必备组件

- 如果不熟悉 Azure 资源的托管标识，请查阅[概述部分](overview.md)。 请务必了解[系统分配的托管标识与用户分配的托管标识之间的差异](overview.md#how-does-it-work)  。
- 如果没有 Azure 帐户，请在继续前[注册免费帐户](https://azure.microsoft.com/free/)。
- 安装[最新版本的 Azure PowerShell](/powershell/azure/install-az-ps)（如果尚未安装）。

## <a name="system-assigned-managed-identity"></a>系统分配的托管标识

在此部分中，你将了解如何使用 Azure PowerShell 来启用和禁用系统分配的托管标识。

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>在创建 Azure VM 的过程中启用系统分配托管标识

若要创建启用了系统分配的托管标识的 Azure VM，你的帐户需要[虚拟机参与者](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)角色分配。  无需其他 Azure AD 目录角色分配。

1. 请参阅以下 Azure VM 快速入门之一，完成必要部分 （"登录到 Azure"，"创建资源组"，"创建网络组"，"创建 VM"）。
    
    转到“创建 VM”部分时，需要对 [New-AzVMConfig](/powershell/module/az.compute/new-azvm) cmdlet 语法稍做修改。 务必添加 `-AssignIdentity:$SystemAssigned` 参数，以预配启用了系统分配标识的 VM，例如：
      
    ```powershell
    $vmConfig = New-AzVMConfig -VMName myVM -AssignIdentity:$SystemAssigned ...
    ```

   - [使用 PowerShell 创建 Windows 虚拟机](../../virtual-machines/windows/quick-create-powershell.md)
   - [使用 PowerShell 创建 Linux 虚拟机](../../virtual-machines/linux/quick-create-powershell.md)

> [!NOTE]
> 可以选择性地预配 Azure 资源 VM 扩展的托管的标识，但将很快被弃用。 我们建议使用 Azure 实例元数据标识终结点进行身份验证。 有关详细信息，请参阅[从 VM 扩展迁移到 Azure IMDS 终结点进行身份验证](howto-migrate-vm-extension.md)。

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>在现有 Azure VM 上启用系统分配托管标识

若要在最初未预配系统分配的托管标识的 VM 上启用该托管标识，你的帐户需要[虚拟机参与者](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)角色分配。  无需其他 Azure AD 目录角色分配。

1. 使用 `Connect-AzAccount` 登录到 Azure 门户。 使用与包含 VM 的 Azure 订阅关联的帐户。

   ```powershell
   Connect-AzAccount
   ```

2. 首先，使用 `Get-AzVM` cmdlet 检索 VM 属性。 然后，若要启用系统分配的托管标识，请在 [Update-AzVM](/powershell/module/az.compute/update-azvm) cmdlet 中使用 `-AssignIdentity` 开关：

   ```powershell
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzVM -ResourceGroupName myResourceGroup -VM $vm -AssignIdentity:$SystemAssigned
   ```

> [!NOTE]
> 可以选择性地预配 Azure 资源 VM 扩展的托管的标识，但将很快被弃用。 我们建议使用 Azure 实例元数据标识终结点进行身份验证。 有关详细信息，请参阅[从 VM 扩展迁移到 Azure IMDS 终结点进行身份验证](howto-migrate-vm-extension.md)。

### <a name="add-vm-system-assigned-identity-to-a-group"></a>将 VM 系统分配的标识添加到组

在 VM 上启用系统分配的标识后，可以将其添加到组中。  以下过程将 VM 的系统分配的标识添加到组。

1. 使用 `Connect-AzAccount` 登录到 Azure 门户。 使用与包含 VM 的 Azure 订阅关联的帐户。

   ```powershell
   Connect-AzAccount
   ```

2. 检索并记下 VM 服务主体的 `ObjectID`（在返回值的 `Id` 字段中指定）：

   ```powerhshell
   Get-AzADServicePrincipal -displayname "myVM"
   ```

3. 检索并记下组中的 `ObjectID`（在返回值的 `Id` 字段中指定）：

   ```powershell
   Get-AzADGroup -searchstring "myGroup"
   ```

4. 将 VM 的服务主体添加到组：

   ```powershell
   Add-AzureADGroupMember -ObjectId "<objectID of group>" -RefObjectId "<object id of VM service principal>"
   ```

## <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>从 Azure VM 中禁用系统分配的托管标识

若要在 VM 上禁用系统分配的托管标识，你的帐户需要[虚拟机参与者](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)角色分配。  无需其他 Azure AD 目录角色分配。

如果某个虚拟机不再需要系统分配的托管标识，但仍需要用户分配的托管标识，请使用以下 cmdlet：

1. 使用 `Connect-AzAccount` 登录到 Azure 门户。 使用与包含 VM 的 Azure 订阅关联的帐户。

   ```powershell
   Connect-AzAccount
   ```

2. 使用 `Get-AzVM` cmdlet 检索 VM 属性并将 `-IdentityType` 参数设置为 `UserAssigned`：

   ```powershell   
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM 
   Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType "UserAssigned"
   ```

如果某个虚拟机不再需要系统分配的托管标识，且没有用户分配的托管标识，请使用以下命令：

```powershell
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```

> [!NOTE]
> 如果已预配的 Azure 资源 （若要不推荐使用） 的 VM 扩展托管的标识，则需要使用将其删除[删除 AzVMExtension](/powershell/module/az.compute/remove-azvmextension)。 有关详细信息，请参阅[从 VM 扩展迁移到 Azure 进行身份验证的 IMDS](howto-migrate-vm-extension.md)。

## <a name="user-assigned-managed-identity"></a>用户分配的托管标识

在此部分中，你将了解如何使用 Azure PowerShell 在 VM 中添加和删除用户分配的托管标识。

### <a name="assign-a-user-assigned-managed-identity-to-a-vm-during-creation"></a>在创建过程中向 VM 分配用户分配的托管标识

若要将用户分配的标识分配给 VM，你的帐户需要[虚拟机参与者](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)和[托管标识操作员](/azure/role-based-access-control/built-in-roles#managed-identity-operator)角色分配。 无需其他 Azure AD 目录角色分配。

1. 请参阅以下 Azure VM 快速入门之一，完成必要部分 （"登录到 Azure"，"创建资源组"，"创建网络组"，"创建 VM"）。 
  
    转到“创建 VM”部分时，需要对 [`New-AzVMConfig`](/powershell/module/az.compute/new-azvm) cmdlet 语法稍做修改。 添加 `-IdentityType UserAssigned` 和 `-IdentityID` 参数，为 VM 预配用户分配的标识。  将 `<VM NAME>`、`<SUBSCRIPTION ID>`、`<RESROURCE GROUP>` 和 `<USER ASSIGNED IDENTITY NAME>` 替换为自己的值。  例如：
    
    ```powershell 
    $vmConfig = New-AzVMConfig -VMName <VM NAME> -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>..."
    ```
    
    - [使用 PowerShell 创建 Windows 虚拟机](../../virtual-machines/windows/quick-create-powershell.md)
    - [使用 PowerShell 创建 Linux 虚拟机](../../virtual-machines/linux/quick-create-powershell.md)

> [!NOTE]
> 可以选择性地预配 Azure 资源 VM 扩展的托管的标识，但将很快被弃用。 我们建议使用 Azure 实例元数据标识终结点进行身份验证。 有关详细信息，请参阅[从 VM 扩展迁移到 Azure IMDS 终结点进行身份验证](howto-migrate-vm-extension.md)。

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>向现有 Azure VM 分配用户分配托管标识

若要将用户分配的标识分配给 VM，你的帐户需要[虚拟机参与者](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)和[托管标识操作员](/azure/role-based-access-control/built-in-roles#managed-identity-operator)角色分配。 无需其他 Azure AD 目录角色分配。

1. 使用 `Connect-AzAccount` 登录到 Azure 门户。 使用与包含 VM 的 Azure 订阅关联的帐户。

   ```powershell
   Connect-AzAccount
   ```

2. 使用 [New-AzUserAssignedIdentity](/powershell/module/az.managedserviceidentity/new-azuserassignedidentity) cmdlet 创建用户分配的托管标识。  记下输出中的 `Id`，因为下一步会用到它。

   > [!IMPORTANT]
   > 创建用户分配管理的标识仅支持字母数字、 下划线和连字符 (0-9 或 a 到 z 或 A 到 Z，\_或-) 字符。 此外，名称应介于 3 到分配给 VM/VMSS 才能正常工作的 128 字符长度有限。 有关详细信息，请参阅 [FAQ 和已知问题](known-issues.md)

   ```powershell
   New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
   ```
3. 使用 `Get-AzVM` cmdlet 检索 VM 属性。 然后，若要向 Azure VM 分配用户分配的托管标识，请在 [Update-AzVM](/powershell/module/az.compute/update-azvm) cmdlet 中使用 `-IdentityType` 和 `-IdentityID` 开关。  `-IdentityId` 参数的值是在上一步中记下的 `Id`。  将 `<VM NAME>`、`<SUBSCRIPTION ID>`、`<RESROURCE GROUP>` 和 `<USER ASSIGNED IDENTITY NAME>` 替换为自己的值。

   > [!WARNING]
   > 若要保留分配给 VM 的任何以前用户分配的托管标识，请查询 VM 对象的 `Identity` 属性（例如，`$vm.Identity`）。  如果返回了任何用户分配的托管标识，请将其添加到以下命令以及要分配给 VM 的新用户分配的托管标识中。

   ```powershell
   $vm = Get-AzVM -ResourceGroupName <RESOURCE GROUP> -Name <VM NAME>
   Update-AzVM -ResourceGroupName <RESOURCE GROUP> -VM $vm -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>"
   ```

> [!NOTE]
> 可以选择性地预配 Azure 资源 VM 扩展的托管的标识，但将很快被弃用。 我们建议使用 Azure 实例元数据标识终结点进行身份验证。 有关详细信息，请参阅[从 VM 扩展迁移到 Azure IMDS 终结点进行身份验证](howto-migrate-vm-extension.md)。

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>从 Azure VM 中删除用户分配的托管标识

若要从 VM 中删除用户分配的标识，你的帐户需要[虚拟机参与者](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)角色分配。

如果 VM 具有多个用户分配的托管标识，则可以使用以下命令将这些标识删除到只剩一个。 请务必将 `<RESOURCE GROUP>` 和 `<VM NAME>` 参数值替换为自己的值。 `<USER ASSIGNED IDENTITY NAME>` 是用户分配的托管标识的名称属性，该属性应保留在 VM 上。 可以通过查询 VM 对象的 `Identity` 属性找到该信息。  例如，`$vm.Identity`：

```powershell
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType UserAssigned -IdentityID <USER ASSIGNED IDENTITY NAME>
```
如果 VM 没有系统分配的托管标识，并且希望从中删除所有用户分配的托管标识，请使用以下命令：

```powershell
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```
如果 VM 同时具有系统分配的托管标识和用户分配的托管标识，则可通过切换为仅使用系统分配的托管标识，删除所有用户分配的托管标识。

```powershell 
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>后续步骤

- [Azure 资源的托管标识概述](overview.md)
- 有关完整的 Azure VM 创建快速入门，请参阅：
  
  - [使用 PowerShell 创建 Windows 虚拟机](../../virtual-machines/windows/quick-create-powershell.md) 
  - [使用 PowerShell 创建 Linux 虚拟机](../../virtual-machines/linux/quick-create-powershell.md) 
