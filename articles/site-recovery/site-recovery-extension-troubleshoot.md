---
title: 排查 Azure Site Recovery 的灾难恢复的 Azure VM 扩展故障
description: 排查与 Azure Site Recovery 的灾难恢复的 Azure VM 扩展相关的问题。
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 11/27/2018
ms.openlocfilehash: a780a42179a0bacf0e4a12ba1e75ae84943539b4
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190721"
---
# <a name="troubleshoot-azure-vm-extension-issues"></a>排查 Azure VM 扩展问题

本文提供了疑难解答步骤，可帮助解决与 VM 代理和扩展相关的 Azure Site Recovery 错误。


## <a name="azure-site-recovery-extension-time-out"></a>Azure Site Recovery 扩展超时  

错误消息：“跟踪要启动的扩展操作时，任务执行超时”<br>
错误代码：“151076”

 Azure Site Recovery 在虚拟机上安装了一个扩展，作为 "启用保护" 作业的一部分。 以下任何条件都可能会阻止触发保护并导致作业失败。 完成以下故障排除步骤，然后重试操作：

- [代理安装在 VM 中，但无响应（针对 Windows Vm）](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [VM 中安装的代理已过时（适用于 Linux Vm）](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
- [Site Recovery 扩展无法更新或加载](#the-site-recovery-extension-fails-to-update-or-load)

错误消息： "上一个 Site Recovery 扩展操作花费的时间比预期更多。"<br>
错误代码：“150066”

- [代理安装在 VM 中，但无响应（针对 Windows Vm）](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [VM 中安装的代理已过时（适用于 Linux Vm）](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
- [Site Recovery 扩展状态不正确](#the-site-recovery-extension-fails-to-update-or-load)

## <a name="protection-fails-because-the-vm-agent-is-unresponsive"></a>由于 VM 代理无响应，保护失败

错误消息：“跟踪要启动的扩展操作时，任务执行超时。”<br>
错误代码：“151099”

如果虚拟机中的 Azure 来宾代理未处于 "就绪" 状态，则会发生此错误。

可以在[Azure 门户](https://portal.azure.com/)中查看 Azure 来宾代理的状态。 请在尝试保护的虚拟机上，查看**VM** > **设置** > **属性** > **代理状态**。 大多数情况下，在重新启动虚拟机后，代理的状态为 "就绪"。 但是，如果您无法重新启动或仍遇到问题，请完成以下故障排除步骤：

- [代理安装在 VM 中，但无响应（针对 Windows Vm）](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [VM 中安装的代理已过时（适用于 Linux Vm）](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)


错误消息：“跟踪要启动的扩展操作时，任务执行超时。”<br>
错误代码：“151095”

如果 Linux 计算机上的代理版本过期，则会出现此错误。 完成以下故障排除步骤：

- [VM 中安装的代理已过时（适用于 Linux Vm）](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)  

## <a name="causes-and-solutions"></a>原因和解决方法

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>代理安装在 VM 中，但无响应（针对 Windows VM）

#### <a name="solution"></a>解决方案
VM 代理可能已损坏或服务可能已停止。 重新安装 VM 代理可帮助获取最新版本。 此外，还有助于与服务重新开始通信。

1. 确定 Windows Azure 来宾代理服务是否在 VM 服务 (services.msc) 中运行。 重启 Microsoft Azure 来宾代理服务。    
1. 如果 Windows Azure 来宾代理服务在 "服务" 中不可见，请打开 "控制面板"。 请访问 "**程序和功能**" 以查看是否安装了 Windows 来宾代理服务。
1. 如果“程序和功能”中显示了 Windows Azure 来宾代理，请将其卸载。
1. 下载并安装[最新版本的代理 MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。 你需要管理员权限才能完成安装。
1. 验证 Windows Azure 来宾代理服务是否出现在 "服务" 中。
1. 重启保护作业。

此外，检查是否在 VM 中[安装了 Microsoft .NET 4.5](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed)。 需要为 VM 代理提供 .NET 4.5，才能与服务进行通信。

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>VM 中安装的代理已过时（针对 Linux VM）

#### <a name="solution"></a>解决方案
对于 Linux VM，与代理或扩展相关的大多数失败都是由于影响过时的 VM 代理的问题所造成的。 若要解决此问题，请遵循以下一般性指导：

1. 按照[更新 Linux VM 代理](../virtual-machines/linux/update-agent.md)的说明进行操作。

   > [!NOTE]
   > 我们强烈建议只通过分发存储库更新代理。 我们不建议直接从 GitHub 下载代理代码并对其进行更新。 如果分发的最新代理不可用，请联系分发支持部门，以获取有关如何安装它的说明。 若要检查最新代理，请转到 GitHub 存储库中的 [Microsoft Azure Linux 代理](https://github.com/Azure/WALinuxAgent/releases)页。

1. 运行以下命令，确保 Azure 代理可在 VM 上运行：`ps -e`

   如果该进程未运行，请使用以下命令进行重启：

   - 对于 Ubuntu：`service walinuxagent start`
   - 对于其他分发版：`service waagent start`

1. [配置自动重新启动代理](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash)。
1. 启用对虚拟机的保护。

### <a name="the-site-recovery-extension-fails-to-update-or-load"></a>Site Recovery 扩展未能更新或加载

扩展状态显示为 "Empty"、"NotReady" 或 "过渡"。

#### <a name="solution"></a>解决方案

卸载扩展并再次重启操作。

卸载扩展：

1. 在[Azure 门户](https://portal.azure.com/)中，请切换到遇到备份失败的 VM。
1. 单击“设置”。
1. 选择“扩展”。
1. 选择“Site Recovery 扩展”。
1. 选择“卸载”。

对于 Linux VM，如果 Azure 门户中未显示 VMSnapshot 扩展，请[更新 Azure Linux 代理](../virtual-machines/linux/update-agent.md)。 然后运行保护。

完成这些步骤后，会在保护期间重新安装扩展。
