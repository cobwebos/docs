---
title: 排除 Azure Site Recovery 代理故障：来宾代理状态不可用 | Microsoft Docs
description: 与代理和扩展相关的 Azure Site Recovery 故障的症状、原因及解决方法
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 05/02/2018
ms.author: asgang
ms.openlocfilehash: 9bfe181b2271f4e8af6f43e1728167712dade8ee
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
ms.locfileid: "33777587"
---
# <a name="troubleshoot-azure-site-recovery-extension-failures-issues-with-the-agent-or-extension"></a>排除 Azure Site Recovery 扩展故障：代理或扩展的问题

本文提供故障排查步骤，可帮助解决与 VM 代理和扩展相关的 Azure Site Recovery 错误。


## <a name="azure-site-recovery-extension-time-out"></a>Azure Site Recovery 扩展超时  

错误消息：“跟踪要启动的扩展操作时，任务执行超时”<br>
错误代码：“151076”

 Azure Site Recovery 在启用保护作业时在虚拟机上安装扩展。 以下任何条件都可能阻止触发保护并导致作业失败。 完成以下故障排除步骤，然后重试操作：

**原因 1：[代理安装在 VM 中，但无响应（针对 Windows VM）](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**    
**原因 2：[VM 中安装的代理已过时（针对 Linux VM）](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**原因 3：[Site Recovery 扩展未能更新或加载](#the-site-recovery-extension-fails-to-update-or-load)**  

错误消息：“之前的站点恢复扩展操作花费的时间比预期更多。”<br>
错误代码：“150066”<br>

**原因 1：[代理安装在 VM 中，但无响应（针对 Windows VM）](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**    
**原因 2：[VM 中安装的代理已过时（针对 Linux VM）](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**原因 3：[Site Recovery 扩展状态不正确](#the-site-recovery-extension-fails-to-update-or-load)**  

## <a name="protection-fails-because-the-vm-agent-is-unresponsive"></a>由于 VM 代理无响应，保护失败

错误消息：“跟踪要启动的扩展操作时，任务执行超时。”<br>
错误代码：“151099”<br>

如果虚拟机中的 Azure 来宾代理未处于就绪状态，则会发生此错误。
可在 [Azure 门户](https://portal.azure.com/)中检查 Azure 来宾代理的状态。 转到尝试保护的虚拟机并在“VM”>“设置”>“属性”>“代理状态”中检查状态。 大多数情况下，重新启动虚拟机后代理的状态变为准备就绪。 但是，如果不能重新启动或仍然面临问题，请完成以下故障排除步骤。

**原因 1：[代理安装在 VM 中，但无响应（针对 Windows VM）](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**    
**原因 2：[VM 中安装的代理已过时（针对 Linux VM）](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  


错误消息：“跟踪要启动的扩展操作时，任务执行超时。”<br>
错误代码：“151095”<br>

如果 Linux 计算机上的代理版本较旧，则可能发生此情况。 请完成以下故障排除步骤。<br>
  **原因 1：[VM 中安装的代理已过时（针对 Linux VM）](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
## <a name="causes-and-solutions"></a>原因和解决方法

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>代理安装在 VM 中，但无响应（针对 Windows VM）

#### <a name="solution"></a>解决方案
VM 代理可能已损坏或服务可能已停止。 重新安装 VM 代理可帮助获取最新版本。 此外，还有助于与服务重新开始通信。

1. 确定“Windows Azure 来宾代理服务”是否在 VM 服务中运行 (services.msc)。 尝试重启“Windows Azure 来宾代理服务”。    
2. 如果服务中未显示 Windows Azure 来宾代理服务，请在“控制面板”中转到“程序和功能”，确定是否已安装 Windows 来宾代理服务。
4. 如果“程序和功能”中显示了 Windows Azure 来宾代理，请将其卸载。
5. 下载并安装[最新版本的代理 MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。 必须拥有管理员权限才能完成安装。
6. 检查服务中是否显示了 Windows Azure 来宾代理服务。
7. 重启保护作业。

此外，检查是否在 VM 中[安装了 Microsoft .NET 4.5](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed)。 VM 代理需要使用 .NET 4.5 来与服务通信。

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>VM 中安装的代理已过时（针对 Linux VM）

#### <a name="solution"></a>解决方案
对于 Linux VM，与代理或扩展相关的大多数失败都是由于影响过时的 VM 代理的问题所造成的。 若要解决此问题，请遵循以下一般性指导：

1. 按照[更新 Linux VM 代理](../virtual-machines/linux/update-agent.md)的说明进行操作。

 > [!NOTE]
 > 我们强烈建议只通过分发存储库更新代理。 不建议直接从 GitHub 下载代理代码进行更新。 如果分发没有可用的最新代理，请联系分发支持部门，了解如何安装最新代理。 若要检查最新代理，请转到 GitHub 存储库中的 [Microsoft Azure Linux 代理](https://github.com/Azure/WALinuxAgent/releases)页。

2. 运行以下命令，确保 Azure 代理可在 VM 上运行：`ps -e`

 如果该进程未运行，请使用以下命令进行重启：

 * 对于 Ubuntu：`service walinuxagent start`
 * 对于其他分发版：`service waagent start`

3. [配置自动重启代理](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash)。
4. 启用对虚拟机的保护。



### <a name="the-site-recovery-extension-fails-to-update-or-load"></a>Site Recovery 扩展未能更新或加载
如果扩展状态为“空”、“未就绪”或正在转换。

#### <a name="solution"></a>解决方案

卸载扩展并再次重启操作。

卸载扩展：

1. 在 [Azure 门户](https://portal.azure.com/)中，找到备份失败的 VM。
2. 选择“设置”。
3. 选择“扩展”。
4. 选择“Site Recovery 扩展”。
5. 选择“卸载”。

对于 Linux VM，如果 VMSnapshot 扩展未显示在 Azure 门户中，请[更新 Azure Linux 代理](../virtual-machines/linux/update-agent.md)，然后运行保护。 

完成这些步骤可在保护期间重新安装扩展。


