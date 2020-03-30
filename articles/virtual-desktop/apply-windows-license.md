---
title: 将 Windows 许可证应用于会话主机虚拟机 - Azure
description: 介绍如何为 Windows 虚拟桌面 VM 应用 Windows 许可证。
services: virtual-desktop
author: ChristianMontoya
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/14/2019
ms.author: chrimo
ms.openlocfilehash: 2543dd12e8a75a038a1fc04371b8c562ef696e25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254230"
---
# <a name="apply-windows-license-to-session-host-virtual-machines"></a>将 Windows 许可证应用于会话主机虚拟机

获得正确授权以运行 Windows 虚拟桌面工作负荷的客户有资格将 Windows 许可证应用于其会话主机虚拟机，并在无需支付其他许可证的情况下运行它们。 有关详细信息，请参阅[Windows 虚拟桌面定价](https://azure.microsoft.com/pricing/details/virtual-desktop/)。

## <a name="ways-to-use-your-windows-virtual-desktop-license"></a>使用 Windows 虚拟桌面许可证的方法
Windows 虚拟桌面许可允许您将许可证应用于在主机池中注册为会话主机并接收用户连接的任何 Windows 或 Windows Server 虚拟机。 此许可证不适用于作为文件共享服务器、域控制器等运行的虚拟机。

有几种方法可以使用 Windows 虚拟桌面许可证：
- 可以使用[Azure 应用商店产品](./create-host-pools-azure-marketplace.md)创建主机池及其会话主机虚拟机。 以这种方式创建的虚拟机会自动应用许可证。
- 您可以使用[GitHub Azure 资源管理器模板](./create-host-pools-arm-template.md)创建主机池及其会话主机虚拟机。 以这种方式创建的虚拟机会自动应用许可证。
- 您可以将许可证应用于现有会话主机虚拟机。 为此，请先按照使用[PowerShell 创建主机池](./create-host-pools-powershell.md)的说明创建主机池和相关 VM，然后返回到本文了解如何应用许可证。

## <a name="apply-a-windows-license-to-a-session-host-vm"></a>将 Windows 许可证应用于会话主机 VM
确保[已安装并配置最新的 Azure PowerShell](/powershell/azure/overview)。 运行以下 PowerShell cmdlet 以应用 Windows 许可证：

```powershell
$vm = Get-AzVM -ResourceGroup <resourceGroupName> -Name <vmName>
$vm.LicenseType = "Windows_Client"
Update-AzVM -ResourceGroupName <resourceGroupName> -VM $vm
```

## <a name="verify-your-session-host-vm-is-utilizing-the-licensing-benefit"></a>验证会话主机 VM 是否利用许可优势
部署 VM 后，运行此 cmdlet ot 验证许可证类型：
```powershell
Get-AzVM -ResourceGroupName <resourceGroupName> -Name <vmName>
```

具有应用 Windows 许可证的会话主机 VM 将显示如下所示的内容：

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

没有应用 Windows 许可证的 VM 将显示如下所示的内容：

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

运行以下 cmdlet 以查看在 Azure 订阅中应用 Windows 许可证的所有会话主机 VM 的列表：

```powershell
$vms = Get-AzVM
$vms | Where-Object {$_.LicenseType -like "Windows_Client"} | Select-Object ResourceGroupName, Name, LicenseType
```
