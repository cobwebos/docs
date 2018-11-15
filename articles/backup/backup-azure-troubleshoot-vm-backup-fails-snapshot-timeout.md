---
title: Azure 备份故障排除：客户代理状态不可用
description: 与代理、扩展和磁盘相关的 Azure 备份失败的症状、原因及解决方法。
services: backup
author: genlin
manager: cshepard
keywords: Azure 备份；VM 代理；网络连接；
ms.service: backup
ms.topic: troubleshooting
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 9511e4f90348d58c7b5f6e85d9a5eb74af276461
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2018
ms.locfileid: "51260493"
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>Azure 备份故障排除：代理或扩展的问题

本文提供故障排查步骤，可帮助解决与 VM 代理和扩展通信相关的 Azure 备份错误。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="UserErrorGuestAgentStatusUnavailable-vm-agent-unable-to-communicate-with-azure-backup"></a>UserErrorGuestAgentStatusUnavailable - VM 代理无法与 Azure 备份通信

**错误代码**：UserErrorGuestAgentStatusUnavailable <br>
**错误消息**：VM 代理无法与 Azure 备份通信<br>

注册并计划备份服务的 VM 后，备份将通过与 VM 代理进行通信获取时间点快照，从而启动作业。 以下任何条件都可能阻止快照的触发。 如果未触发快照，则备份可能失败。 请按所列顺序完成以下故障排除步骤，然后重试操作：<br>
**原因 1：[VM 无法访问 Internet](#the-vm-has-no-internet-access)**  
**原因 2：[代理安装在 VM 中，但无响应（针对 Windows VM）](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**    
**原因 3：[VM 中安装的代理已过时（针对 Linux VM）](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**原因 4：[无法检索快照状态或无法创建快照](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**    
**原因 5：[备份扩展无法更新或加载](#the-backup-extension-fails-to-update-or-load)**  

## <a name="guestagentsnapshottaskstatuserror---could-not-communicate-with-the-vm-agent-for-snapshot-status"></a>GuestAgentSnapshotTaskStatusError - 无法与 VM 代理通信以获取快照状态

**错误代码**：GuestAgentSnapshotTaskStatusError<br>
**错误消息**：无法与 VM 代理通信，因此无法获取快照状态 <br>

注册和计划 Azure 备份服务的 VM 后，备份将通过与 VM 备份扩展进行通信获取时间点快照，从而启动作业。 以下任何条件都可能阻止快照的触发。 如果未触发快照，则备份可能失败。 请按所列顺序完成以下故障排除步骤，然后重试操作：  
**原因 1：[代理安装在 VM 中，但无响应（针对 Windows VM）](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**原因 2：[VM 中安装的代理已过时（针对 Linux VM）](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**原因 3：[VM 无法访问 Internet](#the-vm-has-no-internet-access)**

## <a name="usererrorrpcollectionlimitreached---the-restore-point-collection-max-limit-has-reached"></a>UserErrorRpCollectionLimitReached - 已达到还原点集合的最大限制

**错误代码**：UserErrorRpCollectionLimitReached <br>
**错误消息**：已达到还原点集合的最大限制。 <br>
* 如果恢复点资源组中的锁阻止自动清理恢复点，则可能会发生此问题。
* 如果每天触发多个备份，则也可能发生此问题。 目前，我们建议每天只创建一个备份，因为即时 RP 只保留 7 天，并且在任意给定时间，只能将 18 个即时 RP 与一个 VM 相关联。 <br>

建议的操作：<br>
若要解决此问题，请删除资源组中的锁，并重试触发清理的操作。

> [!NOTE]
    > 备份服务将创建一个单独的资源组而非 VM 的资源组来存储还原点集合。 建议客户不要锁定为备份服务使用而创建的资源组。 备份服务创建的资源组的命名格式为：AzureBackupRG_`<Geo>`_`<number>`，例如 AzureBackupRG_northeurope_1


**步骤 1：[删除还原点资源组中的锁](#remove_lock_from_the_recovery_point_resource_group)** <br>
**步骤 2：[清理还原点集合](#clean_up_restore_point_collection)**<br>

## <a name="ExtensionSnapshotFailedNoNetwork-snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>ExtensionSnapshotFailedNoNetwork - 由于虚拟机上无网络连接，快照操作失败

**错误代码**：ExtensionSnapshotFailedNoNetwork<br>
**错误消息**：由于虚拟机未建立网络连接，快照操作失败<br>

注册和计划 Azure 备份服务的 VM 后，备份将通过与 VM 备份扩展进行通信获取时间点快照，从而启动作业。 以下任何条件都可能阻止快照的触发。 如果未触发快照，则备份可能失败。 请按所列顺序完成以下故障排除步骤，然后重试操作：    
**原因 1：[VM 无法访问 Internet](#the-vm-has-no-internet-access)**  
**原因 2：[无法检索快照状态或无法创建快照](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**原因 3：[备份扩展无法更新或加载](#the-backup-extension-fails-to-update-or-load)**  

## <a name="ExtentionOperationFailed-vmsnapshot-extension-operation-failed"></a>ExtentionOperationFailed - VMSnapshot 扩展操作失败

**错误代码**：ExtentionOperationFailed <br>
**错误消息**：VMSnapshot 扩展操作失败<br>

注册和计划 Azure 备份服务的 VM 后，备份将通过与 VM 备份扩展进行通信获取时间点快照，从而启动作业。 以下任何条件都可能阻止快照的触发。 如果未触发快照，则备份可能失败。 请按所列顺序完成以下故障排除步骤，然后重试操作：  
**原因 1：[无法检索快照状态或无法创建快照](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**原因 2：[备份扩展无法更新或加载](#the-backup-extension-fails-to-update-or-load)**  
**原因 3：[代理安装在 VM 中，但无响应（针对 Windows VM）](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**原因 4：[VM 中安装的代理已过时（针对 Linux VM）](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="backupoperationfailed--backupoperationfailedv2---backup-fails-with-an-internal-error"></a>BackUpOperationFailed/BackUpOperationFailedV2 - 备份失败并出现内部错误

**错误代码**：BackUpOperationFailed/BackUpOperationFailedV2 <br>
**错误消息**：备份失败并出现内部错误 - 请在几分钟后重试操作 <br>

注册和计划 Azure 备份服务的 VM 后，备份将通过与 VM 备份扩展进行通信获取时间点快照，从而启动作业。 以下任何条件都可能阻止快照的触发。 如果未触发快照，则备份可能失败。 请按所列顺序完成以下故障排除步骤，然后重试操作：  
**原因 1：[VM 无法访问 Internet](#the-vm-has-no-internet-access)**  
**原因 2：[代理安装在 VM 中，但无响应（针对 Windows VM）](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**原因 3：[VM 中安装的代理已过时（针对 Linux VM）](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**原因 4：[无法检索快照状态或无法创建快照](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**原因 5：[备份扩展无法更新或加载](#the-backup-extension-fails-to-update-or-load)**  
**原因 6：[备份服务因资源组锁定而无权删除旧的还原点](#backup-service-does-not-have-permission-to-delete-the-old-restore-points-due-to-resource-group-lock)**

## <a name="usererrorunsupporteddisksize---currently-azure-backup-does-not-support-disk-sizes-greater-than-1023gb"></a>UserErrorUnsupportedDiskSize - 当前，Azure 备份不支持大于 1023GB 的磁盘大小

**错误代码**：UserErrorUnsupportedDiskSize <br>
**错误消息**：当前 Azure 备份不支持大于 1023GB 的磁盘大小 <br>

对磁盘大小大于 1023GB 的 VM 进行备份时，备份操作可能会失败，因为你的保管库未升级到 Azure VM 备份堆栈 V2。 升级到 Azure VM 备份堆栈 V2 后，最多可支持 4TB。 首先查看这些[优势](backup-upgrade-to-vm-backup-stack-v2.md)、[注意事项](backup-upgrade-to-vm-backup-stack-v2.md#considerations-before-upgrade)，然后根据这些[说明](backup-upgrade-to-vm-backup-stack-v2.md#upgrade)继续进行升级。  

## <a name="usererrorstandardssdnotsupported---currently-azure-backup-does-not-support-standard-ssd-disks"></a>UserErrorStandardSSDNotSupported - 当前，Azure 备份不支持标准 SSD 磁盘

**错误代码**：UserErrorStandardSSDNotSupported <br>
**错误消息**：当前，Azure 备份不支持标准 SSD 磁盘 <br>

当前，只有对于已升级到 Azure VM 备份堆栈 V2 的保管库，Azure 备份才支持标准 SSD 磁盘。 首先查看这些[优势](backup-upgrade-to-vm-backup-stack-v2.md)、[注意事项](backup-upgrade-to-vm-backup-stack-v2.md#considerations-before-upgrade)，然后根据这些[说明](backup-upgrade-to-vm-backup-stack-v2.md#upgrade)继续进行升级。


## <a name="causes-and-solutions"></a>原因和解决方法

### <a name="the-vm-has-no-internet-access"></a>VM 无法访问 Internet
VM 无法根据部署要求访问 Internet。 或者现有的限制阻止访问 Azure 基础结构。

若要正常工作，备份扩展需要连接到 Azure 公共 IP 地址。 扩展将命令发送到 Azure 存储终结点 (HTTPS URL)，以管理 VM 快照。 如果扩展无法访问公共 Internet，则备份最终会失败。

可以部署代理服务器来路由 VM 流量。
##### <a name="create-a-path-for-https-traffic"></a>创建 HTTPS 流量路径

1. 若有网络限制（例如，网络安全组），请部署 HTTPS 代理服务器来路由流量。
2. 若要允许从 HTTPS 代理服务器访问 Internet，请将规则（若有）添加到网络安全组。

若要了解如何为 VM 备份设置 HTTPS 代理，请参阅[准备环境以备份 Azure 虚拟机](backup-azure-arm-vms-prepare.md#establish-network-connectivity)。

无论是备份的 VM 还是路由流量的代理服务器，都需要对 Azure 公共 IP 地址的访问权限

####  <a name="solution"></a>解决方案
若要解决此问题，请尝试下列方法：

##### <a name="allow-access-to-azure-storage-that-corresponds-to-the-region"></a>允许访问与该区域对应的 Azure 存储

可以使用[服务标记](../virtual-network/security-overview.md#service-tags)允许与特定区域存储建立连接。 确保允许访问存储帐户的规则的优先级高于阻止 Internet 访问的规则。

![使用区域存储标记的网络安全组](./media/backup-azure-arm-vms-prepare/storage-tags-with-nsg.png)

若要了解配置服务标记的分步过程，请观看[此视频](https://youtu.be/1EjLQtbKm1M)。

> [!WARNING]
> 存储服务标记以预览版提供。 它们只在特定的区域中可用。 有关区域列表，请参阅[存储的服务标记](../virtual-network/security-overview.md#service-tags)。

如果使用 Azure 托管磁盘，可能需要在防火墙上打开另一个端口 (8443)。

此外，如果子网不具有 Internet 出站流量的路由，则需要将具有服务标记“Microsoft.Storage”的服务终结点添加到子网。

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>代理安装在 VM 中，但无响应（针对 Windows VM）

#### <a name="solution"></a>解决方案
VM 代理可能已损坏或服务可能已停止。 重新安装 VM 代理可帮助获取最新版本。 此外，还有助于与服务重新开始通信。

1. 确定 Windows 来宾代理服务是否在 VM 服务 (services.msc) 中运行。 尝试重启 Windows 来宾代理服务并启动备份。    
2. 如果“服务”中未显示 Windows 来宾代理服务，请在控制面板中转到“程序和功能”，确定是否已安装 Windows 来宾代理服务。
4. 如果“程序和功能”中显示了 Windows 来宾代理，请将其卸载。
5. 下载并安装[最新版本的代理 MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。 必须拥有管理员权限才能完成安装。
6. 检查能否在服务中看到 Windows 来宾代理服务。
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
2. 将 **Logs.Verbose** 值从 n 更改为 y。
3. 保存更改，然后遵循本部分前面所述的步骤重启 waagent。

###  <a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>无法检索快照状态或无法创建快照
VM 备份依赖于向基础存储帐户发出快照命令。 备份失败的原因可能是它无法访问存储帐户，也可能是快照任务的执行被延迟。

#### <a name="solution"></a>解决方案
以下状态可能导致快照任务失败：

| 原因 | 解决方案 |
| --- | --- |
| 由于在远程桌面协议 (RDP) 中关闭了 VM，VM 状态报告不正确。 | 如果在 RDP 中关闭了 VM，请检查门户，确定 VM 状态是否正确。 如果不正确，请在门户中使用 VM 仪表板上的“关闭”选项来关闭 VM。 |
| VM 无法从 DHCP 获取主机或结构地址。 | 必须在来宾内启用 DHCP，才能正常进行 IaaS VM 备份。 如果 VM 无法从 DHCP 响应 245 获取主机或结构地址，则无法下载或运行任何扩展。 如果需要静态专用 IP 地址，请通过平台配置该 IP。 VM 内的 DHCP 选项应保持启用。 有关详细信息，请参阅[设置静态内部专用 IP](../virtual-network/virtual-networks-reserved-private-ip.md)。 |

### <a name="the-backup-extension-fails-to-update-or-load"></a>无法更新或加载备份扩展
如果无法加载扩展，则会由于无法创建快照而导致备份失败。

#### <a name="solution"></a>解决方案

卸载扩展以强制重新加载 VMSnapshot 扩展。 下一次备份尝试将重新加载扩展。

卸载扩展：

1. 在 [Azure 门户](https://portal.azure.com/)中，找到备份失败的 VM。
2. 选择“设置”。
3. 选择“扩展”。
4. 选择“Vmsnapshot 扩展”。
5. 选择“卸载”。

对于 Linux VM，如果 VMSnapshot 扩展未显示在 Azure 门户中，请[更新 Azure Linux 代理](../virtual-machines/linux/update-agent.md)，然后运行备份。

完成这些步骤可在下一次备份期间重新安装扩展。

### <a name="remove_lock_from_the_recovery_point_resource_group"></a>删除恢复点资源组中的锁
1. 登录到 [Azure 门户](http://portal.azure.com/)。
2. 转到“所有资源选项”，选择采用 AzureBackupRG_`<Geo>`_`<number>` 格式的还原点集合资源组。
3. 在“设置”部分，选择“锁”以显示锁。
4. 若要删除锁，请选择省略号，然后单击“删除”。

    ![删除锁 ](./media/backup-azure-arm-vms-prepare/delete-lock.png)

### <a name="clean_up_restore_point_collection"></a>清理还原点集合
删除锁后，必须清理还原点。 若要清理还原点，请执行以下任一方法：<br>
* [通过运行即席备份来清理还原点集合](#clean-up-restore-point-collection-by-running-ad-hoc-backup)<br>
* [从 Azure 门户清理还原点集合](#clean-up-restore-point-collection-from-azure-portal)<br>

#### <a name="clean-up-restore-point-collection-by-running-ad-hoc-backup"></a>通过运行即席备份来清理还原点集合
删除锁后，触发即席/手动备份。 这可以确保自动清理还原点。 预期此即席/手动操作第一次会失败；但是，它可以确保自动完成清理，而无需手动删除还原点。 清理后，下一个计划的备份应会成功。

> [!NOTE]
    > 自动清理将在触发即席/手动备份的数小时后发生。 如果计划的备份仍然失败，请尝试使用[此处](#clean-up-restore-point-collection-from-azure-portal)列出的步骤手动删除还原点集合。

#### <a name="clean-up-restore-point-collection-from-azure-portal"></a>从 Azure 门户清理还原点集合 <br>

若要手动清除由于资源组中存在锁而未能清除的还原点集合，请尝试以下步骤：
1. 登录到 [Azure 门户](http://portal.azure.com/)。
2. 在“中心”菜单中单击“所有资源”，选择 VM 所在的、采用 AzureBackupRG_`<Geo>`_`<number>` 格式的资源组。

    ![删除锁 ](./media/backup-azure-arm-vms-prepare/resource-group.png)

3. 单击“资源组”。此时会显示“概述”边栏选项卡。
4. 选择“显示隐藏的类型”选项，以显示所有已隐藏的资源。 选择采用 AzureBackupRG_`<VMName>`_`<number>` 格式的还原点集合。

    ![删除锁 ](./media/backup-azure-arm-vms-prepare/restore-point-collection.png)

5. 单击“删除”以清理还原点集合。
6. 再次重试备份操作。
