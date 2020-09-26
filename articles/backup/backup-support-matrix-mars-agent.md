---
title: MARS 代理的支持矩阵
description: 本文汇总了备份运行 Microsoft Azure 恢复服务 (MARS) 代理的计算机时的 Azure 备份支持。
ms.date: 08/30/2019
ms.topic: conceptual
ms.openlocfilehash: b11a2e3ec2fdf3a46b324dcc0f95d4666a84c179
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91332672"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>使用 Microsoft Azure 恢复服务 (MARS) 代理进行备份的支持矩阵

可以使用 [Azure 备份服务](backup-overview.md)备份本地计算机和应用，以及 Azure 虚拟机 (VM)。 本文汇总了使用 Microsoft Azure 恢复服务 (MARS) 代理备份计算机时的支持设置和限制。

## <a name="the-mars-agent"></a>MARS 代理

Azure 备份使用 MARS 代理将本地计算机和 Azure VM 中的数据备份到 Azure 中的恢复服务保管库。 MARS 代理可以：

- 在本地 Windows 计算机上运行，使这些计算机能够直接备份到 Azure 中的备份恢复服务保管库。
- 在 Windows VM 上运行，使这些 VM 能够直接备份到保管库。
- 在 Microsoft Azure 备份服务器 (MABS) 或 System Center Data Protection Manager (DPM) 服务器上运行。 在此方案中，计算机和工作负荷将备份到 MABS 或 DPM 服务器。 然后，MARS 代理将此服务器备份到 Azure 中的保管库。

> [!NOTE]
>Azure 备份不支持自动调整夏令时 (DST) 时钟。 修改策略以确保考虑到夏令时，以防止实际时间和计划备份时间之间出现差异。

备份选项取决于该代理的安装位置。 有关详细信息，请参阅[使用 MARS 代理的 Azure 备份体系结构](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)。 有关 MABS 和 DPM 备份体系结构的信息，请参阅[备份到 DPM 或 MABS](backup-architecture.md#architecture-back-up-to-dpmmabs)。 另请参阅备份体系结构的[要求](backup-support-matrix-mabs-dpm.md)。

**安装** | **详细信息**
--- | ---
下载最新的 MARS 代理 | 可以从保管库下载最新版本的代理，或者[直接下载它](https://aka.ms/azurebackup_agent)。
直接在计算机上安装 | 可以直接在本地 Windows 服务器上安装 MARS 代理，或者在运行任意[受支持操作系统](./backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems)的 Windows VM 上安装它。
在备份服务器上安装 | 将 DPM 或 MABS 设置为备份到 Azure 时，可以在服务器上下载并安装 MARS 代理。 可在备份服务器支持矩阵中的[受支持操作系统](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems)上安装该代理。

> [!NOTE]
> 默认情况下，启用了备份的 Azure VM 上安装了 Azure 备份扩展。 此扩展备份整个 VM。 若要备份特定的文件夹和文件而不是整个 VM，可以在 Azure VM 上同时安装并运行 MARS 代理和该扩展。
> 在 Azure VM 上运行 MARS 代理时，该代理会备份 VM 上的临时存储中的文件或文件夹。 如果从临时存储中删除了文件或文件夹，或者删除了临时存储，则备份将会失败。

## <a name="cache-folder-support"></a>缓存文件夹支持

使用 MARS 代理备份数据时，该代理将创建数据的快照并将其存储在本地缓存文件夹中，然后将数据发送到 Azure。 缓存（暂存）文件夹有几项要求：

**缓存** | **详细信息**
--- | ---
大小 |  缓存文件夹中的可用空间应至少为备份数据总大小的 5% 到 10%。
位置 | 缓存文件夹必须存储在要备份的计算机本地，并且该计算机必须联机。 缓存文件夹不应位于网络共享、可移动媒体或脱机卷上。
文件夹 | 不应在删除了重复数据的卷上加密缓存文件夹，也不能对压缩后的文件夹进行加密，即稀疏文件或具有重新分析点的文件夹。
位置更改 | 可以通过停止备份引擎 (`net stop bengine`)，并将缓存文件夹复制到新驱动器来更改缓存位置。 （确保新驱动器有足够的空间。）然后，将 **HKLM\SOFTWARE\Microsoft\Windows Azure Backup** 下的两个注册表项（**Config/ScratchLocation** 和 **Config/CloudBackupProvider/ScratchLocation**）更新为新位置，并重启引擎。

## <a name="networking-and-access-support"></a>网络和访问支持

### <a name="url-and-ip-access"></a>URL 和 IP 访问

MARS 代理需要以下 URL 的访问权限：

- `http://www.msftncsi.com/ncsi.txt`
- *.Microsoft.com
- *.WindowsAzure.com
- *.MicrosoftOnline.com
- *.Windows.net

以及以下 IP 地址：

- 20.190.128.0/18
- 40.126.0.0/18

对上面列出的所有 URL 和 IP 地址的访问都使用端口 443 上的 HTTPS 协议。

使用 MARS 代理在 Azure Vm 中备份文件和文件夹时，还需要将 Azure 虚拟网络配置为允许访问。 如果使用网络安全组 (NSG)，请使用 AzureBackup 服务标记以允许对 Azure 备份进行出站访问。 除了 Azure 备份标记外，还需要通过为 Azure AD (*AzureActiveDirectory*) 和 Azure 存储 (*存储*) 创建类似的[NSG 规则](https://docs.microsoft.com/azure/virtual-network/network-security-groups-overview#service-tags)，以允许进行身份验证和数据传输连接。 以下步骤介绍了为 Azure 备份标记创建规则的过程：

1. 在“所有服务”中转到“网络安全组”，然后选择“网络安全组”。
2. 在“设置”下选择“出站安全规则”。
3. 选择“添加”  。 根据[安全规则设置](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#security-rule-settings)中所述，输入创建新规则所需的所有详细信息。 请确保将选项“目标”设置为“服务标记”，将“目标服务标记”设置为“AzureBackup”。
4. 选择 " **添加** " 以保存新创建的出站安全规则。

同样，可以为 Azure 存储和 Azure AD 创建 NSG 出站安全规则。 有关服务标记的详细信息，请参阅[此文](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)。

### <a name="azure-expressroute-support"></a>Azure ExpressRoute 支持

可以使用公共对等互连（适用于旧线路）和 Microsoft 对等互连通过 Azure ExpressRoute 备份数据。 不支持通过专用对等互连进行备份。

使用公共对等互连：确保访问以下域/地址：

- `http://www.msftncsi.com/ncsi.txt`
- `microsoft.com`
- `.WindowsAzure.com`
- `.microsoftonline.com`
- `.windows.net`

使用 Microsoft 对等互连，选择以下服务/区域和相关社区值：

- Azure 备份 (根据恢复服务保管库的位置) 
- Azure Active Directory (12076:5060)
- Azure 存储（根据恢复服务保管库的位置）

有关详细信息，请参阅 [ExpressRoute 路由要求](../expressroute/expressroute-routing.md#bgp)。

>[!NOTE]
>对于新线路，公共对等互连已弃用。

### <a name="private-endpoint-support"></a>私有终结点支持

你现在可以使用专用终结点将数据从服务器安全备份到恢复服务保管库。 由于 Azure Active Directory 当前不支持私有终结点，因此需要对 Azure Active Directory 所需的 Ip 和 Fqdn 进行单独的出站访问。

使用 MARS 代理备份本地资源时，请确保包含要备份的资源的本地网络 () 与包含保管库的专用终结点的 Azure VNet 对等互连。 然后，你可以继续安装 MARS 代理并配置备份。 但是，必须确保仅通过对等互连网络进行备份的所有通信。

如果在向保管库注册了 MARS 代理后，删除该保管库的专用终结点，则需要使用保管库重新注册该容器。 不需要停止对它们的保护。

阅读有关 [Azure 备份的专用终结点的](private-endpoints.md)详细信息。

### <a name="throttling-support"></a>限制支持

**功能** | **详细信息**
--- | ---
带宽控制 | 。 在 MARS 代理中，使用“更改属性”来调整带宽。****
网络限制 | 不适用于运行 Windows Server 2008 R2、Windows Server 2008 SP2 或 Windows 7 的备份计算机。

## <a name="supported-operating-systems"></a>支持的操作系统

>[!NOTE]
> MARS 代理不支持 Windows Server Core SKU。

可以使用 MARS 代理在以下运行的操作系统上直接备份到 Azure：

1. 本地 Windows 服务器
2. 运行 Windows 的 Azure VM

所有操作系统必须是 64 位，并且应该运行最新的服务包和更新。 下表汇总了这些操作系统：

**操作系统** | **文件/文件夹** | **系统状态** | **软件/模块要求**
--- | --- | --- | ---
Windows 10（Enterprise、Pro、Home） | 是 | 否 |  检查软件/模块要求的相应服务器版本
Windows 8.1（Enterprise、Pro）| 是 |否 | 检查软件/模块要求的相应服务器版本
Windows 8（Enterprise、Pro） | 是 | 否 | 检查软件/模块要求的相应服务器版本
Windows Server 2016（Standard、Datacenter、Essentials） | 是 | 是 | - .NET 4.5 <br> - Windows PowerShell <br> - 最新兼容的 Microsoft VC++ 可再发行包 <br> - Microsoft 管理控制台 (MMC) 3.0
Windows Server 2012 R2（Standard、Datacenter、Foundation、Essentials） | 是 | 是 | - .NET 4.5 <br> - Windows PowerShell <br> - 最新兼容的 Microsoft VC++ 可再发行包 <br> - Microsoft 管理控制台 (MMC) 3.0
Windows Server 2012（Standard、Datacenter、Foundation） | 是 | 是 |- .NET 4.5 <br> -Windows PowerShell <br> - 最新兼容的 Microsoft VC++ 可再发行包 <br> - Microsoft 管理控制台 (MMC) 3.0 <br> - 部署映像服务和管理 (DISM.exe)
Windows Storage Server 2016/2012 R2/2012（Standard、Workgroup） | 是 | 否 | - .NET 4.5 <br> - Windows PowerShell <br> - 最新兼容的 Microsoft VC++ 可再发行包 <br> - Microsoft 管理控制台 (MMC) 3.0
Windows Server 2019（Standard、Datacenter、Essentials） | 是 | 是 | - .NET 4.5 <br> - Windows PowerShell <br> - 最新兼容的 Microsoft VC++ 可再发行包 <br> - Microsoft 管理控制台 (MMC) 3.0

有关详细信息，请参阅[支持的 MABS 和 DPM 操作系统](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems)。

### <a name="operating-systems-at-end-of-support"></a>支持的操作系统

以下操作系统已结束支持，强烈建议升级操作系统以继续保持受保护状态。

如果现有承诺阻止升级操作系统，请考虑将 Windows 服务器迁移到 Azure Vm 并利用 Azure VM 备份来继续保护。 有关迁移 Windows server 的详细信息，请访问 [此处的迁移页面](https://azure.microsoft.com/migration/windows-server/) 。

对于本地或托管环境，在无法升级操作系统或迁移到 Azure 的情况下，请为计算机激活扩展的安全更新以继续保护并支持。 请注意，只有特定版本有资格进行扩展安全更新。 请访问 [FAQ 页面](https://www.microsoft.com/windows-server/extended-security-updates) 了解更多。

| **操作系统**                                       | **文件/文件夹** | **系统状态** | **软件/模块要求**                           |
| ------------------------------------------------------------ | ----------------- | ------------------ | ------------------------------------------------------------ |
| Windows 7 (旗舰版、企业版、专业版、家庭高级版/基本版、初学者)  | 是               | 否                 | 检查软件/模块要求的相应服务器版本 |
| Windows Server 2008 R2 (Standard、Enterprise、Datacenter、Foundation)  | 是               | 是                | -.NET 3.5，.NET 4。5 <br>  - Windows PowerShell <br>  - 兼容的 Microsoft VC++ 可再发行包 <br>  - Microsoft 管理控制台 (MMC) 3.0 <br>  - 部署映像服务和管理 (DISM.exe) |
| Windows Server 2008 SP2 (Standard、Datacenter、Foundation)   | 是               | 否                 | -.NET 3.5，.NET 4。5 <br>  - Windows PowerShell <br>  - 兼容的 Microsoft VC++ 可再发行包 <br>  - Microsoft 管理控制台 (MMC) 3.0 <br>  - 部署映像服务和管理 (DISM.exe) <br>  -Virtual Server 2005 base + KB KB948515 |

## <a name="backup-limits"></a>备份限制

### <a name="size-limits"></a>大小限制

Azure 备份会限制可备份的文件或文件夹数据源大小。 在单个卷中备份的项大小不能超过下表中汇总的大小：

**操作系统** | **大小限制**
--- | ---
Windows Server 2012 或更高版本 |54,400 GB
Windows Server 2008 R2 SP1 |1,700 GB
Windows Server 2008 SP2| 1,700 GB
Windows 8 或更高版本| 54,400 GB
Windows 7| 1,700 GB

### <a name="other-limitations"></a>其他限制

- MARS 不支持对单个保管库具有相同名称的多个计算机的保护。

## <a name="supported-file-types-for-backup"></a>支持备份的文件类型

**类型** | **支持**
--- | ---
过<sup>*</sup>| 。
Compressed | 。
稀疏 | 。
压缩和稀疏 |。
硬链接| 不支持。 跳过。
重分析点| 不支持。 跳过。
加密和稀疏 |不支持。 跳过。
压缩流| 不支持。 跳过。
稀疏流| 不支持。 跳过。
OneDrive（同步的文件是稀疏流）| 不支持。
已启用 DFS 复制的文件夹 | 不支持。

\* 确保 MARS 代理有权访问所需的证书来访问加密的文件。 将跳过不可访问的文件。

## <a name="supported-drives-or-volumes-for-backup"></a>支持备份的驱动器或卷

**驱动器/卷** | **支持** | **详细信息**
--- | --- | ---
只读卷| 不支持 | 卷必须可写才能正常使用卷影复制服务 (VSS)。
脱机卷| 不支持 |卷必须联机才能正常使用 VSS。
网络共享| 不支持 |卷必须位于服务器本地。
BitLocker 锁定卷| 不支持 |必须先解锁卷才能开始备份。
文件系统标识| 不支持 |仅支持 NTFS。
可移动媒体| 不支持 |所有备份项源必须处于固定状态。**
已删除重复数据的驱动器 | 支持 | Azure 备份将删除了重复项的数据转换为正常数据。 它可以优化、加密、存储数据并将其发送到保管库。

## <a name="support-for-initial-offline-backup"></a>初始脱机备份支持

Azure 备份支持“脱机种子设定”，以使用磁盘将初始备份数据传输到 Azure。** 如果初始备份的大小可能会达到 TB 量级，则此项支持很有帮助。 支持以下脱机备份：

- 直接备份运行 MARS 代理的本地计算机上的文件和文件夹。
- 备份 DPM 服务器或 MABS 中的工作负荷和文件。

脱机备份不可用于系统状态文件。

## <a name="support-for-data-restoration"></a>数据还原支持

使用 Azure 备份的[即时还原](backup-instant-restore-capability.md)功能可以先还原数据，然后再将数据复制到保管库。 要备份的计算机必须运行 .NET Framework 4.5.2 或更高版本。

无法将备份还原到运行较低 操作系统版本的目标计算机。 例如，在 Windows 8 或更高版本上，可以还原在 Windows 7 计算机中创建的备份。 但是，在 Windows 8 计算机中创建的备份无法还原到 Windows 7 计算机。

## <a name="next-steps"></a>后续步骤

- 详细了解[使用 MARS 代理的备份体系结构](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)。
- 了解[在 MABS 或 DPM 服务器上运行 MARS 代理](backup-support-matrix-mabs-dpm.md)时支持哪些操作。
