---
title: 使用 Azure 备份对运行 Microsoft Azure 恢复服务 (MARS) 代理的计算机进行备份的支持矩阵
description: 当备份正在运行 Microsoft Azure 恢复服务 (MARS) 代理的计算机时，本文汇总了 Azure 备份支持。
services: backup
author: rayne-wiselman
ms.service: backup
ms.date: 02/17/2019
ms.topic: conceptual
ms.author: raynew
manager: carmonm
ms.openlocfilehash: 3e2c6a550a9358656fd0870c7e785d131c5b6380
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "57894387"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>使用 Microsoft Azure 恢复服务 (MARS) 代理进行备份的支持矩阵

可以使用[Azure 备份服务](backup-overview.md)要备份的本地计算机和应用程序，并以备份 Azure 虚拟机 (Vm)。 使用 Microsoft Azure 恢复服务 (MARS) 代理机备份时，本文汇总了支持设置和限制。

## <a name="the-mars-agent"></a>MARS 代理

Azure 备份使用 MARS 代理将数据从备份在本地计算机和 Azure Vm 到 Azure 的备份恢复服务保管库。 MARS 代理可以：
- 在本地 Windows 计算机上运行，以便它们可以直接备份到 Azure 的备份恢复服务保管库。
- 在 Windows Vm 上运行，以便它们可以直接备份到保管库。
- 在 Microsoft Azure 备份服务器 (MABS) 或 System Center Data Protection Manager (DPM) 服务器上运行。 在此方案中，计算机和工作负荷备份到 MABS 或 DPM 服务器。 MARS 代理然后将此服务器备份到 Azure 中的保管库中。 

备份选项取决于安装了代理。 有关详细信息，请参阅[使用 MARS 代理的 Azure 备份体系结构](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)。 MABS 和 DPM 备份的体系结构有关的信息，请参阅[备份到 DPM 或 MABS](backup-architecture.md#architecture-back-up-to-dpmmabs)。 另请参阅[要求](backup-support-matrix-mabs-dpm.md)备份体系结构。

**安装** | **详细信息**
--- | ---
下载最新的 MARS 代理 | 可以从保管库下载最新版本的代理，或者[直接下载它](https://aka.ms/azurebackup_agent)。
直接在一台计算机上安装 | 您可以直接在本地 Windows 服务器上或运行任何的 Windows VM 上安装 MARS 代理[受支持的操作系统](https://docs.microsoft.com/en-us/azure/backup/backup-support-matrix-mabs-dpm#supported-mabs-and-dpm-operating-systems)。
备份服务器上安装 | 将 DPM 或 MABS 设置为备份到 Azure 时，可以在服务器上下载并安装 MARS 代理。 可以在安装代理[受支持的操作系统](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems)备份服务器支持矩阵中。

> [!NOTE]
> 默认情况下已启用备份的 Azure Vm 有 Azure 备份扩展安装。 此扩展备份整个 VM。 若要备份特定的文件夹和文件而不是整个 VM，可以在 Azure VM 上同时安装并运行 MARS 代理和该扩展。
> Azure VM 上运行的 MARS 代理时，它备份文件或文件夹中的 VM 上的临时存储。 如果从临时存储中删除的文件或文件夹或者删除临时存储，备份将失败。


## <a name="cache-folder-support"></a>缓存文件夹支持

当 MARS 代理用于将备份数据时，该代理获取数据的快照，并将数据发送到 Azure 之前将其存储在本地缓存文件夹中。 缓存 （暂存） 文件夹具有多个要求：

**缓存** | **详细信息**
--- | ---
大小 |  缓存文件夹中的可用空间应至少 5 到 10%的备份数据的总体大小。 
位置 | 缓存文件夹必须为要备份，在计算机上本地存储，并且它必须处于联机状态。 缓存文件夹不应为网络共享、 可移动媒体或脱机的卷上。 
Folder | 已删除重复卷上或文件夹的压缩，这就是稀疏，或具有包含重分析点，缓存文件夹应进行加密。
位置的更改 | 可以通过停止备份引擎来更改缓存位置 (`net stop bengine`) 并将缓存文件夹复制到新的驱动器。 （确保新的驱动器具有足够的空间。）然后更新两个注册表项下的**HKLM\SOFTWARE\Microsoft\Windows Azure 备份**(**Config/ScratchLocation**和**Config/CloudBackupProvider ScratchLocation**) 到新位置并重新启动引擎。

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
带宽控制 | 。 在 MARS 代理，使用**更改属性**来调整带宽。
网络限制 | 对备份运行 Windows Server 2008 R2、 Windows Server 2008 SP2 或 Windows 7 的计算机不可用。

## <a name="support-for-direct-backups"></a>直接备份支持

MARS 代理可用于直接备份到 Azure 的本地计算机和 Azure Vm 运行某些操作系统上。 操作系统必须是 64 位，并且应运行的最新服务包和更新。 下表总结了这些操作系统：

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
Windows Storage Server 2016/2012 R2/2012 （标准版、 工作组） | 是 | 否

有关详细信息，请参阅[支持 MABS 和 DPM 操作系统](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems)。

## <a name="backup-limits"></a>备份限制

Azure 备份限制的文件或文件夹的数据源，可以备份的大小。 从单个卷备份的项不能超过此表中总结的大小：

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
加密   | 。 
压缩 | 。
稀疏 | 。
压缩和稀疏 | 。
硬链接  | 不支持。 已跳过。
重分析点   | 不支持。 已跳过。
加密和稀疏 |  不支持。 已跳过。
压缩流   | 不支持。 已跳过。
稀疏流   | 不支持。 已跳过。
OneDrive （已同步的文件是稀疏流）  | 不支持。 

## <a name="supported-drives-or-volumes-for-backup"></a>支持的驱动器或卷的备份

**驱动器/卷** | **支持** | **详细信息**
--- | --- | ---
只读卷   | 不支持 | 卷影复制服务 (VSS) 仅适用于该卷可写的情况。
脱机卷 | 不支持 |   VSS 仅适用于该卷处于联机状态。
网络共享   | 不支持 |   卷必须是本地服务器上。
受 BitLocker 保护的卷 | 不支持 |   必须先解锁卷备份开始。
文件系统标识  | 不支持 |   仅支持 NTFS。
可移动媒体 | 不支持 |   所有备份项源必须具有*修复*状态。
已删除重复数据的驱动器 | 支持 | Azure 备份将删除了重复项的数据转换为正常数据。 它可优化、 加密、 存储和将数据发送到保管库。

## <a name="support-for-initial-offline-backup"></a>初始脱机备份支持

Azure 备份支持*脱机种子设定*使用磁盘将初始备份数据传输到 Azure。 这种支持很有帮助，如果初始备份可能会出现在千吉字节 (Tb) 的大小范围。 支持以下脱机备份：

- 直接备份文件和运行 MARS 代理的本地计算机上的文件夹。
- 备份 DPM 服务器或 MABS 中的工作负荷和文件。

脱机备份不能用于系统状态文件。

## <a name="support-for-data-restoration"></a>对于数据恢复的支持

通过使用[即时还原](backup-instant-restore-capability.md)功能的 Azure 备份，则可以还原数据之前将其复制到保管库。 要备份的计算机必须运行.NET Framework 4.5.2 或更高版本。

无法将备份还原到运行早期版本的操作系统的目标计算机。 例如，Windows 8 或更高版本，则可以还原从运行 Windows 7 的计算机执行的备份。 但不能在运行 Windows 7 的计算机上还原从运行 Windows 8 的计算机执行的备份。

## <a name="next-steps"></a>后续步骤
- 详细了解如何[备份使用 MARS 代理体系结构](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)。
- 了解何时支持内容您[MABS 或 DPM 服务器上运行的 MARS 代理](backup-support-matrix-mabs-dpm.md)。
