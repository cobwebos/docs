---
title: 如何使用 PowerShell 在 Azure VM 上配置 MSI
description: 分步说明如何使用 PowerShell 在 Azure VM 上配置托管服务标识 (MSI)。
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/27/2017
ms.author: daveba
ms.openlocfilehash: 20bf16eeb6aff952423af6754812f9532e55cd5f
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2018
ms.locfileid: "37444442"
---
# <a name="configure-a-vm-managed-service-identity-msi-using-powershell"></a>使用 PowerShell 配置 VM 托管服务标识 (MSI)

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

托管服务标识在 Azure Active Directory 中为 Azure 服务提供自动托管标识。 此标识可用于通过支持 Azure AD 身份验证的任何服务的身份验证，这样就无需在代码中插入凭据了。 

<a name="in-this-article-you-learn-how-to-perform-the-following-managed-service-identity-operations-on-an-azure-vm-using-powershell"></a>在本文中，你将了解如何在 Azure VM 上使用 PowerShell 执行以下托管服务标识操作：
- 

## <a name="prerequisites"></a>先决条件

- 如果不熟悉托管服务标识，请查阅[概述部分](overview.md)。 请务必了解[系统分配标识与用户分配标识之间的差异](overview.md#how-does-it-work)。
- 如果没有 Azure 帐户，请在继续前[注册免费帐户](https://azure.microsoft.com/free/)。
- 安装[最新版本的 Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM)（如果尚未安装）。

## <a name="system-assigned-identity"></a>系统分配标识

在此部分中，你将了解如何使用 Azure PowerShell 来启用和禁用系统分配标识。

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vm"></a>在创建 Azure VM 的过程中启用系统分配标识

若要创建启用了系统分配标识的 Azure VM，请执行以下操作：

1. 请参阅以下 Azure VM 快速入门之一，仅完成必要部分（“登录到 Azure”、“创建资源组”、“创建网络组”、“创建 VM”）。
    
    转到“创建 VM”部分时，需要对 [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvm) cmdlet 语法稍做修改。 务必添加 `-AssignIdentity:$SystemAssigned` 参数，以预配启用了系统分配标识的 VM，例如：
      
    ```powershell
    $vmConfig = New-AzureRmVMConfig -VMName myVM -AssignIdentity:$SystemAssigned ...
    ```

   - [使用 PowerShell 创建 Windows 虚拟机](../../virtual-machines/windows/quick-create-powershell.md)
   - [使用 PowerShell 创建 Linux 虚拟机](../../virtual-machines/linux/quick-create-powershell.md)

2. （可选）在 [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) cmdlet 上使用 `-Type` 参数添加 MSI VM 扩展。 可以传递“ManagedIdentityExtensionForWindows”或“ManagedIdentityExtensionForLinux”（具体取决于 VM 的类型），并使用 `-Name` 参数为其命名。 `-Settings` 参数指定 OAuth 令牌终结点用于令牌获取的端口：

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```
    > [!NOTE]
    > 此步骤是可选的，因为你也可以使用 Azure 实例元数据服务 (IMDS) 标识终结点来检索令牌。

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>在现有 Azure VM 上启用系统分配标识

如果需要在现有虚拟机上启用系统分配标识，请执行以下操作：

1. 使用 `Login-AzureRmAccount` 登录到 Azure 门户。 使用与包含 VM 的 Azure 订阅关联的帐户。 此外，请确保该帐户属于可授予对 VM 的写权限的角色，如“虚拟机参与者”：

   ```powershell
   Login-AzureRmAccount
   ```

2. 首先，使用 `Get-AzureRmVM` cmdlet 检索 VM 属性。 然后，若要启用系统分配标识，请在 [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) cmdlet 上使用 `-AssignIdentity` 开关：

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm -AssignIdentity:$SystemAssigned
   ```

3. （可选）在 [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) cmdlet 上使用 `-Type` 参数添加 MSI VM 扩展。 可以传递“ManagedIdentityExtensionForWindows”或“ManagedIdentityExtensionForLinux”（具体取决于 VM 的类型），并使用 `-Name` 参数为其命名。 `-Settings` 参数指定 OAuth 令牌终结点用于令牌获取的端口。 请务必指定正确的 `-Location` 参数，以匹配现有 VM 的位置：

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```
    > [!NOTE]
    > 此步骤是可选的，因为你也可以使用 Azure 实例元数据服务 (IMDS) 标识终结点来检索令牌。

## <a name="disable-the-system-assigned-identity-from-an-azure-vm"></a>从 Azure VM 中禁用系统分配标识

> [!NOTE]
>  目前不支持从虚拟机中禁用托管服务标识。 同时，你可以切换使用系统分配标识和用户分配标识。

如果某个虚拟机不再需要系统分配标识，但仍需要用户分配标识，请使用以下 cmdlet：

1. 使用 `Login-AzureRmAccount` 登录到 Azure 门户。 使用与包含 VM 的 Azure 订阅关联的帐户。 此外，请确保该帐户属于可授予对 VM 的写权限的角色，如“虚拟机参与者”：

   ```powershell
   Login-AzureRmAccount
   ```

2. 运行以下 cmdlet： 
    ```powershell       
    Update-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm -IdentityType "UserAssigned"
    ```
若要删除 MSI VM 扩展，请将 -Name 开关与 [Remove-AzureRmVMExtension](/powershell/module/azurerm.compute/remove-azurermvmextension) cmdlet 配合使用，以指定在添加扩展时使用的同一名称：

   ```powershell
   Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
   ```

## <a name="user-assigned-identity"></a>用户分配标识

在此部分中，你将了解如何使用 Azure PowerShell 在 VM 中添加和删除用户分配标识。

### <a name="assign-a-user-assigned-identity-to-a-vm-during-creation"></a>在创建过程中向 VM 分配用户分配标识

若要在创建 Azure VM 时向该 VM 分配用户分配标识，请执行以下操作：

1. 请参阅以下 Azure VM 快速入门之一，仅完成必要部分（“登录到 Azure”、“创建资源组”、“创建网络组”、“创建 VM”）。 
  
    转到“创建 VM”部分时，需要对 [`New-AzureRmVMConfig`](/powershell/module/azurerm.compute/new-azurermvm) cmdlet 语法稍做修改。 添加 `-IdentityType UserAssigned` 和 `-IdentityID ` 参数，为 VM 预配用户分配标识。  将 `<VM NAME>`、`<SUBSCRIPTION ID>`、`<RESROURCE GROUP>` 和 `<MSI NAME>` 替换为自己的值。  例如：
    
    ```powershell 
    $vmConfig = New-AzureRmVMConfig -VMName <VM NAME> -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>..."
    ```
    
    - [使用 PowerShell 创建 Windows 虚拟机](../../virtual-machines/windows/quick-create-powershell.md)
    - [使用 PowerShell 创建 Linux 虚拟机](../../virtual-machines/linux/quick-create-powershell.md)

2. （可选）在 [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) cmdlet 上使用 `-Type` 参数添加 MSI VM 扩展。 可以传递“ManagedIdentityExtensionForWindows”或“ManagedIdentityExtensionForLinux”（具体取决于 VM 的类型），并使用 `-Name` 参数为其命名。 `-Settings` 参数指定 OAuth 令牌终结点用于令牌获取的端口。 请务必指定正确的 `-Location` 参数，以匹配现有 VM 的位置：
      > [!NOTE]
    > 此步骤是可选的，因为你也可以使用 Azure 实例元数据服务 (IMDS) 标识终结点来检索令牌。

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

### <a name="assign-a-user-identity-to-an-existing-azure-vm"></a>向现有 Azure VM 分配用户标识

若要向现有 Azure VM 分配用户分配标识，请执行以下操作：

1. 使用 `Connect-AzureRmAccount` 登录到 Azure 门户。 使用与包含 VM 的 Azure 订阅关联的帐户。 此外，请确保该帐户属于可授予对 VM 的写权限的角色，如“虚拟机参与者”：

   ```powershell
   Connect-AzureRmAccount
   ```

2. 使用 [New-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/new-azurermuserassignedidentity) cmdlet 创建用户分配标识。  记下输出中的 `Id`，因为下一步会用到它。

    > [!IMPORTANT]
    > 创建用户分配标识时仅支持字母数字和连字符（0-9 或 a-z 或 A-Z 或 -）字符。 另外，分配给 VM/VMSS 的名称长度应限制为 24 个字符，否则它无法正常工作。 请关注后续更新。 有关详细信息，请参阅 [FAQ 和已知问题](known-issues.md)


  ```powershell
  New-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
  ```
3. 使用 `Get-AzureRmVM` cmdlet 检索 VM 属性。 然后，若要向 Azure VM 分配用户分配标识，请在 [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) cmdlet 上使用 `-IdentityType` 和 `-IdentityID` 开关。  `-IdentityId` 参数的值是在上一步中记下的 `Id`。  将 `<VM NAME>`、`<SUBSCRIPTION ID>`、`<RESROURCE GROUP>` 和 `<USER ASSIGNED IDENTITY NAME>` 替换为自己的值。

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName <RESOURCE GROUP> -Name <VM NAME>
   Update-AzureRmVM -ResourceGroupName <RESOURCE GROUP> -VM $vm -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>"
   ```

4. 在 [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) cmdlet 上使用 `-Type` 参数，添加 MSI VM 扩展。 可以传递“ManagedIdentityExtensionForWindows”或“ManagedIdentityExtensionForLinux”（具体取决于 VM 的类型），并使用 `-Name` 参数为其命名。 `-Settings` 参数指定 OAuth 令牌终结点用于令牌获取的端口。 指定正确的 `-Location` 参数，以匹配现有 VM 的位置。

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>从 Azure VM 中删除用户分配的托管标识

> [!NOTE]
>  目前不支持从虚拟机中删除所有用户分配标识，除非有系统分配标识。 稍后返回查看更新。

如果 VM 具有多个用户分配标识，则可以使用以下命令将这些标识删除到只剩一个。 请务必将 `<RESOURCE GROUP>` 和 `<VM NAME>` 参数值替换为自己的值。 `<MSI NAME>` 是用户分配标识的名称属性，该属性应保留在 VM 上。 可通过 `az vm show` 在 VM 的标识部分中找到此信息：

```powershell
$vm = Get-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm
$vm.Identity.IdentityIds = "<MSI NAME>"
Update-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm -VirtualMachine $vm
```

如果 VM 同时具有系统分配标识和用户分配标识，则可以切换为仅使用系统分配标识，从而删除所有用户分配标识。 请使用以下命令：

```powershell 
$vm = Get-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm
$vm.Identity.IdentityIds = $null
Update-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm -VirtualMachine $vm -IdentityType "SystemAssigned"
```

## <a name="related-content"></a>相关内容

- [托管服务标识概述](overview.md)
- 有关完整的 Azure VM 创建快速入门，请参阅：
  
  - [使用 PowerShell 创建 Windows 虚拟机](../../virtual-machines/windows/quick-create-powershell.md) 
  - [使用 PowerShell 创建 Linux 虚拟机](../../virtual-machines/linux/quick-create-powershell.md) 
