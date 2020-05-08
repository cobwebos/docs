---
title: 将 Windows 许可证应用到会话主机虚拟机-Azure
description: 描述如何为 Windows 虚拟桌面 Vm 应用 Windows 许可证。
services: virtual-desktop
author: ChristianMontoya
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/14/2019
ms.author: chrimo
ms.openlocfilehash: 994edf26e43c7ad67d4f8822d4ed0d18d53b510b
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612446"
---
# <a name="apply-windows-license-to-session-host-virtual-machines"></a>将 Windows 许可证应用到会话主机虚拟机

已获得正确授权运行 Windows 虚拟桌面工作负载的客户有资格将 Windows 许可证应用到其会话主机虚拟机，并运行这些虚拟机，而无需支付其他许可证。 有关详细信息，请参阅[Windows 虚拟桌面定价](https://azure.microsoft.com/pricing/details/virtual-desktop/)。

## <a name="ways-to-use-your-windows-virtual-desktop-license"></a>使用 Windows 虚拟桌面许可证的方式
使用 windows 虚拟桌面许可，你可以将许可证应用到在主机池中注册为会话主机的任何 Windows 或 Windows Server 虚拟机，并接收用户连接。 此许可证不适用于作为文件共享服务器、域控制器等运行的虚拟机。

可以通过多种方式使用 Windows 虚拟桌面许可证：
- 可以使用[Azure Marketplace 产品/服务](./create-host-pools-azure-marketplace.md)创建主机池及其会话主机虚拟机。 以这种方式创建的虚拟机将自动应用许可证。
- 可以使用[GitHub Azure 资源管理器模板](./virtual-desktop-fall-2019/create-host-pools-arm-template.md)创建主机池及其会话主机虚拟机。 以这种方式创建的虚拟机将自动应用许可证。
- 可以将许可证应用到现有的会话主机虚拟机。 为此，请首先按照[使用 PowerShell 创建主机池](./create-host-pools-powershell.md)中的说明创建主机池和关联的 vm，然后返回到本文了解如何应用许可证。

## <a name="apply-a-windows-license-to-a-session-host-vm"></a>将 Windows 许可证应用到会话主机 VM
确保[已安装并配置最新的 Azure PowerShell](/powershell/azure/overview)。 运行以下 PowerShell cmdlet 以应用 Windows 许可证：

```powershell
$vm = Get-AzVM -ResourceGroup <resourceGroupName> -Name <vmName>
$vm.LicenseType = "Windows_Client"
Update-AzVM -ResourceGroupName <resourceGroupName> -VM $vm
```

## <a name="verify-your-session-host-vm-is-utilizing-the-licensing-benefit"></a>验证会话主机 VM 是否正在利用许可权益
部署 VM 后，运行此 cmdlet，验证许可证类型：
```powershell
Get-AzVM -ResourceGroupName <resourceGroupName> -Name <vmName>
```

带有应用的 Windows 许可证的会话主机 VM 将显示类似于下面的内容：

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

没有已应用的 Windows 许可证的 Vm 将显示如下所示的内容：

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

运行以下 cmdlet，以查看在 Azure 订阅中应用了 Windows 许可证的所有会话主机 Vm 的列表：

```powershell
$vms = Get-AzVM
$vms | Where-Object {$_.LicenseType -like "Windows_Client"} | Select-Object ResourceGroupName, Name, LicenseType
```
