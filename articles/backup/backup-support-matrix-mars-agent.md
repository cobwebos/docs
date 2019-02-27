---
title: 使用 Azure 备份对运行 Microsoft Azure 恢复服务 (MARS) 代理的计算机进行备份的支持矩阵
description: 本文汇总了备份运行 Microsoft Azure 恢复服务 (MARS) 代理的计算机时的 Azure 备份支持。
services: backup
author: rayne-wiselman
ms.service: backup
ms.date: 02/17/2019
ms.topic: conceptual
ms.author: raynew
manager: carmonm
ms.openlocfilehash: 9d0f751e8d0bc0275cc5fd2c47aaba7a6058931c
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430931"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>使用 Microsoft Azure 恢复服务 (MARS) 代理进行备份的支持矩阵

可以使用 [Azure 备份服务](backup-overview.md)备份本地计算机和应用以及 Azure VM。 本文汇总了备份使用 Microsoft Azure 恢复服务 (MARS) 代理的计算机时的支持设置和限制。

## <a name="about-the-mars-agent"></a>关于 MARS 代理

Azure 备份使用 MARS 代理将本地计算机和 Azure VM 中的数据备份到 Azure 中的恢复服务保管库。 MARS 代理的用法如下：
- 在本地 Windows 计算机上运行该代理，使这些计算机能够直接备份到 Azure 中的备份恢复服务保管库。
- 在 Windows Azure VM 上运行该代理，使这些 VM 能够直接备份到保管库。
- 在 Microsoft Azure 备份服务器 (MABS) 或 System Center Data Protection Manager (DPM) 服务器上运行该代理。 在此方案中，计算机和工作负荷将备份到 MABS/DPM，然后 MABS/DPM 将通过 MARS 代理备份到 Azure 中的保管库。 

可备份的内容取决于该代理的安装位置。

- [详细了解](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-machinesazure-vm-filesfolders)使用 MARS 代理的备份体系结构。
- [详细了解]() MABS/DPM [备份体系结构](backup-architecture.md#architecture-back-up-to-dpmmabs)和[要求](backup-support-matrix-mabs-dpm.md)。


## <a name="supported-installation"></a>支持的安装

**安装** | **详细信息**
--- | ---
**下载最新的 MARS 代理** | 可以从保管库下载最新版本的代理，或者[直接下载它](https://aka.ms/azurebackup_agent)。
**直接在计算机上安装** | 可以直接在本地 Windows 服务器上安装 MARS 代理，或者在运行任意[受支持操作系统]()的 Windows Azure VM 上安装它。
**在备份服务器上安装** | 将 DPM 或 MABS 设置为备份到 Azure 时，可以在服务器上下载并安装 MARS 代理。 可根据备份服务器支持矩阵中的[支持的操作系统](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems)安装该代理。

> [!NOTE]
> 默认情况下，启用了备份的 Azure VM 上安装了 Azure 备份扩展。 此扩展备份整个 VM。 若要备份特定的文件夹和文件而不是整个 VM，可以在 Azure VM 上同时安装并运行 MARS 代理和该扩展。
> 在 Azure VM 上运行 MARS 代理时，该代理会备份 VM 上的临时存储中的文件/文件夹。 如果从临时存储中删除了文件/文件夹，或者删除了临时存储，则备份将会失败。


## <a name="cache-folder-support"></a>缓存文件夹支持

使用 MARS 代理备份时，该代理将创建数据的快照并将其存储在本地缓存文件夹中，然后将其发送到 Azure。 缓存（暂存）文件夹存在许多要求。

**缓存** | **详细信息**
--- | ---
**缓存大小** |  缓存文件夹中的可用空间大小应至少为备份数据总大小的 5-10%。 
**缓存位置** | 缓存文件夹必须位于要备份的计算机本地，并且必须联机。<br/><br/> 缓存文件夹不应位于网络共享、可移动媒体或脱机卷上。 
**缓存文件夹** | 缓存文件夹应已加密，位于已删除重复数据的卷中，或者位于压缩/稀疏/重分析点类型的文件夹中
**修改缓存位置** | 可以通过停止备份引擎 (net stop bengine)，并将缓存文件夹复制到新驱动器（确保该驱动器有足够的空间）来更改缓存位置。 然后，将 HKLM\SOFTWARE\Microsoft\Windows Azure Backup (Config/ScratchLocation and Config/CloudBackupProvider/ScratchLocation) 下的两个注册表项更新为新位置，并重启引擎。

## <a name="networking-and-access-support"></a>网络和访问支持

### <a name="url-access"></a>URL 访问

MARS 代理需要以下 URL 的访问权限：

- http://www.msftncsi.com/ncsi.txt
- *.Microsoft.com
- *.WindowsAzure.com
- *.microsoftonline.com
- *.windows.net

### <a name="throttling-support"></a>限制支持

**功能** | **详细信息**
--- | ---
带宽控制 | 支持<br/><br/> 使用 MARS 代理中的“更改属性”来调整带宽。
网络限制 | 不适用于运行 Windows Server 2008 R2、Windows Server 2008 SP2 或 Windows 7 的备份计算机。

## <a name="support-for-direct-backups"></a>直接备份支持

下表汇总了使用 MARS 代理可将本地计算机和 Azure VM 上运行的哪些操作系统直接备份到 Azure。

- 所有操作系统均为 64 位。
- 所有操作系统应该运行最新的服务包和更新。
- 有关使用 MARS 代理可以备份哪些 DPM 和 MABS 服务器的详细信息，请查看[此表](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems)。

**操作系统** | **文件/文件夹** | **系统状态**
--- | --- | ---
Windows 10（Enterprise、Pro、Home） | 是 | 否
Windows 8.1（Enterprise、Pro）| 是 |否
Windows 8（Enterprise、Pro） | 是 | 否
Windows 7（Ultimate、Enterprise、Pro、Home Premium/Basic、Starter） | 是 | 否
Windows Server 2016（Standard、Datacenter、Essentials） | 是 | 是
Windows Server 2012 R2（Standard、Datacenter、Foundation、Essentials） | 是 | 是
Windows Server 2012（Standard、Datacenter、Foundation） | 是 | 是
Windows Server 2008 R2（Standard、Enterprise、Datacenter、Foundation） | 是 | 是
Windows Server 2008 SP2（Standard、Datacenter、Foundation） | 是 | 否
Windows Storage Server 2016/2012 R2/2012（Standard、Workgroup） | 是 | 否


## <a name="backup-limits"></a>备份限制

Azure 备份对可备份的文件/文件夹数据源大小施加了限制。 在单个卷中选择进行备份的项大小不能超过表中汇总的大小。

**操作系统** | **大小限制**
--- | ---
Windows Server 2012 或更高版本 |  54,400 GB
Windows Server 2008 R2 SP1 |    1700 GB
Windows Server 2008 SP2 | 1700 GB
Windows 8 或更高版本  | 54,400 GB
Windows 7   | 1700 GB


## <a name="supported-file-types-for-backup"></a>支持备份的文件类型

类型 | **支持** 
--- | --- 
加密   | 是 
压缩 | 是
稀疏 | 是 
压缩和稀疏 | 是
硬链接  | 不支持<br/><br/> 已跳过
重分析点   | 不支持<br/><br/> 已跳过
加密和稀疏 |  不支持<br/><br/> 已跳过
压缩流   | 不支持<br/><br/> 已跳过
稀疏流   | 不支持<br/><br/> 已跳过
一个驱动器（同步的文件是稀疏流）    | 不支持 

## <a name="supported-drivesvolumes-for-backup"></a>支持备份的驱动器/卷

**驱动器/卷** | **支持** | **详细信息**
--- | --- | ---
只读卷   | 不支持 | 卷必须可写才能正常使用 VSS。
脱机卷 | 不支持 |   卷必须联机才能正常使用 VSS。
网络共享   | 不支持 |   卷必须位于服务器本地才能进行备份。
Bitlocker 保护的卷 | 不支持 |   必须先解锁卷才能正常备份。
文件系统标识  | 不支持 |   仅限 NTFS。
可移动媒体 | 不支持 |   所有备份项源的状态必须为固定。
已删除重复数据的驱动器 | 支持。<br/><br/> Azure 备份将删除了重复项的数据转换为正常数据。 它可以优化数据、加密数据，存储数据并将其发送到保管库。

## <a name="support-for-initial-offline-backup"></a>初始脱机备份支持

Azure 备份支持“脱机种子设定”，以使用磁盘将初始备份数据传输到 Azure。 如果初始备份的大小可能会达到 TB 量级，则此功能很有帮助。 支持以下脱机备份：

- 直接备份运行 MARS 代理的本地计算机上的文件和文件夹。
- 备份 DPM 服务器或 MABS 中的工作负荷和文件。
- 脱机备份不可用于系统状态文件。


## <a name="support-for-restore"></a>还原支持

- 使用 Azure 备份的新[即时还原](/backup-instant-restore-capability.md)版本，可以先还原数据，然后再将数据复制到保管库。<br/><br/> 若要使用此功能，所要备份的计算机必须运行 .NET Framework 4.5.2 或更高版本。
- 无法将备份还原到运行较低 操作系统版本的目标计算机。 例如，在 Windows 8 或更高版本上，可以还原在 Windows 7 计算机中创建的备份。 但是，在 Windows 8 计算机中创建的备份无法还原到 Windows 7 计算机。


## <a name="next-steps"></a>后续步骤
- [详细了解](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-machinesazure-vm-filesfolders)使用 MARS 代理的备份体系结构。
- [了解](backup-support-matrix-mabs-dpm.md)在 Microsoft Azure 备份服务器 (MABS) 或 System Center DPM 上运行 MARS 代理时支持哪些操作。


