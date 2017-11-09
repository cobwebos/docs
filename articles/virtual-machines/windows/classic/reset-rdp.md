---
title: "在 Azure 的 Windows VM 上重置密码或远程桌面配置 | Microsoft Docs"
description: "了解如何使用 Azure 门户或 Azure PowerShell 在通过经典部署模型创建的 Windows VM 上重置帐户密码或远程桌面服务。"
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: iainfou
ms.openlocfilehash: 200e5667f4bc2ca0b7dc041eef990b7aff0914ff
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/09/2017
---
# <a name="how-to-reset-the-remote-desktop-service-or-its-login-password-in-a-windows-vm-created-using-the-classic-deployment-model"></a>如何在使用经典部署模型创建的 Windows VM 中重置远程桌面服务或其登录密码
> [!IMPORTANT]
> Azure 具有用于创建和处理资源的两个不同的部署模型： [Resource Manager 和经典](../../../resource-manager-deployment-model.md)。 本文介绍如何使用经典部署模型。 Microsoft 建议大多数新部署使用 Resource Manager 模型。 还可对[使用 Resource Manager 部署模型创建的 VM 执行这些步骤](../reset-rdp.md)。
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]


如果无法连接到 Windows 虚拟机 (VM)，可以重置本地管理员密码或远程桌面服务配置。 可以使用 Azure 门户或 Azure PowerShell 中的 VM 访问扩展重置密码。

## <a name="ways-to-reset-configuration-or-credentials"></a>重置配置或凭据的方式
可以根据需要，通过多种不同的方式重置远程桌面服务和凭据：

- [使用 Azure 门户进行重置](#azure-portal)
- [使用 Azure PowerShell 进行重置](#vmaccess-extension-and-powershell)

## <a name="azure-portal"></a>Azure 门户
可使用 [Azure 门户](https://portal.azure.com)重置远程桌面服务。 要展开门户菜单，请单击左上角的三栏，并单击“虚拟机(经典)”：

![浏览 Azure VM](./media/reset-rdp/Portal-Select-Classic-VM.png)

选择 Windows 虚拟机，并单击“重置远程...”。重置远程桌面配置时会显示以下对话：

![重置 RDP 配置页](./media/reset-rdp/Portal-RDP-Reset-Windows.png)

还可以重置本地管理员帐户的用户名和密码。 从 VM 中单击“支持 + 故障排除” > “重置密码”。 此时会显示密码重置边栏选项卡：

![密码重置页](./media/reset-rdp/Portal-PW-Reset-Windows.png)

输入新用户名和密码，并单击“保存”。

## <a name="vmaccess-extension-and-powershell"></a>VMAccess 扩展和 PowerShell
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
使用当前的本地管理员帐户名和新密码创建登录凭据，并运行 `Set-AzureVMAccessExtension`，如下所示。

```powershell
$cred=Get-Credential
Set-AzureVMAccessExtension –vm $vm -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password  | Update-AzureVM
```

如果键入不同于当前帐户的名称，VMAccess 扩展将重命名本地管理员帐户，将密码分配给该帐户，并发出远程桌面注销命令。如果本地管理员帐户处于禁用状态，则 VMAccess 扩展将启用它。

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
如果 Azure VM 访问扩展无响应，并且密码无法重置，可以[脱机重置本地 Windows 密码](../reset-local-password-without-agent.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 此方法是一个较高级的过程，要求将有问题的 VM 的虚拟硬盘连接到另一个 VM。 请先遵循本文中所述的步骤，在万不得已的情况下，才尝试脱机重置密码的方法。

[Azure VM 扩展和功能](../extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[使用 RDP 或 SSH 连接到 Azure 虚拟机](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[对与基于 Windows 的 Azure 虚拟机的远程桌面连接进行故障排除](../troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

