---
title: "使用 Azure Site Recovery 从保护项中排除磁盘 | Microsoft 文档"
description: "介绍在从 Hyper-V 复制到 Azure 时，为何需要从复制中排除 VM 磁盘，以及如何这样做。"
services: site-recovery
author: nsoneji
manager: garavd
ms.service: site-recovery
ms.topic: article
ms.date: 02/14/2018
ms.author: nisoneji
ms.openlocfilehash: 131c98c6772a68c8617df3160b159ece62bd1fc9
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/22/2018
---
# <a name="exclude-disks-from-replication"></a>从复制中排除磁盘
本文介绍如何从复制中排除磁盘。 这种排除可以优化消耗的复制带宽，或者优化此类磁盘利用的目标端资源。

## <a name="supported-scenarios"></a>支持的方案
**功能** | **VMware 到 Azure** | **Hyper-V 到 Azure** | **Azure 到 Azure**| **Hyper-V 到 Hyper-V** 
--|--|--|--|--
排除磁盘 | 是 | 是 | 否 | 否

## <a name="why-exclude-disks-from-replication"></a>为何要从复制中排除磁盘？
从复制中排除磁盘通常因以下原因而有必要：

- 排除的磁盘上改动的数据不重要或不需要复制。

- 用户需要节省存储和网络资源，因此不复制此改动。

## <a name="what-are-the-typical-scenarios"></a>有哪些典型方案？
对于适合进行排除的数据改动，用户可以确定具体的示例。 示例可能包括向页面文件 (pagefile.sys) 写入以及向 Microsoft SQL Server 的 tempdb 文件写入。 页面文件可登记大量改动，具体取决于工作负荷和存储子系统。 但是，从主站点将此数据复制到 Azure 会占用大量资源。 因此，用户可以执行以下步骤，通过单个包含操作系统和页面文件的虚拟磁盘来优化虚拟机的复制：

1. 将单个虚拟磁盘拆分成两个虚拟磁盘。 一个虚拟磁盘包含操作系统，另一个包含页面文件。
2. 从复制中排除页面文件磁盘。

同样，用户可以执行以下步骤，优化包含 Microsoft SQL Server tempdb 文件和系统数据库文件的磁盘：

1. 在两个不同的磁盘上保存系统数据库和 tempdb。
2. 从复制中排除 tempdb 磁盘。

## <a name="how-to-exclude-disks"></a>如何排除磁盘
按照[启用复制](site-recovery-hyper-v-site-to-azure.md)工作流，通过 Azure Site Recovery 门户保护虚拟机。 在工作流的第四步，使用“要复制的磁盘”列从复制中排除磁盘。 默认情况下，选择所有磁盘进行复制。 清除要从复制中排除的磁盘所对应的复选框，并完成启用复制的步骤。

![从复制中排除磁盘，并为 Hyper-V 启用到 Azure 故障回复的复制](./media/hyper-v-exclude-disk/enable-replication6-with-exclude-disk.png)

>[!NOTE]
>
> * 只能从复制中排除基本磁盘。 不能排除操作系统磁盘。 建议不要排除动态磁盘。 Azure Site Recovery 无法确定来宾虚拟机中的虚拟硬盘 (VHD) 是基本磁盘还是动态磁盘。  如果未排除所有依赖性动态卷磁盘，则受保护的动态磁盘就会成为故障转移虚拟机上的故障磁盘，该磁盘上的数据无法访问。
> * 启用复制后，无法添加或删除要复制的磁盘。 如果想要添加或排除磁盘，需要禁用虚拟机保护，并重新启用保护。
> * 如果排除的磁盘是某个应用程序正常运行所必需的，则故障转移到 Azure 之后，需要在 Azure 中手动创建该磁盘，以便复制的应用程序可以运行。 或者，可以将 Azure 自动化集成到恢复计划中，以便在故障转移计算机期间创建磁盘。
> * 在 Azure 中手动创建的磁盘不会执行故障回复。 例如，如果对三个磁盘进行故障转移，并直接在 Azure 虚拟机中创建两个磁盘，则只会对那三个故障转移的磁盘进行从 Azure 到 Hyper-V 的故障回复。 在故障回复中或从 Hyper-V 到 Azure 的反向复制中，不能包括手动创建的磁盘。

## <a name="end-to-end-scenarios-of-exclude-disks"></a>排除磁盘的端到端方案
让我们假设两个方案，以便了解排除磁盘功能：

- SQL Server tempdb 磁盘
- 页面文件 (pagefile.sys) 磁盘

## <a name="excample-1-exclude-the-sql-server-tempdb-disk"></a>示例 1：排除 SQL Server tempdb 磁盘
假设有一台拥有可被排除的 tempdb 的 SQL Server 虚拟机。

虚拟磁盘的名称为 SalesDB。

源虚拟机上的磁盘如下所示：


**磁盘名称** | **来宾操作系统磁盘编号** | **驱动器号** | **磁盘上的数据类型**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | 操作系统磁盘
DB-Disk1| Disk1 | D:\ | SQL 系统数据库和用户数据库 1
DB-Disk2（已从保护中排除该磁盘） | Disk2 | E:\ | 临时文件
DB-Disk3（已从保护中排除该磁盘） | Disk3 | F:\ | SQL tempdb 数据库（文件夹路径 (F:\MSSQL\Data\) </br /> </br />在故障转移前记下该文件夹路径。
DB-Disk4 | Disk4 |G:\ |用户数据库 2

在虚拟机的两个磁盘上的数据改动是临时性的，因此在保护 SalesDB 虚拟机时，可以从复制中排除 Disk2 和 Disk3。 Azure Site Recovery 不会复制这些磁盘。 进行故障转移时，这些磁盘不会存在于 Azure 的故障转移虚拟机上。

故障转移后，Azure 虚拟机上的磁盘如下所示：

**来宾操作系统磁盘编号** | **驱动器号** | **磁盘上的数据类型**
--- | --- | ---
DISK0 | C:\ | 操作系统磁盘
Disk1 | E:\ | 临时存储</br /> </br />Azure 添加此磁盘并分配第一个可用的驱动器号。
Disk2 | D:\ | SQL 系统数据库和用户数据库 1
Disk3 | G:\ | 用户数据库 2

由于从 SalesDB 虚拟机中排除了 Disk2 和 Disk3，E: 是可用列表中的第一个驱动器号。 Azure 将 E: 分配给临时存储卷。 对于所有复制的磁盘，驱动器号保持不变。

Disk3 为 SQL tempdb 磁盘\)（tempdb 文件夹路径为 F:\MSSQL\Data），已从复制中排除。 该磁盘在故障转移虚拟机上不可用。 因此，SQL 服务处于停止状态并需要 F:\MSSQL\Data 路径。

有两种创建该路径的方法：

- 添加新磁盘并分配 tempdb 文件夹路径。
- 使用现有临时存储磁盘作为 tempdb 文件夹路径。

### <a name="add-a-new-disk"></a>添加新磁盘：

1. 在故障转移前，记下 SQL tempdb.mdf 和 tempdb.ldf 的路径。
2. 在 Azure 门户中，将大小等于或大于源 SQL tempdb 磁盘 (Disk3) 的新磁盘添加到故障转移虚拟机。
3. 登录到 Azure 虚拟机。 在磁盘管理 (diskmgmt.msc) 控制台中，初始化并格式化新添加的磁盘。
4. 分配与 SQL tempdb 磁盘 (F:) 所用驱动器号相同的驱动器号。
5. 在 F: 卷上创建 tempdb 文件夹 (F:\MSSQL\Data)。
6. 从服务控制台启动 SQL 服务。

### <a name="use-an-existing-temporary-storage-disk-for-the-sql-tempdb-folder-path"></a>使用现有临时存储磁盘作为 SQL tempdb 文件夹路径：

1. 打开命令提示符。
2. 通过命令提示符在恢复模式下运行 SQL Server。

        Net start MSSQLSERVER /f / T3608

3. 运行以下 sqlcmd，将 tempdb 路径更改为新路径。

        sqlcmd -A -S SalesDB        **Use your SQL DBname**
        USE master;     
        GO      
        ALTER DATABASE tempdb       
        MODIFY FILE (NAME = tempdev, FILENAME = 'E:\MSSQL\tempdata\tempdb.mdf');
        GO      
        ALTER DATABASE tempdb       
        MODIFY FILE (NAME = templog, FILENAME = 'E:\MSSQL\tempdata\templog.ldf');       
        GO


4. 停止 Microsoft SQL Server 服务。

        Net stop MSSQLSERVER
5. 启动 Microsoft SQL Server 服务。

        Net start MSSQLSERVER

请参阅以下有关临时存储磁盘的 Azure 指南：

* [Using SSDs in Azure VMs to store SQL Server TempDB and Buffer Pool Extensions](https://blogs.technet.microsoft.com/dataplatforminsider/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/)（在 Azure VM 中使用 SSD 来存储 SQL Server TempDB 和缓冲池扩展）
* [Azure 虚拟机中 SQL Server 的性能最佳实践](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance)

## <a name="failback-from-azure-to-an-on-premises-host"></a>故障回复（从 Azure 到本地主机）
现在，让我们了解从 Azure 故障转移到本地 Hyper-V 主机时会复制的磁盘。 不会复制在 Azure 中手动创建的磁盘。 例如，如果在 Azure 虚拟机中故障转移三个磁盘并直接创建两个磁盘，则只会故障回复完成故障转移的三个磁盘。 不能包括在故障回复过程中或从本地到 Azure 的反向保护过程中手动创建的磁盘。 也不会将临时存储磁盘复制到本地主机。

### <a name="failback-to-original-location-recovery"></a>故障回复到原始位置恢复

在前面的示例中，Azure 虚拟机磁盘配置如下所示：

**来宾操作系统磁盘编号** | **驱动器号** | **磁盘上的数据类型**
--- | --- | ---
DISK0 | C:\ | 操作系统磁盘
Disk1 | E:\ | 临时存储</br /> </br />Azure 添加此磁盘并分配第一个可用的驱动器号。
Disk2 | D:\ | SQL 系统数据库和用户数据库 1
Disk3 | G:\ | 用户数据库 2

故障回复到原始位置后，故障回复虚拟机磁盘配置与 Hyper-V 的原始虚拟机磁盘配置仍然一样。 从 Hyper-V 站点故障回复到 Azure 时排除的磁盘在故障回复虚拟机中可用。

完成从 Azure 到本地 Hyper-V 的计划故障转移后，Hyper-V 虚拟机（原始位置）上的磁盘如下所示：

**磁盘名称** | **来宾操作系统磁盘编号** | **驱动器号** | **磁盘上的数据类型**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 |   C:\ | 操作系统磁盘
DB-Disk1 | Disk1 | D:\ | SQL 系统数据库和用户数据库 1
DB Disk2（排除的磁盘） | Disk2 | E:\ | 临时文件
DB-Disk3（排除的磁盘） | Disk3 | F:\ | SQL tempdb 数据库（文件夹路径 (F:\MSSQL\Data\)）
DB-Disk4 | Disk4 | G:\ | 用户数据库 2

## <a name="example-2-exclude-the-paging-file-pagefilesys-disk"></a>示例 2：排除页面文件 (pagefile.sys) 磁盘

假设有一台虚拟机，其中的页面文件磁盘可以被排除。
存在两种情况。

### <a name="case-1-the-paging-file-is-configured-on-the-d-drive"></a>情况 1：在 D: 驱动器上配置页面文件
以下为磁盘配置：

**磁盘名称** | **来宾操作系统磁盘编号** | **驱动器号** | **磁盘上的数据类型**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | 操作系统磁盘
DB-Disk1（已从保护中排除该磁盘） | Disk1 | D:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | 用户数据 1
DB-Disk3 | Disk3 | F:\ | 用户数据 2

以下为源虚拟机上的页面文件设置：

![源虚拟机上的页面文件设置](./media/hyper-v-exclude-disk/pagefile-on-d-drive-sourceVM.png)

将虚拟机从 Hyper-V 故障转移到 Azure 以后，Azure 虚拟机上的磁盘如下所示：

**磁盘名称** | **来宾操作系统磁盘编号** | **驱动器号** | **磁盘上的数据类型**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | 操作系统磁盘
DB-Disk1 | Disk1 | D:\ | 临时存储</br /> </br />pagefile.sys
DB-Disk2 | Disk2 | E:\ | 用户数据 1
DB-Disk3 | Disk3 | F:\ | 用户数据 2

Disk1 (D:) 已排除，因此 D: 是可用列表中的首个驱动器号。 Azure 将 D: 分配给临时存储卷。 D: 在 Azure 虚拟机上可用，因此虚拟机的页面文件设置仍然相同。

以下为 Azure 虚拟机上的页面文件设置：

![Azure 虚拟机上的页面文件设置](./media/hyper-v-exclude-disk/pagefile-on-Azure-vm-after-failover.png)

### <a name="case-2-the-paging-file-is-configured-on-another-drive-other-than-d-drive"></a>情况 2：在另一驱动器（D: 以外的驱动器）上配置页面文件

以下为源虚拟机磁盘配置：

**磁盘名称** | **来宾操作系统磁盘编号** | **驱动器号** | **磁盘上的数据类型**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | 操作系统磁盘
DB-Disk1（已从保护中排除该磁盘） | Disk1 | G:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | 用户数据 1
DB-Disk3 | Disk3 | F:\ | 用户数据 2

以下为本地虚拟机上的页面文件设置：

![本地虚拟机上的页面文件设置](./media/hyper-v-exclude-disk/pagefile-on-g-drive-sourceVM.png)

将虚拟机从 Hyper-V 故障转移到 Azure 以后，Azure 虚拟机上的磁盘如下所示：

**磁盘名称**| **来宾操作系统磁盘编号**| **驱动器号** | **磁盘上的数据类型**
--- | --- | --- | ---
DB-Disk0-OS | DISK0  |C:\ |操作系统磁盘
DB-Disk1 | Disk1 | D:\ | 临时存储</br /> </br />pagefile.sys
DB-Disk2 | Disk2 | E:\ | 用户数据 1
DB-Disk3 | Disk3 | F:\ | 用户数据 2

由于 D: 是可用列表中的首个驱动器号，Azure 将 D: 分配给临时存储卷。 对于所有复制的磁盘，驱动器号保持不变。 G: 磁盘不可用，因此系统会使用 C: 驱动器存储页面文件。

以下为 Azure 虚拟机上的页面文件设置：

![Azure 虚拟机上的页面文件设置](./media/hyper-v-exclude-disk/pagefile-on-Azure-vm-after-failover-2.png)

## <a name="next-steps"></a>后续步骤
设置并运行部署后，请 [详细了解](site-recovery-failover.md) 不同类型的故障转移。
