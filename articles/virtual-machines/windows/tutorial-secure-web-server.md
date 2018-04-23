---
title: 在 Azure 中使用 SSL 证书保护 IIS | Microsoft Docs
description: 了解如何在 Azure 中的 Windows VM 上使用 SSL 证书保护 IIS Web 服务器
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/09/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: c86f7ae9ef3eeaa68708df509020af0f6ecc2d1f
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2018
---
# <a name="secure-iis-web-server-with-ssl-certificates-on-a-windows-virtual-machine-in-azure"></a>在 Azure 中的 Windows 虚拟机上使用 SSL 证书保护 IIS Web 服务器
若要保护 Web 服务器，可以使用安全套接字层 (SSL) 证书来加密 Web 流量。 这些 SSL 证书可存储在 Azure Key Vault 中，并可安全部署到 Azure 中的 Windows 虚拟机 (VM)。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建 Azure Key Vault
> * 生成证书或将其上传到 Key Vault
> * 创建 VM 并安装 IIS Web 服务器
> * 将证书注入 VM 并使用 SSL 绑定配置 IIS

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

如果选择在本地安装并使用 PowerShell，则本教程需要 Azure PowerShell 模块 5.3 或更高版本。 运行 `Get-Module -ListAvailable AzureRM` 即可查找版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。 如果在本地运行 PowerShell，则还需运行 `Connect-AzureRmAccount` 以创建与 Azure 的连接。 


## <a name="overview"></a>概述
Azure Key Vault 保护加密密钥和机密、此类证书或密码。 Key Vault 有助于简化证书管理过程，让我们持续掌控用于访问这些证书的密钥。 可以在 Key Vault 中创建自签名证书，或者上传已拥有的现有受信任证书。

无需使用包含植入证书的自定义 VM 映像，而可将证书直接注入正在运行的 VM。 此过程可确保在部署过程中，在 Web 服务器上安装最新的证书。 如果续订或替换了证书，也不需要创建新的自定义 VM 映像。 创建附加的 VM 时，会自动注入最新证书。 在整个过程中，证书永远不会离开 Azure 平台，也不会在脚本、命令行历史记录或模板中公开。


## <a name="create-an-azure-key-vault"></a>创建 Azure Key Vault
创建 Key Vault 和证书之前，需使用 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) 创建资源组。 以下示例在“美国东部”位置创建名为 *myResourceGroupSecureWeb* 的资源组：

```azurepowershell-interactive
$resourceGroup = "myResourceGroupSecureWeb"
$location = "East US"
New-AzureRmResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

接下来，使用 [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault) 创建 Key Vault。 每个 Key Vault 均需具备唯一名称且全部小写。 将下例中的 `mykeyvault` 替换为自己的唯一 Key Vault 名称：

```azurepowershell-interactive
$keyvaultName="mykeyvault"
New-AzureRmKeyVault -VaultName $keyvaultName `
    -ResourceGroup $resourceGroup `
    -Location $location `
    -EnabledForDeployment
```

## <a name="generate-a-certificate-and-store-in-key-vault"></a>生成证书并存储在 Key Vault 中
针对生产用途，应使用 [Import-AzureKeyVaultCertificate](/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate) 导入由受信任提供程序签名的有效证书。 在本教程中，以下示例演示了如何使用 [Add-AzureKeyVaultCertificate](/powershell/module/azurerm.keyvault/add-azurekeyvaultcertificate) 生成一个自签名证书，该证书使用 [New-AzureKeyVaultCertificatePolicy](/powershell/module/azurerm.keyvault/new-azurekeyvaultcertificatepolicy) 指定的默认证书策略： 

```azurepowershell-interactive
$policy = New-AzureKeyVaultCertificatePolicy `
    -SubjectName "CN=www.contoso.com" `
    -SecretContentType "application/x-pkcs12" `
    -IssuerName Self `
    -ValidityInMonths 12

Add-AzureKeyVaultCertificate `
    -VaultName $keyvaultName `
    -Name "mycert" `
    -CertificatePolicy $policy 
```


## <a name="create-a-virtual-machine"></a>创建虚拟机
使用 [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) 设置 VM 的管理员用户名和密码：

```azurepowershell-interactive
$cred = Get-Credential
```

现在，可使用 [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) 创建 VM。 以下示例在“EastUS”位置创建一个名为 myVM 的 VM。 如果支持的网络资源不存在，则会创建这些资源。 此 cmdlet 还打开端口 *443*，目的是允许安全的 Web 流量。

```azurepowershell-interactive
# Create a VM
New-AzureRmVm `
    -ResourceGroupName $resourceGroup `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred `
    -OpenPorts 443

# Use the Custom Script Extension to install IIS
Set-AzureRmVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location $location `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server -IncludeManagementTools"}'
```

创建 VM 需要几分钟时间。 最后一个步骤通过 [Set-AzureRmVmExtension](/powershell/module/azurerm.compute/set-azurermvmextension) 使用 Azure 自定义脚本扩展来安装 IIS Web 服务器。


## <a name="add-a-certificate-to-vm-from-key-vault"></a>将 Key Vault 中的证书添加到 VM
若要将 Key Vault 中的证书添加到 VM，请使用 [Get-AzureKeyVaultSecret](/powershell/module/azurerm.keyvault/get-azurekeyvaultsecret) 获取证书的 ID。 使用 [Add-AzureRmVMSecret](/powershell/module/azurerm.compute/add-azurermvmsecret) 将证书添加到 VM：

```azurepowershell-interactive
$certURL=(Get-AzureKeyVaultSecret -VaultName $keyvaultName -Name "mycert").id

$vm=Get-AzureRmVM -ResourceGroupName $resourceGroup -Name "myVM"
$vaultId=(Get-AzureRmKeyVault -ResourceGroupName $resourceGroup -VaultName $keyVaultName).ResourceId
$vm = Add-AzureRmVMSecret -VM $vm -SourceVaultId $vaultId -CertificateStore "My" -CertificateUrl $certURL

Update-AzureRmVM -ResourceGroupName $resourceGroup -VM $vm
```


## <a name="configure-iis-to-use-the-certificate"></a>将 IIS 配置为使用该证书
再次通过 [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) 使用自定义脚本扩展来更新 IIS 配置。 此项更新会应用从 Key Vault 注入 IIS 的证书，并配置 Web 绑定：

```azurepowershell-interactive
$PublicSettings = '{
    "fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/secure-iis.ps1"],
    "commandToExecute":"powershell -ExecutionPolicy Unrestricted -File secure-iis.ps1"
}'

Set-AzureRmVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location $location `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -SettingString $publicSettings
```


### <a name="test-the-secure-web-app"></a>测试 Web 应用是否安全
使用 [Get-AzureRmPublicIPAddress](/powershell/resourcemanager/azurerm.network/get-azurermpublicipaddress) 获取 VM 的公共 IP 地址。 以下示例获取前面创建的 `myPublicIP` 的 IP 地址：

```azurepowershell-interactive
Get-AzureRmPublicIPAddress -ResourceGroupName $resourceGroup -Name "myPublicIPAddress" | select "IpAddress"
```

现可打开 Web 浏览器，并在地址栏中输入 `https://<myPublicIP>`。 若要接受有关使用自签名证书的安全警告，请依次选择“详细信息”和“继续转到网页”：

![接受 Web 浏览器安全警告](./media/tutorial-secure-web-server/browser-warning.png)

随即显示受保护的 IIS 网站，如下例所示：

![查看运行中的安全 IIS 站点](./media/tutorial-secure-web-server/secured-iis.png)


## <a name="next-steps"></a>后续步骤
本教程已介绍如何使用 Azure Key Vault 中存储的 SSL 证书保护 IIS Web 服务器。 你已了解如何：

> [!div class="checklist"]
> * 创建 Azure Key Vault
> * 生成证书或将其上传到 Key Vault
> * 创建 VM 并安装 IIS Web 服务器
> * 将证书注入 VM 并使用 SSL 绑定配置 IIS

请访问以下链接查看预先生成的虚拟机脚本示例。

> [!div class="nextstepaction"]
> [Windows 虚拟机脚本示例](./powershell-samples.md)
