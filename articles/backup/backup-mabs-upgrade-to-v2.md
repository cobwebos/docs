---
title: "安装 Azure 备份服务器 v2 | Microsoft Docs"
description: "Azure 备份服务器 v2 可提供用于保护 VM、文件和文件夹、工作负载等的增强备份功能。 了解如何安装或升级到 Azure 备份服务器 v2。"
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
ms.assetid: 
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: masaran;markgal
ms.openlocfilehash: 1bbb16afef7940933b4c3ae23873f212770137e0
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2017
---
# <a name="install-azure-backup-server-v2"></a>安装 Azure 备份服务器 v2

Azure 备份服务器可帮助保护虚拟机 (VM)、工作负载、文件和文件夹等。 Azure 备份服务器 v2 以 Azure 备份服务器 v1 为基础进行构建，提供了在 v1 中不可用的新功能。 有关 v1 与 v2 之间的功能比较，请参阅 [Azure 备份服务器保护矩阵](backup-mabs-protection-matrix.md)。 

备份服务器 v2 中的其他功能是针对备份服务器 v1 的升级。 但是，备份服务器 v1 不是安装备份服务器 v2 的先决条件。 如果要从备份服务器 v1 升级到备份服务器 v2，请在备份服务器保护服务器上安装备份服务器 v2。 现有备份服务器设置保持不变。

可以在 Windows Server 2012 R2 或 Windows Server 2016 上安装备份服务器 v2。 若要利用新功能（如 System Center 2016 Data Protection Manager 新式备份存储），必须在 Windows Server 2016 上安装备份服务器 v2。 升级到或安装备份服务器 v2 之前，请阅读[安装先决条件](https://docs.microsoft.com/system-center/dpm/install-dpm#setup-prerequisites)。

> [!NOTE]
> Azure 备份服务器的基本代码与 System Center Data Protection Manager 相同。 备份服务器 v1 相当于 Data Protection Manager 2012 R2，而备份服务器 v2 相当于 Data Protection Manager 2016。 本文有时会引用 Data Protection Manager 文档。
>
>

## <a name="upgrade-backup-server-to-v2"></a>将备份服务器升级到 v2
若要从备份服务器 v1 升级到备份服务器 v2，请确保安装具有所需更新：

- 在受保护的服务器上[更新保护代理](backup-mabs-upgrade-to-v2.md#update-the-dpm-protection-agent)。
- 将 Windows Server 2012 R2 升级到 Windows Server 2016。
- 在所有生产服务器上升级 Azure 备份服务器远程管理员。
- 确保备份设置为继续进行而不重新启动生产服务器。


### <a name="upgrade-steps-for-backup-server-v2"></a>备份服务器 v2 的升级步骤

1. 在下载中心内，[下载升级安装程序](https://go.microsoft.com/fwlink/?LinkId=626082)。

2. 提取安装向导之后，确保选择“执行 setup.exe”，然后选择“完成”。

  ![安装程序 - 运行安装程序](./media/backup-mabs-upgrade-to-v2/run-setup.png)

3. 在 Microsoft Azure 备份服务器向导中的“安装”下，选择“Microsoft Azure 备份服务器”。

  ![安装程序 - 选择安装](./media/backup-mabs-upgrade-to-v2/mabs-installer-s1.png)

4. 在“欢迎”页上，检查警告，然后选择“下一步”。

  ![安装程序 -“欢迎”页](./media/backup-mabs-upgrade-to-v2/mabs-installer-s2.png)

5. 安装向导会执行先决条件检查，以确保环境可以进行升级。 在“先决条件检查”页上，选择“检查”。

  ![安装程序 -“先决条件检查”页](./media/backup-mabs-upgrade-to-v2/mabs-installer-s3-perform-checks.png)

6. 环境必须通过先决条件检查。 如果环境未通过检查，请记下问题并修复它们。 然后选择“再次检查”。 通过先决条件检查之后，选择“下一步”。

  ![安装程序 -“再次检查”按钮](./media/backup-mabs-upgrade-to-v2/mabs-installer-s4-pass-checks.png)

7. 在“SQL 设置”页上，选择 SQL 安装的相关选项，然后选择“检查并安装”。

  ![安装程序 -“SQL 设置”页](./media/backup-mabs-upgrade-to-v2/mabs-installer-s5-sql-settings.png)

  检查可能需要几分钟。 检查完成之后，选择“下一步”。

  ![安装程序 - SQL 设置“检查并安装”按钮](./media/backup-mabs-upgrade-to-v2/mabs-installer-s5a-check-and fix-settings.png)

8. 在“安装设置”页上，对安装备份服务器的位置或“暂存位置”进行任何更改。 选择“下一步”。

  ![安装程序 -“安装设置”页](./media/backup-mabs-upgrade-to-v2/mabs-installer-s6-installation-settings.png)

9. 若要完成安装向导，请选择“完成”。

  ![安装程序 - 完成](./media/backup-mabs-upgrade-to-v2/run-setup.png)



## <a name="add-storage-for-modern-backup-storage"></a>为 Modern Backup Storage 添加存储

为了提高备份存储效率，备份服务器 v2 添加了卷支持。 与备份服务器 v1 一样，备份服务器 v2 支持磁盘。

### <a name="add-volumes-and-disks"></a>添加卷和磁盘
如果在 Windows Server 2016 上运行备份服务器 v2，则可以使用卷存储备份数据。 卷可节省存储并加快备份。 因为卷是备份服务器的新增功能，所以必须添加它们。 

将卷添加到备份服务器时，可以为卷提供友好名称。 单击要命名的卷的“友好名称”列。 可以在以后需要时更改名称。 还可以使用 PowerShell 为卷添加或更改友好名称。

在管理员控制台中添加卷：

1. 在 Azure 备份服务器管理员控制台中，选择“管理” > “磁盘存储” > “添加”。

    ![打开“添加磁盘存储”向导](./media//backup-mabs-upgrade-to-v2/open-add-disk-storage-wizard.png)

    这会打开“添加磁盘存储”向导。

2. 在“添加磁盘存储”页上的“可用卷”框中，选择卷，然后选择“添加”。
3. 在“所选卷”框中，输入卷的友好名称，然后选择“确定”。

      ![“添加磁盘存储”向导 - 添加卷](./media/backup-mabs-upgrade-to-v2/add-volume.png)

  如果要添加磁盘，则磁盘必须属于具有旧存储的保护组。 这些磁盘只能用于这些保护组。 如果备份服务器没有具有旧保护的源，则不会列出磁盘。

  有关添加磁盘的详细信息，请参阅[添加磁盘以增大旧存储](http://docs.microsoft.com/system-center/dpm/upgrade-to-dpm-2016#adding-disks-to-increase-legacy-storage)。 无法为磁盘提供友好名称。


### <a name="assign-workloads-to-volumes"></a>将工作负载分配给卷

在备份服务器中，可指定将哪些工作负载分配给哪些卷。 例如，可以将支持较高每秒输入/输出操作次数 (IOPS) 的昂贵卷设置为仅存储需要频繁的高容量备份的工作负载。 一个示例是具有事务日志的 SQL Server。

#### <a name="update-dpmdiskstorage"></a>Update-DPMDiskStorage

若要更新备份服务器上存储池中的卷的属性，请使用 PowerShell cmdlet Update-DPMDiskStorage。

语法：

`Parameter Set: Volume`

```
Update-DPMDiskStorage [-Volume] <Volume> [[-FriendlyName] <String> ] [[-DatasourceType] <VolumeTag[]> ] [-Confirm] [-WhatIf] [ <CommonParameters>]
```

使用 PowerShell 进行的所有更改都会在 UI 中得到反映。


## <a name="protect-data-sources"></a>保护数据源
若要开始保护数据源，请创建保护组。 以下步骤重点说明对新建保护组向导进行的更改或添加。

创建保护组：

1. 在备份服务器管理员控制台中，选择“保护”。

2. 在工具功能区中，选择“新建”。

    这会打开“创建新保护组”向导。

  ![“创建新保护组”向导](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-1.png)

3. 在“欢迎”页上，选择“下一步”。
4. 在“选择保护组类型”页上，选择要创建的保护组的类型，然后选择“下一步”。

  ![“选择保护组类型”页](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-2.png)

5. 在“选择组成员”页上的“可用成员”窗格中，会列出具有保护代理的成员。 对于此示例，选择卷 D:\ 和 E:\，然后将它们添加到“所选成员”窗格。 选择“下一步”。

  ![“选择组成员”页](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-3.png)

6. 在“选择数据保护方法”页上，输入“保护组名称”，选择保护方法，然后选择“下一步”。 如果要进行短期保护，则必须选择“磁盘”备份方法。

  ![“选择数据保护方法”页](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-4.png)

7. 在“指定短期目标”页上，选择“保持期”和“同步频率”的详细信息。 然后，选择“下一步”。 （可选）若要针对恢复点执行时间更改计划，请选择“修改”。

  ![“指定短期目标”页](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-5.png)

8. 在“检查磁盘存储分配”页上，检查有关所选数据源、其大小以及要设置的空间和目标存储卷的值的详细信息。

  ![“检查磁盘存储分配”页](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-6.png)

  存储卷基于工作负载卷分配（使用 PowerShell 设置）和可用存储。 可以通过在下拉菜单中选择其他卷来更改存储卷。 如果你更改“目标存储”的值，则“可用磁盘存储”的值会动态更改以在“可用空间”和“设置不足的空间”下反映值。

  如果数据源按计划增长，则“可用磁盘存储”中的“设置不足的空间”列的值反映所需额外存储量。 使用此值可帮助规划存储需求以实现平稳备份。 如果该值为零，则在可预见的将来内不存在潜在存储问题。 如果该值为非零数字，则未分配足够存储（基于保护策略和受保护成员的数据大小）。

  ![分配不足的磁盘存储](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-7.png)

   若要完成保护组创建，请完成向导。

## <a name="migrate-legacy-storage-to-modern-backup-storage"></a>将旧存储迁移到 Modern Backup Storage
升级到或安装备份服务器 v2 并将操作系统升级到 Windows Server 2016 之后，可更新保护组以使用新式备份存储。 默认情况下，保护组不会进行更改。 它们会继续按照初始设置运行。 

可以选择更新保护组以使用新式备份存储。 若要更新保护组，请使用保留数据选项停止所有数据源的保护。 然后，将数据源添加到新保护组。

1. 在管理员控制台中，选择“保护”功能。 在“保护组成员”列表中，右键单击成员，然后选择“停止保护成员”。

  ![停止保护成员](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-stop-protection1.png)

2. 在“从组中删除”对话框中，检查存储池的已用磁盘空间和可用空闲空间。 默认设置是在磁盘上保留恢复点，并让它们可以按照关联保留策略过期。 单击 **“确定”**。

  如果要立即将已用磁盘空间返回到可用存储池，则选中“删除磁盘上的副本”复选框以删除与成员关联的备份数据（和恢复点）。

  ![“从组中删除”对话框](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-retain-data.png)

3. 创建一个使用新式备份存储的保护组。 包括未受保护的数据源。


## <a name="add-disks-to-increase-legacy-storage"></a>添加磁盘以增大旧存储

如果要将旧存储与备份服务器一起使用，则可能需要添加磁盘以增大旧存储。 

添加磁盘存储：

1. 在管理员控制台中，选择“管理” > “磁盘存储” > “添加”。

    ![“添加磁盘存储”对话框](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-add-disk-storage.png)

4. 在“添加磁盘存储”对话框中，选择“添加磁盘”。

5. 在可用磁盘的列表中，选择要添加的磁盘，选择“添加”，然后选择“确定”。

## <a name="update-the-data-protection-manager-protection-agent"></a>更新 Data Protection Manager 保护代理

备份服务器将 System Center Data Protection Manager 保护代理用于更新。 如果要升级未连接到网络的保护代理，则无法使用 Data Protection Manager 管理员控制台完成已连接代理升级。 必须在非活动域环境中升级保护代理。 在客户端计算机连接到网络之前，Data Protection Manager 管理员控制台会显示保护代理更新处于挂起状态。

下列各部分介绍如何为连接的客户端计算机和未连接的客户端计算机更新保护代理。

### <a name="update-a-protection-agent-for-a-connected-client-computer"></a>为连接的客户端计算机更新保护代理

1. 在备份服务器管理员控制台中，选择“管理” > “代理”。

2. 在显示窗格中，选择要为其更新保护代理的客户端计算机。

  > [!NOTE]
  > “代理更新”列指示每个受保护计算机何时有保护代理更新可用。 在“操作”窗格中，仅当选择了受保护计算机并且有可用更新时，“更新”操作才可用。
  >
  >

3. 要在所选计算机上安装更新的保护代理，请在“操作”窗格中，选择“更新”。

### <a name="update-a-protection-agent-on-a-client-computer-that-is-not-connected"></a>在未连接的客户端计算机上更新保护代理

1. 在备份服务器管理员控制台中，选择“管理” > “代理”。

2. 在显示窗格中，选择要为其更新保护代理的客户端计算机。

  > [!NOTE]
   > “代理更新”列指示每个受保护计算机何时有保护代理更新可用。 在“操作”窗格中，“更新”操作在选择了受保护计算机时不可用，除非有更新可用。
  >
  >

3. 要在所选计算机上安装更新的保护代理，请选择“更新”。

4. 对于未连接到网络的客户端计算机，在计算机连接到网络之前，“代理状态”列会显示“挂起更新”状态。

  在客户端计算机连接到网络之后，客户端计算机的“代理更新”列会显示“正在更新”状态。
  
### <a name="move-legacy-protection-groups-from-old-version-and-sync-the-new-version-with-azure"></a>通过 Azure 将旧保护组从旧版本中移出并同步新版本

Azure 备份服务器和 OS 均更新后，便可以使用新式备份存储保护新的数据源。 但已受保护的数据源会继续以之前存储在 Azure 备份服务器时的保护方式接受保护，但所有新保护均使用 Modern Backup Storage。

以下步骤可以将数据源从旧保护模式迁移到 Modern Backup Storage。

• 将新卷添加到 DPM 存储池，如有需要可分配友好名称和数据源标记。
• 对于处于旧模式的每个数据源，请停止保护该数据源并“保留受保护的数据”。  这将在迁移后允许恢复旧恢复点。

• 创建新 PG 并选择将使用新格式进行存储的数据源。
• DPM 将从旧备份存储进行副本复制，复制到本地 Modern Backup Storage 卷。
注意：这将被视为恢复后的操作作业 • 随后会将所有新同步和恢复点都存储到 Modern Backup Storage。
• 旧恢复点过期后将被删除，最终释放磁盘空间。
• 删除旧存储中的所有旧卷后，便可从 Azure 备份和系统中删除磁盘。
• 对 Azure DPMDB 进行备份。

第 2 部分：- 重要内容 > 新服务器的名称需要与原始 Azure 备份服务器的名称相同。 如果希望使用旧存储池和 DPMDB 来保留恢复点，则不能更改新 Azure 备份服务器的名称 - 必须对 DPMDB 进行备份（因为需要将其还原）

1) 关闭原始 Azure 备份服务器或对其停止传输。
2) 重置 Active Directory 中的计算机帐户。
3) 在新计算机上安装 Server 2016 并将其命名为与原始 Azure 备份服务器相同的计算机名称。
4) 加入域
5) 安装 Azure 备份服务器 V2（将 DPM 存储池磁盘从旧服务器中移出并导入到该服务器）
6) 还原在第 2 部分末尾备份的 DPMDB
7) 将存储从原始备份服务器连接到新服务器。
8) 从 SQL 还原 DPMDB
9) 从新服务器 cd 的管理员命令行到 Microsoft Azure 备份安装位置和 bin 文件夹

路径示例：C:\windows\system32>cd "c:\Program Files\Microsoft Azure Backup\DPM\DPM\bin\
到 Azure 备份运行 DPMSYNC - SYNC

10) 运行 DPMSYNC - SYNC 时请注意，如果已将新磁盘添加到 DPM 存储池（而不是移动旧磁盘），请运行 DPMSYNC -Reallocatereplica

## <a name="new-powershell-cmdlets-in-v2"></a>v2 中的新 PowerShell cmdlet

安装 Azure 备份服务器 v2 时，有两个新 cmdlet 可用： 
* [Mount-DPMRecoveryPoint](https://technet.microsoft.com/library/mt787159.aspx)
* [Dismount-DPMRecoveryPoint](https://technet.microsoft.com/library/mt787158.aspx)

## <a name="next-steps"></a>后续步骤

了解如何准备服务器或开始保护工作负载：
- [准备备份服务器工作负载](backup-azure-microsoft-azure-backup.md)
- [使用备份服务器备份 VMware 服务器](backup-azure-backup-server-vmware.md)
- [使用备份服务器备份 SQL Server](backup-azure-sql-mabs.md)
- [将新式备份存储与备份服务器一起使用](backup-mabs-add-storage.md)

