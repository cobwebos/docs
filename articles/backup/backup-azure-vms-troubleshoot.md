---
title: 排查 Azure 虚拟机备份错误
description: Azure 虚拟机备份和还原疑难解答
services: backup
author: trinadhk
manager: shreeshd
ms.service: backup
ms.topic: conceptual
ms.date: 8/7/2018
ms.author: trinadhk
ms.openlocfilehash: dbd49166bbcb6bbd648b6a64f7eb97d8073f33fc
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2018
ms.locfileid: "51706817"
---
# <a name="troubleshoot-azure-virtual-machine-backup"></a>Azure 虚拟机备份疑难解答
可以参考下表中所列的信息，排查使用 Azure 备份时遇到的错误。

| 错误详细信息 | 解决方法 |
| --- | --- |
| 无法执行该操作，因为 VM 不再存在。 - 停止保护虚拟机，无需删除备份数据。 有关详细信息，请访问 http://go.microsoft.com/fwlink/?LinkId=808124 |当主 VM 已删除，而备份策略仍继续查找用于备份的 VM 时，会发生这种情况。 若要修复此错误，请执行以下操作： <ol><li> 使用相同的名称和相同的资源组名称 [云服务名称] 重新创建虚拟机，<br>（或者）</li><li> 停止保护虚拟机，删除或不删除备份数据。 [更多详细信息](https://go.microsoft.com/fwlink/?LinkId=808124)</li></ol> |
| 由于虚拟机上无网络连接，快照操作失败 - 请确保 VM 具有网络访问权限。 要成功进行快照操作，请将 Azure 数据中心 IP 范围加入允许列表，或设置代理服务器用于网络访问。 有关详细信息，请参阅 http://go.microsoft.com/fwlink/?LinkId=800034。 如果已经在使用代理服务器，请确保代理服务器设置配置正确 | 拒绝虚拟机上的出站 Internet 连接时会出现此错误。 VM 快照扩展需要 Internet 连接才可拍摄基础磁盘的快照。 请参阅[如何解决由阻止网络访问引起的快照操作问题](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#ExtensionSnapshotFailedNoNetwork-snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine)这一部分。 |
| VM 代理无法与 Azure 备份服务进行通信。 - 确保 VM 具有网络连接、VM 代理为最新版且正常运行。 有关详细信息，请参阅文章 http://go.microsoft.com/fwlink/?LinkId=800034。 |如果 VM 代理出现问题，或以某种方式阻止了对 Azure 基础结构的网络访问，则会引发此错误。 [详细了解](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#UserErrorGuestAgentStatusUnavailable-vm-agent-unable-to-communicate-with-azure-backup)如何调试 VM 快照问题。<br> 如果 VM 代理未导致任何问题，请重启 VM。 VM 状态错误可能导致出现问题，重启 VM 会重置状态。 |
| VM 处于失败预配状态 - 请重启 VM，并确保 VM 正在运行或已关闭。 | 当一个扩展失败导致 VM 状态为失败预配状态时，会出现此错误。 转到扩展列表，查看是否有失败的扩展，将其删除并尝试重启虚拟机。 如果所有扩展的状态都为正在运行，请检查 VM 代理服务是否正在运行。 如果未运行，请重启 VM 代理服务。 |
| 托管磁盘的 VMSnapshot 扩展操作失败 - 请重试备份操作。 如果问题仍然存在，请按照 http://go.microsoft.com/fwlink/?LinkId=800034 中的说明操作。 如果该问题仍然存在，请联系 Microsoft 支持部门 | 备份服务未能触发快照时，会发生此错误。 [详细了解](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#ExtentionOperationFailed-vmsnapshot-extension-operation-failed)如何调试 VM 快照问题。 |
| 由于存储帐户可用空间不足，无法复制虚拟机快照 - 请确保存储帐户的可用空间等效于附加到虚拟机的高级存储磁盘上存在的数据 | 对于 VM 备份堆栈 V1 上的高级 VM，会将快照复制到存储帐户。 这是为了确保适用于快照的备份管理流量不会限制使用高级磁盘的应用程序可用的 IOPS 数。 Microsoft 建议仅分配总存储帐户空间的 50% (17.5 TB)，以便 Azure 备份服务可以将快照复制到存储帐户，并将数据从存储帐户中的复制位置传输到保管库。 |
| 无法执行操作，因为 VM 代理没有响应 |如果 VM 代理出现问题，或以某种方式阻止了对 Azure 基础结构的网络访问，则会引发此错误。 对于 Windows VM，请检查服务中的 VM 代理服务状态，以及代理是否显示在控制面板的程序中。 尝试从控制面板中删除程序，再重新安装代理，如[下](#vm-agent)所述。 重新安装代理后，将触发临时备份进行验证。 |
| 恢复服务扩展操作失败。 - 确保虚拟机上有最新的虚拟机代理，并且代理服务正在运行。 请重试备份操作。 如果备份操作失败，请联系 Microsoft 支持部门。 |VM 代理过期会引发此错误。 请参阅以下“更新 VM 代理”部分，更新 VM 代理。 |
| 虚拟机不存在。 - 请确保该虚拟机存在，或选择其他虚拟机。 |当主 VM 已删除，而备份策略仍继续查找要执行备份的 VM 时，会出现此错误。 若要修复此错误，请执行以下操作： <ol><li> 使用相同的名称和相同的资源组名称 [云服务名称] 重新创建虚拟机，<br>（或者）<br></li><li>停止保护虚拟机，无需删除备份数据。 [更多详细信息](https://go.microsoft.com/fwlink/?LinkId=808124)</li></ol> |
| 命令执行失败。 - 此项上当前正在进行另一项操作。 请等待前一项操作完成，然后重试此操作。 |现有备份作业正在运行，当前作业结束前无法开始新的作业。 |
| 从恢复服务保管库复制 VHD 超时 - 请在几分钟后重试操作。 如果问题持续出现，请联系 Microsoft 支持。 | 如果存储端存在暂时性错误，或者备份服务未在超时范围内接收足够的存储帐户 IOPS 来将数据传输到保管库，则会出现此错误。 请确保按照 [VM 配置最佳做法](backup-azure-vms-introduction.md#best-practices)进行操作。 将 VM 移到未加载的其他存储帐户，然后重试备份作业。|
| 发生内部错误，备份失败 - 请在几分钟后重试操作。 如果问题持续出现，请联系 Microsoft 支持 |出现此错误的原因有两个： <ol><li> 在访问 VM 存储时存在暂时性问题。 请查看 [Azure 状态网站](https://azure.microsoft.com/status/)，检查区域中是否存在计算、存储或网路问题。 问题解决后，请重试备份作业。 <li>原始 VM 已被删除，恢复点无法采用。 若要保留已删除 VM 的备份数据，但要删除备份错误，请取消保护 VM 并选择保留数据选项。 此操作可停止计划备份作业和阻止反复出现的错误消息。 |
| 未能在所选项上安装 Azure 恢复服务扩展 - VM 代理是 Azure 恢复服务扩展的必备组件。 安装 Azure VM 代理并重启注册操作 |<ol> <li>检查是否已正确安装 VM 代理。 <li>确保已正确设置 VM 配置中的标志。</ol> [详细了解](#validating-vm-agent-installation)如何安装 VM 代理以及如何验证 VM 代理安装。 |
| 扩展安装失败，出现错误“COM+ 无法与 Microsoft 分布式事务处理协调器通信”。 |这通常意味着到 COM+ 服务未运行。 请与 Microsoft 支持部门联系，以获取解决此问题所需的帮助。 |
| 快照操作失败，出现 VSS 操作错误"此驱动器已通过 BitLocker 驱动器加密锁定”。 必须通过控制面板解锁此驱动器。 |针对 VM 上的所有驱动器关闭 BitLocker，观察 VSS 问题是否得到解决 |
| VM 未处于允许备份的状态下。 |<ul><li>如果 VM 处于“正在运行”和“已关闭”之间的瞬时状态，请等待状态改变，然后触发备份作业。 <li> 如果 VM 是 Linux VM 并使用安全性增强的 Linux 内核模块，则需要从安全策略排除 Linux 代理路径 (_/var/lib/waagent_)，确保安装备份扩展。  |
| 找不到 Azure 虚拟机。 |当主 VM 已删除，而备份策略仍继续查找已删除的 VM 时，会出现此错误。 若要修复此错误，请执行以下操作： <ol><li>使用相同的名称和相同的资源组名称 [云服务名称] 重新创建虚拟机， <br>（或者） <li> 禁用对此 VM 的保护，从而不创建备份作业。 </ol> |
| 虚拟机上不存在虚拟机代理 - 请安装任何必备组件和 VM 代理，并重启操作。 |[详细了解](#vm-agent)如何安装 VM 代理以及如何验证 VM 代理安装。 |
| 快照操作失败，因为 VSS 编写器处于错误状态 |需重新启动处于错误状态的 VSS（卷影复制服务）编写器。 为此，请在提升权限的命令提示符处运行 ```vssadmin list writers```。 输出包含所有 VSS 编写器及其状态。 对于每个状态不为“[1] 稳定”的 VSS 编写器，请在提升权限的命令提示符处运行以下命令，以便重启 VSS 编写器：<br> ```net stop serviceName``` <br> ```net start serviceName```|
| 快照操作失败，因为对配置进行分析失败 |发生这种情况是因为以下 MachineKeys 目录的权限已更改：_%systemdrive%\programdata\microsoft\crypto\rsa\machinekeys_ <br> 请运行以下命令，验证 MachineKeys 目录的权限是否为默认权限：<br>_icacls %systemdrive%\programdata\microsoft\crypto\rsa\machinekeys_ <br><br> 默认权限为：<br>Everyone:(R,W) <br>BUILTIN\Administrators:(F)<br><br>如果看到 MachineKeys 目录的权限不同于默认权限，请执行以下步骤来更正权限、删除证书以及触发备份。<ol><li>修复 MachineKeys 目录上的权限。<br>对目录使用 Explorer 安全属性和高级安全设置，将权限重新设为默认值。 从目录中删除所有用户对象（默认值除外），确保 Everyone 权限对以下各项具有特殊访问权限：<br>-列出文件夹/读取数据 <br>-读取属性 <br>-读取扩展的属性 <br>-创建文件/写入数据 <br>-创建文件夹/追加数据<br>-写入属性<br>-写入扩展的属性<br>-读取权限<br><br><li>删除其中发布对象为经典部署模式或 Windows Azure CRP 证书生成器的所有证书。<ul><li>[打开证书（本地计算机）控制台](https://msdn.microsoft.com/library/ms788967(v=vs.110).aspx)<li>在“个人” > “证书”下，删除其中发布对象为经典部署模式或 Windows Azure CRP 证书生成器的所有证书。</ul><li>触发 VM 备份作业。 </ol>|
| Azure 备份服务对 Key Vault 没有足够的权限，无法备份加密的虚拟机。 |应在 PowerShell 中使用 [PowerShell 文档](backup-azure-vms-automation.md)的**启用备份**部分中所述的步骤向备份服务提供这些权限。 |
|快照扩展安装失败，出现错误“COM+ was unable to talk to the Microsoft Distributed Transaction Coordinator”（COM+ 无法与 Microsoft 分布式事务处理协调器通信） | 通过提升权限的命令提示符（例如 net start COMSysApp）启动 Windows 服务“COM + 系统应用程序”。 <br>如果此服务启动失败，则执行下述操作：<ol><li> 确保“分布式事务处理协调器”服务的登录帐户为“网络服务”。 如果不是，请将登录帐户更改为“网络服务”，重启此服务，然后尝试启动“COM + 系统应用程序”。<li>如果“COM + 系统应用程序”无法启动，请按照以下步骤卸载/安装“分布式事务处理协调器”服务：<br> - 停止 MSDTC 服务<br> - 打开命令提示符 (cmd) <br> - 运行 ```msdtc -uninstall``` 命令 <br> - 运行 ```msdtc -install``` 命令 <br> - 启动 MSDTC 服务<li>启动 Windows 服务“COM + 系统应用程序”。 “COM + 系统应用程序”启动后，从 Azure 门户触发备份作业。</ol> |
|  由于 COM + 错误导致快照操作失败 | 建议措施是，重启 Windows 服务“COM + 系统应用程序”（通过提升的命令提示符 net start COMSysApp）。 如果此问题一再出现，请重启 VM。 如果重启 VM 不起作用，请尝试[删除 VMSnapshot 扩展](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout#cause-3-the-backup-extension-fails-to-update-or-load)并手动触发备份。 |
| 未能冻结一个或多个 VM 装入点来获取文件系统一致快照 | 请执行以下步骤： <ol><li>使用 _“tune2fs”_ 命令检查所有装入设备的文件系统状态。<br> 例如：tune2fs -l /dev/sdb1 \| grep "Filesystem state" <li>使用 _“umount”_ 命令卸载文件系统状态不是干净状态的设备 <li> 使用 _“fsck”_ 命令在这些设备上运行 FileSystemConsistency 检查 <li> 再次装入设备，并尝试备份。</ol> |
| 快照操作失败，因为创建安全网络通信通道失败 | <ol><Li> 在权限提升模式下运行 regedit.exe，打开注册表编辑器。 <li> 标识系统中存在的所有 .NET Framework 版本。 它们位于注册表项“HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft”的层次结构下 <li> 为注册表项中存在的每个 .Net Framework 添加以下键： <br> "SchUseStrongCrypto"=dword:00000001 </ol>|
| 快照操作失败，因为安装 Visual C++ Redistributable for Visual Studio 2012 失败 | 导航到 C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot\agentVersion and install vcredist2012_x64。 确保允许安装此服务的注册表项值设置为正确的值，即注册表项 _HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Msiserver_ 的值设置为 3，而不是 4。 如果仍然遇到安装问题，请通过权限提升的命令提示符运行 _MSIEXEC /UNREGISTER_，并运行 _MSIEXEC /REGISTER_，重启安装服务。  |


## <a name="jobs"></a>作业
| 错误详细信息 | 解决方法 |
| --- | --- |
| 此作业类型不支持取消操作 - 请等待作业完成。 |无 |
| 作业未处于可取消状态 - 请等待作业完成。 <br>或<br> 所选作业未处于可取消状态 - 请等待作业完成。 |作业很可能已大致完成。 请等待作业完成。|
| 不能取消作业，因为作业并未处于进行状态 - 只能对处于进行状态的作业执行取消操作。 请尝试取消正在进行的作业。 |如果存在临时状态，则可能会发生这种情况。 请稍等片刻，并重试取消操作。 |
| 无法取消作业 - 请等待作业完成。 |无 |

## <a name="restore"></a>还原
| 错误详细信息 | 解决方法 |
| --- | --- |
| 发生云内部错误，还原失败 |<ol><li>使用 DNS 设置配置了你正在尝试还原的云服务。 可以检查 <br>$deployment = Get-AzureDeployment -ServiceName "ServiceName" -Slot "Production"     Get-AzureDns -DnsSettings $deployment.DnsSettings<br>如果配置了地址，则表示配置了 DNS 设置。<br> <li>尝试还原的云服务配置了 ReservedIP，且云服务中现有的 VM 处于停止状态。<br>可以使用以下 PowerShell cmdlet 检查云服务是否有保留的 IP：<br>$deployment = Get-AzureDeployment -ServiceName "servicename" -Slot "Production" $dep.ReservedIPName <br><li>正在尝试还原同一云服务中具有以下特殊网络配置的虚拟机。 <br>- 采用负载均衡器配置的虚拟机（内部和外部）<br>- 具有多个保留 IP 的虚拟机<br>- 具有多个 NIC 的虚拟机<br> 请在 UI 中选择新的云服务，或参阅[还原注意事项](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations)，了解具有特殊网络配置的 VM。</ol> |
| 所选 DNS 名称已被使用 - 请指定其他 DNS 名称，然后重试。 |此处的 DNS 名称是指云服务名称（通常以 .cloudapp.net 结尾）。 此名称必须是唯一名称。 如果遇到此错误，则需在还原过程中选择其他 VM 名称。 <br><br> 此错误仅向 Azure 门户用户显示。 通过 PowerShell 进行的还原操作会成功，因为它只还原磁盘，不创建 VM。 如果在磁盘还原操作之后显式创建 VM，则会遇到该错误。 |
| 指定的虚拟网络配置不正确 - 请指定其他虚拟网络配置，然后重试。 |无 |
| 指定的云服务使用的是保留 IP，这不符合要还原的虚拟机的配置 - 请指定其他不使用保留 IP 的云服务，或者选择其他用于还原的恢复点。 |无 |
| 云服务已达到输入终结点的数目限制 - 请重新尝试该操作，指定其他云服务或使用现有终结点。 |无 |
| 恢复服务保管库和目标存储帐户位于两个不同的区域 - 请确保在还原操作中指定的存储帐户与恢复服务保管库位于相同的 Azure 区域。 |无 |
| 不支持为还原操作指定的存储帐户 - 仅支持具有本地冗余或地域冗余复制设置的“基本/标准”存储帐户。 请选择支持的存储帐户 |无 |
| 针对还原操作指定的存储帐户类型不处于在线状态 - 请确保在还原操作中指定的存储帐户处于在线状态 |在 Azure 存储中出现暂时性错误或断电时，可能会发生这种情况。 请选择另一个存储帐户。 |
| 已达到资源组配额限制 - 请从 Azure 门户中删除某些资源组，或者与 Azure 支持部门联系，请求他们提高限制。 |无 |
| 所选子网不存在 - 请选择存在的子网 |无 |
| 备份服务没有权限访问订阅中的资源。 |若要解决此问题，请先使用[选择 VM 还原配置](backup-azure-arm-restore-vms.md#choose-a-vm-restore-configuration)的**还原已备份磁盘**部分中提到的步骤还原磁盘。 之后，使用[基于还原的磁盘创建 VM](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) 中提到的 PowerShell 步骤基于还原的磁盘创建完整的 VM。 |

## <a name="backup-or-restore-taking-time"></a>备份或还原需要一定时间
如果超过 12 小时才看到备份或还原时间超过 6 小时：
* 了解[哪些因素导致备份时间](backup-azure-vms-introduction.md#total-vm-backup-time)和[还原时间如此长](backup-azure-vms-introduction.md#total-restore-time)。
* 务必遵循[备份最佳做法](backup-azure-vms-introduction.md#best-practices)。

## <a name="vm-agent"></a>VM 代理
### <a name="setting-up-the-vm-agent"></a>设置 VM 代理
通常，VM 代理已存在于从 Azure 库创建的 VM 中。 但是，从本地数据中心迁移的虚拟机上未安装 VM 代理。 对于此类 VM，必须显式安装 VM 代理。

对于 Windows VM：

* 下载并安装 [代理 MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。 需要有管理员权限才能完成安装。
* 对于经典虚拟机，请[更新 VM 属性](https://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx)，指明代理已安装。 对于 Resource Manager 虚拟机，无需执行这一步。

对于 Linux VM：

* 从分发存储库安装最新版本的代理。 有关包名称的详细信息，请参阅 [Linux 代理存储库](https://github.com/Azure/WALinuxAgent)。
* 对于经典 VM，请[使用博客条目更新 VM 属性](https://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx)，并确保代理成功安装。 资源管理器虚拟机无需执行此步骤。

### <a name="updating-the-vm-agent"></a>更新 VM 代理
对于 Windows VM：

* 要更新 VM 代理，请重新安装 [VM 代理二进制文件](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。 更新此代理之前，请确保 VM 代理更新期间不存在任何备份操作。

对于 Linux VM：

* 要更新 Linux VM 代理，请按照[更新 Linux VM 代理](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)一文中的说明进行操作。
**更新代理时应始终使用分发存储库**。 请勿从 GitHub 下载代理代码。 如果分发没有可用的最新代理，请联系分发支持部门，了解如何获取最新代理。 还可以在 GitHub 存储库中查看最新的 [Windows Azure Linux 代理](https://github.com/Azure/WALinuxAgent/releases)信息。

### <a name="validating-vm-agent-installation"></a>验证 VM 代理安装

验证 Windows VM 上的 VM 代理版本：

1. 登录 Azure 虚拟机并导航到 C:\WindowsAzure\Packages 文件夹。 应会发现 WaAppAgent.exe 文件已存在。
2. 右键单击该文件，转到“**属性**”，并选择“**详细信息**”选项卡。“产品版本”字段应为 2.6.1198.718 或更高

## <a name="troubleshoot-vm-snapshot-issues"></a>排查 VM 快照问题
VM 备份依赖于向底层存储发出快照命令。 如果无法访问存储或者快照任务执行延迟，则备份作业可能会失败。 以下因素可能会导致快照任务失败。

1. 使用 NSG 阻止对存储进行网络访问<br>
    详细了解如何使用 IP 白名单或代理服务器[启用对存储的网络访问权限](backup-azure-arm-vms-prepare.md#establish-network-connectivity)。
2. 配置了 SQL Server 备份的 VM 可能会导致快照任务延迟 <br>
   默认情况下，VM 备份在 Windows VM 上创建 VSS 完整备份。 运行 SQL Server 且配置有 SQL Server 备份的 VM 可能出现快照延迟。 如果快照延迟导致备份失败，请设置如下注册表项。

   ```
   [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
   "USEVSSCOPYBACKUP"="TRUE"
   ```

3. 由于在 RDP 中关闭了 VM，VM 状态报告不正确。  <br>
   如果使用远程桌面关闭虚拟机，请验证门户中的 VM 状态是否正确。 如果状态不正确，请使用门户 VM 仪表板中的“关闭”选项关闭 VM。
4. 如果四个以上的 VM 共享同一云服务，请为 VM 选择多个不同的备份策略。 错开备份时间，使同时开始的 VM 备份不超过四个。 尝试将策略中的开始时间至少隔开一小时。
5. VM 正在以高 CPU/内存使用率运行。<br>
   如果虚拟机在运行时的内存或 CPU 使用率很高（超过 90%），快照任务将排队、延迟并最终超时。在这种情况下，请尝试进行按需备份。

<br>

## <a name="networking"></a>网络
与所有扩展一样，备份扩展也需要访问公共 Internet 才能工作。 无法访问公共 Internet 可能会出现以下各种情况：

* 扩展安装可能失败
* 备份操作（如磁盘快照）可能失败
* 显示备份操作状态可能失败

[此处](https://blogs.msdn.com/b/mast/archive/2014/06/18/azure-vm-provisioning-stuck-on-quot-installing-extensions-on-virtual-machine-quot.aspx)说明了在哪些情况下需要解析公共 Internet 地址。 用户需要检查 VNET 的 DNS 配置，并确保可以解析 Azure URI。

正确完成名称解析后，还需要提供对 Azure IP 的访问权限。 若要取消阻止对 Azure 基础结构的访问，请执行以下步骤之一：

1. 将 Azure 数据中心 IP 范围加入白名单。
   * 获取要列入允许列表的 [Azure 数据中心 IP](https://www.microsoft.com/download/details.aspx?id=41653) 列表。
   * 使用 [New-NetRoute](https://docs.microsoft.com/powershell/module/nettcpip/new-netroute) cmdlet 取消阻止 IP。 在 Azure VM 上提升权限的 PowerShell 窗口中运行此 cmdlet（以管理员身份运行）。
   * 向 NSG 添加规则（如果已创建规则），以允许访问这些 IP。
2. 为 HTTP 流量创建路径
   * 如果指定了某种网络限制（例如网络安全组），请部署 HTTP 代理服务器来路由流量。 可在[此处](backup-azure-arm-vms-prepare.md#establish-network-connectivity)找到部署 HTTP 代理服务器的步骤。
   * 向 NSG 添加规则（如果已创建规则），以允许从 HTTP 代理访问 INTERNET。

> [!NOTE]
> 必须在来宾内启用 DHCP，才能正常进行 IaaS VM 备份。 如果需要静态专用 IP，则应通过 Azure 门户或 PowerShell 进行配置，同时确保启用 VM 内的 DHCP 选项。
> 有关如何通过 PowerShell 设置静态 IP 的详细信息，请参阅[经典 VM](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm) 和[资源管理器 VM](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface)。
>
>
