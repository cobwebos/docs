---
title: 排查使用 Azure Site Recovery 进行灾难恢复时的 Azure VM 扩展问题
description: 排查使用 Azure Site Recovery 进行灾难恢复时的 Azure VM 扩展问题。
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 11/27/2018
ms.openlocfilehash: c1915d108bf9465d3e5b8d6a55053b583ee4f580
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88184612"
---
# <a name="troubleshoot-azure-vm-extension-issues"></a>排查 Azure VM 扩展问题

本文提供了故障排查步骤，可帮助你解决与 VM 代理和扩展相关的 Azure Site Recovery 错误。

## <a name="low-system-resources"></a>系统资源不足

如果系统内存不足，且无法为移动服务安装分配内存，则会出现此问题。 确保已释放足够的内存，以便安装继续进行并成功完成。

## <a name="azure-site-recovery-extension-time-out"></a>Azure Site Recovery 扩展超时  

错误消息：“跟踪要启动的扩展操作时，任务执行超时”<br>
错误代码：“151076”

 Azure Site Recovery 在启用保护作业时在虚拟机上安装了一个扩展。 以下任何条件都可能阻止触发保护并导致作业失败。 完成以下故障排除步骤，然后重试操作：

- [代理安装在 VM 中，但无响应（针对 Windows VM）](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [VM 中安装的代理已过时（针对 Linux VM）](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
- [Site Recovery 扩展未能更新或加载](#the-site-recovery-extension-fails-to-update-or-load)

错误消息：“之前的 Site Recovery 扩展操作花费的时间比预期更多。”<br>
错误代码：“150066”

- [代理安装在 VM 中，但无响应（针对 Windows VM）](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [VM 中安装的代理已过时（针对 Linux VM）](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
- [Site Recovery 扩展状态不正确](#the-site-recovery-extension-fails-to-update-or-load)

## <a name="protection-fails-because-the-vm-agent-is-unresponsive"></a>由于 VM 代理无响应，保护失败

错误消息：“跟踪要启动的扩展操作时，任务执行超时。”<br>
错误代码：“151099”

如果虚拟机中的 Azure 来宾代理未处于就绪状态，则会发生此错误。

可以在 [Azure 门户](https://portal.azure.com/)中检查 Azure 来宾代理的状态。 转到你尝试保护的虚拟机，在“VM” > “设置” > “属性” > “代理状态”中检查状态。    大多数情况下，重新启动虚拟机后，代理的状态是准备就绪。 但是，如果无法重启或仍然面临问题，请完成以下故障排除步骤：

- [代理安装在 VM 中，但无响应（针对 Windows VM）](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [VM 中安装的代理已过时（针对 Linux VM）](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)


错误消息：“跟踪要启动的扩展操作时，任务执行超时。”<br>
错误代码：“151095”

如果 Linux 计算机上的代理版本过时，则可能发生此错误。 请完成以下故障排除步骤：

- [VM 中安装的代理已过时（针对 Linux VM）](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)  

## <a name="causes-and-solutions"></a>原因和解决方案

### <a name="the-agent-is-installed-in-the-vm-but-its-unresponsive-for-windows-vms"></a><a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>代理安装在 VM 中，但无响应（针对 Windows VM）

#### <a name="solution"></a>解决方案
VM 代理可能已损坏或服务可能已停止。 重新安装 VM 代理可帮助获取最新版本。 此外，还有助于与服务重新开始通信。

1. 确定 Windows Azure 来宾代理服务是否在 VM 服务 (services.msc) 中运行。 重启 Windows Azure 来宾代理服务。    
1. 如果“Windows Azure 来宾代理”服务在“服务”中不可见，请打开“控制面板”。 转到“程序和功能”来查看是否安装了“Windows 来宾代理”服务。
1. 如果“程序和功能”中显示了 Windows Azure 来宾代理，请将其卸载****。
1. 下载并安装[最新版本的代理 MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。 必须拥有管理员权限才能完成安装。
1. 检查服务中是否显示了“Windows Azure 来宾代理”服务。
1. 重启保护作业。

此外，检查是否在 VM 中[安装了 Microsoft .NET 4.5](/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed)。 需要具有 .NET 4.5，VM 代理才能与该服务进行通信。

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>VM 中安装的代理已过时（针对 Linux VM）

#### <a name="solution"></a>解决方案
对于 Linux VM，与代理或扩展相关的大多数失败都是由于影响过时的 VM 代理的问题所造成的。 若要解决此问题，请遵循以下通用准则：

1. 按照[更新 Linux VM 代理](../virtual-machines/extensions/update-linux-agent.md)的说明进行操作。

   > [!NOTE]
   > *强烈建议* 只通过分发存储库更新代理。 建议不要直接从 GitHub 下载代理代码并将其更新。 如果你的分发没有可用的最新代理，请联系分发支持部门，了解如何安装最新代理。 若要检查最新代理，请转到 GitHub 存储库中的 [Microsoft Azure Linux 代理](https://github.com/Azure/WALinuxAgent/releases)页。

1. 运行以下命令，确保 Azure 代理可在 VM 上运行：`ps -e`

   如果该进程未运行，请使用以下命令进行重启：

   - 对于 Ubuntu： `service walinuxagent start`
   - 对于其他分发版： `service waagent start`

1. [配置自动重启代理](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash)。
1. 启用对虚拟机的保护。

### <a name="the-site-recovery-extension-fails-to-update-or-load"></a>Site Recovery 扩展未能更新或加载

扩展状态显示为“空”、“未就绪”或“正在转换”。

#### <a name="solution"></a>解决方案

卸载扩展并再次重启操作。

卸载扩展：

1. 在 [Azure 门户](https://portal.azure.com/)中，找到备份失败的 VM。
1. 选择“设置”  。
1. 选择“扩展”。
1. 选择“Site Recovery 扩展”****。
1. 选择“卸载” 。

对于 Linux VM，如果 VMSnapshot 扩展未显示在 Azure 门户中，请[更新 Azure Linux 代理](../virtual-machines/extensions/update-linux-agent.md)。 然后运行保护。

完成这些步骤后，会在保护期间重新安装扩展。
