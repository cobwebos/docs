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
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: 104c43e6fab2c3f18824a00860c30c8d6f82bbc4
ms.contentlocale: zh-cn
ms.lasthandoff: 09/14/2017

---

# <a name="configure-a-vm-managed-service-identity-msi-using-powershell"></a>使用 PowerShell 配置 VM 托管服务标识 (MSI)

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

托管服务标识在 Azure Active Directory 中为 Azure 服务提供自动托管标识。 可以使用此标识向任何支持 Azure AD 身份验证的服务进行身份验证，而无需在代码中包含凭据。 

本文介绍如何使用 PowerShell 启用和删除 Azure Windows VM 的 MSI。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [msi-qs-configure-prereqs](../../includes/msi-qs-configure-prereqs.md)]

此处，安装 [Azure PowerShell 4.3.1](https://www.powershellgallery.com/packages/AzureRM/4.3.1)（如果尚未安装）。

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>在 Azure VM 的创建过程中启用 MSI

将使用指定配置参数在新的资源组中创建一个新的启用了 MSI 的 Windows 虚拟机资源。 另请注意，上述多个 cmdlet 可能运行 30 秒或更长时间才返回，最终的 VM 创建需要几分钟才能完成。

1. 使用 `Login-AzureRmAccount` 登录到 Azure 门户。 使用与要在其下部署 VM 的 Azure 订阅关联的帐户。

   ```powershell
   Login-AzureRmAccount
   ```

2. 使用 `New-AzureRmResourceGroup` cmdlet 创建用于包含和部署 VM 及其相关资源的[资源组](../azure-resource-manager/resource-group-overview.md#terminology)。 如果已有要改用的资源组，则可以跳过此步骤：

   ```powershell
   New-AzureRmResourceGroup -Name myResourceGroup -Location WestUS
   ```
3. 为 VM 创建网络资源。

   a. 创建虚拟网络、子网和公共 IP 地址。 这些资源用来与虚拟机建立网络连接，以及连接到 Internet：

   ```powershell
   # Create a subnet configuration
   $subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

   # Create a virtual network
   $vnet = New-AzureRmVirtualNetwork -ResourceGroupName myResourceGroup -Location WestUS -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

   # Create a public IP address and specify a DNS name
   $pip = New-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup -Location WestUS -AllocationMethod Static -IdleTimeoutInMinutes 4 -Name "mypublicdns$(Get-Random)"
   ```

   b. 创建网络安全组和网络安全组规则。 网络安全组使用入站和出站规则保护虚拟机。 在本例中，将为端口 3389 创建一个入站规则，该规则允许传入的远程桌面连接。 我们还需要为端口 80 创建入站规则，以允许传入的 Web 流量：

   ```powershell
   # Create an inbound network security group rule for port 3389
   $nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow

   # Create an inbound network security group rule for port 80
   $nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleWWW  -Protocol Tcp -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 80 -Access Allow

   # Create a network security group
   $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName myResourceGroup -Location WestUS -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP,$nsgRuleWeb
   ```

   c. 为虚拟机创建虚拟网卡。 网卡将虚拟机连接到子网、网络安全组和公共 IP 地址：

   ```powershell
   # Create a virtual network card and associate with public IP address and NSG
   $nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName myResourceGroup -Location WestUS -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
   ```

4. 创建 VM。

   a. 创建可配置的 VM 对象。 部署虚拟机时使用这些设置，例如虚拟机映像、大小和身份验证配置。 [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvm) cmdlet 中使用的 `-IdentityType "SystemAssigned"` 参数会导致 VM 预配有 MSI。 `Get-Credential` cmdlet 将提示输入凭据，这些凭据配置为虚拟机的用户名和密码：

   ```powershell
   # Define a credential object (prompts for user/password to be used for VM authentication)
   $cred = Get-Credential

   # Create a configurable VM object with a Managed Service Identity
   $vmConfig = New-AzureRmVMConfig -VMName myVM -VMSize Standard_DS2 -IdentityType "SystemAssigned" | Set-AzureRmVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest | Add-AzureRmVMNetworkInterface -Id $nic.Id
   ```

   b. 预配新 VM：

   ```powershell
   New-AzureRmVM -ResourceGroupName myResourceGroup -Location WestUS -VM $vmConfig
   ```

5. 在 [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) cmdlet 上使用 `-Type "ManagedIdentityExtensionForWindows"` 参数，添加 MSI VM 扩展。 `-Settings` 参数指定 OAuth 令牌终结点用于令牌获取的端口：

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

## <a name="enable-msi-on-an-existing-azure-vm"></a>在现有 Azure VM 上启用 MSI

如果需要在现有虚拟机上启用 MSI，请执行以下操作：

1. 使用 `Login-AzureRmAccount` 登录到 Azure 门户。 使用与要在其下部署 VM 的 Azure 订阅关联的帐户：

   ```powershell
   Login-AzureRmAccount
   ```

2. 首先，使用 `Get-AzureRmVM` cmdlet 检索 VM 属性。 然后，若要启用 MSI，请在 [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) cmdlet 上使用 `-IdentityType` 开关：

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm -IdentityType "SystemAssigned"
   ```

3. 在 [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) cmdlet 上使用 `-Type "ManagedIdentityExtensionForWindows"` 参数，添加 MSI VM 扩展。 `-Settings` 参数指定 OAuth 令牌终结点用于令牌获取的端口。 请务必指定正确的 `-Location` 参数，以匹配现有 VM 的位置：

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

## <a name="remove-msi-from-an-azure-vm"></a>从 Azure VM 中删除 MSI

如果虚拟机不再需要 MSI，可以使用 `RemoveAzureRmVMExtension` cmdlet 从 VM 中删除 MSI：

1. 在 [Remove-AzureRmVMExtension](/powershell/module/azurerm.compute/remove-azurermvmextension) cmdlet 中使用 `-Name "ManagedIdentityExtensionForWindows"` 开关：

   ```powershell
   Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
   ```

## <a name="related-content"></a>相关内容

- [托管服务标识概述](msi-overview.md)
- 本文改编自[使用 PowerShell 创建 Windows 虚拟机](../virtual-machines/windows/quick-create-powershell.md)快速入门，经修改包含特定于 MSI 的说明。 

使用以下评论部分提供反馈，帮助我们改进内容。

















