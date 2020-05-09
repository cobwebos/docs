---
title: 排除使用 Azure Site Recovery 复制的磁盘
description: 如何排除使用 Azure Site Recovery 复制到 Azure 的磁盘。
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: abecc19cac57a4a95d01b7a7ec076259088b101b
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2020
ms.locfileid: "82900283"
---
# <a name="exclude-disks-from-disaster-recovery"></a>排除磁盘的灾难恢复

本文介绍在使用 [Azure Site Recovery](site-recovery-overview.md) 从本地灾难恢复到 Azure 期间，如何排除磁盘的复制。 出于多种原因，你可能需要排除磁盘的复制：

- 避免复制已排除磁盘上改动的非重要数据。
- 优化消耗的复制带宽或目标端资源。
- 不复制不需要的数据，以此节省存储和网络资源。
- Azure VM 已达到 Site Recovery 复制限制。


## <a name="supported-scenarios"></a>支持的方案

可根据下表中汇总的信息排除磁盘的复制。

**Azure 到 Azure** | **VMware 到 Azure** | **Hyper-V 到 Azure** 
--- | --- | ---
是 | 是 | 是 

## <a name="exclude-limitations"></a>排除限制

**限制** | **Azure VM** | **VMware VM** | **Hyper-V VM**
--- | --- | ---
**磁盘类型** | 可以从复制中排除基本磁盘。<br/><br/> 不能排除操作系统磁盘或动态磁盘。 默认会排除临时磁盘。 | 可以从复制中排除基本磁盘。<br/><br/> 不能排除操作系统磁盘或动态磁盘。 | 可以从复制中排除基本磁盘。<br/><br/> 不能排除操作系统磁盘。 建议不要排除动态磁盘。 Site Recovery 无法识别来宾 VM 上的 VHS 是基本磁盘还是动态磁盘。 如果未排除所有依赖性动态卷磁盘，则受保护的动态磁盘就会成为故障转移 VM 上的故障磁盘，且该磁盘上的数据无法访问。
**复制磁盘** | 不能排除正在复制的磁盘。<br/><br/> 禁用再重新启用 VM 的复制。 |  不能排除正在复制的磁盘。 |  不能排除正在复制的磁盘。
**移动服务 (VMware)** | 不相关 | 只能排除已装有移动服务的 VM 上的磁盘。<br/><br/> 这意味着，必须在要排除其磁盘的 VM 上手动安装移动服务。不能使用推送安装机制，因为此机制只会在启用复制后才安装移动服务。 | 不相关。
**添加/删除** | 可以在具有托管磁盘的已启用复制的 Azure Vm 上添加托管磁盘。 不能删除启用了复制的 Azure Vm 上的磁盘。 | 启用复制后，无法添加或删除磁盘。 禁用再重新启用复制即可添加磁盘。 | 启用复制后，无法添加或删除磁盘。 禁用再重新启用复制。
**故障转移** | 如果某个应用需要已排除的某个磁盘，则在故障转移后，需要手动创建该磁盘，使复制的应用可以运行。<br/><br/> 或者，可以通过将 Azure 自动化集成到恢复计划中，在 VM 故障转移期间创建磁盘。 | 如果排除应用所需的某个磁盘，请在故障转移后在 Azure 中手动创建该磁盘。 | 如果排除应用所需的某个磁盘，请在故障转移后在 Azure 中手动创建该磁盘。
**本地故障回复 - 手动创建的磁盘** | 不相关 | **Windows vm**：在 Azure 中手动创建的磁盘不会故障回复。 例如，如果在 Azure VM 中故障转移三个磁盘并直接创建两个磁盘，则只会故障回复完成故障转移的三个磁盘。<br/><br/> **Linux vm**：在 Azure 中手动创建的磁盘将故障回复。 例如，如果要故障转移三个磁盘，并在 Azure VM 上创建两个磁盘，则会故障回复所有五个磁盘。 无法从故障回复中排除手动创建的磁盘。 | 在 Azure 中手动创建的磁盘不会故障回复。 例如，如果在 Azure VM 中故障转移三个磁盘并直接创建两个磁盘，则只会故障回复已故障转移的三个磁盘。
**本地故障回复 - 已排除的磁盘** | 不相关 | 如果故障回复到原始计算机，则故障回复 VM 磁盘配置不包含已排除的磁盘。 在从 VMware 到 Azure 的复制中排除的磁盘在故障回复 VM 中不可用。 | 故障回复到原始 Hyper-V 位置后，故障回复 VM 磁盘配置与原始源 VM 磁盘的配置保持相同。 在从 Hyper-V 站点到 Azure 的复制中排除的磁盘在故障回复 VM 中可用。



## <a name="typical-scenarios"></a>典型方案

很适合排除的数据改动示例包括写入页面文件 (pagefile.sys)，以及写入 Microsoft SQL Server 的 tempdb 文件。 页面文件和 tempdb 文件可注册大量改动，具体取决于工作负荷和存储子系统。 将此类数据复制到 Azure 会消耗大量资源。

- 若要优化具有单个虚拟磁盘（其中包含操作系统和页面文件）的 VM 的复制，可以：
    1. 将单个虚拟磁盘拆分成两个虚拟磁盘。 一个虚拟磁盘包含操作系统，另一个包含页面文件。
    2. 从复制中排除页面文件磁盘。

- 若要优化既包含 Microsoft SQL Server tempdb 文件，又包含系统数据库文件的磁盘的复制，可以：
    1. 在两个不同的磁盘上保存系统数据库和 tempdb。
    2. 从复制中排除 tempdb 磁盘。

## <a name="example-1-exclude-the-sql-server-tempdb-disk"></a>示例 1：排除 SQL Server tempdb 磁盘

让我们看看如何处理源 SQL Server Windows VM **SalesDB*** 的磁盘排除、故障转移和故障转移，我们将排除此 VM 的 tempdb。 

### <a name="exclude-disks-from-replication"></a>从复制中排除磁盘

源 Windows VM SalesDB 包含这些磁盘。

**磁盘名称** | **来宾操作系统磁盘** | **驱动器号** | **磁盘数据类型**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | 操作系统磁盘。
DB-Disk1| Disk1 | D:\ | SQL 系统数据库和用户数据库 1。
DB-Disk2（已从保护中排除该磁盘） | Disk2 | E:\ | 临时文件。
DB-Disk3（已从保护中排除该磁盘） | Disk3 | F:\ | SQL tempdb 数据库。<br/><br/> 文件夹路径 - F:\MSSQL\Data\。 在故障转移之前记下文件夹路径。
DB-Disk4 | Disk4 |G:\ | 用户数据库 2

1. 为 SalesDB VM 启用复制。
2. 从复制中排除 Disk2 和 Disk3，因为这些磁盘上的数据改动是暂时性的。 


### <a name="handle-disks-during-failover"></a>在故障转移期间处理磁盘

由于不会复制磁盘，在故障转移到 Azure 时，这些磁盘不会在故障转移后创建的 Azure VM 中存在。 该 Azure VM 包含下表中汇总的磁盘。

**来宾操作系统磁盘** | **驱动器号** | **磁盘数据类型**
--- | --- | ---
Disk0 | C:\ | 操作系统磁盘。
Disk1 | E:\ | 临时存储<br/><br/>Azure 将添加此磁盘。 由于从复制中排除了 Disk2 和 Disk3，E: 是可用列表中的第一个驱动器号。 Azure 将 E: 分配给临时存储卷。 复制的磁盘的其他驱动器号保持相同。
Disk2 | D:\ | SQL 系统数据库和用户数据库 1
Disk3 | G:\ | 用户数据库 2

在本示例中，由于 Disk3（SQL tempdb 磁盘）已从复制中排除并且在 Azure VM 上不可用，因此 SQL 服务处于停止状态，需要 F:\MSSQL\Data 路径。 可通过多种方式创建此路径： 

- 故障转移后添加新磁盘，并分配 tempdb 文件夹路径。
- 使用现有临时存储磁盘作为 tempdb 文件夹路径。

#### <a name="add-a-new-disk-after-failover"></a>故障转移后添加新磁盘

1. 在故障转移前，记下 SQL tempdb.mdf 和 tempdb.ldf 的路径。
2. 在 Azure 门户中，将新磁盘添加到故障转移 Azure VM。 该磁盘的大小应等于（或大于）源 SQL tempdb 磁盘 (Disk3)。
3. 登录到 Azure VM。
4. 在磁盘管理 (diskmgmt.msc) 控制台中，初始化并格式化新添加的磁盘。
5. 分配 SQL tempdb 磁盘 (F:) 所用的相同驱动器号。
6. 在 F: 卷上创建 tempdb 文件夹 (F:\MSSQL\Data)。
7. 从服务控制台启动 SQL 服务。

#### <a name="use-an-existing-temporary-storage-disk"></a>使用现有的临时存储磁盘 

1. 打开命令提示。
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

现在，让我们看看如何在故障回复到原始本地位置时处理 VMware VM 上的磁盘。

- **在 azure 中创建的磁盘**：由于我们的示例使用 Windows VM，因此当你故障回复或重新保护 VM 时，你在 azure 中手动创建的磁盘不会复制回你的站点。
- **Azure 中的临时存储磁盘**：不会将临时存储磁盘复制回本地主机。
- **排除的磁盘**：故障回复后，不能在本地 VM 上使用从 VMware 到 Azure 的复制中排除的磁盘。

在将 VMware VM 故障回复到原始位置之前，Azure VM 磁盘设置如下。

**来宾操作系统磁盘** | **驱动器号** | **磁盘数据类型**
--- | --- | ---
Disk0 | C:\ | 操作系统磁盘。
Disk1 | E:\ | 临时存储。
Disk2 | D:\ | SQL 系统数据库和用户数据库 1。
Disk3 | G:\ | 用户数据库 2。

故障回复后，原始位置中的 VMware VM 包含下表中汇总的磁盘。

**来宾操作系统磁盘** | **驱动器号** | **磁盘数据类型**
--- | --- | ---
Disk0 | C:\ | 操作系统磁盘。
Disk1 | D:\ | SQL 系统数据库和用户数据库 1。
Disk2 | G:\ | 用户数据库 2。


### <a name="hyper-v-vms-disks-during-failback-to-original-location"></a>Hyper-v Vm：故障回复到原始位置期间的磁盘

现在，让我们看看如何在故障回复到原始本地位置时处理 Hyper-V VM 上的磁盘。

- **在 azure 中创建的磁盘**：在故障回复或重新保护 VM 时，在 azure 中手动创建的磁盘不会复制回你的站点。
- **Azure 中的临时存储磁盘**：不会将临时存储磁盘复制回本地主机。
- **排除的磁盘**：故障回复后，vm 磁盘配置与原始 vm 磁盘配置相同。 在从 Hyper-V 到 Azure 的复制中排除的磁盘在故障回复 VM 中可用。

在将 Hyper-V VM 故障回复到原始位置之前，Azure VM 磁盘设置如下。

**来宾操作系统磁盘** | **驱动器号** | **磁盘数据类型**
--- | --- | ---
Disk0 | C:\ | 操作系统磁盘。
Disk1 | E:\ | 临时存储。
Disk2 | D:\ | SQL 系统数据库和用户数据库 1。
Disk3 | G:\ | 用户数据库 2。

完成从 Azure 到本地 Hyper-V 的计划内故障转移（故障回复）后，原始位置中的 Hyper-V VM 包含下表中汇总的磁盘。

**磁盘名称** | **来宾 OS 磁盘编号** | **驱动器号** | **磁盘数据类型**
 --- | --- | --- | ---
DB-Disk0-OS | Disk0 |   C:\ | 操作系统磁盘。
DB-Disk1 | Disk1 | D:\ | SQL 系统数据库和用户数据库 1。
DB Disk2（排除的磁盘） | Disk2 | E:\ | 临时文件。
DB-Disk3（排除的磁盘） | Disk3 | F:\ | SQL tempdb 数据库<br/><br/> 文件夹路径 - (F:\MSSQL\Data\)。
DB-Disk4 | Disk4 | G:\ | 用户数据库 2


## <a name="example-2-exclude-the-paging-file-disk"></a>示例2：排除页面文件磁盘

让我们看看如何处理某个源 Windows VM 的磁盘排除、故障转移和故障转移，我们想要排除 D 驱动器和备用驱动器上的 pagefile.sys 文件磁盘。


### <a name="paging-file-on-the-d-drive"></a>D 驱动器上的页面文件

源 VM 包含这些磁盘。

**磁盘名称** | **来宾操作系统磁盘** | **驱动器号** | **磁盘数据类型**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | 操作系统磁盘
DB-Disk1（从复制中排除） | Disk1 | D:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | 用户数据 1
DB-Disk3 | Disk3 | F:\ | 用户数据 2

源 VM 上的页面文件设置如下：

![源虚拟机上的页面文件设置](./media/exclude-disks-replication/pagefile-d-drive-source-vm.png)

1. 为 VM 启用复制。
2. 从复制中排除 DB-Disk1。

#### <a name="disks-after-failover"></a>故障转移后的磁盘

故障转移后，Azure VM 包含下表中汇总的磁盘。

**磁盘名称** | **来宾操作系统磁盘编号** | **驱动器号** | **磁盘上的数据类型**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | 操作系统磁盘
DB-Disk1 | Disk1 | D:\ | 临时存储/pagefile.sys <br/><br/> 由于 DB-Disk1 (D:) 已排除，因此 D: 是可用列表中的首个驱动器号。<br/><br/> Azure 将 D: 分配给临时存储卷。<br/><br/> 由于 D: 可用，因此 VM 页面文件设置保持相同。
DB-Disk2 | Disk2 | E:\ | 用户数据 1
DB-Disk3 | Disk3 | F:\ | 用户数据 2

Azure VM 上的页面文件设置如下：

![Azure 虚拟机上的页面文件设置](./media/exclude-disks-replication/pagefile-azure-vm-after-failover.png)

### <a name="paging-file-on-another-drive-not-d"></a>另一驱动器（非 D:）上的页面文件

让我们看看页面文件不在 D 驱动器上的示例。  

源 VM 包含这些磁盘。

**磁盘名称** | **来宾操作系统磁盘** | **驱动器号** | **磁盘数据类型**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | 操作系统磁盘
DB-Disk1（从复制中排除） | Disk1 | G:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | 用户数据 1
DB-Disk3 | Disk3 | F:\ | 用户数据 2

本地 VM 上的页面文件设置如下：

![本地虚拟机上的页面文件设置](./media/exclude-disks-replication/pagefile-g-drive-source-vm.png)

1. 为 VM 启用复制。
2. 从复制中排除 DB-Disk1。

#### <a name="disks-after-failover"></a>故障转移后的磁盘

故障转移后，Azure VM 包含下表中汇总的磁盘。

**磁盘名称** | **来宾 OS 磁盘编号** | **驱动器号** | **磁盘数据类型**
--- | --- | --- | ---
DB-Disk0-OS | Disk0  |C:\ | 操作系统磁盘
DB-Disk1 | Disk1 | D:\ | 临时存储<br/><br/> 由于 D: 是可用列表中的首个驱动器号，Azure 将 D: 分配给临时存储卷。<br/><br/> 对于所有复制的磁盘，驱动器号保持不变。<br/><br/> 由于 G: 磁盘不可用，因此系统会使用 C: 驱动器存储页面文件。
DB-Disk2 | Disk2 | E:\ | 用户数据 1
DB-Disk3 | Disk3 | F:\ | 用户数据 2

Azure VM 上的页面文件设置如下：

![Azure 虚拟机上的页面文件设置](./media/exclude-disks-replication/pagefile-azure-vm-after-failover-2.png)


## <a name="next-steps"></a>后续步骤

- 详细了解临时存储磁盘的指导原则：
    - [了解](https://blogs.technet.microsoft.com/dataplatforminsider/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/)如何在 Azure VM 中使用 SSD 来存储 SQL Server TempDB 和缓冲池扩展
    - [查看](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance) Azure VM 中 SQL Server 的性能最佳做法。
- 设置并运行部署后，请 [详细了解](failover-failback-overview.md) 不同类型的故障转移。

