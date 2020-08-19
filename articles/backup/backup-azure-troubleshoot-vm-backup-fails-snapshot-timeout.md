---
title: 排查代理和扩展问题
description: 与代理、扩展和磁盘相关的 Azure 备份失败的症状、原因及解决方法。
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.service: backup
ms.openlocfilehash: 53c0ede07040e782b683e8ff4b0b08e6fe0a9caf
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2020
ms.locfileid: "88585554"
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>对 Azure 备份失败进行故障排除：代理或扩展的问题

本文提供故障排查步骤，可帮助解决与 VM 代理和扩展通信相关的 Azure 备份错误。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="step-by-step-guide-to-troubleshoot-backup-failures"></a>解决备份失败的循序渐进指南

可以按照以下列出的故障排除步骤自行解决最常见的备份失败：

### <a name="step-1-check-azure-vm-health"></a>步骤1：检查 Azure VM 运行状况

- **确保 AZURE vm 预配状态为 "正在运行"**：如果 [VM 预配状态](../virtual-machines/states-lifecycle.md#provisioning-states) 为 " **已停止/已解除分配/正在更新** " 状态，则它将干扰备份操作。 打开 *Azure 门户 > vm > 概述 >* 并检查 vm 状态以确保其 **正在运行**  ，然后重试备份操作。
- **查看挂起的操作系统更新或重新启动**：确保 VM 上没有挂起的操作系统更新或挂起的重新启动。

### <a name="step-2-check-azure-vm-guest-agent-service-health"></a>步骤2：检查 Azure VM 来宾代理服务运行状况

- **确保 AZURE VM 来宾代理服务已启动并处于最**新状态：
  - 在 Windows VM 上：
    - 导航到 **services.msc** 并确保 **Windows Azure VM 来宾代理服务** 已启动并正在运行。 此外，请确保已安装 [最新版本](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) 。 若要了解详细信息，请参阅 [WINDOWS VM 来宾代理问题](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)。
    - 默认情况下，Azure VM 代理默认安装在从门户、PowerShell、命令行接口或 Azure 资源管理器模板中的 Azure Marketplace 映像部署的任何 Windows VM 上。 在创建部署到 Azure 的自定义 VM 映像时，可能需要 [手动安装代理](../virtual-machines/extensions/agent-windows.md#manual-installation) 。
    - 查看支持矩阵以检查 VM 是否在 [受支持的 Windows 操作系统](backup-support-matrix-iaas.md#operating-system-support-windows)上运行。
  - 在 Linux VM 上，
    - 通过执行命令来确保 Azure VM 来宾代理服务正在运行 `ps-e` 。 此外，请确保已安装 [最新版本](../virtual-machines/extensions/update-linux-agent.md) 。 若要了解详细信息，请参阅 [LINUX VM 来宾代理问题](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)。
    - 确保 [系统包上的 LINUX VM 代理依赖项](../virtual-machines/extensions/agent-linux.md#requirements) 具有受支持的配置。 例如：支持的 Python 版本为2.6 及更高版本。
    - 查看支持矩阵以检查 VM 是否在[受支持的 Linux 操作系统](backup-support-matrix-iaas.md#operating-system-support-linux)上运行。

### <a name="step-3-check-azure-vm-extension-health"></a>步骤3：检查 Azure VM 扩展运行状况

- **确保所有 AZURE VM 扩展都处于 "预配成功" 状态**：如果任何扩展处于失败状态，则它可能会干扰备份。
- *打开 Azure 门户 > VM > 设置 > 扩展 > 扩展状态* ，并检查是否所有扩展都处于 **预配成功** 状态。
- 请确保所有 [扩展问题](../virtual-machines/extensions/overview.md#troubleshoot-extensions) 均已解决，然后重试备份操作。
- **确保 COM + 系统应用程序** 已启动并正在运行。 此外， **分布式事务处理协调器服务** 应作为 **网络服务帐户**运行。 按照本文中的步骤 [解决 COM + 和 MSDTC 问题](backup-azure-vms-troubleshoot.md#extensionsnapshotfailedcom--extensioninstallationfailedcom--extensioninstallationfailedmdtc---extension-installationoperation-failed-due-to-a-com-error)。

### <a name="step-4-check-azure-backup-vm-extension-health"></a>步骤4：检查 Azure 备份 VM 扩展运行状况

Azure 备份使用 VM 快照扩展对 Azure 虚拟机进行应用程序一致的备份。 Azure 备份会在启用备份后第一个触发的第一个计划备份中安装扩展。

- **确保 VMSnapshot 扩展未处于 "失败" 状态**：按照此 [部分](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state) 中列出的步骤验证并确保 Azure 备份扩展正常。

- **检查防病毒是否正在阻止扩展**：某些防病毒软件可能会阻止扩展执行。
  
  在备份失败时，请验证 ***事件查看器应用程序日志*** 中是否存在具有错误 ***应用程序名称 ***的日志条目： IaaSBcdrExtension.exe。 如果你看到条目，则可能是 VM 中配置的防病毒软件限制了备份扩展的执行。 请在防病毒配置中排除以下目录以进行测试，然后重试备份操作。
  - `C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot`
  - `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot`

- **检查是否需要网络访问**：从 azure 存储扩展存储库下载扩展包，并将扩展状态上载发送到 Azure 存储。 [了解详细信息](../virtual-machines/extensions/features-windows.md#network-access)。
  - 如果使用不受支持的代理版本，则需要允许从 VM 对该区域中 Azure 存储的出站访问。
  - 如果已阻止访问 `168.63.129.16` 使用来宾防火墙或代理，则无论以上哪种情况，扩展都将失败。 需要端口80、443和32526， [了解详细信息](../virtual-machines/extensions/features-windows.md#network-access)。

- **请确保在来宾 vm 内启用 dhcp**：这是从 DHCP 获取主机或构造地址以使 IaaS VM 备份正常运行所必需的。 如果需要静态专用 IP，应通过 Azure 门户或 PowerShell 进行配置，并确保已启用 VM 内的 DHCP 选项， [了解详细信息](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)。

- **确保 vss 编写器服务已启动并正在运行**：请按照以下步骤 [解决 vss 编写器问题](backup-azure-vms-troubleshoot.md#extensionfailedvsswriterinbadstate---snapshot-operation-failed-because-vss-writers-were-in-a-bad-state)。
- **遵循备份最佳做法指南**：查看 [启用 Azure VM 备份的最佳实践](backup-azure-vms-introduction.md#best-practices)。
- **查看加密磁盘的准则**：如果要为具有加密磁盘的 vm 启用备份，请确保已提供所有必需的权限。 若要了解详细信息，请参阅 [备份和还原加密的 AZURE VM](backup-azure-vms-encryption.md)。

## <a name="usererrorguestagentstatusunavailable---vm-agent-unable-to-communicate-with-azure-backup"></a><a name="UserErrorGuestAgentStatusUnavailable-vm-agent-unable-to-communicate-with-azure-backup"></a>UserErrorGuestAgentStatusUnavailable - VM 代理无法与 Azure 备份通信

**错误代码**：UserErrorGuestAgentStatusUnavailable <br>
**错误消息**：VM 代理无法与 Azure 备份进行通信<br>

Azure VM 代理可能已停止、已过期、处于不一致状态或未安装。 这些状态会阻止 Azure 备份服务触发快照。

- 打开 Azure 门户>“VM”>“设置”>“属性”窗格 > 确保 VM“状态”为“正在运行”，“代理状态”为“就绪”**** **** **** **** ****。 如果 VM 代理已停止或处于不一致状态，请重启代理<br>
  - 对于 Windows VM，请遵循以下[步骤](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)重启来宾代理。<br>
  - 对于 Linux VM，请遵循以下[步骤](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)重启来宾代理。
- 打开 Azure 门户>“VM”>“设置”>“扩展”> 确保所有扩展均处于“预配成功”状态**** ****。 如果未处于该状态，请按照以下[步骤](#usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state)解决问题。

## <a name="guestagentsnapshottaskstatuserror---could-not-communicate-with-the-vm-agent-for-snapshot-status"></a>GuestAgentSnapshotTaskStatusError - 无法与 VM 代理通信以获取快照状态

**错误代码**：GuestAgentSnapshotTaskStatusError<br>
**错误消息**：无法与 VM 代理通信，因此无法获取快照状态 <br>

注册和计划 Azure 备份服务的 VM 后，备份会通过与 VM 备份扩展通信来获取时间点快照，从而启动作业。 以下任何条件都可能阻止快照的触发。 如果未触发快照，则备份可能失败。 请按所列顺序完成以下故障排除步骤，然后重试操作：  

**原因 1：[代理安装在 VM 中，但无响应（针对 Windows VM）](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  

**原因 2：[VM 中安装的代理已过时（针对 Linux VM）](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

**原因 3：[无法检索快照状态或无法创建快照](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**

**原因 4：[未设置 VM-Agent 配置选项（对于 Linux VM）](#vm-agent-configuration-options-are-not-set-for-linux-vms)**

**原因 5：[应用程序控制解决方案正在阻止 IaaSBcdrExtension.exe](#application-control-solution-is-blocking-iaasbcdrextensionexe)**

## <a name="usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state"></a>UserErrorVmProvisioningStateFailed - VM 处于预配失败状态

**错误代码**：UserErrorVmProvisioningStateFailed<br>
**错误消息**：VM 处于预配失败状态<br>

当其中一个扩展失败将 VM 状态置于预配失败状态时，会发生此错误。<br>打开 Azure 门户>“VM”>“设置”>“扩展”>“扩展状态”，然后检查所有扩展是否都处于“预配成功”状态**** ****。 若要了解详细信息，请参阅[预配状态](../virtual-machines/states-lifecycle.md#provisioning-states)。

- 如果 VMSnapshot 扩展处于失败状态，则右键单击失败的扩展并将其删除。 触发按需备份。 此操作会重新安装扩展并运行备份作业。  <br>
- 如果其他任何扩展处于失败状态，则可能会干扰备份。 确保这些扩展问题已解决，然后重试备份操作。
- 如果 VM 预配状态处于“正在更新”状态，则可能会干扰备份。 请确保此操作正常，然后重试备份操作。

## <a name="usererrorrpcollectionlimitreached---the-restore-point-collection-max-limit-has-reached"></a>UserErrorRpCollectionLimitReached - 已达到还原点集合的最大限制

**错误代码**：UserErrorRpCollectionLimitReached <br>
**错误消息**：已达到还原点集合的最大限制。 <br>

- 如果恢复点资源组中的锁阻止自动清理恢复点，则可能会发生此问题。
- 如果每天触发多个备份，则也可能发生此问题。 目前，我们建议每天只创建一个备份，因为即时还原点只保留 1-5 天（按照配置的快照保留期的要求），并且在任意给定时间，只能将 18 个即时 RP 与一个 VM 相关联。 <br>
- VM 的各个还原点集合和资源组的还原点数量不能超过 18 个。 若要创建新的还原点，请删除现有的还原点。

建议的操作：<br>
若要解决此问题，请删除 VM 资源组中的锁，并重试触发清理的操作。
> [!NOTE]
> 备份服务将创建一个单独的资源组而非 VM 的资源组来存储还原点集合。 建议客户不要锁定为备份服务使用而创建的资源组。 备份服务创建的资源组的命名格式为：AzureBackupRG_`<Geo>`_`<number>` 例如：AzureBackupRG_northeurope_1

**步骤 1：[删除还原点资源组中的锁](#remove_lock_from_the_recovery_point_resource_group)** <br>
**步骤 2：[清理还原点集合](#clean_up_restore_point_collection)**<br>

## <a name="usererrorkeyvaultpermissionsnotconfigured---backup-doesnt-have-sufficient-permissions-to-the-key-vault-for-backup-of-encrypted-vms"></a>UserErrorKeyvaultPermissionsNotConfigured - 备份服务对密钥保管库没有足够的权限，无法备份已加密的 VM

**错误代码**：UserErrorKeyvaultPermissionsNotConfigured <br>
**错误消息**：备份服务对 Key Vault 没有足够的权限，无法备份已加密的 VM。 <br>

要使备份操作在加密的 VM 上成功，该服务必须具有访问密钥保管库的权限。 可以通过 [Azure 门户](./backup-azure-vms-encryption.md)或 [PowerShell](./backup-azure-vms-automation.md#enable-protection) 来设置权限。

## <a name="extensionsnapshotfailednonetwork---snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a><a name="ExtensionSnapshotFailedNoNetwork-snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>ExtensionSnapshotFailedNoNetwork - 由于虚拟机上无网络连接，快照操作失败

**错误代码**：ExtensionSnapshotFailedNoNetwork<br>
**错误消息**：由于虚拟机上无网络连接，快照操作失败<br>

注册和计划 Azure 备份服务的 VM 后，备份会通过与 VM 备份扩展通信来获取时间点快照，从而启动作业。 以下任何条件都可能阻止快照的触发。 如果未触发快照，则备份可能失败。 完成以下故障排除步骤，然后重试操作：

[无法检索快照状态或无法创建快照](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)****  

## <a name="extensionoperationfailedformanageddisks---vmsnapshot-extension-operation-failed"></a><a name="ExtensionOperationFailed-vmsnapshot-extension-operation-failed"></a>ExtensionOperationFailedForManagedDisks - VMSnapshot 扩展操作失败

**错误代码**：ExtensionOperationFailedForManagedDisks <br>
**错误消息**：VMSnapshot 扩展操作失败<br>

注册和计划 Azure 备份服务的 VM 后，备份会通过与 VM 备份扩展通信来获取时间点快照，从而启动作业。 以下任何条件都可能阻止快照的触发。 如果未触发快照，则备份可能失败。 请按所列顺序完成以下故障排除步骤，然后重试操作：  
**原因 1：[无法检索快照状态或无法创建快照](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**原因 2：[代理安装在 VM 中，但无响应（针对 Windows VM）](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**原因 3：[VM 中安装的代理已过时（针对 Linux VM）](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="backupoperationfailed--backupoperationfailedv2---backup-fails-with-an-internal-error"></a>BackUpOperationFailed/BackUpOperationFailedV2 - 备份失败并出现内部错误

**错误代码**：BackUpOperationFailed/BackUpOperationFailedV2 <br>
**错误消息**：发生内部错误，备份失败 - 请在几分钟后重试操作 <br>

注册和计划 Azure 备份服务的 VM 后，备份会通过与 VM 备份扩展通信来获取时间点快照，从而启动作业。 以下任何条件都可能阻止快照的触发。 如果未触发快照，则备份可能失败。 请按所列顺序完成以下故障排除步骤，然后重试操作：  
**原因 1：[代理安装在 VM 中，但无响应（针对 Windows VM）](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**原因 2：[VM 中安装的代理已过时（针对 Linux VM）](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**原因 3：[无法检索快照状态或无法创建快照](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**原因 4：[备份服务因资源组锁定而无权删除旧的还原点](#remove_lock_from_the_recovery_point_resource_group)**<br>

## <a name="usererrorunsupporteddisksize---the-configured-disk-sizes-is-currently-not-supported-by-azure-backup"></a>UserErrorUnsupportedDiskSize - Azure 备份当前不支持配置的磁盘大小

**错误代码**：UserErrorUnsupportedDiskSize <br>
**错误消息**：Azure 备份当前不支持配置的磁盘大小。 <br>

对磁盘大小大于 32 TB 的 VM 进行备份时，备份操作可能会失败。 此外，目前不支持备份大小超过 4 TB 的加密磁盘。 通过拆分磁盘，确保磁盘大小于或等于支持的限制。

## <a name="usererrorbackupoperationinprogress---unable-to-initiate-backup-as-another-backup-operation-is-currently-in-progress"></a>UserErrorBackupOperationInProgress - 无法启动备份，因为另一个备份操作当前正在进行中

**错误代码**：UserErrorBackupOperationInProgress <br>
**错误消息**：无法启动备份，因为另一个备份操作当前正在进行中<br>

最近的备份作业失败，因为某个现有的备份作业正在进行。 在当前作业完成前，无法启动新的备份作业。 在触发或计划其他备份操作之前，请确保完成当前正在进行的备份操作。 若要检查备份作业状态，请执行以下步骤：

1. 登录到 Azure 门户，选择 " **所有服务**"。 键入 "恢复服务"，并选择 " **恢复服务保管库**"。 此时显示恢复服务保管库列表。
2. 在恢复服务保管库列表中，选择在其中配置了备份的保管库。
3. 在保管库仪表板菜单上，选择 " **备份作业** "，它将显示所有备份作业。
   - 如果某个备份作业正在进行，请等待它完成或取消备份作业。
     - 若要取消备份作业，请右键单击备份作业，然后选择 " **取消** " 或 "使用 [PowerShell](/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob)"。
   - 如果已在另一个保管库中重新配置了备份，则确保旧保管库中没有正在运行的备份作业。 如果存在，则取消备份作业。
     - 若要取消备份作业，请右键单击备份作业，然后选择 "**取消**" 或 "使用[PowerShell](/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob) "
4. 重试备份操作。

如果计划备份操作花费时间长且与下一个备份配置冲突，请查看[最佳做法](backup-azure-vms-introduction.md#best-practices)、[备份性能](backup-azure-vms-introduction.md#backup-performance)和[还原注意事项](backup-azure-vms-introduction.md#backup-and-restore-considerations)。

## <a name="usererrorcrpreportedusererror---backup-failed-due-to-an-error-for-details-see-job-error-message-details"></a>UserErrorCrpReportedUserError - 由于出现错误，备份失败。 有关详细信息，请参阅“作业错误消息详细信息”

**错误代码**：UserErrorCrpReportedUserError <br>
**错误消息**：由于出现错误，备份失败。 有关详细信息，请参阅“作业错误消息详细信息”。

此错误从 IaaS VM 报告。 若要确定问题的根本原因，请访问恢复服务保管库设置。 在“监视”部分选择“备份作业”，筛选并查看状态。**** **** 选择 " **失败** " 以查看基本错误消息详细信息。 根据错误详细信息页中的建议采取进一步操作。

## <a name="usererrorbcmdatasourcenotpresent---backup-failed-this-virtual-machine-is-not-actively-protected-by-azure-backup"></a>UserErrorBcmDatasourceNotPresent - 备份失败：此虚拟机不受 Azure 备份的(主动)保护

**错误代码**：UserErrorBcmDatasourceNotPresent <br>
**错误消息**：备份失败：此虚拟机不受 Azure 备份的(主动)保护。

检查给定虚拟机是否正在主动 (不处于暂停状态，) 受 Azure 备份保护。 若要解决此问题，请确保虚拟机处于活动状态，然后重试该操作。

## <a name="causes-and-solutions"></a>原因和解决方法

### <a name="the-agent-is-installed-in-the-vm-but-its-unresponsive-for-windows-vms"></a><a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>代理安装在 VM 中，但无响应（针对 Windows VM）

#### <a name="solution-for-this-error"></a>此错误的解决方案

VM 代理可能已损坏或服务可能已停止。 重新安装 VM 代理可帮助获取最新版本。 此外，还有助于与服务重新开始通信。

1. 确定 Windows Azure 来宾代理服务是否在 VM 服务 (services.msc) 中运行。 尝试重启 Windows Azure 来宾代理服务并启动备份。
2. 如果服务中未显示 Windows Azure 来宾代理服务，请在“控制面板”中转到“程序和功能”，确定是否已安装 Windows Azure 来宾代理服务****。
3. 如果“程序和功能”中显示了 Windows Azure 来宾代理，请将其卸载****。
4. 下载并安装[最新版本的代理 MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。 必须拥有管理员权限才能完成安装。
5. 检查服务中是否显示了 Windows Azure 来宾代理服务。
6. 运行按需备份：
   - 在门户中，选择“立即备份”****。

此外，检查是否在 VM 中[安装了 Microsoft .NET 4.5](/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed)。 VM 代理需要使用 .NET 4.5 来与服务通信。

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>VM 中安装的代理已过时（针对 Linux VM）

#### <a name="solution"></a>解决方案

对于 Linux VM，与代理或扩展相关的大多数失败都是由于影响过时的 VM 代理的问题所造成的。 若要解决此问题，请遵循以下通用准则：

1. 按照[更新 Linux VM 代理](../virtual-machines/extensions/update-linux-agent.md)的说明进行操作。

   > [!NOTE]
   > *强烈建议* 只通过分发存储库更新代理。 建议不要直接从 GitHub 下载代理代码并将其更新。 如果分发没有可用的最新代理，请联系分发支持部门，了解如何安装最新代理。 若要检查最新代理，请转到 GitHub 存储库中的 [Microsoft Azure Linux 代理](https://github.com/Azure/WALinuxAgent/releases)页。

2. 运行以下命令，确保 Azure 代理可在 VM 上运行：`ps -e`

   如果该进程未运行，请使用以下命令进行重启：

   - 对于 Ubuntu： `service walinuxagent start`
   - 对于其他分发版： `service waagent start`

3. [配置自动重新启动代理](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash)。
4. 运行新的测试备份。 如果仍然失败，请从 VM 收集以下日志：

   - /var/lib/waagent/*.xml
   - /var/log/waagent.log
   - /var/log/azure/*

如果需要 waagent 的详细日志记录，请执行以下步骤：

1. 在 /etc/waagent.conf 文件中，找到以下行：**Enable verbose logging (y|n)**
2. 将 **Logs.Verbose** 值从 n** 更改为 y**。
3. 保存更改，然后遵循本部分前面所述的步骤重启 waagent。

### <a name="vm-agent-configuration-options-are-not-set-for-linux-vms"></a>未设置 VM-Agent 配置选项（对于 Linux VM）

配置文件 (/etc/waagent.conf) 可控制 waagent 的操作。 配置文件选项 Extensions.Enable 和 Provisioning.Agent 应分别设置为 y 和 auto，以使备份正常工作**** **** **** ****。
有关 VM-Agent 配置文件选项的完整列表，请参阅 <https://github.com/Azure/WALinuxAgent#configuration-file-options>

### <a name="application-control-solution-is-blocking-iaasbcdrextensionexe"></a>应用程序控制解决方案正在阻止 IaaSBcdrExtension.exe

如果正在运行 [AppLocker](/windows/security/threat-protection/windows-defender-application-control/applocker/what-is-applocker) (或另一个应用程序控制解决方案) ，并且规则基于发布服务器或路径，则它们可能会阻止 **IaaSBcdrExtension.exe** 可执行文件运行。

#### <a name="solution-to-this-issue"></a>此问题的解决方案

从 AppLocker（或其他应用程序控制软件）中排除 `/var/lib` 路径或 IaaSBcdrExtension.exe 可执行文件****。

### <a name="the-snapshot-status-cant-be-retrieved-or-a-snapshot-cant-be-taken"></a><a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>无法检索快照状态或无法创建快照

VM 备份依赖于向基础存储帐户发出快照命令。 备份失败的原因可能是它无法访问存储帐户，也可能是快照任务的执行被延迟。

#### <a name="solution-for-this-issue"></a>此问题的解决方案

以下状态可能导致快照任务失败：

| 原因 | 解决方案 |
| --- | --- |
| 由于在远程桌面协议 (RDP) 中关闭了 VM，VM 状态报告不正确。 | 如果在 RDP 中关闭了 VM，请检查门户，确定 VM 状态是否正确。 如果不正确，请在门户中使用 VM 仪表板上的“关闭”**** 选项来关闭 VM。 |
| VM 无法从 DHCP 获取主机或结构地址。 | 必须在来宾内启用 DHCP，才能正常进行 IaaS VM 备份。 如果 VM 无法从 DHCP 响应 245 获取主机或结构地址，则无法下载或运行任何扩展。 如果需要静态专用 IP，则应通过 Azure 门户或 PowerShell 进行配置，同时确保启用 VM 内的 DHCP 选项**** ****。 [详细了解](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface)如何通过 PowerShell 设置静态 IP 地址。

### <a name="remove-lock-from-the-recovery-point-resource-group"></a><a name="remove_lock_from_the_recovery_point_resource_group"></a>删除恢复点资源组中的锁

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 转到“所有资源选项”，选择采用 AzureBackupRG_`<Geo>`_`<number>` 格式的还原点集合资源组。****
3. 在“设置”部分，选择“锁”以显示锁。**** ****
4. 若要删除锁，请选择省略号，然后选择 " **删除**"。

    ![删除锁](./media/backup-azure-arm-vms-prepare/delete-lock.png)

### <a name="clean-up-restore-point-collection"></a><a name="clean_up_restore_point_collection"></a>清理还原点集合

删除锁后，必须清理还原点。

如果删除 VM 的资源组或 VM 本身，则托管磁盘的即时还原快照会保持活动状态，并根据保留集过期。 若要删除存储在还原点集合中的即时还原快照（如果不再需要这些快照），请按照以下步骤清理还原点集合。

若要清理还原点，请执行以下任一方法：<br>

- [通过运行按需备份来清理还原点集合](#clean-up-restore-point-collection-by-running-on-demand-backup)<br>
- [从 Azure 门户清理还原点集合](#clean-up-restore-point-collection-from-azure-portal)<br>

#### <a name="clean-up-restore-point-collection-by-running-on-demand-backup"></a><a name="clean-up-restore-point-collection-by-running-on-demand-backup"></a>通过运行按需备份来清理还原点集合

删除锁后，将触发按需备份。 此操作可以确保自动清理还原点。 预期此按需操作第一次会失败；但是，它可以确保自动完成清理，而无需手动删除还原点。 清理后，下一个计划的备份应会成功。

> [!NOTE]
> 自动清理将在触发按需备份的数小时后发生。 如果计划的备份仍然失败，请尝试使用[此处](#clean-up-restore-point-collection-from-azure-portal)列出的步骤手动删除还原点集合。

#### <a name="clean-up-restore-point-collection-from-azure-portal-br"></a><a name="clean-up-restore-point-collection-from-azure-portal"></a>从 Azure 门户清理还原点集合 <br>

若要手动清除由于资源组中存在锁而未能清除的还原点集合，请尝试以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 在 " **中心** " 菜单上，选择 " **所有资源**"，选择具有以下格式的资源组 AZUREBACKUPRG_ `<Geo>` _ `<number>` 其中包含 VM。

    ![选择资源组](./media/backup-azure-arm-vms-prepare/resource-group.png)

3. 选择 "资源组"，将显示 " **概述** " 窗格。
4. 选择“显示隐藏的类型”选项，以显示所有已隐藏的资源。**** 选择采用 AzureBackupRG_`<VMName>`_`<number>` 格式的还原点集合。

    ![选择还原点集合](./media/backup-azure-arm-vms-prepare/restore-point-collection.png)

5. 选择 " **删除** " 以清理还原点集合。
6. 再次重试备份操作。

> [!NOTE]
 >如果资源（RP 集合）具有大量还原点，则从门户中将其删除的操作可能会超时并失败。 这是已知的 CRP 问题，其中所有还原点不会在规定时间内都删除且操作会超时；但是，删除操作通常会在 2 次或 3 次重试后成功。
