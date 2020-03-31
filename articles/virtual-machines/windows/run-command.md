---
title: 在 Azure 中的 Windows VM 中运行 PowerShell 脚本
description: 本主题介绍了如何使用“运行命令”功能在 Azure Windows 虚拟机中运行 PowerShell 脚本
services: automation
ms.service: automation
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: article
manager: carmonm
ms.openlocfilehash: fa7f72989d47499127714eddfa6b5e98aa80178c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "73749229"
---
# <a name="run-powershell-scripts-in-your-windows-vm-by-using-run-command"></a>使用“运行命令”在 Windows VM 中运行 PowerShell 脚本

“运行命令”功能使用虚拟机 (VM) 代理在 Azure Windows VM 中运行 PowerShell 脚本。 可以使用这些脚本进行一般性的计算机或应用程序管理。 它们可以帮助你快速诊断和修正 VM 访问与网络问题，使 VM 恢复正常状态。

 

## <a name="benefits"></a>优点

可通过多种方式访问虚拟机。 “运行命令”可以使用 VM 代理在虚拟机上以远程方式运行脚本。 通过 Windows VM 的 Azure 门户、[REST API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand) 或 [PowerShell](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) 使用“运行命令”。

此功能适用于要在虚拟机中运行脚本的所有方案。 它是排查和修正因网络或管理用户配置错误而未打开 RDP 或 SSH 端口的虚拟机的唯一方法。

## <a name="restrictions"></a>限制

使用“运行命令”时存在以下限制：

* 输出限制为最后的 4,096 个字节。
* 运行脚本的最短时间大约为 20 秒。
* 脚本在 Windows 上作为系统运行。
* 一次只能运行一个脚本。
* 不支持提示输入信息（交互模式）的脚本。
* 无法取消正在运行的脚本。
* 脚本最长可以运行 90 分钟。 之后，它会超时。
* 需要从 VM 建立出站连接才能返回脚本的结果。

> [!NOTE]
> 若要正常工作，“运行命令”需要连接（通过端口 443）到 Azure 公共 IP 地址。 如果扩展无法访问这些终结点，则脚本可能会成功运行，但不会返回结果。 如果要阻止虚拟机上的流量，可以使用[服务标记](../../virtual-network/security-overview.md#service-tags)以通过 `AzureCloud` 标记允许流量发往 Azure 公共 IP 地址。

## <a name="available-commands"></a>可用命令

下表显示了可用于 Windows VM 的命令的列表。 可以使用 **RunPowerShellScript** 命令运行所需的任何自定义脚本。 使用 Azure CLI 或 PowerShell 运行命令时，为 `--command-id` 或 `-CommandId` 参数提供的值必须是下面列出的值之一。 如果指定的值不是可用的命令，将会收到以下错误：

```error
The entity was not found in this Azure location
```

|**名称**|**说明**|
|---|---|
|**RunPowerShellScript**|运行 PowerShell 脚本。|
|**EnableRemotePS**|配置计算机以启用远程 PowerShell。|
|**EnableAdminAccount**|检查本地管理员帐户是否被禁用，如果是，则启用它。|
|**IPConfig**| 显示绑定到 TCP/IP 的每个适配器的 IP 地址、子网掩码和默认网关的详细信息。|
|**RDPSettings**|检查注册表设置和域策略设置。 提供策略操作建议（如果计算机属于某个域），或者将设置修改为默认值。|
|**ResetRDPCert**|删除绑定到 RDP 侦听器的 SSL 证书，并将 RDP 侦听器安全性还原为默认值。 如果看到与证书有关的任何问题，请使用此脚本。|
|**SetRDPPort**|为远程桌面连接设置默认的或用户指定的端口号。 为端口的入站访问启用防火墙规则。|

## <a name="azure-cli"></a>Azure CLI

以下示例使用 [az vm run-command](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) 命令在 Azure Windows VM 上运行 shell 脚本。

```azurecli-interactive
# script.ps1
#   param(
#       [string]$arg1,
#       [string]$arg2
#   )
#   Write-Host This is a sample script with parameters $arg1 and $arg2

az vm run-command invoke  --command-id RunPowerShellScript --name win-vm -g my-resource-group \
    --scripts @script.ps1 --parameters "arg1=somefoo" "arg2=somebar"
```

## <a name="azure-portal"></a>Azure 门户

转到[Azure 门户](https://portal.azure.com)中的 VM，然后选择 **"操作**"下的 **"运行"命令**。 您将看到要在 VM 上运行的可用命令的列表。

![命令列表](./media/run-command/run-command-list.png)

选择要运行的命令。 某些命令可能具有可选或所需的输入参数。 对于这些命令，参数将作为文本字段显示，以便您提供输入值。 对于每个命令，可以通过展开**视图脚本**来查看正在运行的脚本。 **RunPowerShellScript**不同于其他命令，因为它允许您提供自己的自定义脚本。

> [!NOTE]
> 内置命令不可编辑。

选择该命令后，选择 **"运行"** 以运行脚本。 脚本完成后，它将返回输出窗口中的输出和任何错误。 下面的屏幕截图显示了运行 **RDPSettings** 命令时的示例输出。

![运行命令脚本输出](./media/run-command/run-command-script-output.png)

## <a name="powershell"></a>PowerShell

以下示例使用 [Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) cmdlet 在 Azure VM 上运行 PowerShell 脚本。 该 cmdlet 需要 `-ScriptPath` 参数中引用的脚本位于运行该 cmdlet 的位置本地。

```azurepowershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>限制对“运行命令”的访问

列出运行命令或显示命令详细信息需要订阅级别的 `Microsoft.Compute/locations/runCommands/read` 权限。 内置[读取者](../../role-based-access-control/built-in-roles.md#reader)角色和更高级别的角色具有此权限。

运行命令需要订阅级别的 `Microsoft.Compute/virtualMachines/runCommand/action` 权限。 [虚拟机参与者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)角色和更高级别的角色具有此权限。

您可以使用其中一个[内置角色](../../role-based-access-control/built-in-roles.md)，也可以创建[自定义角色](../../role-based-access-control/custom-roles.md)来使用 Run 命令。

## <a name="next-steps"></a>后续步骤

若要了解在 VM 中远程运行脚本和命令的其他方法，请参阅[在 Windows VM 中运行脚本](run-scripts-in-vm.md)。
