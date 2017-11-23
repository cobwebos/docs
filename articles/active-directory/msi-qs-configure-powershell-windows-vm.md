---
title: "如何使用 PowerShell 在 Azure VM 上配置 MSI"
description: "分步说明如何使用 PowerShell 在 Azure VM 上配置托管服务标识 (MSI)。"
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: bryanla
ms.openlocfilehash: c166d5d4e0ae054e89eb3a5728f1d86f4e008c12
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/23/2017
---
# <a name="configure-a-vm-managed-service-identity-msi-using-powershell"></a>使用 PowerShell 配置 VM 托管服务标识 (MSI)

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

托管服务标识在 Azure Active Directory 中为 Azure 服务提供自动托管标识。 此标识可用于通过支持 Azure AD 身份验证的任何服务的身份验证，这样就无需在代码中插入凭据了。 

本文介绍如何使用 PowerShell 为 Azure VM 启用和删除 MSI。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

此处，安装 [Azure PowerShell 4.3.1](https://www.powershellgallery.com/packages/AzureRM/4.3.1)（如果尚未安装）。

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>在 Azure VM 创建过程中启用 MSI

若要创建启用 MSI 的 VM，请执行以下操作：

1. 请参阅以下 Azure VM 快速入门之一，仅完成必要部分（“登录到 Azure”、“创建资源组”、“创建网络组”、“创建 VM”）。 

   > [!IMPORTANT] 
   > 转到“创建 VM”部分时，需要对 [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvm) cmdlet 语法稍做修改。 请务必添加 `-IdentityType "SystemAssigned"` 参数，以便为 VM 预配 MSI，例如：
   >  
   > `$vmConfig = New-AzureRmVMConfig -VMName myVM -IdentityType "SystemAssigned" ...`

   - [使用 PowerShell 创建 Windows 虚拟机](../virtual-machines/windows/quick-create-powershell.md)
   - [使用 PowerShell 创建 Linux 虚拟机](../virtual-machines/linux/quick-create-powershell.md)



2. 在 [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) cmdlet 上使用 `-Type` 参数，添加 MSI VM 扩展。 可以传递“ManagedIdentityExtensionForWindows”或“ManagedIdentityExtensionForLinux”（具体取决于 VM 的类型），并使用 `-Name` 参数为其命名。 `-Settings` 参数指定 OAuth 令牌终结点用于令牌获取的端口：

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

## <a name="enable-msi-on-an-existing-azure-vm"></a>在现有 Azure VM 上启用 MSI

如果需要在现有虚拟机上启用 MSI，请执行以下操作：

1. 使用 `Login-AzureRmAccount` 登录到 Azure 门户。 使用与包含 VM 的 Azure 订阅关联的帐户。 此外，请确保该帐户属于可授予对 VM 的写权限的角色，如“虚拟机参与者”：

   ```powershell
   Login-AzureRmAccount
   ```

2. 首先，使用 `Get-AzureRmVM` cmdlet 检索 VM 属性。 然后，若要启用 MSI，请在 [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) cmdlet 上使用 `-IdentityType` 开关：

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm -IdentityType "SystemAssigned"
   ```

3. 在 [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) cmdlet 上使用 `-Type` 参数，添加 MSI VM 扩展。 可以传递“ManagedIdentityExtensionForWindows”或“ManagedIdentityExtensionForLinux”（具体取决于 VM 的类型），并使用 `-Name` 参数为其命名。 `-Settings` 参数指定 OAuth 令牌终结点用于令牌获取的端口。 请务必指定正确的 `-Location` 参数，以匹配现有 VM 的位置：

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

## <a name="remove-msi-from-an-azure-vm"></a>从 Azure VM 中删除 MSI

如果虚拟机不再需要 MSI，可以使用 `RemoveAzureRmVMExtension` cmdlet 从 VM 中删除 MSI：

1. 使用 `Login-AzureRmAccount` 登录到 Azure 门户。 使用与包含 VM 的 Azure 订阅关联的帐户。 此外，请确保该帐户属于可授予对 VM 的写权限的角色，如“虚拟机参与者”：

   ```powershell
   Login-AzureRmAccount
   ```

2. 将 `-Name` 开关与 [Remove-AzureRmVMExtension](/powershell/module/azurerm.compute/remove-azurermvmextension) cmdlet 配合使用，以指定在添加扩展时使用的同一名称：

   ```powershell
   Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
   ```

## <a name="related-content"></a>相关内容

- [托管服务标识概述](msi-overview.md)
- 有关完整的 Azure VM 创建快速入门，请参阅：
  
  - [使用 PowerShell 创建 Windows 虚拟机](../virtual-machines/windows/quick-create-powershell.md) 
  - [使用 PowerShell 创建 Linux 虚拟机](../virtual-machines/linux/quick-create-powershell.md) 

使用以下评论部分提供反馈，帮助我们改进内容。
















