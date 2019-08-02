---
title: 对 Azure 备份失败进行故障排除：来宾部署状态不可用
description: 与代理、扩展和磁盘相关的 Azure 备份失败的症状、原因及解决方法。
ms.reviewer: saurse
author: dcurwin
manager: carmonm
keywords: Azure 备份；VM 代理；网络连接；
ms.service: backup
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.author: dacurwin
ms.openlocfilehash: 7fc288ad9e33088b1b5248c1b61ed439ac95a9c4
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2019
ms.locfileid: "68688981"
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>对 Azure 备份失败进行故障排除：代理或扩展的问题

本文提供故障排查步骤，可帮助解决与 VM 代理和扩展通信相关的 Azure 备份错误。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]



## <a name="UserErrorGuestAgentStatusUnavailable-vm-agent-unable-to-communicate-with-azure-backup"></a>UserErrorGuestAgentStatusUnavailable - VM 代理无法与 Azure 备份通信

**错误代码**：UserErrorGuestAgentStatusUnavailable <br>
**错误消息**：VM 代理无法与 Azure 备份进行通信<br>

注册并计划备份服务的 VM 后，备份将通过与 VM 代理进行通信获取时间点快照，从而启动作业。 以下任何条件都可能阻止快照的触发。 如果未触发快照，则备份可能失败。 请按所列顺序完成以下故障排除步骤，然后重试操作：<br>
**原因 1：[代理安装在 VM 中，但无响应（针对 Windows VM）](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**     
**原因 2：[VM 中安装的代理已过时（针对 Linux VM）](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**原因 3：[无法检索快照状态或无法创建快照](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**     
**原因 4：[备份扩展无法更新或加载](#the-backup-extension-fails-to-update-or-load)**  
**原因 5：[VM 无法访问 Internet](#the-vm-has-no-internet-access)**

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
* 如果每天触发多个备份，则也可能发生此问题。 目前，我们建议每天只创建一个备份，因为即时还原点只保留 1-5 天（按照配置的快照保留期的要求），并且在任意给定时间，只能将 18 个即时 RP 与一个 VM 相关联。 <br>

建议的操作：<br>
若要解决此问题，请删除 VM 资源组中的锁，并重试触发清理的操作。
> [!NOTE]
> 备份服务将创建一个单独的资源组而非 VM 的资源组来存储还原点集合。 建议客户不要锁定为备份服务使用而创建的资源组。 备份服务创建的资源组的命名格式为：AzureBackupRG_`<Geo>`_`<number>` 例如：AzureBackupRG_northeurope_1

**步骤 1：[删除还原点资源组中的锁](#remove_lock_from_the_recovery_point_resource_group)** <br>
**步骤 2：[清理还原点集合](#clean_up_restore_point_collection)**<br>

## <a name="usererrorkeyvaultpermissionsnotconfigured---backup-doesnt-have-sufficient-permissions-to-the-key-vault-for-backup-of-encrypted-vms"></a>UserErrorKeyvaultPermissionsNotConfigured - 备份服务对密钥保管库没有足够的权限，无法备份已加密的 VM

**错误代码**：UserErrorKeyvaultPermissionsNotConfigured <br>
**错误消息**：备份服务对 Key Vault 没有足够的权限，无法备份已加密的 VM。 <br>

要使备份操作在加密的 VM 上成功，该服务必须具有访问密钥保管库的权限。 这可以使用[Azure 门户](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption)或通过[PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#enable-protection)来完成。

## <a name="ExtensionSnapshotFailedNoNetwork-snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>ExtensionSnapshotFailedNoNetwork - 由于虚拟机上无网络连接，快照操作失败

**错误代码**：ExtensionSnapshotFailedNoNetwork<br>
**错误消息**：由于虚拟机上无网络连接，快照操作失败<br>

注册和计划 Azure 备份服务的 VM 后，备份将通过与 VM 备份扩展进行通信获取时间点快照，从而启动作业。 以下任何条件都可能阻止快照的触发。 如果未触发快照，则备份可能失败。 请按所列顺序完成以下故障排除步骤，然后重试操作：    
**原因 1：[无法检索快照状态或无法创建快照](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**原因 2：[备份扩展无法更新或加载](#the-backup-extension-fails-to-update-or-load)**  
**原因 3：[VM 无法访问 Internet](#the-vm-has-no-internet-access)**

## <a name="ExtensionOperationFailed-vmsnapshot-extension-operation-failed"></a>ExtensionOperationFailedForManagedDisks-VMSnapshot 扩展操作失败

**错误代码**：ExtensionOperationFailedForManagedDisks <br>
**错误消息**：VMSnapshot 扩展操作失败<br>

注册和计划 Azure 备份服务的 VM 后，备份将通过与 VM 备份扩展进行通信获取时间点快照，从而启动作业。 以下任何条件都可能阻止快照的触发。 如果未触发快照，则备份可能失败。 请按所列顺序完成以下故障排除步骤，然后重试操作：  
**原因 1：[无法检索快照状态或无法创建快照](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**原因 2：[备份扩展无法更新或加载](#the-backup-extension-fails-to-update-or-load)**  
**原因 3：[代理安装在 VM 中，但无响应（针对 Windows VM）](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**原因 4：[VM 中安装的代理已过时（针对 Linux VM）](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="backupoperationfailed--backupoperationfailedv2---backup-fails-with-an-internal-error"></a>BackUpOperationFailed/BackUpOperationFailedV2 - 备份失败并出现内部错误

**错误代码**：BackUpOperationFailed/BackUpOperationFailedV2 <br>
**错误消息**：发生内部错误，备份失败 - 请在几分钟后重试操作 <br>

注册和计划 Azure 备份服务的 VM 后，备份将通过与 VM 备份扩展进行通信获取时间点快照，从而启动作业。 以下任何条件都可能阻止快照的触发。 如果未触发快照，则备份可能失败。 请按所列顺序完成以下故障排除步骤，然后重试操作：  
**原因 1：[代理安装在 VM 中，但无响应（针对 Windows VM）](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**原因 2：[VM 中安装的代理已过时（针对 Linux VM）](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**原因 3：[无法检索快照状态或无法创建快照](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**原因 4：[备份扩展无法更新或加载](#the-backup-extension-fails-to-update-or-load)**  
**原因 5：备份服务因资源组锁定而无权删除旧的还原点** <br>
**原因 6：[VM 无法访问 Internet](#the-vm-has-no-internet-access)**

## <a name="usererrorunsupporteddisksize---currently-azure-backup-does-not-support-disk-sizes-greater-than-4095gb"></a>UserErrorUnsupportedDiskSize - 当前，Azure 备份不支持大于 4095GB 的磁盘大小

**错误代码**：UserErrorUnsupportedDiskSize <br>
**错误消息**：当前 Azure 备份不支持大于 4095GB 的磁盘大小 <br>

对磁盘大小大于 4095GB 的 VM 进行备份时，备份操作可能会失败。 若要注册 Azure 备份的专用预览版, 大磁盘支持大于4TB 的磁盘, 最大大小为 30TB, 请写回我们AskAzureBackupTeam@microsoft.com。

## <a name="usererrorbackupoperationinprogress---unable-to-initiate-backup-as-another-backup-operation-is-currently-in-progress"></a>UserErrorBackupOperationInProgress - 无法启动备份，因为另一个备份操作当前正在进行中

**错误代码**：UserErrorBackupOperationInProgress <br>
**错误消息**：无法启动备份，因为另一个备份操作当前正在进行中<br>

最近的备份作业失败，因为某个现有的备份作业正在进行。 在当前作业完成前，无法启动新的备份作业。 在触发或计划其他备份操作之前，请确保完成当前正在进行的备份操作。 若要检查备份作业状态，请执行以下步骤：

1. 登录到 Azure 门户，单击“所有服务”。 键入“恢复服务”，然后单击“恢复服务保管库”。 此时会显示恢复服务保管库列表。
2. 在恢复服务保管库列表中，选择在其中配置了备份的保管库。
3. 在保管库仪表板菜单中，单击“备份作业”显示所有备份作业。

    * 如果某个备份作业正在进行，请等待它完成或取消备份作业。
        * 若要取消备份作业，请右键单击备份作业并单击“取消”或使用 [PowerShell](https://docs.microsoft.com/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob?view=azps-1.4.0)。
    * 如果已在另一个保管库中重新配置了备份，则确保旧保管库中没有正在运行的备份作业。 如果存在，则取消备份作业。
        * 若要取消备份作业，请右键单击备份作业并单击“取消”或使用 [PowerShell](https://docs.microsoft.com/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob?view=azps-1.4.0)
4. 请重试备份操作。

如果计划备份操作花费时间长且与下一个备份配置冲突，请查看[最佳做法](backup-azure-vms-introduction.md#best-practices)、[备份性能](backup-azure-vms-introduction.md#backup-performance)和[还原注意事项](backup-azure-vms-introduction.md#backup-and-restore-considerations)。


## <a name="causes-and-solutions"></a>原因和解决方法

### <a name="the-vm-has-no-internet-access"></a>VM 无法访问 Internet
VM 无法根据部署要求访问 Internet。 或者现有的限制阻止访问 Azure 基础结构。

若要正常工作，备份扩展需要连接到 Azure 公共 IP 地址。 扩展将命令发送到 Azure 存储终结点 (HTTPS URL)，以管理 VM 快照。 如果扩展无法访问公共 Internet，则备份最终会失败。

####  <a name="solution"></a>解决方案
若要解决网络问题，请参阅[建立网络连接](backup-azure-arm-vms-prepare.md#establish-network-connectivity)。

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>代理安装在 VM 中，但无响应（针对 Windows VM）

#### <a name="solution"></a>解决方案
VM 代理可能已损坏或服务可能已停止。 重新安装 VM 代理可帮助获取最新版本。 此外，还有助于与服务重新开始通信。

1. 确定 Windows Azure 来宾代理服务是否在 VM 服务 (services.msc) 中运行。 尝试重启 Windows Azure 来宾代理服务并启动备份。    
2. 如果服务中未显示 Windows Azure 来宾代理服务，请在“控制面板”中转到“程序和功能”，确定是否已安装 Windows Azure 来宾代理服务。
4. 如果“程序和功能”中显示了 Windows Azure 来宾代理，请将其卸载。
5. 下载并安装[最新版本的代理 MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。 必须拥有管理员权限才能完成安装。
6. 检查服务中是否显示了 Windows Azure 来宾代理服务。
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
| VM 无法从 DHCP 获取主机或结构地址。 | 必须在来宾内启用 DHCP，才能正常进行 IaaS VM 备份。 如果 VM 无法从 DHCP 响应 245 获取主机或结构地址，则无法下载或运行任何扩展。 如果需要静态专用 IP，则应通过 Azure 门户或 PowerShell 进行配置，同时确保启用 VM 内的 DHCP 选项。 [详细了解](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface)如何通过 PowerShell 设置静态 IP 地址。

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
1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 转到“所有资源选项”，选择采用 AzureBackupRG_`<Geo>`_`<number>` 格式的还原点集合资源组。
3. 在“设置”部分，选择“锁”以显示锁。
4. 若要删除锁，请选择省略号，然后单击“删除”。

    ![删除锁](./media/backup-azure-arm-vms-prepare/delete-lock.png)

### <a name="clean_up_restore_point_collection"></a>清理还原点集合
删除锁后，必须清理还原点。 若要清理还原点，请执行以下任一方法：<br>
* [通过运行即席备份来清理还原点集合](#clean-up-restore-point-collection-by-running-ad-hoc-backup)<br>
* [从 Azure 门户清理还原点集合](#clean-up-restore-point-collection-from-azure-portal)<br>

#### <a name="clean-up-restore-point-collection-by-running-ad-hoc-backup"></a>通过运行临时备份来清理还原点集合
删除锁后，触发即席/手动备份。 这可以确保自动清理还原点。 预期此即席/手动操作第一次会失败；但是，它可以确保自动完成清理，而无需手动删除还原点。 清理后，下一个计划的备份应会成功。

> [!NOTE]
> 自动清理将在触发即席/手动备份的数小时后发生。 如果计划的备份仍然失败，请尝试使用[此处](#clean-up-restore-point-collection-from-azure-portal)列出的步骤手动删除还原点集合。

#### <a name="clean-up-restore-point-collection-from-azure-portal"></a>从 Azure 门户清理还原点集合 <br>

若要手动清除由于资源组中存在锁而未能清除的还原点集合，请尝试以下步骤：
1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 在“中心”菜单中单击“所有资源”，选择 VM 所在的、采用 AzureBackupRG_`<Geo>`_`<number>` 格式的资源组。

    ![删除锁](./media/backup-azure-arm-vms-prepare/resource-group.png)

3. 单击“资源组”。此时会显示“概述”边栏选项卡。
4. 选择“显示隐藏的类型”选项，以显示所有已隐藏的资源。 选择采用 AzureBackupRG_`<VMName>`_`<number>` 格式的还原点集合。

    ![删除锁](./media/backup-azure-arm-vms-prepare/restore-point-collection.png)

5. 单击“删除”以清理还原点集合。
6. 再次重试备份操作。

> [!NOTE]
 >如果资源（RP 集合）具有大量还原点，则从门户中删除相同数量还原点的操作可能会超时并失败。 这是已知的 CRP 问题，其中所有还原点不会在规定时间内都删除且操作会超时；但是，删除操作通常会在 2 次或 3 次重试后成功。
