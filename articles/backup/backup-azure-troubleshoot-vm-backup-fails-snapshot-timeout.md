---
title: "Azure 备份故障排除：客户代理状态不可用 | Microsoft Docs"
description: "与代理、扩展和磁盘相关的 Azure 备份失败的症状、原因及解决方法。"
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
ms.date: 01/09/2018
ms.author: genli;markgal;sogup;
ms.openlocfilehash: c205023b025a477ee05ddcbfc536573f31426167
ms.sourcegitcommit: e19742f674fcce0fd1b732e70679e444c7dfa729
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2018
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>Azure 备份故障排除：代理或扩展的问题

本文提供故障排查步骤，可帮助解决与 VM 代理和扩展通信相关的 Azure 备份错误。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="vm-agent-unable-to-communicate-with-azure-backup"></a>VM 代理无法与 Azure 备份进行通信

错误消息：“VM 代理无法与 Azure 备份进行通信”

> [!NOTE]
> 如果 Azure Linux VM 备份从 2018 年 1 月 4 日开始失败，请在 VM 中运行以下命令，然后重试备份：`sudo rm -f /var/lib/waagent/*.[0-9]*.xml`

注册并计划备份服务的 VM 后，备份将通过与 VM 代理进行通信获取时间点快照，从而启动作业。 以下任何条件都可能阻止快照的触发。 如果未触发快照，则备份可能失败。 请按所列顺序完成以下故障排除步骤，然后重试操作：

**原因 1：[VM 无法访问 Internet](#the-vm-has-no-internet-access)**  
**原因 2：[代理安装在 VM 中，但无响应（针对 Windows VM）](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**    
**原因 3：[VM 中安装的代理已过时（针对 Linux VM）](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**原因 4：[无法检索快照状态或无法创建快照](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**    
**原因 5：[备份扩展无法更新或加载](#the-backup-extension-fails-to-update-or-load)**  

## <a name="snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>由于虚拟机未连接到网络，快照操作失败

错误消息：“由于虚拟机未建立网络连接，快照操作失败”

注册和计划 Azure 备份服务的 VM 后，备份将通过与 VM 备份扩展进行通信获取时间点快照，从而启动作业。 以下任何条件都可能阻止快照的触发。 如果未触发快照，则备份可能失败。 请按所列顺序完成以下故障排除步骤，然后重试操作：    
**原因 1：[VM 无法访问 Internet](#the-vm-has-no-internet-access)**  
**原因 2：[无法检索快照状态或无法创建快照](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**原因 3：[备份扩展无法更新或加载](#the-backup-extension-fails-to-update-or-load)**  

## <a name="vmsnapshot-extension-operation-failed"></a>VMSnapshot 扩展操作失败

错误消息：“VMSnapshot 扩展操作失败”

注册和计划 Azure 备份服务的 VM 后，备份将通过与 VM 备份扩展进行通信获取时间点快照，从而启动作业。 以下任何条件都可能阻止快照的触发。 如果未触发快照，则备份可能失败。 请按所列顺序完成以下故障排除步骤，然后重试操作：  
**原因 1：[无法检索快照状态或无法创建快照](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**原因 2：[备份扩展无法更新或加载](#the-backup-extension-fails-to-update-or-load)**  
**原因 3：[VM 无法访问 Internet](#the-vm-has-no-internet-access)**  
**原因 4：[代理安装在 VM 中，但无响应（针对 Windows VM）](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**原因 5：[VM 中安装的代理已过时（针对 Linux VM）](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="backup-fails-because-the-vm-agent-is-unresponsive"></a>由于 VM 代理无响应，备份失败

错误消息：“由于 VM 代理无响应，无法执行操作”

注册和计划 Azure 备份服务的 VM 后，备份将通过与 VM 备份扩展进行通信获取时间点快照，从而启动作业。 以下任何条件都可能阻止快照的触发。 如果未触发快照，则备份可能失败。 请按所列顺序完成以下故障排除步骤，然后重试操作：  
**原因 1：[代理安装在 VM 中，但无响应（针对 Windows VM）](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**原因 2：[VM 中安装的代理已过时（针对 Linux VM）](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**原因 3：[VM 无法访问 Internet](#the-vm-has-no-internet-access)**  

## <a name="backup-fails-with-an-internal-error"></a>备份失败并出现内部错误

错误消息：“备份失败并出现内部错误 - 请在几分钟后重试操作”

注册和计划 Azure 备份服务的 VM 后，备份将通过与 VM 备份扩展进行通信获取时间点快照，从而启动作业。 以下任何条件都可能阻止快照的触发。 如果未触发快照，则备份可能失败。 请按所列顺序完成以下故障排除步骤，然后重试操作：  
**原因 1：[VM 无法访问 Internet](#the-vm-has-no-internet-access)**  
**原因 2：[代理安装在 VM 中，但无响应（针对 Windows VM）](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**原因 3：[VM 中安装的代理已过时（针对 Linux VM）](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**原因 4：[无法检索快照状态或无法创建快照](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**原因 5：[备份扩展无法更新或加载](#the-backup-extension-fails-to-update-or-load)**  
**原因 6：[备份服务因资源组锁定而无权删除旧的还原点](#backup-service-does-not-have-permission-to-delete-the-old-restore-points-due-to-resource-group-lock)**

## <a name="disk-configuration-is-not-supported"></a>磁盘配置不受支持

错误消息：“不支持指定的磁盘配置”

> [!NOTE]
> 我们提供了个人预览版以支持包含 1 TB 以上磁盘的 VM 备份。 有关详细信息，请参阅[支持大型磁盘 VM 备份的专用预览版](https://gallery.technet.microsoft.com/Instant-recovery-point-and-25fe398a)。
>
>

Azure 备份目前不支持[大于 1,023 GB](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms-prepare#limitations-when-backing-up-and-restoring-a-vm) 的磁盘。 如果磁盘大于 1 TB：  
1. [附加小于 1 TB 的新磁盘](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal)。  
2. 将大于 1 TB 的磁盘中的数据复制到新建的小于 1 TB 的磁盘。  
3. 确保已复制所有数据。 然后删除大于 1 TB 的磁盘。  
4. 启动备份。

## <a name="causes-and-solutions"></a>原因和解决方法

### <a name="the-vm-has-no-internet-access"></a>VM 无法访问 Internet
VM 无法根据部署要求访问 Internet。 或者现有的限制阻止访问 Azure 基础结构。

若要正常工作，备份扩展需要连接到 Azure 公共 IP 地址。 该扩展会将命令发送到 Azure 存储终结点 (HTTP URL) 来管理 VM 的快照。 如果扩展无法访问公共 Internet，则备份最终会失败。

####  <a name="solution"></a>解决方案
若要解决此问题，请尝试下列方法：

##### <a name="allow-access-to-azure-storage-that-corresponds-to-the-region"></a>允许访问与该区域对应的 Azure 存储

可以使用[服务标记](../virtual-network/security-overview.md#service-tags)允许与特定区域存储建立连接。 确保允许访问存储帐户的规则的优先级高于阻止 Internet 访问的规则。 

![使用区域存储标记的网络安全组](./media/backup-azure-arm-vms-prepare/storage-tags-with-nsg.png)

> [!WARNING]
> 存储服务标记以预览版提供。 它们只在特定的区域中可用。 有关区域列表，请参阅[存储的服务标记](../virtual-network/security-overview.md#service-tags)。

##### <a name="create-a-path-for-http-traffic"></a>为 HTTP 流量创建路径

1. 如果指定了网络限制（例如网络安全组），请部署 HTTP 代理服务器来路由流量。
2. 要允许从 HTTP 代理服务器访问 Internet，如果有规则，请将其添加到网络安全组。

若要了解如何设置 HTTP 代理进行 VM 备份，请参阅[进行备份 Azure 虚拟机所需的环境准备](backup-azure-arm-vms-prepare.md#establish-network-connectivity)。

如果使用 Azure 托管磁盘，可能需要在防火墙上打开另一个端口 (8443)。

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>代理安装在 VM 中，但无响应（针对 Windows VM）

#### <a name="solution"></a>解决方案
VM 代理可能已损坏或服务可能已停止。 重新安装 VM 代理可帮助获取最新版本。 此外，还有助于与服务重新开始通信。

1. 确定 Windows 来宾代理服务是否在 VM 服务 (services.msc) 中运行。 尝试重启 Windows 来宾代理服务并启动备份。    
2. 如果“服务”中未显示 Windows 来宾代理服务，请在控制面板中转到“程序和功能”，确定是否已安装 Windows 来宾代理服务。
4. 如果“程序和功能”中显示了 Windows 来宾代理，请将其卸载。
5. 下载并安装[最新版本的代理 MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。 必须拥有管理员权限才能完成安装。
6. 检查“服务”中是否显示了 Windows 来宾代理服务。
7. 运行按需备份： 
    * 在门户中，选择“立即备份”。

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
4. 运行新的测试备份。 如果仍然失败，请从 VM 收集以下日志：

   * /var/lib/waagent/*.xml
   * /var/log/waagent.log
   * /var/log/azure/*

如果我们需要 waagent 的详细日志记录，请执行以下步骤：

1. 在 /etc/waagent.conf 文件中，找到以下行：**Enable verbose logging (y|n)**
2. 将 Logs.Verbose 值从 n 更改为 *y*。
3. 保存更改，然后遵循本部分前面所述的步骤重启 waagent。

###  <a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>无法检索快照状态或无法创建快照
VM 备份依赖于向基础存储帐户发出快照命令。 备份失败的原因可能是它无法访问存储帐户，也可能是快照任务的执行被延迟。

#### <a name="solution"></a>解决方案
以下状态可能导致快照任务失败：

| 原因 | 解决方案 |
| --- | --- |
| VM 具有已配置的 SQL Server 备份。 | 默认情况下，VM 备份在 Windows VM 上运行卷影复制服务 (VSS) 完整备份。 在运行基于 SQL Server 的服务器并配置了 SQL Server 备份的 VM 上，快照执行可能发生延迟。<br><br>如果由于快照问题而导致备份失败，请设置以下注册表项：<br><br>**[HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT] "USEVSSCOPYBACKUP"="TRUE"** |
| 由于在远程桌面协议 (RDP) 中关闭了 VM，VM 状态报告不正确。 | 如果在 RDP 中关闭了 VM，请检查门户，确定 VM 状态是否正确。 如果不正确，请在门户中使用 VM 仪表板上的“关闭”选项来关闭 VM。 |
| VM 无法从 DHCP 获取主机或结构地址。 | 必须在来宾内启用 DHCP，才能正常进行 IaaS VM 备份。 如果 VM 无法从 DHCP 响应 245 获取主机或结构地址，则无法下载或运行任何扩展。 如果需要静态专用 IP 地址，请通过平台配置该 IP。 VM 内的 DHCP 选项应保持启用。 有关详细信息，请参阅[设置静态内部专用 IP](../virtual-network/virtual-networks-reserved-private-ip.md)。 |

### <a name="the-backup-extension-fails-to-update-or-load"></a>无法更新或加载备份扩展
如果无法加载扩展，则会由于无法创建快照而导致备份失败。

#### <a name="solution"></a>解决方案

**对于 Windows 来宾：**确认 iaasvmprovider 服务已启用，并且启动类型为自动。 如果该服务的配置并非如此，请启用该服务以确定下一次备份是否成功。

**对于 Linux 来宾：**确认 VMSnapshot for Linux（备份使用的扩展）的最新版本是 1.0.91.0。<br>


如果还是无法更新或加载备份扩展，可以卸载扩展来强制重新加载 VMSnapshot 扩展。 下一次备份尝试将重新加载扩展。

卸载扩展：

1. 在 [Azure 门户](https://portal.azure.com/)中，找到备份失败的 VM。
2. 选择“设置”。
3. 选择“扩展”。
4. 选择“Vmsnapshot 扩展”。
5. 选择“卸载”。

完成这些步骤可在下一次备份期间重新安装扩展。

### <a name="backup-service-does-not-have-permission-to-delete-the-old-restore-points-due-to-resource-group-lock"></a>备份服务因资源组锁定而无权删除旧的还原点
此问题特定于用户锁定了资源组的托管 VM。 在此情况下，备份服务无法删除较早的还原点。 由于存在最多 18 个还原点的限制，新备份开始失败。

#### <a name="solution"></a>解决方案

若要解决此问题，请完成以下步骤删除还原点集合： <br>
 
1. 删除 VM 所在的资源组中的锁。 
2. 使用 Chocolatey 安装 ARMClient： <br>
   https://github.com/projectkudu/ARMClient
3. 登录到 ARMClient： <br>
    `.\armclient.exe login`
4. 获取与 VM 对应的还原点集合： <br>
    `.\armclient.exe get https://management.azure.com/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Compute/restorepointcollections/AzureBackup_<VM-Name>?api-version=2017-03-30`

    示例：`.\armclient.exe get https://management.azure.com/subscriptions/f2edfd5d-5496-4683-b94f-b3588c579006/resourceGroups/winvaultrg/providers/Microsoft.Compute/restorepointcollections/AzureBackup_winmanagedvm?api-version=2017-03-30`
5. 删除还原点集合： <br>
    `.\armclient.exe delete https://management.azure.com/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Compute/restorepointcollections/AzureBackup_<VM-Name>?api-version=2017-03-30` 
6. 下一次计划备份会自动创建还原点集合和新的还原点。

 
如果再次锁定资源组，则还会出现此问题。 

