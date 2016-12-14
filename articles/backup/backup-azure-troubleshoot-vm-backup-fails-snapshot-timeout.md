---
title: "Azure VM 备份失败：无法与 VM 代理通信以获取快照状态 — 快照 VM 子任务超时 | Microsoft 文档"
description: "与以下症状相关的 Azure VM 备份失败的原因与解决方法：无法与 VM 代理通信，因而无法获取快照。 快照 VM 子任务超时错误"
services: backup
documentationcenter: 
author: genlin
manager: cfreeman
editor: 
ms.assetid: 4b02ffa4-c48e-45f6-8363-73d536be4639
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: jimpark; markgal;genli
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 87387e4f182214fa0c34a6a1358c6cc2648be906


---
# <a name="azure-vm-backup-fails-could-not-communicate-with-the-vm-agent-for-snapshot-status---snapshot-vm-sub-task-timed-out"></a>Azure VM 备份失败：无法与 VM 代理通信以获取快照状态 - 快照 VM 子任务超时
## <a name="summary"></a>摘要
注册和计划备份 Azure 备份的 Azure 虚拟机 (VM) 之后，Azure 服务通过与 VM 中的备份扩展通信来获取时间点快照，以便在计划的时间启动备份作业。 某些情况可能造成无法触发快照，从而导致备份失败。 本文提供 Azure VM 备份失败中的快照超时错误的排查步骤。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="symptom"></a>症状
针对基础结构即服务 (IaaS) VM 的 Microsoft Azure 备份失败，[Azure 门户](https://portal.azure.com/)中的作业错误详细信息中返回以下错误消息：

**无法与 VM 代理通信以获取快照状态 - 快照 VM 子任务超时。**

## <a name="cause"></a>原因
此错误有四种常见原因：

* VM 无法访问 Internet。
* VM 中安装的 Microsoft Azure VM 代理已过时（适用于 Linux VM）。
* 无法更新或加载备份扩展。
* 无法检索快照状态或无法创建快照。

## <a name="cause-1-the-vm-does-not-have-internet-access"></a>原因 1：VM 无法访问 Internet
VM 无法根据部署要求访问 Internet，或者现有的限制阻止访问 Azure 基础结构。

备份扩展需要连接到 Azure 公共 IP 地址才能正常运行。 这是因为，它会将命令发送到 Azure 存储空间终结点 (HTTP URL) 来管理 VM 的快照。 如果扩展无法访问公共 Internet，则备份最终会失败。

### <a name="solution"></a>解决方案
在此方案中，请使用以下方法之一来解决问题：

* 将 Azure 数据中心 IP 范围加入允许列表
* 为 HTTP 流量创建路径

### <a name="to-whitelist-the-azure-datacenter-ip-ranges"></a>将 Azure 数据中心 IP 范围加入允许列表
1. 获取要加入允许列表的 [Azure 数据中心 IP 列表](https://www.microsoft.com/download/details.aspx?id=41653)。
2. 使用 New-NetRoute cmdlet 取消阻止 IP。 在 Azure VM 上提升权限的 PowerShell 窗口中运行此 cmdlet（以管理员身份运行）。
3. 如果有允许访问 IP 的规则，请将规则添加到网络安全组 (NSG)。

### <a name="to-create-a-path-for-http-traffic-to-flow"></a>为 HTTP 流量创建路径
1. 如果你指定了网络限制（例如 NSG），请部署 HTTP 代理服务器来路由流量。
2. 如果你有网络安全组 (NSG)，请添加规则来允许从 HTTP 代理访问 Internet 。

了解如何[为 VM 备份设置 HTTP 代理](backup-azure-vms-prepare.md#using-an-http-proxy-for-vm-backups)。

## <a name="cause-2-the-microsoft-azure-vm-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>原因 2：VM 中安装的 Microsoft Azure VM 代理已过时（适用于 Linux VM）
### <a name="solution"></a>解决方案
对于 Linux VM，与代理或扩展相关的大多数失败是由于影响旧 VM 代理的问题所造成的。 作为一般指导，排查此问题的首要步骤如下：

1. [安装最新的 Azure VM 代理](https://github.com/Azure/WALinuxAgent)。
2. 确保 Azure 代理在 VM 上运行。 为此，请运行以下命令：```ps -e```
   
    如果此进程未运行，请使用以下命令来重新启动它。
   
    对于 Ubuntu：```service walinuxagent start```
   
    对于其他分发版：     ```service waagent start
   ```
3. [Configure the auto restart agent](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Run a new test backup. If the failures persist, please collect logs from the following folders for further debugging.
   
    We require the following logs from the customer’s VM:
   
   * /var/lib/waagent/*.xml
   * /var/log/waagent.log
   * /var/log/azure/*

If we require verbose logging for waagent, follow these steps to enable this:

1. In the /etc/waagent.conf file, locate the following line:
   
    Enable verbose logging (y|n)
2. Change the **Logs.Verbose** value from n to y.
3. Save the change, and then restart waagent by following the previous steps in this section.

## Cause 3: The backup extension fails to update or load
If extensions cannot be loaded, then Backup fails because a snapshot cannot be taken.

### Solution
For Windows guests:

1. Verify that the iaasvmprovider service is enabled and has a startup type of automatic.
2. If this is not the configuration, enable the service to determine whether the next backup succeeds.

For Linux guests:

The latest version of VMSnapshot Linux (extension used by backup) is 1.0.91.0

If the backup extension still fails to update or load, you can force the VMSnapshot extension to be reloaded by uninstalling the extension. The next backup attempt will reload the extension.

### To uninstall the extension
1. Go to the [Azure portal](https://portal.azure.com/).
2. Locate the particular VM that has backup problems.
3. Click **Settings**.
4. Click **Extensions**.
5. Click **Vmsnapshot Extension**.
6. Click **uninstall**.

This will cause the extension to be reinstalled during the next backup.

## Cause 4: The snapshots status cannot be retrieved or the snapshots cannot be taken
VM backup relies on issuing snapshot command to underlying storage. The backup can fail because it has no access to storage or because of a delay in snapshot task execution.

### Solution
The following conditions can cause snapshot task failure:

| Cause | Solution |
| --- | --- |
| VMs that have Microsoft SQL Server Backup configured. By default, VM Backup runs a VSS Full backup on Windows VMs. On VMs that are running SQL Server-based servers and on which SQL Server Backup is configured, snapshot execution delays may occur. |Set following registry key if you are experiencing backup failures because of snapshot issues.<br><br>[HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT] "USEVSSCOPYBACKUP"="TRUE" |
| VM status is reported incorrectly because the VM is shut down in RDP. If you shut down the virtual machine in RDP, check the portal to determine whether that VM status is reflected correctly. |If it’s not, shut down the VM in the portal by using the ”Shutdown” option in the VM dashboard. |
| Many VMs from the same cloud service are configured to back up at the same time. |It’s a best practice to spread out the VMs from the same cloud service to have different backup schedules. |
| The VM is running at high CPU or memory usage. |If the VM is running at high CPU usage (more than 90 percent) or high memory usage, the snapshot task is queued and delayed and eventually times out. In this situation, try on-demand backup. |
| The VM cannot get host/fabric address from DHCP. |DHCP must be enabled inside the guest for IaaS VM Backup to work.  If the VM cannot get host/fabric address from DHCP response 245, then it cannot download ir run any extensions. If you need a static private IP, you should configure it through the platform. The DHCP option inside the VM should be left enabled. View more information about [Setting a Static Internal Private IP](../virtual-network/virtual-networks-reserved-private-ip.md). |




<!--HONumber=Nov16_HO3-->


