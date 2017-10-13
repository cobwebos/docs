---
title: "Azure 备份故障排除：客户代理状态不可用 | Microsoft Docs"
description: "与代理、扩展、磁盘相关的 Azure 备份失败的症状、原因及解决方法"
services: backup
documentationcenter: 
author: genlin
manager: cshepard
editor: 
keywords: "Azure 备份；VM 代理；网络连接；"
ms.assetid: 4b02ffa4-c48e-45f6-8363-73d536be4639
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/08/2017
ms.author: genli;markgal;
ms.openlocfilehash: f3195fa83479986a3e605abce618c78bcdb64dac
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-azure-backup-failure-issues-with-agent-andor-extension"></a>Azure 备份故障排除：代理和/或扩展的问题

本文提供故障排查步骤，可帮助解决与 VM 代理和扩展通信相关的备份故障。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="vm-agent-unable-to-communicate-with-azure-backup"></a>VM 代理无法与 Azure 备份进行通信
注册和计划 Azure 备份服务的 VM 后，备份将通过与 VM 代理进行通信获取时间点快照，从而启动作业。 以下任何条件都可能阻止快照的触发，这反过来会导致备份失败。 请依序执行以下故障排除步骤，然后重试操作。
##### <a name="cause-1-the-vm-has-no-internet-accessthe-vm-has-no-internet-access"></a>原因 1：[VM 不具备 Internet 访问权限](#the-vm-has-no-internet-access)
##### <a name="cause-2-the-agent-is-installed-in-the-vm-but-is-unresponsive-for-windows-vmsthe-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>原因 2：[代理安装在 VM 中，但无响应（针对 Windows VM）](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
##### <a name="cause-3-the-agent-installed-in-the-vm-is-out-of-date-for-linux-vmsthe-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>原因 3：[VM 中安装的代理已过时（针对 Linux VM）](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
##### <a name="cause-4-the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-takenthe-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>原因 4：[无法检索快照状态或无法拍摄快照](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)
##### <a name="cause-5-the-backup-extension-fails-to-update-or-loadthe-backup-extension-fails-to-update-or-load"></a>原因 5：[备份扩展无法更新或加载](#the-backup-extension-fails-to-update-or-load)

## <a name="snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>由于虚拟机上无网络连接，快照操作失败
注册和计划 Azure 备份服务的 VM 后，备份将通过与 VM 备份扩展进行通信获取时间点快照，从而启动作业。 以下任何条件都可能阻止快照的触发，这反过来会导致备份失败。 请依序执行以下故障排除步骤，然后重试操作。
##### <a name="cause-1-the-vm-has-no-internet-accessthe-vm-has-no-internet-access"></a>原因 1：[VM 不具备 Internet 访问权限](#the-vm-has-no-internet-access)
##### <a name="cause-2-the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-takenthe-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>原因 2：[无法检索快照状态或无法拍摄快照](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)
##### <a name="cause-3-the-backup-extension-fails-to-update-or-loadthe-backup-extension-fails-to-update-or-load"></a>原因 3：[备份扩展无法更新或加载](#the-backup-extension-fails-to-update-or-load)

## <a name="vmsnapshot-extension-operation-failed"></a>VMSnapshot 扩展操作失败

注册和计划 Azure 备份服务的 VM 后，备份将通过与 VM 备份扩展进行通信获取时间点快照，从而启动作业。 以下任何条件都可能阻止快照的触发，这反过来会导致备份失败。 请依序执行以下故障排除步骤，然后重试操作。
##### <a name="cause-1-the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-takenthe-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>原因 1：[无法检索快照状态或无法拍摄快照](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)
##### <a name="cause-2-the-backup-extension-fails-to-update-or-loadthe-backup-extension-fails-to-update-or-load"></a>原因 2：[备份扩展无法更新或加载](#the-backup-extension-fails-to-update-or-load)
##### <a name="cause-3-the-vm-has-no-internet-accessthe-vm-has-no-internet-access"></a>原因 3：[VM 不具备 Internet 访问权限](#the-vm-has-no-internet-access)
##### <a name="cause-4-the-agent-is-installed-in-the-vm-but-is-unresponsive-for-windows-vmsthe-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>原因 4：[代理安装在 VM 中，但无响应（针对 Windows VM）](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
##### <a name="cause-5-the-agent-installed-in-the-vm-is-out-of-date-for-linux-vmsthe-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>原因 5：[VM 中安装的代理已过时（针对 Linux VM）](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)

## <a name="unable-to-perform-the-operation-as-the-vm-agent-is-not-responsive"></a>无法执行操作，因为 VM 代理没有响应

注册和计划 Azure 备份服务的 VM 后，备份将通过与 VM 备份扩展进行通信获取时间点快照，从而启动作业。 以下任何条件都可能阻止快照的触发，这反过来会导致备份失败。 请依序执行以下故障排除步骤，然后重试操作。
##### <a name="cause-1-the-agent-is-installed-in-the-vm-but-is-unresponsive-for-windows-vmsthe-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>原因 1：[代理安装在 VM 中，但无响应（针对 Windows VM）](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
##### <a name="cause-2-the-agent-installed-in-the-vm-is-out-of-date-for-linux-vmsthe-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>原因 2：[VM 中安装的代理已过时（针对 Linux VM）](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
##### <a name="cause-3-the-vm-has-no-internet-accessthe-vm-has-no-internet-access"></a>原因 3：[VM 不具备 Internet 访问权限](#the-vm-has-no-internet-access)

## <a name="backup-failed-with-an-internal-error---please-retry-the-operation-in-a-few-minutes"></a>发生内部错误，备份失败 - 请在几分钟后重试操作

注册和计划 Azure 备份服务的 VM 后，备份将通过与 VM 备份扩展进行通信获取时间点快照，从而启动作业。 以下任何条件都可能阻止快照的触发，这反过来会导致备份失败。 请依序执行以下故障排除步骤，然后重试操作。
##### <a name="cause-1-the-vm-has-no-internet-accessthe-vm-has-no-internet-access"></a>原因 1：[VM 不具备 Internet 访问权限](#the-vm-has-no-internet-access)
##### <a name="cause-2-the-agent-installed-in-the-vm-but-unresponsive-for-windows-vmsthe-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>原因 2：[代理安装在 VM 中，但无响应（针对 Windows VM）](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
##### <a name="cause-3-the-agent-installed-in-the-vm-is-out-of-date-for-linux-vmsthe-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>原因 3：[VM 中安装的代理已过时（针对 Linux VM）](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
##### <a name="cause-4-the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-takenthe-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>原因 4：[无法检索快照状态或无法拍摄快照](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)
##### <a name="cause-5-the-backup-extension-fails-to-update-or-loadthe-backup-extension-fails-to-update-or-load"></a>原因 5：[备份扩展无法更新或加载](#the-backup-extension-fails-to-update-or-load)

## <a name="the-specified-disk-configuration-is-not-supported"></a>不支持指定的磁盘配置

当前 Azure 备份不支持[大于 1023GB](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms-prepare#limitations-when-backing-up-and-restoring-a-vm) 的磁盘大小。 
- 如果有大于 1 TB 的磁盘，请[附加小于 1 TB 的新磁盘](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal) <br>
- 然后，将大于 1TB 的磁盘中的数据复制到新创建的小于 1TB 的磁盘。 <br>
- 确保已复制所有数据并删除大于 1 TB 的磁盘
- 启动备份

## <a name="causes-and-solutions"></a>原因和解决方案

### <a name="the-vm-has-no-internet-access"></a>VM 不具备 Internet 访问权限
VM 无法根据部署要求访问 Internet，或者现有的限制阻止访问 Azure 基础结构。

若要正常工作，备份扩展需要连接到 Azure 公共 IP 地址。 该扩展会将命令发送到 Azure 存储终结点 (HTTP URL) 来管理 VM 的快照。 如果扩展无法访问公共 Internet，则备份最终会失败。

####  <a name="solution"></a>解决方案
若要解决此问题，请尝试此处列出的方法之一。
##### <a name="allow-access-to-the-azure-datacenter-ip-ranges"></a>允许访问 Azure 数据中心 IP 范围

1. 获取允许访问的 [Azure 数据中心 IP 列表](https://www.microsoft.com/download/details.aspx?id=41653)。
2. 通过在 Azure VM 的提升 PowerShell 窗口中运行 **New-NetRoute** cmdlet，取消阻止 IP。 以管理员身份运行该 cmdlet。
3. 要允许访问 IP，如果有规则，请将其添加到网络安全组。

##### <a name="create-a-path-for-http-traffic-to-flow"></a>为 HTTP 流量创建路径

1. 如果指定了网络限制（例如网络安全组），请部署 HTTP 代理服务器来路由流量。
2. 要允许从 HTTP 代理服务器访问 Internet，如果有规则，请将其添加到网络安全组。

若要了解如何设置 HTTP 代理进行 VM 备份，请参阅[进行备份 Azure 虚拟机所需的环境准备](backup-azure-vms-prepare.md#using-an-http-proxy-for-vm-backups)。

如果使用托管磁盘，可能需要在防火墙上打开另一个端口 (8443)。

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>代理安装在 VM 中，但无响应（针对 Windows VM）

#### <a name="solution"></a>解决方案
VM 代理可能已损坏或服务可能已停止。 重新安装 VM 代理能够获取最新版本并重新启动通信。

1. 验证 Windows 来宾代理服务是否正在虚拟机的服务 (services.msc) 中运行。 尝试重启 Windows 来宾代理服务并启动备份<br>
2. 如果在服务中看不到，请验证是否在程序和功能中安装了 Windows 来宾代理服务。
4. 如果在程序和功能中可查看到，请卸载 Windows 来宾代理。
5. 下载并安装[最新版本的代理 MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。 需要有管理员权限才能完成安装。
6. 然后可以在服务中查看 Windows 来宾代理服务
7. 尝试在门户中单击“立即备份”来运行按需/临时备份。

还需验证在虚拟机[在系统中安装了 .NET 4.5](https://docs.microsoft.com/en-us/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed)。 VM 代理需要与服务进行通信

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>VM 中安装的代理已过时（针对 Linux VM）

#### <a name="solution"></a>解决方案
对于 Linux VM，与代理或扩展相关的大多数失败都是由于影响过时的 VM 代理的问题所造成的。 若要解决此问题，请遵循以下一般性指导：

1. 按照[更新 Linux VM 代理](../virtual-machines/linux/update-agent.md)的说明进行操作。

 >[!NOTE]
 >我们强烈建议只通过分发存储库更新代理。 不建议直接从 GitHub 下载代理代码进行更新。 如果分发没有可用的最新代理，请联系分发支持部门，了解如何安装最新代理。 若要检查最新代理，请转到 GitHub 存储库中的 [Microsoft Azure Linux 代理](https://github.com/Azure/WALinuxAgent/releases)页。

2. 运行以下命令，确保 Azure 代理可在 VM 上运行：`ps -e`

 如果该进程未运行，请使用以下命令进行重启：

 * 对于 Ubuntu：`service walinuxagent start`
 * 对于其他分发版：`service waagent start`

3. [配置自动重启代理](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash)。
4. 运行新的测试备份。 如果错误仍然存在，请从客户的 VM 收集以下日志：

   * /var/lib/waagent/*.xml
   * /var/log/waagent.log
   * /var/log/azure/*

如果我们需要 waagent 的详细日志记录，请执行以下步骤：

1. 在 /etc/waagent.conf 文件中，找到以下行：**Enable verbose logging (y|n)**
2. 将 Logs.Verbose 值从 n 更改为 *y*。
3. 保存更改，并遵循本部分前面的步骤重新启动 waagent。

### <a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>无法检索快照状态或无法拍摄快照
VM 备份依赖于向基础存储帐户发出快照命令。 备份失败的原因可能是它无法访问存储帐户，也可能是快照任务的执行被延迟。

#### <a name="solution"></a>解决方案
以下状态可能会导致快照任务失败：

| 原因 | 解决方案 |
| --- | --- |
| VM 具有已配置的 SQL Server 备份。 | 默认情况下，VM 备份在 Windows VM 上运行 VSS 完整备份。 在运行基于 SQL Server 的服务器并配置了 SQL Server 备份的 VM 上，快照执行可能发生延迟。<br><br>如果由于快照问题而导致备份失败，请设置以下注册表项：<br><br>**[HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT] "USEVSSCOPYBACKUP"="TRUE"** |
| 由于在 RDP 中关闭了 VM，VM 状态报告不正确。 | 如果在远程桌面协议 (RDP) 中关闭了 VM，请检查门户，以确定 VM 状态是否正确。 如果不正确，请在门户中使用 VM 仪表板上的“关闭”选项来关闭 VM。 |
| 同一个云服务中的许多 VM 配置为同时备份。 | 最佳做法是将备份计划分给同一云服务中的 VM。 |
| VM 在运行时使用了很高的 CPU 或内存。 | 如果 VM 在运行时使用了很高的 CPU 使用率（超过 90%）或内存使用率，快照任务将排入队列、延迟并最终超时。在这种情况下，请尝试进行按需备份。 |
| VM 无法从 DHCP 获取主机/结构地址。 | 必须在来宾内启用 DHCP，才能正常进行 IaaS VM 备份。  如果 VM 无法从 DHCP 响应 245 获取主机/结构地址，则无法下载或运行任何扩展。 如果需要静态专用 IP 地址，应该通过平台配置该 IP。 VM 内的 DHCP 选项应保持启用。 有关详细信息，请参阅[设置静态内部专用 IP](../virtual-network/virtual-networks-reserved-private-ip.md)。 |

### <a name="the-backup-extension-fails-to-update-or-load"></a>无法更新或加载备份扩展
如果无法加载扩展，则会由于无法创建快照而导致备份失败。

#### <a name="solution"></a>解决方案

**对于 Windows 来宾：**确认 iaasvmprovider 服务已启用，并且启动类型为自动。 如果该服务的配置并非如此，请启用该服务以确定下一次备份是否成功。

**对于 Linux 来宾：**确认 VMSnapshot for Linux（备份使用的扩展）的最新版本是 1.0.91.0。<br>


如果还是无法更新或加载备份扩展，可以通过卸载扩展来强制重新加载 VMSnapshot 扩展。 下一次备份尝试将重新加载扩展。

若要卸载扩展，请执行以下操作：

1. 转到 [Azure 门户](https://portal.azure.com/)。
2. 找到存在备份问题的 VM。
3. 单击“设置”。
4. 单击“扩展”。
5. 单击“Vmsnapshot 扩展”。
6. 单击“卸载”。

此过程会导致在下一次备份期间重新安装扩展。

