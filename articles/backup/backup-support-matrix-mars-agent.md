---
title: MARS 代理的支持矩阵
description: 本文总结了备份运行 Microsoft Azure 恢复服务（MARS）代理的计算机时的 Azure 备份支持。
ms.date: 08/30/2019
ms.topic: conceptual
ms.openlocfilehash: 26f3dde0bb20443753e2b443ffc00ee23c9124c4
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2019
ms.locfileid: "74893971"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>使用 Microsoft Azure 恢复服务 (MARS) 代理进行备份的支持矩阵

可以使用[Azure 备份服务](backup-overview.md)来备份本地计算机和应用，并备份 Azure 虚拟机（vm）。 本文汇总了使用 Microsoft Azure 恢复服务（MARS）代理备份计算机时支持的设置和限制。

## <a name="the-mars-agent"></a>MARS 代理

Azure 备份使用 MARS 代理将数据从本地计算机和 Azure Vm 备份到 Azure 中的备份恢复服务保管库。 MARS 代理可以：

- 在本地 Windows 计算机上运行，以便它们可以直接备份到 Azure 中的备份恢复服务保管库。
- 在 Windows Vm 上运行，以便它们可以直接备份到保管库。
- 在 Microsoft Azure 备份 Server （MABS）或 System Center Data Protection Manager （DPM）服务器上运行。 在此方案中，计算机和工作负荷备份到 MABS 或 DPM 服务器。 然后 MARS 代理将此服务器备份到 Azure 中的保管库。

> [!NOTE]
>Azure 备份不支持夏令时自动调整时间（DST）。 修改策略以确保考虑夏令时，以防止实际时间和计划的备份时间之间的差异。

备份选项取决于安装代理的位置。 有关详细信息，请参阅[使用 MARS 代理的 Azure 备份体系结构](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)。 有关 MABS 和 DPM 备份体系结构的信息，请参阅[备份到 dpm 或 MABS](backup-architecture.md#architecture-back-up-to-dpmmabs)。 另请参阅备份体系结构的[要求](backup-support-matrix-mabs-dpm.md)。

**安装** | **详细信息**
--- | ---
下载最新的 MARS 代理 | 可以从保管库下载最新版本的代理，或者[直接下载它](https://aka.ms/azurebackup_agent)。
直接在计算机上安装 | 你可以直接在本地 Windows server 或运行任何[受支持操作系统](https://docs.microsoft.com/azure/backup/backup-support-matrix-mabs-dpm#supported-mabs-and-dpm-operating-systems)的 windows VM 上安装 MARS 代理。
在备份服务器上安装 | 将 DPM 或 MABS 设置为备份到 Azure 时，可以在服务器上下载并安装 MARS 代理。 可以在备份服务器支持矩阵中的[受支持操作系统](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems)上安装代理。

> [!NOTE]
> 默认情况下，为备份启用的 Azure Vm 会安装 Azure 备份扩展。 此扩展备份整个 VM。 若要备份特定的文件夹和文件而不是整个 VM，可以在 Azure VM 上同时安装并运行 MARS 代理和该扩展。
> 在 Azure VM 上运行 MARS 代理时，会备份 VM 上的临时存储中的文件或文件夹。 如果从临时存储中删除文件或文件夹，或者删除临时存储，则备份将失败。

## <a name="cache-folder-support"></a>缓存文件夹支持

使用 MARS 代理备份数据时，该代理会创建数据快照并将其存储在本地缓存文件夹中，然后将数据发送到 Azure。 缓存（暂存）文件夹具有几个要求：

**缓存** | **详细信息**
--- | ---
大小 |  缓存文件夹中的可用空间应至少为备份数据的总大小的5% 到10%。
Location | 缓存文件夹必须在要备份的计算机上本地存储，并且必须处于联机状态。 缓存文件夹不应位于网络共享、可移动介质或脱机卷上。
Folder | 缓存文件夹不应在删除了重复数据的卷上，也不能在压缩的文件夹（即稀疏文件或具有重新分析点的文件夹）中进行加密。
位置更改 | 可以通过停止备份引擎（`net stop bengine`）并将缓存文件夹复制到新驱动器来更改缓存位置。 （确保新驱动器具有足够的空间。）然后，在**HKLM\SOFTWARE\Microsoft\Windows Azure Backup** （**Config/ScratchLocation**和**Config/CloudBackupProvider/ScratchLocation**）下将两个注册表项更新到新位置，然后重新启动引擎。

## <a name="networking-and-access-support"></a>网络和访问支持

### <a name="url-and-ip-access"></a>URL 和 IP 访问

MARS 代理需要以下 URL 的访问权限：

- <http://www.msftncsi.com/ncsi.txt>
- *.Microsoft.com
- *.WindowsAzure.com
- *.MicrosoftOnline.com
- *.Windows.net

以下 IP 地址：

- 20.190.128.0/18
- 40.126.0.0/18

对上面列出的所有 Url 和 IP 地址的访问都使用端口443上的 HTTPS 协议。

### <a name="throttling-support"></a>限制支持

**功能** | **详细信息**
--- | ---
带宽控制 | 。 在 MARS 代理中，使用 "**更改属性**" 来调整带宽。
网络限制 | 不适用于运行 Windows Server 2008 R2、Windows Server 2008 SP2 或 Windows 7 的备份计算机。

## <a name="support-for-direct-backups"></a>直接备份支持

>[!NOTE]
> MARS 代理不支持 Windows Server Core Sku。

可以在运行的以下操作系统上，使用 MARS 代理直接备份到 Azure：

1. 本地 Windows 服务器
2. 运行 Windows 的 Azure VM

操作系统必须是64位，并且应该运行最新的服务包和更新。 下表总结了这些操作系统：

**操作系统** | **文件/文件夹** | **系统状态** | **软件/模块要求**
--- | --- | --- | ---
Windows 10（Enterprise、Pro、Home） | 是 | No |  检查软件/模块要求的相应服务器版本
Windows 8.1（Enterprise、Pro）| 是 |No | 检查软件/模块要求的相应服务器版本
Windows 8（Enterprise、Pro） | 是 | No | 检查软件/模块要求的相应服务器版本
Windows 7（Ultimate、Enterprise、Pro、Home Premium/Basic、Starter） | 是 | No | 检查软件/模块要求的相应服务器版本
Windows Server 2016（Standard、Datacenter、Essentials） | 是 | 是 | -.NET 4。5 <br> -Windows PowerShell <br> -最新兼容的 Microsoft VC + + 可再发行组件 <br> -Microsoft 管理控制台（MMC）3。0
Windows Server 2012 R2（Standard、Datacenter、Foundation、Essentials） | 是 | 是 | -.NET 4。5 <br> -Windows PowerShell <br> -最新兼容的 Microsoft VC + + 可再发行组件 <br> -Microsoft 管理控制台（MMC）3。0
Windows Server 2012（Standard、Datacenter、Foundation） | 是 | 是 |-.NET 4。5 <br> -Windows PowerShell <br> -最新兼容的 Microsoft VC + + 可再发行组件 <br> -Microsoft 管理控制台（MMC）3。0 <br> -部署映像服务和管理（DISM.EXE）
Windows Server 2008 R2（Standard、Enterprise、Datacenter、Foundation） | 是 | 是 | -.NET 3.5，.Net 4。5 <br> -Windows PowerShell <br> 兼容的 Microsoft VC + + 可再发行组件 <br> -Microsoft 管理控制台（MMC）3。0 <br> -部署映像服务和管理（DISM.EXE）
Windows Server 2008 SP2（Standard、Datacenter、Foundation） | 是 | No | -.NET 3.5，.Net 4。5 <br> -Windows PowerShell <br> 兼容的 Microsoft VC + + 可再发行组件 <br> -Microsoft 管理控制台（MMC）3。0 <br> -部署映像服务和管理（DISM.EXE） <br> -Virtual Server 2005 base + KB KB948515
Windows Storage Server 2016/2012 R2/2012 （标准版、工作组版） | 是 | No | -.NET 4。5 <br> -Windows PowerShell <br> -最新兼容的 Microsoft VC + + 可再发行组件 <br> -Microsoft 管理控制台（MMC）3。0
Windows Server 2019（Standard、Datacenter、Essentials） | 是 | 是 | -.NET 4。5 <br> -Windows PowerShell <br> -最新兼容的 Microsoft VC + + 可再发行组件 <br> -Microsoft 管理控制台（MMC）3。0

有关详细信息，请参阅[支持的 MABS 和 DPM 操作系统](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems)。

## <a name="backup-limits"></a>备份限制

Azure 备份限制了可以备份的文件或文件夹数据源的大小。 从单个卷备份的项不能超过此表中汇总的大小：

**操作系统** | **大小限制**
--- | ---
Windows Server 2012 或更高版本 |54,400 GB
Windows Server 2008 R2 SP1 |1,700 GB
Windows Server 2008 SP2| 1,700 GB
Windows 8 或更高版本| 54,400 GB
Windows 7| 1,700 GB

## <a name="supported-file-types-for-backup"></a>支持备份的文件类型

类型 | **支持**
--- | ---
加密| 。
已压缩 | 。
稀疏 | 。
压缩和稀疏 |。
硬链接| 不支持。 跳过.
重分析点| 不支持。 跳过.
加密和稀疏 |不支持。 跳过.
压缩流| 不支持。 跳过.
稀疏流| 不支持。 跳过.
OneDrive （同步的文件是稀疏流）| 不支持。

## <a name="supported-drives-or-volumes-for-backup"></a>支持备份的驱动器或卷

**驱动器/卷** | **支持** | **详细信息**
--- | --- | ---
只读卷| 不支持 | 卷影复制服务（VSS）仅适用于卷是否可写。
脱机卷| 不支持 |仅当卷处于联机状态时，VSS 才有效。
网络共享| 不支持 |卷必须是服务器上的本地卷。
BitLocker 锁定的卷| 不支持 |在开始备份之前，必须解锁卷。
文件系统标识| 不支持 |仅支持 NTFS。
可移动媒体| 不支持 |所有备份项源都必须具有*固定*状态。
已删除重复数据的驱动器 | 受支持 | Azure 备份将删除了重复项的数据转换为正常数据。 它优化、加密、存储数据，并将数据发送到保管库。

## <a name="support-for-initial-offline-backup"></a>初始脱机备份支持

Azure 备份支持*脱机种子设定*，使用磁盘将初始备份数据传输到 Azure。 如果初始备份的大小可能是 tb （Tb），则此支持非常有用。 支持以下脱机备份：

- 直接备份运行 MARS 代理的本地计算机上的文件和文件夹。
- 备份 DPM 服务器或 MABS 中的工作负荷和文件。

脱机备份无法用于系统状态文件。

## <a name="support-for-data-restoration"></a>支持数据还原

通过使用 Azure 备份的[即时还原](backup-instant-restore-capability.md)功能，你可以在将数据复制到保管库之前还原数据。 正在备份的计算机必须 .NET Framework 运行4.5.2 或更高版本。

无法将备份还原到运行早期版本的操作系统的目标计算机。 例如，可以在 Windows 8 或更高版本上还原从运行 Windows 7 的计算机上创建的备份。 但不能在运行 Windows 7 的计算机上还原从运行 Windows 8 的计算机上执行的备份。

## <a name="next-steps"></a>后续步骤

- 了解有关[使用 MARS 代理的备份体系结构的](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)详细信息。
- 了解在[MABS 或 DPM 服务器上运行 MARS 代理](backup-support-matrix-mabs-dpm.md)时所支持的内容。
