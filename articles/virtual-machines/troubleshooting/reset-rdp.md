---
title: 重置远程桌面服务或其在 Windows VM 中的管理员密码 | Microsoft Docs
description: 了解如何使用 Azure 门户或 Azure PowerShell 在 Windows VM 上重置帐户密码或远程桌面服务。
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 45c69812-d3e4-48de-a98d-39a0f5675777
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 77eb372bbe3647e03919aae21087d3270c22148a
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2019
ms.locfileid: "55506551"
---
# <a name="reset-remote-desktop-services-or-its-administrator-password-in-a-windows-vm"></a>重置远程桌面服务或其在 Windows VM 中的管理员密码
如果无法连接到 Windows 虚拟机 (VM)，可以重置本地管理员密码或远程桌面服务配置（Windows 域控制器不支持此操作）。 若要重置密码，可以使用 Azure 门户或 Azure PowerShell 中的 VM 访问扩展。 登录到 VM 后，重置该本地管理员的密码。  
如果使用 PowerShell，请务必[安装和配置最新的 PowerShell 模块](/powershell/azure/overview)，并登录到 Azure 订阅。 也可以[对使用经典部署模型创建的 VM 执行这些步骤](https://docs.microsoft.com/azure/virtual-machines/windows/classic/reset-rdp)。

可按以下方式重置远程桌面服务和凭据：

- [使用 Azure 门户重置](#reset-by-using-the-azure-portal)

- [使用 VMAccess 扩展和 PowerShell 重置](#reset-by-using-the-vmaccess-extension-and-powershell)

## <a name="reset-by-using-the-azure-portal"></a>使用 Azure 门户重置

首先，登录到 [Azure 门户](https://portal.azure.com)，然后在左侧菜单中选择“虚拟机”。 

### <a name="reset-the-local-administrator-account-password"></a>**重置本地管理员帐户密码**

1. 选择 Windows VM，然后在“支持 + 故障排除”下选择“重置密码”。 此时会显示“重置密码”窗口。

1. 选择“重置密码”，输入用户名和密码，然后选择“更新”。 

1. 尝试重新连接到 VM。

### <a name="reset-the-remote-desktop-services-configuration"></a>**重置远程桌面服务配置**

1. 选择 Windows VM，然后在“支持 + 故障排除”下选择“重置密码”。 此时会显示“重置密码”窗口。 

1. 依次选择“仅重置配置”、“更新”。 

1. 尝试重新连接到 VM。


## <a name="reset-by-using-the-vmaccess-extension-and-powershell"></a>使用 VMAccess 扩展和 PowerShell 重置

首先，请确保已[安装并配置最新的 PowerShell 模块](/powershell/azure/overview)，然后使用 [Connect-AzureRmAccount](https://docs.microsoft.com/powershell/module/azurerm.profile/connect-azurermaccount) cmdlet 登录到 Azure 订阅。

### <a name="reset-the-local-administrator-account-password"></a>**重置本地管理员帐户密码**

- 使用 [Set-AzureRmVMAccessExtension](/powershell/module/azurerm.compute/set-azurermvmaccessextension) PowerShell cmdlet 重置管理员密码或用户名。 `typeHandlerVersion` 设置必须为 2.0 或以上，因为版本 1 已弃用。 

    ```powershell
    $SubID = "<SUBSCRIPTION ID>" 
    $RgName = "<RESOURCE GROUP NAME>" 
    $VmName = "<VM NAME>" 
    $Location = "<LOCATION>" 
 
    Connect-AzureRmAccount 
    Select-AzureRMSubscription -SubscriptionId $SubID 
    Set-AzureRmVMAccessExtension -ResourceGroupName $RgName -Location $Location -VMName $VmName -Credential (get-credential) -typeHandlerVersion "2.0" -Name VMAccessAgent 
    ```

    > [!NOTE] 
    > 如果在 VM 上输入不同于当前本地管理员帐户的名称，则 VMAccess 扩展使用该名称添加本地管理员帐户，将指定密码分配给该帐户。 如果 VM 上的本地管理员帐户存在，则 VMAccess 扩展将重置密码。 如果该帐户已禁用，则 VMAccess 扩展会将其启用。

### <a name="reset-the-remote-desktop-services-configuration"></a>**重置远程桌面服务配置**

1. 使用 [Set-AzureRmVMAccessExtension](/powershell/module/azurerm.compute/set-azurermvmaccessextension) PowerShell cmdlet 重置对 VM 的远程访问。 以下示例在名为 `myResourceGroup` 的资源组中名为 `myVM` 的 VM 上重置名为 `myVMAccess` 的访问扩展：

    ```powershell
    Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0" -ForceRerun
    ```

    > [!TIP]
    > 无论何时，一个 VM 只能有一个 VM 访问代理。 若要设置 VM 访问代理属性，请使用 `-ForceRerun` 选项。 使用 `-ForceRerun` 时，请确保对 VM 访问代理使用在前面的任何命令中可能用过的相同名称。

1. 如果仍然无法远程连接到虚拟机，请参阅[排查基于 Windows 的 Azure 虚拟机的远程桌面连接问题](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 如果与 Windows 域控制器的连接断开，则需要从域控制器备份中还原。

## <a name="next-steps"></a>后续步骤

- 如果 Azure VM 访问扩展无响应，并且密码无法重置，可以[脱机重置本地 Windows 密码](reset-local-password-without-agent.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 这是一个较高级的方法，需要将有问题的 VM 的虚拟硬盘连接到另一个 VM。 请先遵循本文中所述的步骤，仅当这些步骤不起作用时，才尝试脱机重置密码的方法。

- [了解 Azure VM 扩展和功能](../extensions/features-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

- [使用 RDP 或 SSH 连接到 Azure 虚拟机](https://msdn.microsoft.com/library/azure/dn535788.aspx)。

- [对与基于 Windows 的 Azure 虚拟机的远程桌面连接进行故障排除](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

