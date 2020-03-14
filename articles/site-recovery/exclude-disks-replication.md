---
title: 从包含 Azure Site Recovery 的复制中排除磁盘
description: 如何在 Azure Site Recovery 中排除磁盘与 Azure 的复制。
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 57bf06f0fde85714530c06cbd008db08de7460d2
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79281842"
---
# <a name="exclude-disks-from-disaster-recovery"></a>从灾难恢复中排除磁盘

本文介绍如何在从本地到 Azure 的灾难恢复期间从复制中排除磁盘，并提供[Azure Site Recovery](site-recovery-overview.md)。 出于多种原因，你可能会从复制中排除磁盘：

- 因此不会复制排除的磁盘上改动的不重要数据。
- 优化已使用的复制带宽或目标端资源。
- 如果不复制不需要的数据，可以节省存储和网络资源。
- Azure Vm 已达到 Site Recovery 复制限制。


## <a name="supported-scenarios"></a>支持的方案

可以从复制中排除磁盘，如表中所汇总。

**Azure 到 Azure** | **VMware 到 Azure** | **Hyper-V 到 Azure** 
--- | --- | ---
是（使用 PowerShell） | 是 | 是 

## <a name="exclude-limitations"></a>排除限制

**限制** | **Azure VM** | **VMware VM** | **Hyper-V VM**
--- | --- | ---
**磁盘类型** | 可以从复制中排除基本磁盘。<br/><br/> 不能排除操作系统磁盘或动态磁盘。 默认情况下会排除临时磁盘。 | 可以从复制中排除基本磁盘。<br/><br/> 不能排除操作系统磁盘或动态磁盘。 | 可以从复制中排除基本磁盘。<br/><br/> 不能排除操作系统磁盘。 建议不要排除动态磁盘。 Site Recovery 无法识别来宾 VM 中的基本或动态的 VHS。 如果未排除所有相关动态卷磁盘，则受保护的动态磁盘会成为故障转移 VM 上的故障磁盘，且无法访问该磁盘上的数据。
**复制磁盘** | 不能排除正在复制的磁盘。<br/><br/> 禁用和重新启用 VM 复制。 |  不能排除正在复制的磁盘。 |  不能排除正在复制的磁盘。
**移动服务（VMware）** | 不相关 | 你只能在安装了移动服务的 Vm 上排除磁盘。<br/><br/> 这意味着你必须在要排除磁盘的 Vm 上手动安装移动服务。不能使用推送安装机制，因为它仅在启用复制后才安装移动服务。 | 不相关。
**添加/删除** | 可以在具有托管磁盘的 Azure Vm 上添加和删除磁盘。 | 启用复制后，无法添加或删除磁盘。 禁用并重新启用复制，以添加磁盘。 | 启用复制后，无法添加或删除磁盘。 禁用再重新启用复制。
**故障转移** | 如果某个应用需要你排除的磁盘，则在故障转移后，你需要手动创建该磁盘，以便复制的应用可以运行。<br/><br/> 或者，你可以通过将 Azure 自动化集成到恢复计划中，在 VM 故障转移期间创建磁盘。 | 如果排除应用所需的磁盘，请在故障转移后在 Azure 中手动创建该磁盘。 | 如果排除应用所需的磁盘，请在故障转移后在 Azure 中手动创建该磁盘。
**本地故障回复-手动创建的磁盘** | 不相关 | **Windows vm**：在 Azure 中手动创建的磁盘不会故障回复。 例如，如果在 Azure VM 上故障转移三个磁盘并直接创建两个磁盘，则只会故障回复三个故障转移的磁盘。<br/><br/> **Linux vm**：在 Azure 中手动创建的磁盘将故障回复。 例如，如果在 Azure VM 上故障转移三个磁盘并创建两个磁盘，则将故障回复所有五个磁盘。 无法从故障回复中排除手动创建的磁盘。 | 在 Azure 中手动创建的磁盘不会故障回复。 例如，如果在 Azure VM 上故障转移三个磁盘并直接创建两个磁盘，则只有三个故障转移的磁盘将故障回复。
**本地故障回复-排除的磁盘** | 不相关 | 如果故障回复到原始计算机，故障回复 VM 磁盘配置不包括已排除的磁盘。 从 VMware 到 Azure 的复制中排除的磁盘在故障回复 VM 中不可用。 | 故障回复到原始 Hyper-v 位置时，故障回复 VM 磁盘配置与原始源 VM 磁盘的配置保持一致。 从 Hyper-v 站点到 Azure 的复制中排除的磁盘在故障回复 VM 中可用。



## <a name="typical-scenarios"></a>典型方案

很适合进行排除的数据改动的示例包括写入分页文件（sys.databases），并写入到 Microsoft SQL Server 的 tempdb 文件中。 根据工作负荷和存储子系统，分页和 tempdb 文件可以注册大量的变动。 将此类型的数据复制到 Azure 会占用大量资源。

- 若要为包含操作系统和页面文件的单个虚拟磁盘的 VM 优化复制，可以执行以下操作：
    1. 将单个虚拟磁盘拆分成两个虚拟磁盘。 一个虚拟磁盘包含操作系统，另一个包含页面文件。
    2. 从复制中排除页面文件磁盘。

- 若要优化同时包含 Microsoft SQL Server tempdb 文件和系统数据库文件的磁盘的复制，可以执行以下操作：
    1. 在两个不同的磁盘上保存系统数据库和 tempdb。
    2. 从复制中排除 tempdb 磁盘。

## <a name="example-1-exclude-the-sql-server-tempdb-disk"></a>示例 1：排除 SQL Server tempdb 磁盘

让我们看看如何处理 SQL Server Windows VM 的源的磁盘排除、故障转移和故障转移-* * SalesDB * * *，以使其排除 tempdb。 

### <a name="exclude-disks-from-replication"></a>从复制中排除磁盘

源 Windows VM SalesDB 上有这些磁盘。

**磁盘名称** | **来宾操作系统磁盘** | **驱动器号** | **磁盘数据类型**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | 操作系统磁盘。
DB-Disk1| Disk1 | D:\ | SQL 系统数据库和用户 Database1。
DB-Disk2（已从保护中排除该磁盘） | Disk2 | E:\ | 临时文件。
DB-Disk3（已从保护中排除该磁盘） | Disk3 | F:\ | SQL tempdb 数据库。<br/><br/> 文件夹路径-F:\MSSQL\Data\。 在故障转移之前记下文件夹路径。
DB-Disk4 | Disk4 |G:\ | 用户数据库 2

1. 我们为 SalesDB VM 启用复制。
2. 我们从复制中排除了 Disk2 和 Disk3，因为这些磁盘上的数据改动是临时性的。 


### <a name="handle-disks-during-failover"></a>在故障转移期间处理磁盘

由于未复制磁盘，因此当你故障转移到 Azure 时，这些磁盘不会存在于故障转移后创建的 Azure VM 中。 此表中汇总了 Azure VM 的磁盘。

**来宾操作系统磁盘** | **驱动器号** | **磁盘数据类型**
--- | --- | ---
Disk0 | C:\ | 操作系统磁盘。
Disk1 | E:\ | 临时存储区<br/><br/>Azure 添加了此磁盘。 由于从复制中排除了 Disk2 和 Disk3，因此 E：是可用列表中的第一个驱动器号。 Azure 将 E: 分配给临时存储卷。 复制磁盘的其他驱动器号保持不变。
Disk2 | D:\ | SQL 系统数据库和用户数据库 1
Disk3 | G:\ | 用户数据库 2

在我们的示例中，由于 Disk3 已从复制中排除，并且在 Azure VM 上不可用，因此 SQL 服务处于停止状态，需要 F:\MSSQL\Data 路径。 可以通过以下几种方式创建此路径： 

- 在故障转移后添加新磁盘并分配 tempdb 文件夹路径。
- 使用现有临时存储磁盘作为 tempdb 文件夹路径。

#### <a name="add-a-new-disk-after-failover"></a>故障转移后添加新磁盘

1. 在故障转移前，记下 SQL tempdb.mdf 和 tempdb.ldf 的路径。
2. 在 Azure 门户中，将新磁盘添加到故障转移 Azure VM。 磁盘大小应等于或大于源 SQL tempdb 磁盘（Disk3）。
3. 登录到 Azure VM。
4. 在磁盘管理 (diskmgmt.msc) 控制台中，初始化并格式化新添加的磁盘。
5. 分配 SQL tempdb 磁盘使用的相同驱动器号（F：）
6. 在 F: 卷上创建 tempdb 文件夹 (F:\MSSQL\Data)。
7. 从服务控制台启动 SQL 服务。

#### <a name="use-an-existing-temporary-storage-disk"></a>使用现有的临时存储磁盘 

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



### <a name="vmware-vms-disks-during-failback-to-original-location"></a>VMware Vm：在故障回复到原始位置期间的磁盘

现在，让我们看看如何在故障回复到原始本地位置后，如何处理 VMware Vm 上的磁盘。

- **在 azure 中创建的磁盘**：由于我们的示例使用 Windows VM，因此当你故障回复或重新保护 VM 时，你在 azure 中手动创建的磁盘不会复制回你的站点。
- **Azure 中的临时存储磁盘**：不会将临时存储磁盘复制回本地主机。
- **排除的磁盘**：故障回复后，不能在本地 VM 上使用从 VMware 到 Azure 的复制中排除的磁盘。

将 VMware Vm 故障回复到原始位置之前，Azure VM 磁盘设置如下所示。

**来宾操作系统磁盘** | **驱动器号** | **磁盘数据类型**
--- | --- | ---
Disk0 | C:\ | 操作系统磁盘。
Disk1 | E:\ | 临时存储。
Disk2 | D:\ | SQL 系统数据库和用户 Database1。
Disk3 | G:\ | User Database2。

故障回复后，原位置的 VMware VM 将在表中汇总磁盘。

**来宾操作系统磁盘** | **驱动器号** | **磁盘数据类型**
--- | --- | ---
Disk0 | C:\ | 操作系统磁盘。
Disk1 | D:\ | SQL 系统数据库和用户 Database1。
Disk2 | G:\ | User Database2。


### <a name="hyper-v-vms-disks-during-failback-to-original-location"></a>Hyper-v Vm：故障回复到原始位置期间的磁盘

现在，让我们看看如何在故障回复到原始本地位置后，如何处理 Hyper-v Vm 上的磁盘。

- **在 azure 中创建的磁盘**：在故障回复或重新保护 VM 时，在 azure 中手动创建的磁盘不会复制回你的站点。
- **Azure 中的临时存储磁盘**：不会将临时存储磁盘复制回本地主机。
- **排除的磁盘**：故障回复后，vm 磁盘配置与原始 vm 磁盘配置相同。 从 Hyper-v 到 Azure 的复制中排除的磁盘在故障回复 VM 中可用。

在将 Hyper-v Vm 故障回复到原始位置之前，Azure VM 磁盘设置如下所示。

**来宾操作系统磁盘** | **驱动器号** | **磁盘数据类型**
--- | --- | ---
Disk0 | C:\ | 操作系统磁盘。
Disk1 | E:\ | 临时存储。
Disk2 | D:\ | SQL 系统数据库和用户 Database1。
Disk3 | G:\ | User Database2。

从 Azure 到本地 Hyper-v 的计划故障转移（故障回复）后，原位置中的 Hyper-v VM 将在表中汇总磁盘。

**磁盘名称** | **来宾 OS 磁盘编号** | **驱动器号** | **磁盘数据类型**
 --- | --- | --- | ---
DB-Disk0-OS | Disk0 |   C:\ | 操作系统磁盘。
DB-Disk1 | Disk1 | D:\ | SQL 系统数据库和用户 Database1。
DB Disk2（排除的磁盘） | Disk2 | E:\ | 临时文件。
DB-Disk3（排除的磁盘） | Disk3 | F:\ | SQL tempdb 数据库<br/><br/> 文件夹路径（F:\MSSQL\Data\)。
DB-Disk4 | Disk4 | G:\ | 用户数据库 2


## <a name="example-2-exclude-the-paging-file-disk"></a>示例2：排除页面文件磁盘

让我们看看如何处理源 Windows VM 的磁盘排除、故障转移和故障转移，我们想要在 D 驱动器和备用驱动器上排除文件系统文件磁盘。


### <a name="paging-file-on-the-d-drive"></a>D 驱动器上的页面文件

源 VM 上已有这些磁盘。

**磁盘名称** | **来宾操作系统磁盘** | **驱动器号** | **磁盘数据类型**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | 操作系统磁盘
Disk1 （从复制中排除） | Disk1 | D:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | 用户数据 1
DB-Disk3 | Disk3 | F:\ | 用户数据 2

源 VM 上的页面文件设置如下所示：

![源虚拟机上的页面文件设置](./media/exclude-disks-replication/pagefile-d-drive-source-vm.png)

1. 为 VM 启用复制。
2. 我们从复制中排除 Disk1。

#### <a name="disks-after-failover"></a>故障转移后的磁盘

故障转移后，Azure VM 的磁盘汇总在表中。

**磁盘名称** | **来宾操作系统磁盘编号** | **驱动器号** | **磁盘上的数据类型**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | 操作系统磁盘
DB-Disk1 | Disk1 | D:\ | 临时存储/文件系统 <br/><br/> 因为 Disk1 （D：）已排除，D：是可用列表中的第一个驱动器号。<br/><br/> Azure 将 D: 分配给临时存储卷。<br/><br/> 由于 D：可用，因此 VM 分页文件设置保持不变。
DB-Disk2 | Disk2 | E:\ | 用户数据 1
DB-Disk3 | Disk3 | F:\ | 用户数据 2

Azure VM 上的页面文件设置如下所示：

![Azure 虚拟机上的页面文件设置](./media/exclude-disks-replication/pagefile-azure-vm-after-failover.png)

### <a name="paging-file-on-another-drive-not-d"></a>其他驱动器上的页面文件（不是 D：）

让我们看看页面文件不在 D 驱动器上的示例。  

源 VM 上已有这些磁盘。

**磁盘名称** | **来宾操作系统磁盘** | **驱动器号** | **磁盘数据类型**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | 操作系统磁盘
Disk1 （从复制中排除） | Disk1 | G:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | 用户数据 1
DB-Disk3 | Disk3 | F:\ | 用户数据 2

本地 VM 上的页面文件设置如下所示：

![本地虚拟机上的页面文件设置](./media/exclude-disks-replication/pagefile-g-drive-source-vm.png)

1. 为 VM 启用复制。
2. 我们从复制中排除 Disk1。

#### <a name="disks-after-failover"></a>故障转移后的磁盘

故障转移后，Azure VM 的磁盘汇总在表中。

**磁盘名称** | **来宾 OS 磁盘编号** | **驱动器号** | **磁盘数据类型**
--- | --- | --- | ---
DB-Disk0-OS | Disk0  |C:\ | 操作系统磁盘
DB-Disk1 | Disk1 | D:\ | 临时存储区<br/><br/> 由于 D: 是可用列表中的首个驱动器号，Azure 将 D: 分配给临时存储卷。<br/><br/> 对于所有复制的磁盘，驱动器号保持不变。<br/><br/> 由于 G：磁盘不可用，系统将使用 C：驱动器作为页面文件。
DB-Disk2 | Disk2 | E:\ | 用户数据 1
DB-Disk3 | Disk3 | F:\ | 用户数据 2

Azure VM 上的页面文件设置如下所示：

![Azure 虚拟机上的页面文件设置](./media/exclude-disks-replication/pagefile-azure-vm-after-failover-2.png)


## <a name="next-steps"></a>后续步骤

- 详细了解临时存储磁盘的指导原则：
    - [了解](https://blogs.technet.microsoft.com/dataplatforminsider/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/)如何在 Azure vm 中使用 ssd 来存储 SQL Server TempDB 和缓冲池扩展
    - [查看](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance)Azure vm 中 SQL Server 的性能最佳实践。
- 设置并运行部署后，请 [详细了解](failover-failback-overview.md) 不同类型的故障转移。

