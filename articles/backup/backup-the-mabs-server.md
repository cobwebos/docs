---
title: 备份 MABS 服务器
description: 了解如何备份 Microsoft Azure 备份 Server (MABS) 。
ms.topic: conceptual
ms.date: 09/24/2020
ms.openlocfilehash: de62f0f57273ad7bd77df917d909627819165adb
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2020
ms.locfileid: "91946645"
---
# <a name="back-up-the-mabs-server"></a>备份 MABS 服务器

若要确保在 Microsoft Azure 备份 Server (MABS) 失败时可以恢复数据，您将需要一个用于备份 MABS 服务器的策略。 如果未备份，则需要在出现故障后手动重建，并且基于磁盘的恢复点将无法恢复。 可以通过备份 MABS 数据库来备份 MABS 服务器。

## <a name="back-up-the-mabs-database"></a>备份 MABS 数据库

作为 MABS 备份策略的一部分，你必须备份 MABS 数据库。 MABS 数据库名为 DPMDB。 此数据库包含 MABS 配置以及有关 MABS 备份的数据。 如果发生灾难，可以使用最新的数据库备份重新生成 MABS 服务器的大部分功能。 假设您可以还原数据库，则可以访问基于磁带的备份，并保留所有保护组设置和备份计划。 如果 MABS 存储池磁盘不受中断的影响，则基于磁盘的备份也可以在重建后使用。 可以使用多种不同的方法来备份数据库。

|数据库备份方法|优点|缺点|
|--------------------------|--------------|-----------------|
|[备份到 Azure](#back-up-to-azure)|<li>在 MABS 中轻松配置和监视。<li>备份数据库文件有多个位置。<li>云存储为灾难恢复提供了强大的解决方案。<li>数据库存储非常安全。<li>支持 120 个在线恢复点。|<li>需要 Azure 帐户和其他 MABS 配置。 Azure 存储会产生一些费用。<li> 需要具有 Azure 代理的基于 Windows Server 的受支持版本，才能访问 Azure 备份保管库中存储的 MABS 备份。 这不能是另一个 MABS 服务器。<li>如果数据库在本地托管且你要启用辅助保护，则不可使用此方法。 <li>这需要一些额外的准备和恢复时间。|
|[通过备份 MABS 存储池来备份数据库](#back-up-the-database-by-backing-up-the-mabs-storage-pool)|<li>易于配置和监视。<li>备份保存在 MABS 存储池磁盘上，可以在本地轻松访问。<li>MABS 计划的备份支持512快速完整备份。 如果每小时备份一次，则会有21天的完整保护。|<li>对于灾难恢复而言不是一个好的选择。 如果 MABS 服务器或存储池磁盘出现故障，则其处于联机状态且恢复可能无法按预期方式工作。<li>如果数据库在本地托管且你要启用辅助保护，则不可使用此方法。 <li>如果 MABS 服务或控制台未在运行或工作，则需要一些准备工作和特殊步骤才能访问恢复点。|
|[通过本机 SQL Server 备份，备份到本地磁盘](#back-up-with-native-sql-server-backup-to-a-local-disk)|<li>内置到 SQL Server。<li>备份保存在易于访问的本地磁盘上。<li>可以计划为按需要的频率运行。<li>完全独立于 MABS。<li>你可以计划一个备份文件清理。|<li>除非备份已复制到远程位置，否则对于灾难恢复而言不是一个好的选择。<li>备份需要本地存储，这可能会限制保留期和频率。|
|[使用本机 SQL 备份和 MABS 保护备份到受 MABS 保护的共享](#back-up-to-a-share-protected-by-mabs)|<li>在 MABS 中轻松监视。<li>备份数据库文件有多个位置。<li>易于通过网络从任意 Windows 计算机进行访问。<li>可能是最快的恢复方法。|<li>仅支持 64 个恢复点。<li>对于站点灾难恢复而言不是一个好的选择。 MABS 服务器或 MABS 存储池磁盘故障可能会妨碍恢复工作。<li>如果 MABS DB 在本地托管且你要启用辅助保护，则不能使用此选项。 <li>需要一些额外的准备工作以对其进行配置和测试。<li>如果 MABS 服务器本身已关闭，但 MABS 的存储池磁盘是正常的，则需要一些额外的准备和恢复时间。|

- 如果你使用 MABS 保护组进行备份，我们建议你为数据库使用唯一的保护组。

    > [!NOTE]
    > 出于恢复目的，要通过 MABS 数据库还原的 MABS 安装必须与 MABS 数据库本身的版本匹配。  例如，如果你要恢复的数据库来自带有更新汇总1安装的 MABS V3，则 MABS 服务器必须运行与更新汇总1相同的版本。 这意味着，在还原数据库之前，您可能必须使用兼容的版本卸载并重新安装 MABS。  若要查看数据库版本，你可能需要将其手动装入临时数据库名称，然后针对该数据库运行 SQL 查询，来根据主要版本和次要版本检查最新安装的汇总。

- 若要检查 MABS 数据库版本，请执行以下步骤：

    1. 若要运行查询，请打开 SQL Management Studio，然后连接到运行 MABS 数据库的 SQL 实例。

    2. 选择 MABS 数据库，然后启动一个新查询。

    3. 将以下 SQL 查询粘贴到查询窗格并运行：

        `Select distinct MajorVersionNumber,MinorVersionNumber ,BuildNumber, FileName FROM dbo.tbl\_AM\_AgentPatch order byMajorVersionNumber,MinorVersionNumber,BuildNumber`

    如果查询结果中未返回任何内容，或者如果 MABS 服务器是从以前的版本升级但尚未安装新的更新汇总，则不会出现 MABS 的基本安装的条目。 若要检查与更新汇总相关联的 MABS 版本，请参阅 [MABS 的生成号列表](https://social.technet.microsoft.com/wiki/contents/articles/36381.microsoft-azure-backup-server-list-of-build-numbers.aspx)。

### <a name="back-up-to-azure"></a>备份到 Azure

1. 在开始之前，需要运行脚本来检索 MABS 副本卷装入点路径，以便了解哪个恢复点包含 MABS 备份。 初始复制后使用 Azure 备份执行此操作。 在脚本中，将替换 `dplsqlservername%` 为托管 MABS 数据库的 SQL Server 实例的名称。

    ```SQL
    Select ag.NetbiosName as ServerName,ds.DataSourceName,vol.MountPointPath
    from tbl_IM_DataSource as ds
    join tbl_PRM_LogicalReplica as lr on ds.DataSourceId=lr.DataSourceId
    join tbl_AM_Server as ag on ds.ServerId=ag.ServerId
    join tbl_SPM_Volume as vol on lr.PhysicalReplicaId=vol.VolumeSetID
    and vol.Usage =1
    and lr.Validity in (1,2)
    where ds.datasourcename like '%dpmdb%'
    and servername like '%dpmsqlservername%' --netbios name of server hosting DPMDB
    ```

    请确保已安装 Azure 恢复服务代理并在 Azure 备份保管库中注册 MABS 服务器时指定的密码。 还原备份时需要此密码。

2. 创建 Azure 备份保管库、下载 Azure 备份代理安装文件和保管库凭据。 运行安装文件，在 MABS 服务器上安装代理，并使用保管库凭据在保管库中注册 MABS 服务器。 [了解详细信息](backup-azure-microsoft-azure-backup.md)。

3. 配置保管库后，设置包含 MABS 数据库的 MABS 保护组。 选择此可将其备份到磁盘和 Azure。

#### <a name="recover-the-mabs-database-from-azure"></a>从 Azure 恢复 MABS 数据库

可以使用 Azure 备份保管库中注册的任何 MABS 服务器从 Azure 恢复数据库，如下所示：

1. 在 MABS 控制台中，选择 "**恢复**" "  >  **添加外部 MABS**"。

2. 提供保管库凭据 (从 Azure 备份保管库) 下载。 请注意，凭据的有效期为两天。

3. 在 " **选择要恢复的外部 MABS**" 中，选择要为其恢复数据库的 MABS 服务器，键入加密密码，然后选择 **"确定"。**

4. 从可用点列表中选择要使用的恢复点。 选择 " **清除外部 MABS** " 以返回到 "本地 MABS" 视图。

## <a name="back-up-the-mabs-database-to-mabs-storage-pool"></a>将 MABS 数据库备份到 MABS 存储池

> [!NOTE]  
> 此选项适用于带有新式备份存储的 MABS。

1. 在 MABS 控制台中，选择 "**保护**" "  >  **创建保护组**"。
2. 在“选择保护组类型”  页上，选择“服务器”  。
3. 在 " **选择组成员** " 页上，选择 **DPM 数据库**。 展开 "MABS" 服务器，并选择 "DPMDB"。
4. 在“选择数据保护方法”  页上，选择“我想使用磁盘进行短期保护”  。 指定短期保护策略选项。
5. 初始复制 MABS 数据库后，请运行以下 SQL 脚本：

```SQL
select AG.NetbiosName, DS.DatasourceName, V.AccessPath, LR.PhysicalReplicaId from tbl_IM_DataSource DS
join tbl_PRM_LogicalReplica as LR
on DS.DataSourceId = LR.DataSourceId
join tbl_AM_Server as AG
on DS.ServerId=AG.ServerId
join tbl_PRM_ReplicaVolume RV
on RV.ReplicaId = LR.PhysicalReplicaId
join tbl_STM_Volume V
on RV.StorageId = V.StorageId
where datasourcename like N'%dpmdb%' and ds.ProtectedGroupId is not null
and LR.Validity in (1,2)
and AG.ServerName like N'%<dpmsqlservername>%' -- <dpmsqlservername> is a placeholder, put netbios name of server hosting DPMDB
```

### <a name="recover-mabs-database"></a>恢复 MABS 数据库

若要用相同的 DB 重构 MABS，需要先恢复 MABS 数据库，并将其与全新安装的 MABS 同步。

#### <a name="use-the-following-steps"></a>使用以下步骤

1. 打开管理命令提示符，然后运行 `psexec.exe -s powershell.exe` 以在系统上下文中启动 PowerShell 窗口。
2. 确定要从何处恢复数据库：

#### <a name="to-copy-the-database-from-the-last-backup"></a>从上次备份复制数据库

1. 导航到副本 VHD 路径 `\<MABSServer FQDN\>\<PhysicalReplicaId\>\<PhysicalReplicaId\>`
2. 使用命令装载 disk0 中存在的 **。** `mount-vhd disk0.vhdx`
3. 装载副本 VHD 后，请使用 `mountvol.exe` 通过 SQL 脚本输出中的物理副本 ID 向副本卷分配驱动器号。 例如： `mountvol X: \?\Volume{}\`

#### <a name="to-copy-the-database-from-a-previous-recovery-point"></a>从以前的恢复点复制数据库

1. 导航到 DPMDB 容器目录  `\<MABSServer FQDN\>\<PhysicalReplicaId\>` 。 你将看到多个目录在其下有一些唯一的 GUID 标识符，并在 MABS DB 中执行相应的恢复点。 其他目录表示 PIT/恢复点。
2. 导航到任何 PIT vhd 路径，例如， `\<MABSServer FQDN\>\<PhysicalReplicaId\>\<PITId\>` 使用命令装载其中包含的**disk0。** `mount-vhd disk0.vhdx`
3. 装载副本 VHD 后，使用 `mountvol.exe` SQL 脚本输出中的物理副本 ID 将驱动器号分配给副本卷。 例如： `mountvol X: \?\Volume{}\`

   以上步骤中以角括号显示的所有字词都是占位符。 将它们替换为适当的值，如下所示：
   - **ReFSVolume** -从 SQL 脚本输出访问路径
   - **MABSSERVER FQDN** -MABS 服务器的完全限定名称
   - **PhysicalReplicaId** -SQL 脚本输出中的物理副本 ID
   - **PITId** -GUID 标识符，而不是容器目录中的物理副本 ID。
4. 打开另一个管理命令提示符，然后运行 `psexec.exe -s cmd.exe` 以在系统上下文中启动命令提示符。
5. 将目录更改为 X：驱动器，并导航到 MABS 数据库文件所在的位置。
6. 将它们复制到易于从中还原的位置。 复制后，退出 psexec cmd 窗口。
7. 转到在步骤1中打开的 psexec PowerShell 窗口，导航到 VHDX 路径，然后使用命令卸除 VHDX `dismount-vhd disk0.vhdx` 。
8. 重新安装 MABS 服务器后，可以通过运行命令，使用还原的 DPMDB 附加到 MABS 服务器 `DPMSYNC-RESTOREDB` 。
9. 运行 `DPMSYNC-SYNC` 一次 `DPMSYNC-RESTOREDB` 。

### <a name="back-up-the-database-by-backing-up-the-mabs-storage-pool"></a>通过备份 MABS 存储池来备份数据库

> [!NOTE]
> 此选项适用于具有旧存储的 MABS。

在开始之前，需要运行脚本来检索 MABS 副本卷装入点路径，以便了解哪个恢复点包含 MABS 备份。 初始复制后使用 Azure 备份执行此操作。 在脚本中，将替换 `dplsqlservername%` 为托管 MABS 数据库的 SQL Server 实例的名称。

```SQL
Select ag.NetbiosName as ServerName,ds.DataSourceName,vol.MountPointPath
from tbl_IM_DataSource as ds
join tbl_PRM_LogicalReplica as lr on ds.DataSourceId=lr.DataSourceId
join tbl_AM_Server as ag on ds.ServerId=ag.ServerId
join tbl_SPM_Volume as vol on lr.PhysicalReplicaId=vol.VolumeSetID
and vol.Usage =1
and lr.Validity in (1,2)
where ds.datasourcename like '%dpmdb%'
and servername like '%dpmsqlservername%' --netbios name of server hosting DPMDB
```

1. 在 MABS 控制台中，选择 "**保护**" "  >  **创建保护组**"。

2. 在“选择保护组类型”  页上，选择“服务器”  。

3. 在 " **选择组成员** " 页上，选择 "MABS" 数据库。 展开 "MABS" 服务器项，然后选择 " **DPMDB**"。

4. 在 "  **选择数据保护方法** " 页上，选择 " **我想使用磁盘进行短期保护**"。 指定短期保护策略选项。 建议为 MABS 数据库使用两周的保持期。

#### <a name="recover-the-database"></a>恢复数据库

如果 MABS 服务器仍可正常运行且存储池完好无损 (如 MABS 服务或控制台) 的问题，请按如下所示从副本卷或卷影副本复制数据库：

1. 确定想要开始恢复数据库的时间。

    - 如果要从 MABS 副本卷中直接执行的最后一个备份中复制数据库，请使用 **mountvol.exe** 通过 SQL 脚本输出中的 GUID 将驱动器号分配给副本卷。 例如： `C:\Mountvol X: \\?\Volume{d7a4fd76\-a0a8\-11e2\-8fd3\-001c23cb7375}\`

    - 如果要将数据库从以前的恢复点复制 (卷影副本) ，则需要使用 SQL 脚本输出中的卷 GUID 来列出副本的所有卷影副本。 此命令列出该卷的卷影副本： `C:\>Vssadmin list shadows /for\=\\?\Volume{d7a4fd76-a0a8-11e2-8fd3-001c23cb7375}\` 。 请注意要从中恢复的创建时间和卷影副本 ID。

2. 然后，使用 **diskshadow.exe** 将卷影副本装载到未使用的驱动器号 X：使用卷影副本 ID，以便复制数据库文件。

3. 打开管理命令提示符，然后运行 `psexec.exe -s cmd.exe` 以在系统上下文中启动命令提示符，以便您有权导航到副本卷 (X： ) 并复制文件。

4. CD 转到 X：驱动器，并导航到 MABS 数据库文件所在的位置。 将它们复制到易于从中还原的位置。 复制完成后，"psexec" cmd 窗口，并运行 **diskshadow.exe** 并隐藏 X：卷。

5. 现在，可以使用 SQL Management Studio 或通过运行 **DPMSYNC \- RESTOREDB**来还原数据库文件。

## <a name="back-up-with-native-sql-server-backup-to-a-local-disk"></a>通过本机 SQL Server 备份，备份到本地磁盘

你可以使用本机 SQL Server 备份（独立于 MABS）将 MABS 数据库备份到本地磁盘。

- 获取 SQL Server 备份的[概述](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases)。

- [详细了解](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)如何将 SQL Server 备份到云。

## <a name="back-up-to-a-share-protected-by-mabs"></a>备份到受 MABS 保护的共享

此备份选项使用本机 SQL 将 MABS 数据库备份到共享，使用 MABS 保护共享，并使用 Windows VSS 以前的版本促进还原。

### <a name="before-you-start"></a>开始之前

1. 在 SQL Server 上，在驱动器上创建一个文件夹，使其拥有足够的可用空间来容纳备份的单个副本。 例如：`C:\MABSBACKUP`。

1. 共享该文件夹。 例如，将 `C:\MABSBACKUP` 文件夹共享为 *DPMBACKUP*。

1. 将以下 OSQL 命令复制并粘贴到记事本中，并将其保存到名为的文件中 `C:\MABSACKUP\bkupdb.cmd` 。 请确保没有 .txt 扩展名。 修改 SQL_Instance_name 和 DPMDB_NAME 以匹配 MABS 服务器使用的实例和 DPMDB 名称。

    ```SQL
    OSQL -E -S localhost\SQL_INSTANCE_NAME -Q "BACKUP DATABASE DPMDB_NAME TO DISK='C:\DPMBACKUP\dpmdb.bak' WITH FORMAT"
    ```

1. 使用记事本打开位于 MABS 服务器上的文件夹下的 **ScriptingConfig.xml** 文件 `C:\Program Files\Microsoft System Center\DPM\DPM\Scripting` 。

1. 修改 **ScriptingConfig.xml** **并将 "DPMDBBACKUP" 更改为** 包含 "" 文件夹/共享的驱动器号。 将 PreBackupScript 条目更改为在步骤3中保存的 **bkupdb** 的完整路径和名称。

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ScriptConfiguration xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="https://schemas.microsoft.com/2003/dls/ScriptingConfig.xsd">
    <DatasourceScriptConfig DataSourceName="C:">
    <PreBackupScript>C:\MABSDBBACKUP\bkupdb.cmd</PreBackupScript>
    <TimeOut>120</TimeOut>
    </DatasourceScriptConfig>
    </ScriptConfiguration>
    ```

1. 保存对 **ScriptingConfig.xml**所做的更改。

1. 使用 MABS 保护 C:\MABSBACKUP 文件夹或 `\sqlservername\MABSBACKUP` 共享，并等待创建初始副本。 C:\MABSBACKUP 文件夹中应该有一个 **dpmdb** ，作为运行中的备份前脚本的结果，后者又被复制到 MABS 副本。

1. 如果未启用自助恢复，则需要执行一些额外的步骤来共享副本上的 MABSBACKUP 文件夹：

    1. 在 MABS 控制台中 > **保护**，找到 "MABSBACKUP" 数据源并将其选中。 在 "详细信息" 部分中，选择 " **单击以查看** 指向副本路径的链接的详细信息" 并将路径复制到记事本。 删除源路径，并保留目标路径。 路径应类似于以下内容： `C:\Program Files\Microsoft System Center\DPM\DPM\Volumes\Replica\File System\vol_c9aea05f-31e6-45e5-880c-92ce5fba0a58\454d81a0-0d9d-4e07-9617-d49e3f2aa5de\Full\DPMBACKUP` 。

    2. 使用共享名称 **MABSSERVERNAME-DPMDB**对该路径进行共享。 你可以在管理命令提示符中使用以下 Net Share 命令。

        ```cmd
        Net Share MABSSERVERNAME-dpmdb="C:\Program Files\Microsoft System Center\DPM\DPM\Volumes\Replica\File System\vol_c9aea05f-31e6-45e5-880c-92ce5fba0a58\454d81a0-0d9d-4e07-9617-d49e3f2aa5de\Full\DPMBACKUP"
        ```

### <a name="configure-the-backup"></a>配置备份

你可以像使用 SQL Server 本机备份一样 SQL Server，备份 MABS 数据库。

- 获取 SQL Server 备份的[概述](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases)。

- [详细了解](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)如何将 SQL Server 备份到云。

### <a name="recover-the-mabs-database"></a>恢复 MABS 数据库

1. `\\MABSServer\MABSSERVERNAME-dpmdb`使用资源管理器从任何 Windows 计算机连接到共享。

2. 右键单击要查看其属性的 **dpmdb** 文件。 “以前的版本”  选项卡上将显示可以选择和复制的所有备份。 此外，还可以轻松地访问 C:\MABSBACKUP 文件夹中的最新备份。

3. 如果需要将已连接的 SAN MABS 存储池磁盘移动到另一台服务器以读取副本卷，或重新安装 Windows 以读取本地连接的磁盘，则需要事先知道 MABS 副本卷装入点路径或卷 GUID，以便了解哪些卷包含数据库备份。 你可以在启动保护之后但需要还原之前，随时使用以下 SQL 脚本来提取该信息。 将替换为 `%dpmsqlservername%` 承载数据库的 SQL Server 的名称。

    ```sql
    Select ag.NetbiosName as
    ServerName,ds.DataSourceName,vol.MountPointPath,vol.GuidName
    from tbl_IM_DataSource as ds
    join tbl_PRM_LogicalReplica as lr on ds.DataSourceId=lr.DataSourceId
    join tbl_AM_Server as ag on ds.ServerId=ag.ServerId
    join tbl_SPM_Volume as vol on lr.PhysicalReplicaId=vol.VolumeSetID
    and vol.Usage =1
    and lr.Validity in (1,2)
    where ds.datasourcename like '%C:\%' -- volume drive letter for DPMBACKUP
    and servername like '%dpmsqlservername%' --netbios name of server hosting DPMDB

    ```

4. 如果在移动 MABS 存储池磁盘或 MABS server rebuild 之后需要恢复：

    1. 你具有卷 GUID，因此，如果该卷需要装入另一台 Windows 服务器，或者在 MABS 服务器重新生成后，请使用 **mountvol.exe** 通过 SQL 脚本输出中的卷 GUID 将其分配给驱动器号： `C:\Mountvol X: \\?\Volume{d7a4fd76-a0a8-11e2-8fd3-001c23cb7375}\` 。

    2. 使用表示文件夹结构的副本路径的驱动器号和部分重新共享副本卷上的 MABSBACKUP 文件夹。

        ```cmd
        net share SERVERNAME-DPMDB="X:\454d81a0-0d9d-4e07-9617-d49e3f2aa5de\Full\DPMBACKUP"
        ```

    3. `\\SERVERNAME\MABSSERVERNAME-dpmdb`使用资源管理器从任何 Windows 计算机连接到共享。

    4. 右键单击 **dpmdb** 文件以查看属性。 “以前的版本”  选项卡上将显示可以选择和复制的所有备份。

## <a name="using-dpmsync"></a>使用 DPMSync

**DpmSync** 是一个命令行工具，可用于将 MABS 数据库与存储池中的磁盘状态和已安装的保护代理进行同步。 DpmSync 将还原 MABS 数据库，将 MABS 数据库与存储池中的副本同步，还原报表数据库，并重新分配缺少的副本。

### <a name="parameters"></a>参数

| 参数      | 说明    |
|----------------|-----------------------------|
| -RestoreDb                        | 从指定的位置还原 MABS 数据库。|
| -Sync                             | 同步还原的数据库。 还原数据库后，必须运行 **DpmSync – Sync** 。 运行 **DpmSync – Sync**后，某些副本可能仍标记为缺少。 |
| **-DbLoc** *位置*                | 标识 MABS 数据库的备份的位置。|
| **-InstanceName** <br/>*服务器 \ 实例*     | DPMDB 必须还原到的实例。|
| -ReallocateReplica          | 重新分配所有缺少的副本卷而不进行同步。 |
| -DataCopied                       | 指示已完成将数据加载到新分配的副本卷中。 这仅适用于客户端计算机。 |

**示例1：** 若要从 MABS 服务器上的本地备份媒体还原 MABS 数据库，请运行以下命令：

```cmd
DpmSync –RestoreDb -DbLoc G:\DPM\Backups\2005\November\DPMDB.bak
```

在还原 MABS 数据库后，若要同步数据库，请运行以下命令：

```cmd
DpmSync -Sync
```

在还原并同步 MABS 数据库后以及还原副本之前，请运行以下命令来重新分配副本的磁盘空间：

```cmd
DpmSync -ReallocateReplica
```

**示例2：** 若要从远程数据库还原 MABS 数据库，请在远程计算机上运行以下命令：

```cmd
DpmSync –RestoreDb -DbLoc G:\DPM\Backups\2005\November\DPMDB.bak –InstanceName contoso\ms$dpm
```

在还原 MABS 数据库后，若要同步数据库，请在 MABS 服务器上运行以下命令：

```cmd
DpmSync -Sync
```

在还原并同步 MABS 数据库后以及还原副本之前，请在 MABS 服务器上运行以下命令，以重新分配副本的磁盘空间：

```cmd
DpmSync -ReallocateReplica
```

## <a name="next-steps"></a>后续步骤

- [MABS 支持矩阵](backup-support-matrix-mabs-dpm.md)
- [MABS 常见问题](backup-azure-dpm-azure-server-faq.md)
