---
title: Azure VM 备份的 Azure 备份支持矩阵
description: 提供有关在使用 Azure 备份服务备份 Azure VM 时的支持设置和限制摘要。
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: dacurwin
ms.openlocfilehash: 3f89b5aa427689d5f3da28726897d7a772bf0781
ms.sourcegitcommit: 6eecb9a71f8d69851bc962e2751971fccf29557f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72533156"
---
# <a name="support-matrix-for-azure-vm-backup"></a>Azure VM 备份的支持矩阵

可以使用[Azure 备份服务](backup-overview.md)来备份本地计算机和工作负荷，以及 azure 虚拟机（vm）。 本文汇总了通过 Azure 备份备份 Azure Vm 时的支持设置和限制。

其他支持矩阵：

- Azure 备份的[通用支持矩阵](backup-support-matrix.md)
- Azure 备份服务器/System Center Data Protection Manager （DPM）备份的[支持矩阵](backup-support-matrix-mabs-dpm.md)
- Microsoft Azure 恢复服务（MARS）代理进行备份的[支持矩阵](backup-support-matrix-mars-agent.md)

## <a name="supported-scenarios"></a>支持的方案

下面描述了使用 Azure 备份服务备份和还原 Azure VM 的方式。

**方案** | **备份** | **代理** |**Restore**
--- | --- | --- | ---
直接备份 Azure VM  | 备份整个 VM。  | 无需在 Azure VM 上安装代理。 Azure 备份将安装虚拟机上运行的[AZURE vm 代理](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)，并使用该扩展。 | 按如下所述进行还原：<br/><br/> - **创建基本的 VM**。 如果 VM 没有任何特殊配置（例如多个 IP 地址），这会很有用。<br/><br/> - **还原 VM 磁盘**。 还原磁盘。 然后，将其附加到现有 VM，或使用 PowerShell 从磁盘创建新 VM。<br/><br/> - **替换 VM 磁盘**。 如果 VM 存在并使用托管磁盘（未加密），则你可以还原磁盘并使用它来替换 VM 上的现有磁盘。<br/><br/> - **还原特定的文件/文件夹**。 可以从 VM 而不是整个 VM 还原文件/文件夹。
直接备份 Azure Vm （仅限 Windows）  | 备份特定文件/文件夹/卷。 | 安装[Azure 恢复服务代理](backup-azure-file-folder-backup-faq.md)。<br/><br/> 可将 MARS 代理与适用于 Azure VM 代理的备份扩展一同运行，以便在文件/文件夹级别备份 VM。 | 还原特定的文件夹/文件。
将 Azure VM 备份到备份服务器  | 备份文件/文件夹/卷;系统状态/裸机文件;应用数据到 System Center DPM 或 Microsoft Azure 备份 Server （MABS）。<br/><br/> DPM/MABS 将备份到备份保管库。 | 请在 VM 上安装 DPM/MABS 保护代理。 在 DPM/MABS 上安装 MARS 代理。| 还原文件/文件夹/卷；系统状态/裸机文件；应用数据。

了解有关[使用备份服务器备份的](backup-architecture.md#architecture-back-up-to-dpmmabs)详细信息以及[支持要求](backup-support-matrix-mabs-dpm.md)。

## <a name="supported-backup-actions"></a>支持的备份操作

**Action** | **支持**
--- | ---
创建 Windows Azure VM 时启用备份 | 支持： <br/><br/> -Windows Server 2019 （Datacenter/Datacenter Core/Standard） <br/><br/> -Windows Server 2016 （Datacenter/Datacenter Core/Standard） <br/><br/> -Windows Server 2012 R2 （Datacenter/Standard） <br/><br/> -Windows Server 2008 R2 （RTM 和 SP1 Standard）
创建 Linux VM 时启用备份 | 支持：<br/><br/> -Ubuntu Server：18.04、17.10、17.04、16.04 （LTS）、14.04 （LTS）<br/><br/> -Red Hat： RHEL 6.7、6.8、6.9、7.2、7.3、7。4<br/><br/> -SUSE Linux Enterprise Server： 11 SP4、12 SP2、12 SP3、15 <br/><br/> -Debian：8，9<br/><br/> -CentOS：6.9、7。3<br/><br/> -Oracle Linux：6.7、6.8、6.9、7.2、7。3
备份关闭/脱机 VM 的 VM | 。<br/><br/> 仅创建崩溃一致性快照，不会创建应用一致性快照。
迁移到托管磁盘后备份磁盘 | 。<br/><br/> 备份将继续工作。 无需执行任何操作。
启用资源组锁定后备份托管磁盘 | 不支持。<br/><br/> Azure 备份无法删除较早的还原点，当达到还原点的最大限制时，备份将开始失败。
修改 VM 的备份策略 | 。<br/><br/> 将使用新策略中的 "计划" 和 "保留" 设置来备份虚拟机。 如果保留期设置已延长，则会标记并保留现有的恢复点。 如果减少了，现有恢复点将在下一个清除作业中被修剪，并最终将被删除。
取消备份作业| 在快照过程中受支持。<br/><br/> 快照正在传输到保管库时不受支持。
将 VM 备份到其他区域或订阅 |不支持。
每日备份（通过 Azure VM 扩展） | 每日进行一次计划的备份。<br/><br/> 每天最多可以进行四个按需备份。
每日备份（通过 MARS 代理） | 每日进行三次计划的备份。
每日备份（通过 DPM/MABS） | 每日进行两次计划的备份。
每月/每年备份| 使用 Azure VM 扩展备份时不受支持。 仅支持每日和每周备份。<br/><br/> 可将策略设置为按每月/每年保留期保留每日/每周备份。
自动时钟调整 | 不支持。<br/><br/> 备份 VM 时，Azure 备份不会自动调整夏令时更改。<br/><br/>  请根据需要手动修改策略。
[混合备份的安全功能](https://docs.microsoft.com/azure/backup/backup-azure-security-feature) |不支持禁用安全功能。
备份其计算机时间已更改的 VM | 不支持。<br/><br/> 如果在为 VM 启用备份后将计算机时间更改为将来的日期时间，则为;但是，即使已恢复时间更改，也不能保证成功备份。  

## <a name="operating-system-support-windows"></a>操作系统支持 (Windows)

下表汇总了备份 Windows Azure Vm 时支持的操作系统。

**方案** | **OS 支持**
--- | ---
使用 Azure VM 代理扩展进行备份 | Windows 客户端：不支持<br/><br/>-Windows Server 2019 （Datacenter/Datacenter Core/Standard） <br/><br/> -Windows Server 2016 （Datacenter/Datacenter Core/Standard） <br/><br/> -Windows Server 2012 R2 （Datacenter/Standard） <br/><br/> -Windows Server 2008 R2 （RTM 和 SP1 Standard）
使用 MARS 代理进行备份 | [支持](backup-support-matrix-mars-agent.md#support-for-direct-backups)的操作系统。
用 DPM/MABS 备份 | 使用 [MABS](backup-mabs-protection-matrix.md) 和 [DPM](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807) 备份时支持的操作系统。

## <a name="support-for-linux-backup"></a>Linux 备份支持

下面是在需要备份 Linux 计算机的情况下的支持项目。

**Action** | **支持**
--- | ---
使用 Linux Azure VM 代理备份 Linux Azure VM | 文件一致性备份。<br/><br/> 使用[自定义脚本](backup-azure-linux-app-consistent.md)的应用一致性备份。<br/><br/> 在还原过程中，你可以创建新的 VM、还原磁盘并使用它来创建 VM，或者还原磁盘并使用它来替换现有 VM 上的磁盘。 还可以还原单个文件和文件夹。
使用 MARS 代理备份 Linux Azure VM | 不支持。<br/><br/> MARS 代理只能安装在 Windows 计算机上。
使用 DPM/MABS 备份 Linux Azure VM | 不支持。

## <a name="operating-system-support-linux"></a>操作系统支持 (Linux)

对于 Azure VM Linux 备份，Azure 备份支持 [Azure 认可的 Linux 分发版](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)列表。 注意以下事项：

- Azure 备份不支持 Core OS Linux。
- Azure 备份不支持 32 位操作系统。
- 只要虚拟机上提供适用于[linux 的 AZURE VM 代理](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux)，以及支持 Python，就可以使用其他自带 linux 分发版。
- 如果未安装 Python 版本2.7，Azure 备份不支持代理配置的 Linux VM。

## <a name="backup-frequency-and-retention"></a>备份频率和保留期

**设置** | **限制**
--- | ---
每个受保护实例的最大恢复点数（计算机/工作负荷） | 9999。
恢复点的最长过期时间 | 无限制。
备份到保管库时的最高备份频率（Azure VM 扩展） | 一天一次。
备份到保管库时的最高备份频率（MARS 代理） | 每日三次备份。
备份到 DPM/MABS 时的最高备份频率 | 对于 SQL Server 每15分钟一次。<br/><br/> 对于其他工作负荷，每小时一次。
恢复点保留期 | 每日、每周、每月和每年。
最长数据保留期 | 取决于备份频率。
DPM/MABS 磁盘上的恢复点数 | 64适用于文件服务器，应用程序服务器为448。<br/><br/> 对于本地 DPM 来说，磁带恢复点没有限制。

## <a name="supported-restore-methods"></a>支持的还原方法

**还原方法** | **详细信息**
--- | ---
创建新 VM | 可以在还原过程中创建 VM。 <br/><br/> 此选项可以启动并运行一个基本的 VM。 可以指定 VM 名称、资源组、虚拟网络、子网和存储。  
还原磁盘 | 可以还原磁盘并使用它来创建 VM。<br/><br/> 如果选择此选项，Azure 备份会将保管库中的数据复制到所选的存储帐户。 还原作业生成一个模板。 你可以下载此模板，使用它来指定自定义 VM 设置并创建 VM。<br/><br/> 与前面用于创建 VM 的选项相比，使用此选项可以指定更多的设置。<br/><br/>
替换现有磁盘 | 可以还原某个磁盘，然后使用还原的磁盘来替换当前 VM 上的磁盘。
还原文件 | 可以从所选的恢复点恢复文件。 下载一个脚本，用于从恢复点装载 VM 磁盘。 然后，浏览磁盘卷以查找要恢复的文件/文件夹，完成后将其卸载。

## <a name="support-for-file-level-restore"></a>文件级还原支持

**Restore** | **支持**
--- | ---
跨操作系统还原文件 | 可以在与备份的 VM 使用相同（或兼容）OS 的任何计算机上还原文件。 请参阅[兼容的 OS 表](backup-azure-restore-files-from-vm.md#system-requirements)。
还原经典 VM 上的文件 | 不支持。
还原已加密 VM 中的文件 | 不支持。
还原网络受限的存储帐户中的文件 | 不支持。
使用 Windows 存储空间还原 VM 上的文件 | 不支持在同一 VM 上还原。<br/><br/> 应在兼容的 VM 上还原文件。
使用 LVM/RAID 阵列还原 Linux VM 上的文件 | 不支持在同一 VM 上还原。<br/><br/> 在兼容的 VM 上还原。
使用特殊网络设置还原文件 | 不支持在同一 VM 上还原。 <br/><br/> 在兼容的 VM 上还原。

## <a name="support-for-vm-management"></a>VM 管理支持

下表汇总了 VM 管理任务期间的备份支持，例如添加或更换 VM 磁盘。

**Restore** | **支持**
--- | ---
跨订阅/区域/局部区域还原。 | 不支持。
还原到现有 VM | 使用“替换磁盘”选项。
在为存储帐户启用了 Azure 存储服务加密 (SSE) 的情况下还原磁盘 | 不支持。<br/><br/> 还原到未启用 SSE 的帐户。
还原到混合存储帐户 |不支持。<br/><br/> 根据存储帐户类型，所有已还原的磁盘将是高级或标准类型，而不是混合类型。
将 VM 直接还原到可用性集 | 对于托管磁盘，可以还原磁盘并使用模板中的 "可用性集" 选项。<br/><br/> 不支持非托管磁盘。 对于非托管磁盘，可以还原磁盘，然后在可用性集中创建 VM。
升级到托管 VM 后还原非托管 Vm 的备份| 。<br/><br/> 可以还原磁盘，然后创建托管 VM。
在将 VM 迁移到托管磁盘之前将 VM 还原到还原点 | 。<br/><br/> 还原到非托管磁盘（默认设置），将已还原的磁盘转换为托管磁盘，然后使用托管磁盘创建 VM。
还原已删除的 VM。 | 。<br/><br/> 可以从恢复点还原 VM。
通过门户还原属于多 DC 配置的域控制器 (DC) VM | 如果你使用 PowerShell 还原磁盘并创建 VM，则支持此方法。
还原不同虚拟网络中的 VM |。<br/><br/> 虚拟网络必须位于同一订阅和区域中。

## <a name="vm-compute-support"></a>VM 计算支持

**计算** | **支持**
--- | ---
VM 大小 |至少有 2 个 CPU 核心和 1-GB RAM 的任意 Azure VM 大小。<br/><br/> [了解详细信息。](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)
备份[可用性集](https://docs.microsoft.com/azure/virtual-machine-scale-sets/availability#availability-sets)中的 VM | 。<br/><br/> 使用 "快速创建 VM" 选项无法还原可用集中的 VM。 相反，当你还原 VM 时，请还原磁盘并使用它来部署 VM，或者还原磁盘并使用它来替换现有磁盘。
备份部署了[混合使用权益（HUB）](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing)的 vm | 。
备份[规模集中](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)部署的 vm |不支持。
备份从[Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?filters=virtual-machine-images)部署的 vm<br/><br/> （由 Microsoft、第三方发布） |。<br/><br/> VM 必须运行受支持的操作系统。<br/><br/> 恢复 VM 上的文件时，可以仅还原到兼容的 OS（不是早期版本或更高版本的 OS）。 我们不会将作为 Vm 支持的 Azure Marketplace Vm 还原为 Vm，因为这些 Vm 需要购买信息，而仅作为磁盘。
备份从自定义映像部署的 Vm （第三方） |。<br/><br/> VM 必须运行受支持的操作系统。<br/><br/> 恢复 VM 上的文件时，可以仅还原到兼容的 OS（不是早期版本或更高版本的 OS）。
备份迁移到 Azure 的 Vm| 。<br/><br/> 若要备份 VM，必须在迁移的计算机上安装 VM 代理。
备份多 VM 一致性 | Azure 备份不提供跨多个 Vm 的数据和应用程序一致性。
采用[诊断设置](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview)进行备份  | 不支持. <br/><br/> 如果使用["新建" 选项触发](backup-azure-arm-restore-vms.md#create-a-vm)了包含诊断设置的 Azure VM 还原，则还原将失败。
还原区域固定 Vm | 支持（适用于在2019年1月和[可用性区域](https://azure.microsoft.com/global-infrastructure/availability-zones/)可用的位置）的 VM。<br/><br/>目前支持还原到 Vm 中固定的同一区域。 但是，如果该区域不可用，则还原将失败。
Gen2 Vm | 受支持 <br> Azure 备份支持[Gen2 vm](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/)的备份和还原。 从恢复点还原这些 Vm 时，它们将还原为[Gen2 vm](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/)。

## <a name="vm-storage-support"></a>VM 存储支持

组件 | **支持**
--- | ---
Azure VM 数据磁盘 | 备份包含 16 个或更少数据磁盘的 VM。
数据磁盘大小 | 对于 VM 中的所有磁盘，单独的磁盘大小可以高达 32 TB，最大为 256 TB。
存储类型 | 标准 HDD、标准 SSD 高级 SSD。
托管磁盘 | 。
加密的磁盘 | 。<br/><br/> 可备份通过 Azure 磁盘加密启用的 azure Vm （有或没有 Azure AD 应用）。<br/><br/> 无法在文件/文件夹级别恢复已加密的 VM。 必须恢复整个 VM。<br/><br/> 可以在已受 Azure 备份保护的 VM 上启用加密。
已启用写入加速器的磁盘 | 不支持。<br/><br/> Azure 备份会自动将磁盘排除在备份期间启用写入加速器。 由于未对其进行备份，你将无法从 VM 的恢复点还原这些磁盘。
备份 & 还原已删除重复的 Vm/磁盘 | Azure 备份不支持重复数据删除。 有关详细信息，请参阅此[文](https://docs.microsoft.com/azure/backup/backup-support-matrix#disk-deduplication-support) <br/> <br/>  -Azure Backup 不删除重复在恢复服务保管库中的 Vm 之间 <br/> <br/>  -如果在还原期间有 Vm 处于重复数据删除状态，则无法还原文件，因为保管库不理解格式
将磁盘添加到受保护的 VM | 。
调整受保护 VM 上的磁盘大小 | 。
共享存储| 不建议使用群集共享卷（CSV）或横向扩展文件服务器备份 Vm。 CSV 编写器在备份过程中可能会失败。 还原时，包含 CSV 卷的磁盘可能无法启动。

## <a name="vm-network-support"></a>VM 网络支持

组件 | **支持**
--- | ---
网络接口数（Nic） | 特定 Azure VM 大小支持最大数量的 NIC。<br/><br/> NIC 是在还原过程中创建 VM 时创建的。<br/><br/> 已还原 VM 上的 NIC 数目与启用保护时 VM 上的 NIC 数目相同。 启用保护后删除 Nic 不会影响计数。
内部/外部负载均衡器 |。 <br/><br/> [详细了解](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations)如何还原使用特殊网络设置的 VM。
多个保留 IP 地址 |。 <br/><br/> [详细了解](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations)如何还原使用特殊网络设置的 VM。
具有多个网络适配器的 VM| 。 <br/><br/> [详细了解](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations)如何还原使用特殊网络设置的 VM。
具有公共 IP 地址的 VM| 。<br/><br/> 在完成还原后，将现有的公共 IP 地址与 NIC 关联，或创建地址并将其与 NIC 关联。
NIC/子网上的网络安全组 (NSG)。 |。
静态 IP 地址 | 不支持。<br/><br/> 将为从还原点创建的新 VM 分配动态 IP 地址。<br/><br/> 对于经典 Vm，不能使用保留的 IP 地址和未定义的终结点来备份虚拟机。
动态 IP 地址 |。<br/><br/> 如果源 VM 上的 NIC 使用动态 IP 寻址，则默认情况下，已还原的 VM 上的 NIC 也将使用它。
Azure Traffic Manager| 。<br/><br/>如果备份的 VM 处于流量管理器中，请手动将还原的 VM 添加到同一个流量管理器实例。
Azure DNS |。
自定义 DNS |。
通过 HTTP 代理建立出站连接 | 。<br/><br/> 不支持经过身份验证的代理。
虚拟网络服务终结点| 。<br/><br/> 防火墙和虚拟网络存储帐户设置应允许来自所有网络的访问。

## <a name="vm-security-and-encryption-support"></a>VM 安全性和加密支持

Azure 备份支持针对传输中数据和静态数据的加密：

发往 Azure 的网络流量：

- 使用高级加密标准256对从服务器到恢复服务保管库的备份通信进行加密。
- 备份数据通过安全 HTTPS 链接进行发送。
- 备份数据以加密格式存储在恢复服务保管库中。
- 只有你有解锁此数据的通行短语。 Microsoft 无法解密任何恢复点的备份数据。

  > [!WARNING]
  > 设置保管库后，只有你才能访问加密密钥。 Microsoft 不保留副本，且没有访问该密钥的权限。 如果客户丢失了密钥，Microsoft 无法恢复备份数据。

数据安全：

- 备份 Azure Vm 时，需要在虚拟机*中*设置加密。
- Azure 备份支持 Azure 磁盘加密，在 Linux 虚拟机上使用 Windows 虚拟机和美国**dm-crypt**上的 BitLocker。
- 在后端，Azure 备份使用 [Azure 存储服务加密](../storage/common/storage-service-encryption.md)来保护静态数据。

**计算机** | **传输中** | **静态**
--- | --- | ---
没有 DPM/MABS 的本地 Windows 计算机 | ![是][green] | ![是][green]
Azure VM | ![是][green] | ![是][green]
本地计算机/装有 DPM 的 Azure VM | ![是][green] | ![是][green]
本地计算机/装有 MABS 的 Azure VM | ![是][green] | ![是][green]

## <a name="vm-compression-support"></a>VM 压缩支持

备份支持对备份流量进行压缩，如下表所示。 注意以下事项：

- 对于 Azure Vm，VM 扩展会通过存储网络直接读取 Azure 存储帐户中的数据。 不需要压缩此流量。
- 如果你使用的是 DPM 或 MABS，则可以通过在将数据备份到 DPM/MABS 之前压缩数据来节省带宽。

**计算机** | **压缩到 MABS/DPM (TCP)** | **压缩到保管库（HTTPS）**
--- | --- | ---
没有 DPM/MABS 的本地 Windows 计算机 | NA | ![是][green]
Azure VM | NA | NA
本地计算机/装有 DPM 的 Azure VM | ![是][green] | ![是][green]
本地计算机/装有 MABS 的 Azure VM | ![是][green] | ![是][green]

## <a name="next-steps"></a>后续步骤

- [备份 Azure vm](backup-azure-arm-vms-prepare.md)。
- 不使用备份服务器[直接备份 Windows 计算机](tutorial-backup-windows-server-to-azure.md)。
- [设置 MABS](backup-azure-microsoft-azure-backup.md) 以备份到 Azure，然后将工作负荷备份到 MABS。
- [设置 DPM](backup-azure-dpm-introduction.md) 以备份到 Azure，然后将工作负荷备份到 DPM。

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
