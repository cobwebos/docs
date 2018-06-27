---
title: 在 Azure 中的 Windows VM 中运行 PowerShell 脚本
description: 本主题介绍了如何使用“运行命令”在 Azure Windows 虚拟机中运行 PowerShell 脚本。
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/06/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 3feed9c1c8903db66a0506f09161982dadaa79ba
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2018
ms.locfileid: "36284958"
---
# <a name="run-powershell-scripts-in-your-windows-vm-with-run-command"></a>使用“运行命令”在 Windows VM 中运行 PowerShell 脚本

“运行命令”使用 VM 代理在 Azure Windows VM 中运行 shell PowerShell 脚本。 这些脚本可以用于常规的计算机或应用程序管理，并且可以用来快速诊断和修正 VM 访问和网络问题并使 VM 恢复正常运行状态。

## <a name="benefits"></a>优点

有多个选项可以用来访问虚拟机。 “运行命令”可以使用 VM 代理在虚拟机上以远程方式运行脚本。 可以通过 Azure 门户、[REST API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand)、[Azure CLI](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) 或 [PowerShell](/powershell/module/azurerm.compute/invoke-azurermvmruncommand) 使用“运行命令”。

在需要在虚拟机中运行脚本的所有方案中，此功能都很有用，并且它可以用来对由于网络或管理用户配置不正确而没有打开 RDP 或 SSH 端口的虚拟机进行故障排除和修正，是具有此用途的少数方法之一。

## <a name="restrictions"></a>限制

使用“运行命令”时存在以下限制：

* 输出限制为最后的 4096 个字节
* 运行脚本的最短时间大约为 20 秒
* 脚本以系统身份在 Windows 上运行
* 一次只能运行一个脚本
* 不支持提示输入信息（交互模式）的脚本。
* 无法取消正在运行的脚本
* 脚本最多可以运行 90 分钟，之后它将超时

## <a name="run-a-command"></a>运行命令

导航到 [Azure](https://portal.azure.com) 中的某个 VM，然后在“操作”下选择“运行命令”。 将会显示可以在 VM 上运行的可用命令的列表。

![运行命令列表](./media/run-command/run-command-list.png)

选择要运行的命令。 某些命令可能有可选或必需的输入参数。 对于这些命令，参数将呈现为文本字段，你可以在其中提供输入值。 对于每个命令，可以通过展开“查看脚本”来查看所运行的脚本。 **RunPowerShellScript** 不同于其他命令，因为它允许你提供自己的自定义脚本。

> [!NOTE]
> 内置命令不可编辑。

选择命令后，单击“运行”来运行脚本。 脚本将运行，完成时，将在输出窗口中返回输出和任何错误。 下面的屏幕截图显示了运行 **RDPSettings** 命令时的示例输出。

![运行命令脚本输出](./media/run-command/run-command-script-output.png)

## <a name="commands"></a>命令

下表显示了可用于 Windows VM 的命令的列表。 **RunPowerShellScript** 命令可用来运行你需要的任何自定义脚本。

|名称|**说明**|
|---|---|
|**RunPowerShellScript**|执行 PowerShell 脚本|
|**EnableRemotePS**|配置计算机以启用远程 PowerShell。|
|**EnableAdminAccount**|检查本地管理员帐户是否被禁用，如果是，则启用它。|
|**IPConfig**| 显示绑定到 TCP/IP 的每个适配器的 IP 地址、子网掩码和默认网关的详细信息。|
|**RDPSettings**|检查注册表设置和域策略设置。 提供策略操作建议（如果计算机属于某个域），或者将设置修改为默认值。|
|**ResetAccountPassword**| 重置内置的管理员帐户密码。|
|**ResetRDPCert**|删除绑定到 RDP 侦听器的 SSL 证书，并将 RDP 侦听器安全性还原为默认值。 如果看到与证书有关的任何问题，请使用此脚本。|
|**SetRDPPort**|为远程桌面连接设置默认的或用户指定的端口号。 为端口的入站访问启用防火墙规则。|

## <a name="powershell"></a>PowerShell

下面是使用 [Invoke-AzureRmVMRunCommand](/powershell/module/azurerm.compute/invoke-azurermvmruncommand) cmdlet 在 Azure VM 上运行 PowerShell 脚本的示例。

```azurepowershell-interactive
Invoke-AzureRmVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>限制对“运行命令”的访问

列出“运行命令”或显示某个命令的详细信息需要 `Microsoft.Compute/locations/runCommands/read` 权限，内置的[读者](../../role-based-access-control/built-in-roles.md#reader)角色或更高角色具有此权限。

运行某个命令需要 `Microsoft.Compute/virtualMachines/runCommand/action` 权限，[参与者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)角色和更高角色具有此权限。

若要使用“运行命令”，可以使用[内置](../../role-based-access-control/built-in-roles.md)角色之一，也可以创建一个[自定义](../../role-based-access-control/custom-roles.md)角色。

## <a name="next-steps"></a>后续步骤

参阅[在 Windows VM 中运行脚本](run-scripts-in-vm.md)来了解远程在 VM 中运行脚本和命令的其他方式。