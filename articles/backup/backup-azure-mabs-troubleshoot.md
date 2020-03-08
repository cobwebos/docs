---
title: 对 Azure 备份服务器进行故障排除
description: 排查 Azure 备份服务器的安装和注册以及应用程序工作负荷的备份和还原问题。
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.openlocfilehash: cc0cf7e91c1aacbc637d33ab1e5546cc54836b28
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78673115"
---
# <a name="troubleshoot-azure-backup-server"></a>对 Azure 备份服务器进行故障排除

请参考下表中的信息，排查使用 Azure 备份服务器时遇到的错误。

## <a name="basic-troubleshooting"></a>基本故障排除

建议在开始解决 Microsoft Azure 备份 Server （MABS）之前，执行以下验证：

- [确保 Microsoft Azure 恢复服务（MARS）代理是最新的](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [确保在 MARS 代理和 Azure 之间存在网络连接](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)
- 确保 Microsoft Azure 恢复服务正在运行（在服务控制台中）。 如有必要，请重新启动并重试该操作
- [确保在暂存文件夹位置有 5-10% 的可用卷空间](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#whats-the-minimum-size-requirement-for-the-cache-folder)
- 如果注册失败，则确保你尝试安装的服务器 Azure 备份服务器尚未注册到另一个保管库
- 如果推送安装失败，请检查 DPM 代理是否存在。 如果该代理存在，请将其卸载，然后重试安装
- [确保没有其他进程或防病毒软件在干扰 Azure 备份](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-another-process-or-antivirus-software-interfering-with-azure-backup)<br>
- 确保 SQL 代理服务在 MAB 服务器中正在运行并设置为自动<br>

## <a name="invalid-vault-credentials-provided"></a>提供的保管库凭据无效

| Operation | 错误详细信息 | 解决方法 |
| --- | --- | --- |
| 注册到保管库 | 提供的保管库凭据无效。 该文件已损坏，或者没有与恢复服务关联的最新凭据。 | 建议的操作： <br> <ul><li> 从保管库中下载最新的凭据文件，并重试。 <br>（或者）</li> <li> 如果上述操作不起作用，请尝试将凭据下载到其他本地目录，或创建新的保管库。 <br>（或者）</li> <li> 尝试更新日期和时间设置，如[本文](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#invalid-vault-credentials-provided)所述。 <br>（或者）</li> <li> 检查 C:\windows\temp 中的文件数是否超过 65000。 将过时文件移到另一位置，或者删除 Temp 文件夹中的项。 <br>（或者）</li> <li> 检查证书的状态。 <br> a. 打开“管理计算机证书”（在控制面板中）。 <br> b. 展开“个人”节点及其子节点“证书”。<br> c.  删除证书“Windows Azure Tools”。 <br> d. 重试在 Azure 备份客户端中注册。 <br> （或者） </li> <li> 检查是否部署了任何组策略。 </li></ul> |

## <a name="replica-is-inconsistent"></a>副本不一致

| Operation | 错误详细信息 | 解决方法 |
| --- | --- | --- |
| 备份 | 副本不一致 | 检查是否已在保护组向导中启用自动一致性检查选项。 有关副本不一致和相关建议的原因的详细信息，请参阅文章[副本不一致](https://docs.microsoft.com/previous-versions/system-center/data-protection-manager-2006/cc161593(v=technet.10))。<br> <ol><li> 对于系统状态/BMR 备份，请检查是否已在受保护服务器上安装 Windows Server 备份。</li><li> 检查 DPM/Microsoft Azure 备份服务器上的 DPM 存储池中是否存在空间相关的问题，并根据需要分配存储。</li><li> 检查受保护服务器上卷影复制服务的状态。 如果该服务处于禁用状态，请将其设置为手动启动。 在服务器上启动该服务。 然后返回到 DPM/Microsoft Azure 备份服务器控制台，开始与一致性检查作业同步。</li></ol>|

## <a name="online-recovery-point-creation-failed"></a>在线恢复点创建失败

| Operation | 错误详细信息 | 解决方法 |
| --- | --- | --- |
| 备份 | 在线恢复点创建失败 | **错误消息**： Windows Azure 备份代理无法创建所选卷的快照。 <br> **解决方法**：尝试增加副本卷和恢复点卷中的空间。<br> <br> **错误消息**： Windows Azure 备份代理无法连接到 OBEngine 服务 <br> **解决方法**：请验证并确保 OBEngine 存在于计算机上正在运行的服务的列表中。 如果未运行 OBEngine 服务，请使用“net start OBEngine”命令启动 OBEngine 服务。 <br> <br> **错误消息**：未设置此服务器的加密密码。 Please configure an encryption passphrase.（未设置此服务器的加密通行短语。请配置加密通行短语。） <br> **解决方法**：尝试配置加密密码。 如果此方法不起作用，请执行以下步骤： <br> <ol><li>检查是否存在暂存位置。 注册表项 **HKEY_LOCAL_MACHINE\Software\Microsoft\Windows Azure Backup\Config** 中提到的名为 **ScratchLocation** 的位置应该存在。</li><li> 如果暂存位置存在，请尝试使用旧通行短语重新注册。 *配置加密通行短语时，请将其保存在安全的位置。*</li><ol>|

## <a name="the-original-and-external-dpm-servers-must-be-registered-to-the-same-vault"></a>必须将原始和外部 DPM 服务器注册到同一保管库

| Operation | 错误详细信息 | 解决方法 |
| --- | --- | --- |
| 还原 | **错误代码**： CBPServerRegisteredVaultDontMatchWithCurrent/保管库凭据错误：100110 <br/> <br/>**错误消息**：必须将原始和外部 DPM 服务器注册到同一保管库 | **原因**：当你尝试使用外部 DPM 恢复选项将文件从原始服务器还原到备用服务器，并且如果要恢复的服务器和从其备份数据的原始服务器不关联到同一恢复服务保管库时，会出现此问题。<br/> <br/>**解决方法**若要解决此问题，请确保原始服务器和备用服务器都注册到同一个保管库。|

## <a name="online-recovery-point-creation-jobs-for-vmware-vm-fail"></a>VMware VM 的联机恢复点创建作业失败

| Operation | 错误详细信息 | 解决方法 |
| --- | --- | --- |
| 备份 | VMware VM 的联机恢复点创建作业失败。 DPM 在尝试获取 ChangeTracking 信息时遇到 VMware 错误。 ErrorCode-FileFaultFault （ID 33621） |  <ol><li> 在 VMware 上对受影响的 VM 重置 CTK。</li> <li>检查 VMware 上是否未部署独立磁盘。</li> <li>停止对受影响的 Vm 的保护，并通过 "**刷新**" 按钮重新保护。 </li><li>对受影响的 VM 运行 CC（一致性检查）。</li></ol>|

## <a name="the-agent-operation-failed-because-of-a-communication-error-with-the-dpm-agent-coordinator-service-on-the-server"></a>代理操作失败，因为服务器上的 DPM 代理协调器服务出现通信错误

| Operation | 错误详细信息 | 解决方法 |
| --- | --- | --- |
| 将代理推送到受保护的服务器 | 代理操作失败，因为 \<ServerName> 上的 DPM 代理协调器服务出现通信错误。 | **如果产品中显示的建议操作不起作用，请执行以下步骤**： <ul><li> 如果是从不可信域附加计算机，请执行[这些步骤](https://docs.microsoft.com/system-center/dpm/back-up-machines-in-workgroups-and-untrusted-domains?view=sc-dpm-2019)。 <br> （或者） </li><li> 如果是从可信域附加计算机，则可通过[此博客](https://techcommunity.microsoft.com/t5/system-center-blog/data-protection-manager-agent-network-troubleshooting/ba-p/344726)中所述的步骤进行故障排除。 <br>（或者）</li><li> 尝试禁用防病毒软件以进行故障排除。 如果解决了问题，可根据[此文](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh757911(v=sc.12))中的建议修改防病毒软件设置。</li></ul> |

## <a name="setup-could-not-update-registry-metadata"></a>安装程序无法更新注册表元数据

| Operation | 错误详细信息 | 解决方法 |
|-----------|---------------|------------|
|安装 | 安装程序无法更新注册表元数据。 此更新失败可能导致存储占用过高。 若要避免这种情况，请更新 ReFS 修整注册表项。 | 调整注册表项 **SYSTEM\CurrentControlSet\Control\FileSystem\RefsEnableInlineTrim**。 将 Dword 值设置为 1。 |
|安装 | 安装程序无法更新注册表元数据。 此更新失败可能导致存储占用过高。 为避免发生这种情况，请更新 Volume SnapOptimization 注册表项。 | 使用空字符串值创建注册表项 **SOFTWARE\Microsoft Data Protection Manager\Configuration\VolSnapOptimization\WriteIds**。 |

## <a name="registration-and-agent-related-issues"></a>注册和代理相关问题

| Operation | 错误详细信息 | 解决方法 |
| --- | --- | --- |
| 将代理推送到受保护的服务器 | 为服务器指定的凭据无效。 | **如果产品中显示的建议操作不起作用，请执行以下步骤**： <br> 尝试根据[此文](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-2019)中的说明，在生产服务器上手动安装保护代理。|
| Azure 备份代理无法连接到 Azure 备份服务 (ID: 100050) | Azure 备份代理无法连接到 Azure 备份服务。 | **如果产品中显示的建议操作不起作用，请执行以下步骤**： <br>1. 从权限提升的提示符下运行以下命令： **psexec-i-s "C:\Program Files\Internet Explorer\iexplore.exe**。 此命令会打开 Internet Explorer 窗口。 <br/> 2. > **Internet 选项**"中的" > **连接**" > **LAN 设置**中转到"**工具**"。 <br/> 3. 将设置更改为使用代理服务器。 然后提供代理服务器详细信息。<br/> 4. 如果计算机具有有限的 internet 访问权限，请确保计算机或代理上的防火墙设置允许这些[url](install-mars-agent.md#verify-internet-access)和[IP 地址](install-mars-agent.md#verify-internet-access)。|
| Azure 备份代理安装失败 | Microsoft Azure 恢复服务安装失败。 通过 Microsoft Azure 恢复服务安装对系统所做的所有更改都已回滚。 (ID: 4024) | 手动安装 Azure 代理。

## <a name="configuring-protection-group"></a>配置保护组

| Operation | 错误详细信息 | 解决方法 |
| --- | --- | --- |
| 配置保护组 | DPM 无法枚举受保护计算机（受保护计算机名称）上的应用程序组件。 | 在相关数据源/组件级别的“配置保护组”UI 屏幕上选择“刷新”。 |
| 配置保护组 | 无法配置保护 | 如果受保护的服务器是 SQL Server，请根据[此文](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh757977(v=sc.12))中所述，检查是否已在受保护计算机上将 sysadmin 角色权限提供给系统帐户 (NTAuthority\System)。
| 配置保护组 | 此保护组的存储池中没有足够的可用空间。 | 添加到存储池的磁盘[不应包含分区](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh758075(v=sc.12))。 删除磁盘上的所有现有卷。 然后将磁盘添加到存储池。|
| 策略更改 |无法修改备份策略。 错误：由于内部服务错误 [0x29834]，当前操作失败。 请在一段时间后重试操作。 如果问题持续出现，请联系 Microsoft 支持。 | 原因：<br/>在以下三种情况下会发生此错误：启用安全设置、尝试缩短保留期至低于前面指定的最小值，以及使用不受支持的版本。 （不受支持的版本包括低于 Microsoft Azure 备份服务器版本 2.0.9052 和 Azure 备份服务器 Update 1 的版本。） <br/>**建议的操作：**<br/> 若要继续执行策略相关的更新，请将保留期设置为高于指定的最短保留期。 （每日、每周、每月和每年备份的最短保留期分别为 7 天、4 周、3 个月和 1 年）。 <br><br>（可选）另一种首选方法是更新备份代理和 Azure 备份服务器，以利用所有安全更新。 |

## <a name="backup"></a>备份

| Operation | 错误详细信息 | 解决方法 |
| --- | --- | --- |
| 备份 | 运行作业时发生意外错误。 设备未准备就绪。 | **如果产品中显示的建议操作不起作用，请执行以下步骤：** <br> <ul><li>将卷影副本存储空间设置为不对保护组中的项进行限制，然后运行一致性检查。<br></li> （或者） <li>尝试删除现有的保护组，并创建多个新组。 每个新保护组应包含单个项。</li></ul> |
| 备份 | 如果只备份系统状态，请验证受保护计算机上是否有足够的可用空间来存储系统状态备份。 | <ol><li>检查是否已在受保护计算机上安装 Windows Server 备份。</li><li>检查受保护计算机上是否存在用于系统状态的足够空间。 执行此项检查的最简单方法是转到受保护的计算机，打开“Windows Server 备份”并单击相关选项，然后选择“BMR”。 UI 会告知需要多少空间。 打开“WSB” > “本地备份” > “备份计划” > “选择备份配置” > “完整服务器”（大小已显示）。 使用该大小进行验证。</li></ol>
| 备份 | BMR 备份失败 | 如果 BMR 很大，可将某些应用程序文件移到 OS 驱动器，并重试。 |
| 备份 | 用于在新的 Microsoft Azure 备份服务器上重新保护 VMware VM 的选项未显示为可添加。 | VMware 属性指向已停用的旧 Microsoft Azure 备份服务器实例。 若要解决此问题，请执行下列操作：<br><ol><li>在 VCenter（类似的程序为 SC-VMM）中，转到“摘要”选项卡，然后选择“自定义属性”。</li>  <li>从 **DPMServer** 值中删除旧的 Microsoft Azure 备份服务器名称。</li>  <li>返回到新的 Microsoft Azure 备份服务器并修改 PG。  选择“刷新”按钮后，将显示带有复选框的 VM，可将其添加到保护列表。</li></ol> |
| 备份 | 访问文件/共享文件夹时出错 | 如本文中所建议的那样修改防病毒设置，请在[DPM 服务器上运行防病毒软件](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh757911(v=sc.12))。|

## <a name="change-passphrase"></a>更改通行短语

| Operation | 错误详细信息 | 解决方法 |
| --- | --- | --- |
| 更改通行短语 |输入的安全 PIN 不正确。 提供正确的安全 PIN 来完成此操作。 |原因：<br/> 如果在执行关键操作（例如更改通行短语）时输入无效或已过期的安全 PIN，则会出现此错误。 <br/>**建议的操作：**<br/> 若要完成该操作，必须输入有效的安全 PIN。 若要获取 PIN，请登录到 Azure 门户并转到“恢复服务保管库”。 然后转到“设置” > “属性” > “生成安全 PIN”。 使用此 PIN 更改通行短语。 |
| 更改通行短语 |操作失败。 ID：120002 |原因：<br/>启用安全设置、尝试更改通行短语和使用不受支持的版本时，会出现此错误。<br/>**建议的操作：**<br/> 若要更改通行短语，必须先将备份代理更新到最低版本 2.0.9052。 此外，还需要将 Azure 备份服务器更新到最低的 Update 1 版本，并输入有效的安全 PIN。 若要获取 PIN，请登录到 Azure 门户并转到“恢复服务保管库”。 然后转到“设置” > “属性” > “生成安全 PIN”。 使用此 PIN 更改通行短语。 |

## <a name="configure-email-notifications"></a>配置电子邮件通知

| Operation | 错误详细信息 | 解决方法 |
| --- | --- | --- |
| 使用 Office 365 帐户设置电子邮件通知 |错误 ID：2013| 原因：<br> 尝试使用 Office 365 帐户 <br>**建议的操作：**<ol><li> 首先要确保 DPM 服务器的 "在接收连接器上允许匿名中继" 是在 Exchange 上设置的。 有关如何配置此设置的详细信息，请参阅[允许在接收连接器上使用匿名中继](https://docs.microsoft.com/exchange/mail-flow/connectors/allow-anonymous-relay?view=exchserver-2019)。</li> <li> 如果无法使用内部 SMTP 中继并需要使用 Office 365 服务器进行设置，可将 IIS 设置为中继。 将 DPM 服务器配置为[使用 IIS 将 SMTP 中继到 O365](https://docs.microsoft.com/exchange/mail-flow/test-smtp-with-telnet?view=exchserver-2019)。<br><br>  请确保使用用户\@domain.com 格式，而*非*domain\user<br><br><li>指示 DPM 将本地服务器名用作 SMTP 服务器，并使用端口 587。 然后将它指向于应从中发送电子邮件的用户电子邮件地址。<li> DPM SMTP 设置页上的用户名和密码应属于 DPM 所在域中的域帐户。 </li><br> 更改 SMTP 服务器地址时，请对新设置进行更改，关闭设置框，然后重新打开它以确保反映新值。  只是进行更改和测试可能不一定总能让新设置生效，因此最佳做法是通过此方法进行测试。<br><br>在此过程中，可随时清除这些设置，方法是关闭 DPM 控制台，然后编辑以下注册表项：**HKLM\SOFTWARE\Microsoft\Microsoft Data Protection Manager\Notification\ <br/> 删除 SMTPPassword 和 SMTPUserName 项**。 重新启动 UI 时，可将这些设置添加回到 UI。

## <a name="common-issues"></a>常见问题

本部分介绍在使用 Azure 备份服务器时可能会遇到的常见错误。

### <a name="cbpsourcesnapshotfailedreplicamissingorinvalid"></a>CBPSourceSnapshotFailedReplicaMissingOrInvalid

错误消息 | 建议的操作 |
-- | --
备份失败，因为磁盘备份副本无效或缺失。 | 若要解决此问题，请验证以下步骤，然后重试该操作： <br/> 1. 创建磁盘恢复点<br/> 2. 对数据源运行一致性检查 <br/> 3. 停止对数据源的保护，然后重新配置对此数据源的保护

### <a name="cbpsourcesnapshotfailedreplicametadatainvalid"></a>CBPSourceSnapshotFailedReplicaMetadataInvalid

错误消息 | 建议的操作 |
-- | --
源卷快照失败，因为副本上的元数据无效。 | 请创建此数据源的磁盘恢复点，然后重试联机备份

### <a name="cbpsourcesnapshotfailedreplicainconsistent"></a>CBPSourceSnapshotFailedReplicaInconsistent

错误消息 | 建议的操作 |
-- | --
源卷快照失败，因为数据源副本不一致。 | 对此数据源运行一致性检查，然后重试

### <a name="cbpsourcesnapshotfailedreplicacloningissue"></a>CBPSourceSnapshotFailedReplicaCloningIssue

错误消息 | 建议的操作 |
-- | --
备份失败，因为磁盘备份副本无法克隆。| 确保所有以前的磁盘备份副本文件（.vhdx）都已卸载，并且在联机备份期间没有磁盘到磁盘的备份
