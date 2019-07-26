---
title: 使用 Azure 备份对运行 Microsoft Azure 恢复服务 (MARS) 代理的计算机进行备份的支持矩阵
description: 本文汇总了备份运行 Microsoft Azure 恢复服务 (MARS) 代理的计算机时的 Azure 备份支持。
author: rayne-wiselman
ms.service: backup
ms.date: 02/17/2019
ms.topic: conceptual
ms.author: raynew
manager: carmonm
ms.openlocfilehash: d9f3c9b94a093df539a6ca4660383837becf1709
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2019
ms.locfileid: "68464851"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>使用 Microsoft Azure 恢复服务 (MARS) 代理进行备份的支持矩阵

可以使用 [Azure 备份服务](backup-overview.md)备份本地计算机和应用，以及 Azure 虚拟机 (VM)。 本文汇总了使用 Microsoft Azure 恢复服务 (MARS) 代理备份计算机时的支持设置和限制。

## <a name="the-mars-agent"></a>MARS 代理

Azure 备份使用 MARS 代理将本地计算机和 Azure VM 中的数据备份到 Azure 中的恢复服务保管库。 MARS 代理可以：
- 在本地 Windows 计算机上运行，使这些计算机能够直接备份到 Azure 中的备份恢复服务保管库。
- 在 Windows VM 上运行，使这些 VM 能够直接备份到保管库。
- 在 Microsoft Azure 备份服务器 (MABS) 或 System Center Data Protection Manager (DPM) 服务器上运行。 在此方案中，计算机和工作负荷将备份到 MABS 或 DPM 服务器。 然后，MARS 代理将此服务器备份到 Azure 中的保管库。

备份选项取决于该代理的安装位置。 有关详细信息，请参阅[使用 MARS 代理的 Azure 备份体系结构](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)。 有关 MABS 和 DPM 备份体系结构的信息，请参阅[备份到 DPM 或 MABS](backup-architecture.md#architecture-back-up-to-dpmmabs)。 另请参阅备份体系结构的[要求](backup-support-matrix-mabs-dpm.md)。

**安装** | **详细信息**
--- | ---
下载最新的 MARS 代理 | 可以从保管库下载最新版本的代理，或者[直接下载它](https://aka.ms/azurebackup_agent)。
直接在计算机上安装 | 可以直接在本地 Windows 服务器上安装 MARS 代理，或者在运行任意[受支持操作系统](https://docs.microsoft.com/azure/backup/backup-support-matrix-mabs-dpm#supported-mabs-and-dpm-operating-systems)的 Windows VM 上安装它。
在备份服务器上安装 | 将 DPM 或 MABS 设置为备份到 Azure 时，可以在服务器上下载并安装 MARS 代理。 可在备份服务器支持矩阵中的[受支持操作系统](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems)上安装该代理。

> [!NOTE]
> 默认情况下，启用了备份的 Azure VM 上安装了 Azure 备份扩展。 此扩展备份整个 VM。 若要备份特定的文件夹和文件而不是整个 VM，可以在 Azure VM 上同时安装并运行 MARS 代理和该扩展。
> 在 Azure VM 上运行 MARS 代理时，该代理会备份 VM 上的临时存储中的文件或文件夹。 如果从临时存储中删除了文件或文件夹，或者删除了临时存储，则备份将会失败。


## <a name="cache-folder-support"></a>缓存文件夹支持

使用 MARS 代理备份数据时，该代理将创建数据的快照并将其存储在本地缓存文件夹中，然后将数据发送到 Azure。 缓存（暂存）文件夹有几项要求：

**缓存** | **详细信息**
--- | ---
Size |  缓存文件夹中的可用空间应至少为备份数据总大小的 5% 到 10%。
Location | 缓存文件夹必须存储在要备份的计算机本地，并且该计算机必须联机。 缓存文件夹不应位于网络共享、可移动媒体或脱机卷上。
文件夹 | 缓存文件夹应已加密，位于已删除重复数据的卷中，或者位于压缩、稀疏或重分析点类型的文件夹中。
位置更改 | 可以通过停止备份引擎 (`net stop bengine`)，并将缓存文件夹复制到新驱动器来更改缓存位置。 （确保新驱动器有足够的空间。）然后，将 **HKLM\SOFTWARE\Microsoft\Windows Azure Backup** 下的两个注册表项（**Config/ScratchLocation** 和 **Config/CloudBackupProvider/ScratchLocation**）更新为新位置，并重启引擎。

## <a name="networking-and-access-support"></a>网络和访问支持

### <a name="url-access"></a>URL 访问

MARS 代理需要以下 URL 的访问权限：

- http://www.msftncsi.com/ncsi.txt
- *.Microsoft.com
- *.WindowsAzure.com
- *.MicrosoftOnline.com
- *.Windows.net

### <a name="throttling-support"></a>限制支持

**功能** | **详细信息**
--- | ---
带宽控制 | 。 在 MARS 代理中，使用“更改属性”来调整带宽。
网络限制 | 不适用于运行 Windows Server 2008 R2、Windows Server 2008 SP2 或 Windows 7 的备份计算机。

## <a name="support-for-direct-backups"></a>直接备份支持

在本地计算机和 Azure VM 上运行的某些操作系统中，可以使用 MARS 代理直接备份到 Azure。 所有操作系统必须是 64 位，并且应该运行最新的服务包和更新。 下表汇总了这些操作系统：

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

有关详细信息，请参阅[支持的 MABS 和 DPM 操作系统](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems)。

## <a name="backup-limits"></a>备份限制

Azure 备份会限制可备份的文件或文件夹数据源大小。 在单个卷中备份的项大小不能超过下表中汇总的大小：

**操作系统** | **大小限制**
--- | ---
Windows Server 2012 或更高版本 |  54,400 GB
Windows Server 2008 R2 SP1 |    1,700 GB
Windows Server 2008 SP2 | 1,700 GB
Windows 8 或更高版本  | 54,400 GB
Windows 7   | 1,700 GB


## <a name="supported-file-types-for-backup"></a>支持备份的文件类型

类型 | **支持**
--- | ---
已加密   | 。
压缩 | 。
稀疏 | 。
压缩和稀疏 | 。
硬链接  | 不受支持。 跳过。
重分析点   | 不受支持。 跳过。
加密和稀疏 |  不受支持。 跳过。
压缩流   | 不受支持。 跳过。
稀疏流   | 不受支持。 跳过。
OneDrive（同步的文件是稀疏流）  | 不受支持。

## <a name="supported-drives-or-volumes-for-backup"></a>支持备份的驱动器或卷

**驱动器/卷** | **支持** | **详细信息**
--- | --- | ---
只读卷   | 不支持 | 卷必须可写才能正常使用卷影复制服务 (VSS)。
脱机卷 | 不支持 |   卷必须联机才能正常使用 VSS。
网络共享   | 不支持 |   卷必须位于服务器本地。
BitLocker 保护的卷 | 不支持 |   必须先解锁卷才能开始备份。
文件系统标识  | 不支持 |   仅支持 NTFS。
可移动媒体 | 不支持 |   所有备份项源必须处于固定状态。
已删除重复数据的驱动器 | 支持 | Azure 备份将删除了重复项的数据转换为正常数据。 它可以优化、加密、存储数据并将其发送到保管库。

## <a name="support-for-initial-offline-backup"></a>初始脱机备份支持

Azure 备份支持“脱机种子设定”，以使用磁盘将初始备份数据传输到 Azure。 如果初始备份的大小可能会达到 TB 量级，则此项支持很有帮助。 支持以下脱机备份：

- 直接备份运行 MARS 代理的本地计算机上的文件和文件夹。
- 备份 DPM 服务器或 MABS 中的工作负荷和文件。

脱机备份不可用于系统状态文件。

## <a name="support-for-data-restoration"></a>数据还原支持

使用 Azure 备份的[即时还原](backup-instant-restore-capability.md)功能可以先还原数据，然后再将数据复制到保管库。 要备份的计算机必须运行 .NET Framework 4.5.2 或更高版本。

无法将备份还原到运行较低 操作系统版本的目标计算机。 例如，在 Windows 8 或更高版本上，可以还原在 Windows 7 计算机中创建的备份。 但是，在 Windows 8 计算机中创建的备份无法还原到 Windows 7 计算机。

## <a name="next-steps"></a>后续步骤
- 详细了解[使用 MARS 代理的备份体系结构](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)。
- 了解[在 MABS 或 DPM 服务器上运行 MARS 代理](backup-support-matrix-mabs-dpm.md)时支持哪些操作。
