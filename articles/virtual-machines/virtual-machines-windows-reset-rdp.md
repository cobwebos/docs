---
title: "在 Windows VM 上重置密码或远程桌面配置 | Microsoft Docs"
description: "了解如何使用 Azure 门户或 Azure PowerShell 在 Windows VM 上重置帐户密码或远程桌面服务。"
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 45c69812-d3e4-48de-a98d-39a0f5675777
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 8062106872bd50cd265fc4e64bc6f4c0fb2dbb1d
ms.openlocfilehash: b7458e13174dc03f2c4cab707c033d96a80628e6


---
# <a name="how-to-reset-the-remote-desktop-service-or-its-login-password-in-a-windows-vm"></a>如何在 Windows VM 中重置远程桌面服务或其登录密码
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

如果无法连接到 Windows 虚拟机 (VM)，可以重置本地管理员密码或远程桌面服务配置。 可以使用 Azure 门户或 Azure PowerShell 中的 VM 访问扩展重置密码。 如果使用 PowerShell，请务必在工作计算机上安装最新的 PowerShell 模块，并登录 Azure 订阅。 有关详细步骤，请阅读 [How to install and configure Azure PowerShell](/powershell/azureps-cmdlets-docs)（如何安装和配置 Azure PowerShell）。

> [!TIP]
> 可以使用以下方式检查安装的 PowerShell 版本：
>
> `Import-Module Azure, AzureRM; Get-Module Azure, AzureRM | Format-Table Name, Version`


## <a name="ways-to-reset-configuration-or-credentials"></a>重置配置或凭据的方式
可以通过多种不同的方式重置远程桌面服务和凭据，具体取决于用户需求。 对于使用 Resource Manager 部署模型创建的 VM：

- [使用 Azure 门户进行重置](#azure-portal---resource-manager)
- [使用 Azure PowerShell 进行重置](#vmaccess-extension-and-powershell---resource-manager)

对于使用经典部署模型创建的 VM：

- [使用 Azure 门户进行重置](#azure-portal---classic)
- [使用 Azure PowerShell 进行重置](#vmaccess-extension-and-powershell---classic)

## <a name="azure-portal---resource-manager"></a>Azure 门户 - Resource Manager
若要展开门户菜单，请单击左上角的三栏，然后单击“虚拟机”：

![浏览 Azure VM](./media/virtual-machines-windows-reset-rdp/Portal-Select-VM.png)

选择 Windows 虚拟机，然后单击“支持 + 故障排除” > “重置密码”。 此时会显示密码重置边栏选项卡：

![密码重置页](./media/virtual-machines-windows-reset-rdp/Portal-RM-PW-Reset-Windows.png)

输入用户名和新密码，然后单击“保存”。 尝试重新连接到 VM。


## <a name="vmaccess-extension-and-powershell---resource-manager"></a>VMAccess 扩展和 PowerShell - Resource Manager
确保已安装 Azure PowerShell 1.0 或更高版本，并且已使用 `Login-AzureRmAccount` cmdlet 登录到帐户。

### <a name="reset-the-local-administrator-account-password"></a>**重置本地管理员帐户密码**
可以使用 [Set-AzureRmVMAccessExtension](https://msdn.microsoft.com/library/mt619447.aspx) PowerShell 命令来重置管理员密码或用户名。

使用以下命令创建本地管理员帐户凭据：

```powershell
$cred=Get-Credential
```

如果键入不同于当前帐户的名称，以下 VMAccess 扩展命令将重命名本地管理员帐户，将密码分配给该帐户，并发出远程桌面注销事件。 如果本地管理员帐户处于禁用状态，则 VMAccess 扩展将启用它。

使用 VM Access 扩展设置新凭据，如下所示：

```powershell
Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" `
    -Name "myVMAccess" -Location WestUS -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"
```

将 `myResourceGroup`、`myVM`、`myVMAccess` 和 location 替换为与设置相关的值。

### <a name="reset-the-remote-desktop-service-configuration"></a>**重置远程桌面服务配置**
可以使用 [Set-AzureRmVMExtension](https://msdn.microsoft.com/library/mt603745.aspx) 或 [Set-AzureRmVMAccessExtension](https://msdn.microsoft.com/library/mt619447.aspx) 来重置对 VM 的远程访问，如下所示。 （将 `myResourceGroup`、`myVM`、`myVMAccess` 和 location 替换为自己的值。）

```powershell
Set-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" `
    -Name "myVMAccess" -ExtensionType "VMAccessAgent" -Location WestUS `
    -Publisher "Microsoft.Compute" -typeHandlerVersion "2.0"
```

或：

```powershell
Set-AzureRmVMAccessExtension -ResourceGroupName "myResoureGroup" -VMName "myVM" `
    -Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0
```

> [!TIP]
> 这两个命令都在虚拟机中添加新的命名 VM 访问代理。 无论何时，一个 VM 只能有一个 VM 访问代理。 若要成功设置 VM 访问代理属性，请使用 `Remove-AzureRmVMAccessExtension` 或 `Remove-AzureRmVMExtension` 删除以前设置的访问代理。 
>
> 从 Azure PowerShell 版本 1.2.2 开始，如果将 `Set-AzureRmVMExtension` 与 `-ForceRerun` 选项结合使用，则无需执行此步骤。 使用 `-ForceRerun` 时，请务必使用与前述命令设置的 VM 访问代理相同的名称。

如果仍然无法远程连接到虚拟机，请参阅 [Troubleshoot Remote Desktop connections to a Windows-based Azure virtual machine](virtual-machines-windows-troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)（对与基于 Windows 的 Azure 虚拟机的远程桌面连接进行故障排除），了解其他值得一试的步骤。


## <a name="azure-portal---classic"></a>Azure 门户 - 经典
对于使用经典部署模型创建的虚拟机，可以使用 [Azure 门户](https://portal.azure.com)来重置远程桌面服务。 若要展开门户菜单，请单击左上角的三栏，然后单击“虚拟机(经典)”：

![浏览 Azure VM](./media/virtual-machines-windows-reset-rdp/Portal-Select-Classic-VM.png)

选择 Windows 虚拟机，然后单击“重置远程...”。 重置远程桌面配置时将显示以下对话：

![重置 RDP 配置页](./media/virtual-machines-windows-reset-rdp/Portal-RDP-Reset-Windows.png)

还可以重置本地管理员帐户的用户名和密码。 从 VM 中单击“支持 + 故障排除” > “重置密码”。 此时会显示密码重置边栏选项卡：

![密码重置页](./media/virtual-machines-windows-reset-rdp/Portal-PW-Reset-Windows.png)

输入新用户名和密码，然后单击“保存”。

## <a name="vmaccess-extension-and-powershell---classic"></a>VMAccess 扩展和 PowerShell - 经典
确保在虚拟机上安装 VM 代理。 只要 VM 代理可用，就不需要事先安装 VMAccess 扩展。 使用以下命令验证是否已在虚拟机上安装 VM 代理。 （分别将“myCloudService”和“myVM”替换为云服务和 VM 的名称。 若要了解这些名称，可运行不带任何参数的 `Get-AzureVM`。）

```powershell
$vm = Get-AzureVM -ServiceName "myCloudService" -Name "myVM"
write-host $vm.VM.ProvisionGuestAgent
```

如果 **write-host** 命令显示 **True**，则已安装 VM 代理。 如果该命令显示 **False**，请参阅 Azure 博客文章 [VM 代理和扩展 - 第 2 部分](http://go.microsoft.com/fwlink/p/?linkid=403947&clcid=0x409)中的说明和下载链接。

如果虚拟机是使用门户创建的，请检查 `$vm.GetInstance().ProvisionGuestAgent` 是否返回 **True**。 如果不是，请使用以下命令进行设置：

```powershell
$vm.GetInstance().ProvisionGuestAgent = $true
```

此命令可防止在后续步骤中运行 **Set-AzureVMExtension** 命令时出现以下错误：“在设置 IaaS VM Access 扩展前，必须对 VM 对象启用预配来宾代理。”

### <a name="reset-the-local-administrator-account-password"></a>**重置本地管理员帐户密码**
使用当前的本地管理员帐户名和新密码创建登录凭据，然后运行 `Set-AzureVMAccessExtension`，如下所示。

```powershell
$cred=Get-Credential
Set-AzureVMAccessExtension –vm $vm -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password  | Update-AzureVM
```

如果你键入不同于当前帐户的名称，VMAccess 扩展将重命名本地管理员帐户，将密码分配给该帐户，并发出远程桌面注销命令。 如果本地管理员帐户处于禁用状态，则 VMAccess 扩展将启用它。

这些命令也重置远程桌面服务配置。

### <a name="reset-the-remote-desktop-service-configuration"></a>**重置远程桌面服务配置**
若要重置远程桌面服务配置，请运行以下命令：

```powershell
Set-AzureVMAccessExtension –vm $vm | Update-AzureVM
```

VMAccess 扩展在虚拟机上运行两个命令：

```powershell
netsh advfirewall firewall set rule group="Remote Desktop" new enable=Yes
```

此命令启用允许传入远程桌面流量的内置 Windows 防火墙组，该组使用 TCP 端口 3389。

```powershell
Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0
```

此命令将 fDenyTSConnections 注册表值设为 0，以启用远程桌面连接。

## <a name="next-steps"></a>后续步骤
如果 Azure VM 访问扩展无响应，并且密码无法重置，可以[脱机重置本地 Windows 密码](virtual-machines-windows-reset-local-password-without-agent.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 此方法是一个较高级的过程，要求将有问题的 VM 的虚拟硬盘连接到另一个 VM。 请先遵循本文中所述的步骤，在万不得已的情况下，才尝试脱机重置密码的方法。

[Azure VM 扩展和功能](virtual-machines-windows-extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[使用 RDP 或 SSH 连接到 Azure 虚拟机](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[对与基于 Windows 的 Azure 虚拟机的远程桌面连接进行故障排除](virtual-machines-windows-troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)




<!--HONumber=Jan17_HO3-->


