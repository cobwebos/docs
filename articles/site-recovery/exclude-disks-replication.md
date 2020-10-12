---
title: 使用 Azure Site Recovery 将磁盘排除在复制之外
description: 如何使用 Azure Site Recovery 免于将磁盘复制到 Azure。
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 15989fbfd65f758eb777c5170c217aba8707e0be
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91333658"
---
# <a name="exclude-disks-from-disaster-recovery"></a>将磁盘排除在灾难恢复之外

本文介绍了如何使用 [Azure Site Recovery](site-recovery-overview.md) 在灾难恢复期间免于将磁盘从本地复制到 Azure。 出于多种原因，你可能要将磁盘排除在复制之外：

- 为了不复制已排除的磁盘上所改动的非重要数据。
- 为了优化已使用的复制带宽或目标端资源。
- 为了通过不复制非必需的数据来节省存储和网络资源。
- Azure VM 已达到 Site Recovery 复制限制。


## <a name="supported-scenarios"></a>支持的方案

可将磁盘排除在复制之外，如下图汇总所示。

**Azure 到 Azure** | **VMware 到 Azure** | **Hyper-V 到 Azure** | **物理服务器到 Azure**
--- | --- | --- | ---
是 | 是 | 是 | 是

## <a name="exclude-limitations"></a>排除限制

**限制** | **Azure VM** | **VMware VM** | **Hyper-V VM**
--- | --- | ---
**磁盘类型** | 可将基本磁盘排除在复制之外。<br/><br/> 不能排除操作系统磁盘或动态磁盘。 默认排除临时磁盘。 | 可将基本磁盘排除在复制之外。<br/><br/> 不能排除操作系统磁盘或动态磁盘。 | 可将基本磁盘排除在复制之外。<br/><br/> 不能排除操作系统磁盘。 建议不要排除动态磁盘。 Site Recovery 无法识别 VHS 在来宾 VM 中是基本磁盘还是动态磁盘。 如果未排除所有依赖的动态卷磁盘，则受保护的动态磁盘就会成为故障转移 VM 上的故障磁盘，且该磁盘上的数据不可访问。
**正在复制的磁盘** | 不能排除正在复制的磁盘。<br/><br/> 禁用再重新启用 VM 的复制。 |  不能排除正在复制的磁盘。 |  不能排除正在复制的磁盘。
**移动服务 (VMware)** | 不相关 | 只能排除 VM 上已安装移动服务的磁盘。<br/><br/> 这意味着你必须在要排除磁盘的 VM 上手动安装移动服务。无法使用推送安装机制，因为它仅在启用复制后才安装移动服务。 | 不相关。
**添加/删除** | 可在已启用复制且具有托管磁盘的 Azure VM 上添加托管磁盘。 不能在已启用复制的 Azure VM 上删除磁盘。 | 启用复制后，无法添加或删除磁盘。 要添加磁盘，请禁用再重新启用复制。 | 启用复制后，无法添加或删除磁盘。 先禁用，然后再重新启用复制。
**故障转移** | 如果某项应用需要你已排除的某个磁盘，则需要在故障转移后手动创建该磁盘，以便复制应用能够运行。<br/><br/> 或者，可在 VM 故障转移期间，通过将 Azure 自动化集成到恢复计划来创建磁盘。 | 如果排除某应用需要的磁盘，则要在故障转移后在 Azure 中手动创建该磁盘。 | 如果排除某应用需要的磁盘，则要在故障转移后在 Azure 中手动创建该磁盘。
**本地故障回复 - 手动创建的磁盘** | 不相关 | **Windows VM**：Azure 中手动创建的磁盘不会故障回复。 例如，如果故障转移 3 个磁盘并在 Azure VM 中直接创建 2 个磁盘，则之后仅对故障转移的 3 个磁盘进行故障回复。<br/><br/> **Linux VM**：Azure 中手动创建的磁盘会故障回复。 例如，如果故障转移 3 个磁盘，并在 Azure VM 中创建 2 个磁盘，则全部 5 个磁盘都将故障回复。 无法从故障回复中排除手动创建的磁盘。 | Azure 中手动创建的磁盘不会故障回复。 例如，如果故障转移 3 个磁盘并直接在 Azure VM 中创建 2 个磁盘，则将仅对故障转移的 3 个磁盘进行故障回复。
**本地故障回复 - 排除的磁盘** | 不相关 | 如果故障回复到原始计算机，则故障回复 VM 磁盘配置不包含排除的磁盘。 无法在故障回复 VM 上使用免于从 VMware 复制到 Azure 的磁盘。 | 故障回复到原始 Hyper-V 位置时，故障回复 VM 磁盘配置与原始源 VM 磁盘的配置保持一致。 可在故障回复 VM 上使用免于从 Hyper-V 站点复制到 Azure 的磁盘。



## <a name="typical-scenarios"></a>典型方案

数据改动导致出现大量排除首选项的示例包括导页面文件 (pagefile.sys) 的写入，以及到 Microsoft SQL Server 的 tempdb 文件的写入。 页面文件和 tempdb 文件可登记大量改动，具体取决于工作负载和存储子系统。 将此类型的数据复制到 Azure 需耗用大量资源。

- 如果 VM 具有同时包含操作系统和页面文件的单个虚拟磁盘，则可通过以下方法优化复制：
    1. 将单个虚拟磁盘拆分成两个虚拟磁盘。 一个虚拟磁盘包含操作系统，另一个包含页面文件。
    2. 从复制中排除页面文件磁盘。

- 如果磁盘同时包含 Microsoft SQL Server tempdb 文件和系统数据库文件，则可通过以下方法优化复制：
    1. 在两个不同的磁盘上保存系统数据库和 tempdb。
    2. 从复制中排除 tempdb 磁盘。

## <a name="example-1-exclude-the-sql-server-tempdb-disk"></a>示例 1：排除 SQL Server tempdb 磁盘

我们来看看如何处理源 SQL Server Windows VM (**SalesDB***) 的磁盘排除和故障转移操作，其中我们要排除 tempdb。 

### <a name="exclude-disks-from-replication"></a>将磁盘排除在复制之外

源 Windows VM SalesDB 上有以下磁盘。

**磁盘名称** | **来宾 OS 磁盘** | **驱动器号** | **磁盘数据类型**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | 操作系统磁盘。
DB-Disk1| Disk1 | D:\ | SQL 系统数据库和用户数据库 1。
DB-Disk2（已从保护中排除该磁盘） | Disk2 | E:\ | 临时文件。
DB-Disk3（已从保护中排除该磁盘） | Disk3 | F:\ | SQL tempdb 数据库。<br/><br/> 文件夹路径 - F:\MSSQL\Data\。 请在故障转移之前记下文件夹路径。
DB-Disk4 | Disk4 |G:\ | 用户数据库 2

1. 为 SalesDB VM 启用复制。
2. 将 Disk2 和 Disk3 排除在复制之外，因为这些磁盘上的数据改动是临时的。 


### <a name="handle-disks-during-failover"></a>在故障转移期间处理磁盘

由于未复制磁盘，因此在你故障转移到 Azure 时，故障转移后创建的 Azure VM 上没有这些磁盘。 Azure VM 具有下表汇总的磁盘。

**来宾 OS 磁盘** | **驱动器号** | **磁盘数据类型**
--- | --- | ---
Disk0 | C:\ | 操作系统磁盘。
Disk1 | E:\ | 临时存储<br/><br/>Azure 添加了该磁盘。 由于 Disk2 和 Disk3 已被排除在复制之外，因此 E: 是可用列表中的第一个驱动器号。 Azure 将 E: 分配给临时存储卷。 已复制磁盘的其他驱动器号保持不变。
Disk2 | D:\ | SQL 系统数据库和用户数据库 1
Disk3 | G:\ | 用户数据库 2

在我们的示例中，由于 Disk3（SQL 临时磁盘）已被排除在复制之外且在 Azure VM 上不可用，因此 SQL 服务的状态为“已停止”，它需要 F:\MSSQL\Data 路径。 可通过多种方式创建此路径： 

- 在故障转移后添加新磁盘，并分配 tempdb 文件夹路径。
- 使用现有临时存储磁盘作为 tempdb 文件夹路径。

#### <a name="add-a-new-disk-after-failover"></a>在故障转移后添加新磁盘

1. 在故障转移前，记下 SQL tempdb.mdf 和 tempdb.ldf 的路径。
2. 在 Azure 门户中，将新磁盘添加到故障转移 Azure VM。 磁盘大小应等于大于源 SQL tempdb 磁盘 (Disk3) 的大小。
3. 登录 Azure VM。
4. 在磁盘管理 (diskmgmt.msc) 控制台中，初始化并格式化新添加的磁盘。
5. 分配与 SQL tempdb 磁盘 (F:) 所用驱动器号相同的驱动器号
6. 在 F: 卷上创建 tempdb 文件夹 (F:\MSSQL\Data)。
7. 从服务控制台启动 SQL 服务。

#### <a name="use-an-existing-temporary-storage-disk"></a>使用现有的临时存储磁盘 

1. 打开命令提示符。
2. 通过命令提示符在恢复模式下运行 SQL Server。

    ```console
    Net start MSSQLSERVER /f / T3608
    ```

3. 运行以下 sqlcmd，将 tempdb 路径更改为新路径。

    ```sql
    sqlcmd -A -S SalesDB        **Use your SQL DBname**
    USE master;     
    GO      
    ALTER DATABASE tempdb       
    MODIFY FILE (NAME = tempdev, FILENAME = 'E:\MSSQL\tempdata\tempdb.mdf');
    GO      
    ALTER DATABASE tempdb       
    MODIFY FILE (NAME = templog, FILENAME = 'E:\MSSQL\tempdata\templog.ldf');       
    GO
    ```

4. 停止 Microsoft SQL Server 服务。

    ```console
    Net stop MSSQLSERVER
    ```

5. 启动 Microsoft SQL Server 服务。

    ```console
    Net start MSSQLSERVER
    ```

### <a name="vmware-vms-disks-during-failback-to-original-location"></a>VMware VM：在故障回复期间将磁盘恢复到原始位置

现在，让我们来看看在你故障回复到原始本地位置时如何处理 VMware VM 上的磁盘。

- **在 Azure 中创建的磁盘**：由于我们的示例使用的是 Windows VM，因此在故障回复或重新保护 VM 时，你在 Azure 中手动复制的磁盘不会复制回到你的站点。
- **Azure 中的临时存储磁盘**：临时存储磁盘不会复制回到本地主机。
- **排除的磁盘**：故障回复后，无法在本地 VM 上使用免于从 VMware 复制到 Azure 的磁盘。

在将 VMware VM 故障回复到原始位置时，Azure VM 磁盘设置如下所示。

**来宾 OS 磁盘** | **驱动器号** | **磁盘数据类型**
--- | --- | ---
Disk0 | C:\ | 操作系统磁盘。
Disk1 | E:\ | 临时存储。
Disk2 | D:\ | SQL 系统数据库和用户数据库 1。
Disk3 | G:\ | 用户数据库 2。

故障回复后，原始位置中的 VMware VM 具有下表汇总的磁盘。

**来宾 OS 磁盘** | **驱动器号** | **磁盘数据类型**
--- | --- | ---
Disk0 | C:\ | 操作系统磁盘。
Disk1 | D:\ | SQL 系统数据库和用户数据库 1。
Disk2 | G:\ | 用户数据库 2。


### <a name="hyper-v-vms-disks-during-failback-to-original-location"></a>Hyper-V VM：在故障回复期间将磁盘恢复到原始位置

现在，让我们来看看在你故障回复到原始本地位置时如何处理 Hyper-V VM 上的磁盘。

- **在 Azure 中创建的磁盘**：在故障回复或重新保护 VM 时，你在 Azure 中手动复制的磁盘不会复制回到你的站点。
- **Azure 中的临时存储磁盘**：临时存储磁盘不会复制回到本地主机。
- **排除的磁盘**：故障回复后，VM 磁盘配置与原始 VM 磁盘配置相同。 可在故障回复 VM 上使用免于从 Hyper-V 复制到 Azure 的磁盘。

在将 Hyper-V VM 故障回复到原始位置时，Azure VM 磁盘设置如下所示。

**来宾 OS 磁盘** | **驱动器号** | **磁盘数据类型**
--- | --- | ---
Disk0 | C:\ | 操作系统磁盘。
Disk1 | E:\ | 临时存储。
Disk2 | D:\ | SQL 系统数据库和用户数据库 1。
Disk3 | G:\ | 用户数据库 2。

在按计划从 Azure 故障转移（故障回复）到本地 Hyper-V 之后，原始位置中的 Hyper-V VM 具有下表汇总的磁盘。

**磁盘名称** | **来宾 OS 磁盘编号** | **驱动器号** | **磁盘数据类型**
 --- | --- | --- | ---
DB-Disk0-OS | Disk0 |   C:\ | 操作系统磁盘。
DB-Disk1 | Disk1 | D:\ | SQL 系统数据库和用户数据库 1。
DB Disk2（排除的磁盘） | Disk2 | E:\ | 临时文件。
DB-Disk3（排除的磁盘） | Disk3 | F:\ | SQL tempdb 数据库<br/><br/> 文件夹路径 (F:\MSSQL\Data\)。
DB-Disk4 | Disk4 | G:\ | 用户数据库 2


## <a name="example-2-exclude-the-paging-file-disk"></a>示例 2：排除页面文件磁盘

让我们看看如何处理源 Windows VM 的磁盘排除和故障转移，其中我们要排除 D 驱动器和备用驱动器上 pagefile.sys 文件磁盘。


### <a name="paging-file-on-the-d-drive"></a>D 驱动器上的页面文件

源 VM 上有以下磁盘。

**磁盘名称** | **来宾 OS 磁盘** | **驱动器号** | **磁盘数据类型**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | 操作系统磁盘
DB-Disk1（排除在复制之外） | Disk1 | D:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | 用户数据 1
DB-Disk3 | Disk3 | F:\ | 用户数据 2

源 VM 上的页面文件设置如下所示：

!["虚拟内存" 对话框的屏幕截图，其中显示了 "D：驱动器 [页面文件卷]" 行，其中突出显示了页面文件大小 (MB) 为3000-7000。](./media/exclude-disks-replication/pagefile-d-drive-source-vm.png)

1. 为 VM 启用复制。
2. 将 DB-Disk1 排除在复制之外。

#### <a name="disks-after-failover"></a>故障转移后的磁盘

故障转移后，Azure VM 具有下表汇总的磁盘。

**磁盘名称** | **来宾操作系统磁盘编号** | **驱动器号** | **磁盘上的数据类型**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | 操作系统磁盘
DB-Disk1 | Disk1 | D:\ | 临时存储/pagefile.sys <br/><br/> 由于 DB-Disk1 (D:) 已被排除，因此 D: 是可用列表中的第一个驱动器号。<br/><br/> Azure 将 D: 分配给临时存储卷。<br/><br/> 由于 D: 可用，VM 页面文件设置保持不变。
DB-Disk2 | Disk2 | E:\ | 用户数据 1
DB-Disk3 | Disk3 | F:\ | 用户数据 2

Azure VM 上的页面文件设置如下所示：

![Azure 虚拟机上的页面文件设置](./media/exclude-disks-replication/pagefile-azure-vm-after-failover.png)

### <a name="paging-file-on-another-drive-not-d"></a>其他驱动器（非 D:）上的页面文件

让我们来看一个示例，其中页面文件不位于 D 驱动器上。  

源 VM 上有以下磁盘。

**磁盘名称** | **来宾 OS 磁盘** | **驱动器号** | **磁盘数据类型**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | 操作系统磁盘
DB-Disk1（排除在复制之外） | Disk1 | G:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | 用户数据 1
DB-Disk3 | Disk3 | F:\ | 用户数据 2

本地 VM 上的页面文件设置如下所示：

![本地虚拟机上的页面文件设置](./media/exclude-disks-replication/pagefile-g-drive-source-vm.png)

1. 为 VM 启用复制。
2. 将 DB-Disk1 排除在复制之外。

#### <a name="disks-after-failover"></a>故障转移后的磁盘

故障转移后，Azure VM 具有下表汇总的磁盘。

**磁盘名称** | **来宾 OS 磁盘编号** | **驱动器号** | **磁盘数据类型**
--- | --- | --- | ---
DB-Disk0-OS | Disk0  |C:\ | 操作系统磁盘
DB-Disk1 | Disk1 | D:\ | 临时存储<br/><br/> 由于 D: 是可用列表中的首个驱动器号，Azure 将 D: 分配给临时存储卷。<br/><br/> 对于所有复制的磁盘，驱动器号保持不变。<br/><br/> 由于 G: 磁盘不可用，因此系统将使用 C: 驱动器存储页面文件。
DB-Disk2 | Disk2 | E:\ | 用户数据 1
DB-Disk3 | Disk3 | F:\ | 用户数据 2

Azure VM 上的页面文件设置如下所示：

![显示了 "系统管理" 的页面文件大小设置的 "虚拟内存" 对话框的屏幕截图。](./media/exclude-disks-replication/pagefile-azure-vm-after-failover-2.png)


## <a name="next-steps"></a>后续步骤

- 详细了解临时存储磁盘的指导原则：
    - [了解](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/)如何在 Azure VM 中使用 SSD 来存储 SQL Server TempDB 和缓冲池扩展
    - [查看](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md) Azure VM 中 SQL Server 的性能最佳做法。
- 设置并运行部署后，请 [详细了解](failover-failback-overview.md) 不同类型的故障转移。
