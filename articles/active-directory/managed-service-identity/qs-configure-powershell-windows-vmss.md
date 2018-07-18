---
title: 如何使用 PowerShell 在 Azure VMSS 上配置 MSI
description: 分步说明如何在 Azure VMSS 上使用 PowerShell 配置系统分配标识和用户分配标识。
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/27/2017
ms.author: daveba
ms.openlocfilehash: 61fa6c94c0d717fe1e71bf8929f2e3b4a0982562
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2018
ms.locfileid: "37903873"
---
# <a name="configure-a-vmss-managed-service-identity-msi-using-powershell"></a>使用 PowerShell 配置 VMSS 托管服务标识 (MSI)

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

托管服务标识为 Azure 服务提供了 Azure Active Directory 中的自动托管标识。 此标识可用于通过支持 Azure AD 身份验证的任何服务的身份验证，这样就无需在代码中插入凭据了。 

在本文中，你将了解如何在虚拟机规模集 (VMSS) 上使用 PowerShell 执行托管服务标识操作：
- 在 Azure VMSS 上启用和禁用系统分配标识
- 在 Azure VMSS 上添加和删除用户分配标识

## <a name="prerequisites"></a>先决条件

- 如果不熟悉托管服务标识，请查阅[概述部分](overview.md)。 请务必了解[系统分配标识与用户分配标识之间的差异](overview.md#how-does-it-work)。
- 如果没有 Azure 帐户，请在继续前[注册免费帐户](https://azure.microsoft.com/free/)。
- 安装[最新版本的 Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM)（如果尚未安装）。 

## <a name="system-assigned-managed-identity"></a>系统分配托管标识

在此部分中，你将了解如何使用 Azure PowerShell 启用和删除系统分配标识。

### <a name="enable-system-assigned-identity-during-the-creation-of-an-azure-vmss"></a>在创建 Azure VMSS 的过程中启用系统分配标识

若要创建启用了系统分配标识的 VMSS，请执行以下操作：

1. 请参阅 [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) cmdlet 参考文章中的*示例 1*，创建具有系统分配标识的 VMSS。  将参数 `-IdentityType SystemAssigned` 添加到 `New-AzureRmVmssConfig` cmdlet：

    ```powershell
    $VMSS = New-AzureRmVmssConfig -Location $Loc -SkuCapacity 2 -SkuName "Standard_A0" -UpgradePolicyMode "Automatic" -NetworkInterfaceConfiguration $NetCfg -IdentityType SystemAssigned`
    ```

2. （可选）在 [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension) cmdlet 上使用 `-Name` 和 `-Type` 参数添加 MSI VMSS 扩展。 可以传递“ManagedIdentityExtensionForWindows”或“ManagedIdentityExtensionForLinux”（具体取决于 VM 的类型），并使用 `-Name` 参数为其命名。 `-Settings` 参数指定 OAuth 令牌终结点用于令牌获取的端口：

    > [!NOTE]
    > 此步骤是可选的，因为你也可以使用 Azure 实例元数据服务 (IMDS) 标识终结点来检索令牌。

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzureRmVmss
   Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```

## <a name="enable-system-assigned-identity-on-an-existing-azure-vmss"></a>在现有 Azure VMSS 上启用系统分配标识

如果需要在现有 Azure VMSS 上启用系统分配标识，请执行以下操作：

1. 使用 `Login-AzureRmAccount` 登录到 Azure 门户。 使用与包含 VM 的 Azure 订阅关联的帐户。 此外，请确保该帐户属于可授予对 VM 的写权限的角色，如“虚拟机参与者”：

   ```powershell
   Login-AzureRmAccount
   ```

2. 首先，使用 [`Get-AzureRmVmss`](/powershell/module/azurerm.compute/get-azurermvmss) cmdlet 检索 VMSS 属性。 然后，若要启用系统分配标识，请在 [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) cmdlet 上使用 `-IdentityType` 开关：

   ```powershell
   $vm = Get-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name -myVM -IdentityType "SystemAssigned"
   ```

3. 在 [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension) cmdlet 上使用 `-Name` 和 `-Type` 参数添加 MSI VMSS 扩展。 可以传递“ManagedIdentityExtensionForWindows”或“ManagedIdentityExtensionForLinux”（具体取决于 VM 的类型），并使用 `-Name` 参数为其命名。 `-Settings` 参数指定 OAuth 令牌终结点用于令牌获取的端口：

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzureRmVmss
   Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```

### <a name="disable-the-system-assigned-identity-from-an-azure-vmss"></a>从 Azure VMSS 中禁用系统分配标识

> [!NOTE]
> 目前不支持从虚拟机规模集中禁用托管服务标识。 同时，你可以切换使用系统分配标识和用户分配标识。

如果某个虚拟机规模集不再需要系统分配标识，但仍需要用户分配标识，请使用以下 cmdlet：

1. 使用 `Login-AzureRmAccount` 登录到 Azure 门户。 使用与包含 VM 的 Azure 订阅关联的帐户。 此外，请确保该帐户属于可授予对 VM 的写权限的角色，如“虚拟机参与者”：

2. 运行以下 cmdlet：

    ```powershell
    Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "UserAssigned"
    ```

## <a name="user-assigned-identity"></a>用户分配标识

在此部分中，你将了解如何使用 Azure PowerShell 在 VMSS 中添加和删除用户分配标识。

### <a name="assign-a-user-assigned-identity-during-creation-of-an-azure-vmss"></a>在创建 Azure VMSS 的过程中分配用户分配标识

目前不支持通过 PowerShell 创建具有用户分配标识的新 VMSS。 有关如何将用户分配标识添加到现有的 VMSS 的信息，请参阅下一部分。 稍后返回查看更新。

### <a name="assign-a-user-identity-to-an-existing-azure-vmss"></a>向现有 Azure VMSS 分配用户标识

若要向现有 Azure VMSS 分配用户分配标识，请执行以下操作：

1. 使用 `Connect-AzureRmAccount` 登录到 Azure 门户。 使用与包含 VM 的 Azure 订阅关联的帐户。 此外，请确保该帐户属于可授予对 VM 的写权限的角色，如“虚拟机参与者”：

   ```powershell
   Connect-AzureRmAccount
   ```

2. 首先，使用 `Get-AzureRmVM` cmdlet 检索 VM 属性。 然后，若要向 Azure VMSS 分配用户分配标识，请在 [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) cmdlet 上使用 `-IdentityType` 和 `-IdentityID` 开关。 将 `<VM NAME>`、`<SUBSCRIPTION ID>`、`<RESROURCE GROUP>`、`<USER ASSIGNED ID1>`、`USER ASSIGNED ID2` 替换为自己的值。

   [!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]


   ```powershell
   $vmss = Get-AzureRmVmss -ResourceGroupName <RESOURCE GROUP> -Name <VMSS NAME>
   Update-AzureRmVmss -ResourceGroupName <RESOURCE GROUP> -VM $vmss -IdentityType UserAssigned -IdentityID "<USER ASSIGNED ID1>","<USER ASSIGNED ID2>"
   ```

### <a name="remove-a-user-assigned-identity-from-an-azure-vmss"></a>从 Azure VMSS 中删除用户分配标识

> [!NOTE]
> 目前不支持从虚拟机规模集中删除所有用户分配标识，除非有系统分配标识。 稍后返回查看更新。

如果 VMSS 具有多个用户分配标识，则可以使用以下命令将这些标识删除到只剩一个。 请务必将 `<RESOURCE GROUP>` 和 `<VMSS NAME>` 参数值替换为自己的值。 `<MSI NAME>` 是用户分配标识的名称属性，该属性应保留在 VMSS 上。 可通过 `az vmss show` 在 VMSS 的标识部分中找到此信息：

```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss
$vmss.Identity.IdentityIds = "<MSI NAME>"
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -VirtualMachineScaleSet $vmss
```

如果 VMSS 同时具有系统分配标识和用户分配标识，则可以切换为仅使用系统分配标识，从而删除所有用户分配标识。 请使用以下命令：

```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss
$vmss.Identity.IdentityIds = $null
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -VirtualMachine $vmss -IdentityType "SystemAssigned"
```

## <a name="related-content"></a>相关内容

- [托管服务标识概述](overview.md)
- 有关完整的 Azure VM 创建快速入门，请参阅：
  
  - [使用 PowerShell 创建 Windows 虚拟机](../../virtual-machines/windows/quick-create-powershell.md) 
  - [使用 PowerShell 创建 Linux 虚拟机](../../virtual-machines/linux/quick-create-powershell.md) 

















