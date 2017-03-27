---
title: "用作备份目标的 StorSimple 8000 系列与 NetBackup 的集成 | Microsoft 文档"
description: "介绍包含 Veritas NetBackup 的 StorSimple 备份目标配置。"
services: storsimple
documentationcenter: 
author: harshakirank
manager: matd
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/05/2016
ms.author: hkanna
translationtype: Human Translation
ms.sourcegitcommit: 83dc91972ad5fec85e562e45227747568b1fea75
ms.openlocfilehash: 2ac7c119e0706f0c5d479aa04a3afc34cc55cf22
ms.lasthandoff: 01/26/2017

---

# <a name="storsimple-as-a-backup-target-with-netbackup"></a>用作备份目标的 StorSimple 与 NetBackup 的集成

## <a name="overview"></a>概述

Azure StorSimple 是 Microsoft 提供的一种混合云存储解决方案。 StorSimple 使用 Azure 存储帐户作为本地解决方案的扩展，跨本地存储和云存储自动将数据分层，解决指数级数据增长带来的复杂性。

本文介绍 StorSimple 与 Veritas NetBackup 的集成，以及集成这两种解决方案时的最佳实践。 此外，还提供了有关如何设置 Veritas NetBackup，以实现与 StorSimple 的最佳集成的建议。 我们将遵循 Veritas 最佳实践以及备份架构师与管理员的意见，最合理地设置 Veritas NetBackup，旨在满足个人的备份要求和服务级别协议 (SLA)。

本文虽然介绍了配置步骤和重要概念，但并未提供循序渐进的配置或安装指南。 本文假设基本组件与基础结构工作正常，随时能够支持本文中所述的操作。

### <a name="who-should-read-this"></a>目标读者

本文中的信息主要面向具有存储、Windows Server 2012 R2、以太网、云服务和 Veritas NetBackup 相关知识的备份管理员、存储管理员和存储架构师。

### <a name="supported-versions"></a>支持的版本

-   NetBackup 7.7.x 和更高版本
-   [StorSimple Update 3 和更高版本](storsimple-overview.md#storsimple-workload-summary)


## <a name="why-storsimple-as-a-backup-target"></a>为何将 StorSimple 用作备份目标？

StorSimple 是极佳的备份目标，原因如下：

-   它为备份应用程序提供标准的本地存储，无需进行任何更改即可用作快速备份目标。 StorSimple 还可用于快速还原最近的备份。
-   它的云层与 Azure 云存储帐户无缝集成，可以利用经济高效的 Azure 存储。
-   它提供场外存储，实现灾难恢复。

## <a name="key-concepts"></a>关键概念

与任何存储解决方案一样，对解决方案的存储性能、SLA、更改率以及容量增长需求展开细致的评估是成功的关键所在。 主要思路是引入云层，要使 StorSimple 发挥其作用，云的访问时间与吞吐量发挥着至关重要的作用。

StorSimple 旨在为处理妥善定义的工作数据集（热数据）的应用程序提供存储。 在此模型中，工作数据集存储在本地层中，剩余的非工作/冷/存档数据集在云中分层。 下图演示了此模型。 平坦的绿线表示存储在 StorSimple 设备本地层中的数据。 红线表示 StorSimple 解决方案的所有层中存储的总数据量。 平坦绿线与呈指数级升高的红色曲线之间的空间表示存储在云中的总数据量。

**StorSimple 分层**
![StorSimple 分层示意图](./media/storsimple-configure-backup-target-using-netbackup/image1.jpg)

知道这种体系结构后，将会发现 StorSimple 非常适合用作备份目标。 使用 StorSimple 可以：
-   通过本地工作数据集执行最常见的还原。
-   使用云实现场外灾难恢复，存储不经常还原的陈旧数据。

## <a name="storsimple-benefits"></a>StorSimple 的优势

StorSimple 提供与 Microsoft Azure 无缝集成的本地解决方案，可让用户无缝访问本地存储和云存储。

StorSimple 在包含固态设备 (SSD) 的本地设备、串行附加 SCSI (SAS) 存储与 Azure 存储之间使用自动分层。 自动分层将频繁访问的本地数据保留在 SSD 和 SAS 层中， 将不经常访问的数据移到 Azure 存储。

StorSimple 提供以下优势：

-   独特的重复数据删除和压缩算法，使用云实现前所未有的重复数据删除能力
-   高可用性
-   使用 Azure 异地复制实现异地复制
-   Azure 集成
-   云中的数据加密
-   改进灾难恢复和合规性

尽管 StorSimple 提供两种主要部署方案（主备份目标和辅助备份目标），但它本质上是一个普通的块存储设备。 StorSimple 执行所有的压缩和重复数据删除操作。 它在云与应用程序和文件系统之间无缝发送和检索数据。

有关 StorSimple 的详细信息，请参阅 [StorSimple 8000 系列：混合云存储解决方案](storsimple-overview.md)。 另外，还可以参阅 [StorSimple 8000 系列技术规范](storsimple-technical-specifications-and-compliance.md)。

> [!IMPORTANT]
> 只有 StorSimple 8000 Update 3 和更高版本才支持使用 StorSimple 设备作为备份目标。

## <a name="architecture-overview"></a>体系结构概述

下表显示设备模型到体系结构的初步指导。

**用于本地存储和云存储的 StorSimple 容量**

| 存储容量       | 8100          | 8600            |
|------------------------|---------------|-----------------|
| 本地存储容量 | &lt; 10 TiB\*  | &lt; 20 TiB\*  |
| 云存储容量 | &gt; 200 TiB\* | &gt; 500 TiB\* |
\* 未经过重复数据删除或压缩的存储大小。

**用于主备份和辅助备份的 StorSimple 容量**

| 备份方案  | 本地存储容量  | 云存储容量  |
|---|---|---|
| 主备份  | 最近的备份存储在本地存储中以加快恢复速度，满足恢复点目标 (RPO)。 | 备份历史记录 (RPO) 占用云容量 |
| 辅助备份 | 备份数据的辅助副本可存储在云容量中  | 不适用  |

## <a name="storsimple-as-a-primary-backup-target"></a>用作主备份目标的 StorSimple

在此方案中，StorSimple 卷将提供给备份应用程序作为唯一的备份存储库。 下图显示了其中所有备份使用 StorSimple 分层卷进行备份和还原的解决方案体系结构。

![用作主备份目标的 StorSimple 的逻辑示意图](./media/storsimple-configure-backup-target-using-netbackup/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>主目标备份逻辑步骤

1.  备份服务器与目标备份代理通信，备份代理将数据传输到备份服务器。
2.  备份服务器将数据写入 StorSimple 分层卷。
3.  备份服务器更新目录数据库，然后完成备份作业。
4.  快照脚本触发 StorSimple 快照管理器（启动或删除）。
5.  备份服务器根据保留策略删除过期的备份。

### <a name="primary-target-restore-logical-steps"></a>主目标还原逻辑步骤

1.  备份服务器开始从存储库还原相应的数据。
2.  备份代理从备份服务器接收数据。
3.  备份服务器完成还原作业。

## <a name="storsimple-as-a-secondary-backup-target"></a>用作辅助备份目标的 StorSimple

在此方案中，StorSimple 卷主要用于长期保留或存档。

下图显示了其中的初始备份和还原针对高性能卷的体系结构。 这些备份将按设置的计划复制并存档到 StorSimple 分层卷。

必须调整高性能卷的大小，使其能够应对保留策略容量和性能要求。

![用作辅助备份目标的 StorSimple 的逻辑示意图](./media/storsimple-configure-backup-target-using-netbackup/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>辅助目标备份逻辑步骤

1.  备份服务器与目标备份代理通信，备份代理将数据传输到备份服务器。
2.  备份服务器将数据写入高性能存储。
3.  备份服务器更新目录数据库，然后完成备份作业。
4.  备份服务器根据保留策略将备份复制到 StorSimple。
5.  快照脚本触发 StorSimple 快照管理器（启动或删除）。
6.  备份服务器根据保留策略删除过期的备份。

### <a name="secondary-target-restore-logical-steps"></a>辅助目标还原逻辑步骤

1.  备份服务器开始从存储库还原相应的数据。
2.  备份代理从备份服务器接收数据。
3.  备份服务器完成还原作业。

## <a name="deploy-the-solution"></a>部署解决方案

部署此解决方案需要执行三个步骤：
1. 准备网络基础结构。
2. 将 StorSimple 设备部署为备份目标。
3. 部署 Veritas NetBackup。

以下部分详细介绍了每个步骤。

### <a name="set-up-the-network"></a>设置网络

由于 StorSimple 是与 Azure 云集成的解决方案，因此 StorSimple 需要与 Azure 云建立有效的连接。 此连接用于如下所述的操作：云快照、数据管理、元数据传输，以及在 Azure 云存储中将不经常访问的旧数据分层。

为使该解决方案保持最佳性能，我们建议遵循以下网络最佳实践：

-   用于将 StorSimple 层连接到 Azure 的链路必须满足带宽要求。 为此，请向基础结构交换机应用适当的服务质量 (QoS) 级别，使之与 RPO 和恢复时间目标 (RTO) SLA 相符。

-   最大 Azure Blob 存储访问延迟应在 80 毫秒左右。

### <a name="deploy-storsimple"></a>部署 StorSimple

有关 StorSimple 分步部署指南，请参阅[部署本地 StorSimple 设备](storsimple-deployment-walkthrough-u2.md)。

### <a name="deploy-netbackup"></a>部署 NetBackup

有关 NetBackup 7.7.x 分步部署指南，请参阅 [NetBackup 7.7.x 文档](http://www.veritas.com/docs/000094423)。

## <a name="set-up-the-solution"></a>设置解决方案

本部分演示一些配置示例。 以下示例和建议演示最基本的实现。 不一定能够根据具体的备份要求直接应用此实现。

### <a name="set-up-storsimple"></a>设置 StorSimple

| StorSimple 部署任务  | 附加备注 |
|---|---|
| 部署本地 StorSimple 设备。 | 支持的版本：Update 3 和更高版本。 |
| 打开备份目标。 | 使用以下命令打开或关闭备份目标模式并获取状态。 有关详细信息，请参阅[远程连接到 StorSimple 设备](storsimple-remote-connect.md)。</br> 打开备份模式：`Set-HCSBackupApplianceMode -enable`。 </br> 关闭备份模式：`Set-HCSBackupApplianceMode -disable`。 </br> 获取备份模式设置的当前状态：`Get-HCSBackupApplianceMode`。 |
| 为存储备份数据的卷创建通用卷容器。 卷容器中的所有数据都已删除重复数据。 | StorSimple 卷容器定义重复数据删除域。  |
| 创建 StorSimple 卷。 | 创建大小尽量接近预期用量的卷，因为卷大小会影响云快照持续时间。 有关如何调整卷大小的信息，请阅读[保留策略](#retention-policies)。</br> </br> 使用 StorSimple 分层卷，并选中“将此卷用于不常访问的存档数据”复选框。 </br> 不能只使用本地固定卷。 |
| 为所有备份目标卷创建唯一的 StorSimple 备份策略。 | StorSimple 备份策略定义卷一致性组。 |
| 快照过期时禁用计划。 | 快照将作为后处理操作触发。 |

### <a name="set-up-the-host-backup-server-storage"></a>设置主机备份服务器存储

根据以下指导原则设置主机备份服务器存储：  

- 不要使用跨区卷（由 Windows 磁盘管理创建）；不支持跨区卷。
- 使用 NTFS 和 64-KB 分配大小格式化卷。
- 将 StorSimple 卷直接映射到 NetBackup 服务器。
    - 为物理服务器使用 iSCSI。
    - 为虚拟服务器使用直通磁盘。


## <a name="best-practices-for-storsimple-and-netbackup"></a>StorSimple 和 Veritas NetBackup 的最佳实践

根据以下几个部分中的指导原则设置解决方案。

### <a name="operating-system-best-practices"></a>操作系统最佳实践

-   在 NTFS 文件系统中禁用 Windows Server 加密和重复数据删除。
-   在 StorSimple 卷上禁用 Windows Server 碎片整理。
-   在 StorSimple 卷上禁用 Windows Server 索引编制。
-   在源主机上运行防病毒扫描（不是针对 StorSimple 卷）。
-   在任务管理器中关闭默认的 [Windows Server 维护](https://msdn.microsoft.com/library/windows/desktop/hh848037.aspx)。 通过以下方式之一执行此操作：
    - 在 Windows 任务计划程序中关闭“维护”配置器。
    - 通过 Windows Sysinternals 下载 [PsExec](https://technet.microsoft.com/sysinternals/bb897553.aspx)。 下载 PsExec 后，以管理员身份运行 Windows PowerShell，键入：
      ```powershell
      psexec \\%computername% -s schtasks /change /tn “MicrosoftWindowsTaskSchedulerMaintenance Configurator" /disable
      ```

### <a name="storsimple-best-practices"></a>StorSimple 最佳实践

-   请务必将 StorSimple 设备更新到 [Update 3 或更高版本](storsimple-install-update-3.md)。
-   隔离 iSCSI 和云流量。 使用专用的 iSCSI 连接在 StorSimple 与备份服务器之间传送流量。
-   确保 StorSimple 设备是专用的备份目标。 不支持混合工作负荷，因为它们会影响 RTO 和 RPO。

### <a name="netbackup-best-practices"></a>NetBackup 最佳实践

-   NetBackup 数据库应位于服务器本地，而不能驻留在 StorSimple 卷上。
-   为实现灾难恢复，请在 StorSimple 卷上备份 NetBackup 数据库。
-   支持在此解决方案中使用 NetBackup 完整和增量备份。 建议不要使用合成备份和差异备份。
-   备份数据文件应只包含特定作业的数据。 例如，不允许跨不同的作业附加媒体。

有关最新的 NetBackup 设置以及如何满足这些要求的最佳实践，请参阅 [www.veritas.com](https://www.veritas.com) 上的 NetBackup 文档。


## <a name="retention-policies"></a>保留策略

最常用的备份保留策略类型之一为祖父、父和子 (GFS) 策略。 在 GSF 策略中，增量备份每日执行，完整备份每周和每月执行。 此策略会导致生成&6; 个 StorSimple 分层卷：一个卷包含每周、每月和每年完整备份；另外&5; 个卷存储每日增量备份。

以下示例使用 GFS 轮转。 该示例的假设条件如下：

-   使用未经过重复数据删除或压缩的数据。
-   每个完整备份的大小为 1 TiB。
-   每个每日增量备份的大小为 500 GiB。
-   4 个每周备份保留一个月。
-   12 个每月备份保留一年。
-   1 个每年备份保留 10 年。

根据前面的假设，将为每月和每年完整备份创建 26-TiB 的 StorSimple 分层卷。 将为每个每日增量备份创建 5-TiB 的 StorSimple 分层卷。

| 每种备份类型的保留期 | 大小 (TiB) | GFS 乘数\* | 总容量 (TiB)  |
|---|---|---|---|
| 每周完整备份 | 1 | 4  | 4 |
| 每日增量备份 | 0.5 | 20（周期等于每月周数） | 12（使用&2; 个以提高配额） |
| 每月完整备份 | 1 | 12 | 12 |
| 每年完整备份 | 1  | 10 | 10 |
| GFS 要求 |   | 38 |   |
| 提高的配额  | 4  |   | 总共&42;，满足 GFS 要求  |
\* GFS 乘数是需要根据备份策略要求保护和保留的副本数。

## <a name="set-up-netbackup-storage"></a>设置 NetBackup 存储

### <a name="to-set-up-netbackup-storage"></a>设置 NetBackup 存储

1.  在 NetBackup 管理控制台中，选择“媒体和设备存储” > “设备” > “磁盘池”。 在磁盘池配置向导中，选择存储服务器类型“AdvancedDisk”，然后选择“下一步”。

    ![NetBackup 管理控制台，磁盘池配置向导](./media/storsimple-configure-backup-target-using-netbackup/nbimage1.png)

2.  选择服务器，然后选择“下一步”。

    ![NetBackup 管理控制台，选择服务器](./media/storsimple-configure-backup-target-using-netbackup/nbimage2.png)

3.  选择 StorSimple 卷。

    ![NetBackup 管理控制台，选择 StorSimple 卷磁盘](./media/storsimple-configure-backup-target-using-netbackup/nbimage3.png)

4.  输入备份目标的名称，然后选择“下一步” > “下一步”完成向导中的操作。

5.  检查设置，然后选择“完成”。

6.  每次分配卷结束时，请根据 [StorSimple 和 NetBackup 的最佳实践](#best-practices-for-storsimple-and-netbackup)中的建议更改存储设备设置。

7. 重复步骤 1-6，直到完成 StorSimple 卷的分配。

    ![NetBackup 管理控制台，磁盘配置](./media/storsimple-configure-backup-target-using-netbackup/nbimage5.png)

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>将 StorSimple 设置为主备份目标

> [!NOTE]
> 从云中分层的备份还原数据将以云的速度进行。

下图显示了典型卷到备份作业的映射。 在本例中，所有每周备份映射到星期六完整备份磁盘，增量备份映射到星期一至星期五增量备份磁盘。 所有备份和还原操作从 StorSimple 分层卷发生。

![主备份目标配置逻辑示意图 ](./media/storsimple-configure-backup-target-using-netbackup/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>用作主备份目标的 StorSimple 的 GFS 计划示例

下面是四周、每月和每年的 GFS 轮转计划示例：

| 频率/备份类型 | 完整 | 增量备份（第 1-5 天）  |   
|---|---|---|
| 每周（第 1-4 周） | 星期六 | 星期一至星期五 |
| 每月  | 星期六  |   |
| 每年 | 星期六  |   |   |

## <a name="assigning-storsimple-volumes-to-a-netbackup-backup-job"></a>将 StorSimple 卷分配到 NetBackup 备份作业

以下步骤顺序假设已根据 NetBackup 代理指导原则配置 NetBackup 和目标主机。

### <a name="to-assign-storsimple-volumes-to-a-netbackup-backup-job"></a>将 StorSimple 卷分配到 NetBackup 备份作业

1.  在 NetBackup 管理控制台中，选择“NetBackup 管理”，右键单击“策略”，然后选择“新建策略”。

    ![NetBackup 管理控制台，创建新策略](./media/storsimple-configure-backup-target-using-netbackup/nbimage6.png)

2.  在“添加新策略”对话框中，输入策略的名称，然后选中“使用策略配置向导”复选框。 选择“确定”。

    ![NetBackup 管理控制台，“添加新策略”对话框](./media/storsimple-configure-backup-target-using-netbackup/nbimage7.png)

3.  在备份策略配置向导中，选择所需的备份类型，然后选择“下一步”。

    ![NetBackup 管理控制台，选择备份类型](./media/storsimple-configure-backup-target-using-netbackup/nbimage8.png)

4.  若要设置策略类型，请选择“标准”，然后选择“下一步”。

    ![NetBackup 管理控制台，选择策略类型](./media/storsimple-configure-backup-target-using-netbackup/nbimage9.png)

5.  选择主机，选中“检测客户端操作系统”复选框，，然后选择“添加”。 选择“**下一步**”。

    ![NetBackup 管理控制台，在新策略中列出客户端](./media/storsimple-configure-backup-target-using-netbackup/nbimage10.png)

6.  选择要备份的驱动器。

    ![NetBackup 管理控制台，新策略的备份选择内容](./media/storsimple-configure-backup-target-using-netbackup/nbimage11.png)

7.  选择满足备份轮转要求的频率和保留值。

    ![NetBackup 管理控制台，新策略的备份频率和轮转](./media/storsimple-configure-backup-target-using-netbackup/nbimage12.png)

8.  选择“下一步” > “下一步” > “完成”。  创建策略后，可以修改计划。

9.  选择刚刚创建的策略将它展开，然后选择“计划”。

    ![NetBackup 管理控制台，新策略的计划](./media/storsimple-configure-backup-target-using-netbackup/nbimage13.png)

10.  右键单击“Differential-Inc”，选择“复制到新计划”，然后选择“确定”。

    ![NetBackup 管理控制台，将计划复制到新策略](./media/storsimple-configure-backup-target-using-netbackup/nbimage14.png)

11.  右键单击新建的计划，然后选择“更改”。

12.  在“属性”选项卡中，选中“覆盖策略存储选择”复选框，然后选择星期一增量备份要存储到的卷。

    ![NetBackup 管理控制台，更改计划](./media/storsimple-configure-backup-target-using-netbackup/nbimage15.png)

13.  在“开始时段”选项卡中选择备份的时段。

    ![NetBackup 管理控制台，更改开始时段](./media/storsimple-configure-backup-target-using-netbackup/nbimage16.png)

14.  选择“确定”。

15.  针对每个增量备份重复步骤 10-14。 针对创建的每个备份选择适当的卷和计划。

16.  右键单击“Differential-Inc”计划并将它删除。

17.  根据备份需要修改完整备份计划。

    ![NetBackup 管理控制台，更改完整备份计划](./media/storsimple-configure-backup-target-using-netbackup/nbimage17.png)

18.  更改开始时段。

    ![NetBackup 管理控制台，更改开始时段](./media/storsimple-configure-backup-target-using-netbackup/nbimage18.png)

19.  最终的计划如下所示：

    ![NetBackup 管理控制台，最终的计划](./media/storsimple-configure-backup-target-using-netbackup/nbimage19.png)

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>将 StorSimple 设置为辅助备份目标

> [!NOTE]
>从云中分层的备份还原数据将以云的速度进行。

在此模型中，必须使用一个存储媒体（不是 StorSimple）充当临时缓存。 例如，可以使用独立磁盘冗余阵列 (RAID) 卷来满足空间、输入/输出 (I/O) 和带宽要求。 建议使用 RAID 5、50 和 10。

下图显示了典型的短期保留本地卷（在服务器上）和长期保留存档卷。 在此方案中，所有备份在服务器本地 RAID 卷上运行。 将定期删除这些备份中的重复数据并将其存档到存档卷。 必须调整服务器本地 RAID 卷的大小，使其能够应对短期保留容量和性能要求。

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>用作辅助备份目标的 StorSimple GFS 示例

![用作辅助备份目标的 StorSimple 的逻辑示意图](./media/storsimple-configure-backup-target-using-netbackup/secondarybackuptargetdiagram.png)

下表显示如何将备份设置为在本地和 StorSimple 磁盘中运行。 其中包含各个磁盘的要求以及总容量要求。

### <a name="backup-configuration-and-capacity-requirements"></a>备份配置和容量要求

| 备份类型和保留期 | 配置的存储 | 大小 (TiB) | GFS 乘数 | 总容量\* (TiB) |
|---|---|---|---|---|
| 第 1 周（完整和增量） |本地磁盘（短期）| 1 | 1 | 1 |
| StorSimple（第 2-4 周） |StorSimple 磁盘（长期） | 1 | 4 | 4 |
| 每月完整备份 |StorSimple 磁盘（长期） | 1 | 12 | 12 |
| 每年完整备份 |StorSimple 磁盘（长期） | 1 | 1 | 1 |
|GFS 卷大小要求 |  |  |  | 18*|
\* 总容量包括 17 TiB 的 StorSimple 磁盘和 1 TiB 的本地 RAID 卷。


### <a name="gfs-example-schedule-gfs-rotation-weekly-monthly-and-yearly-schedule"></a>GSF 示例计划：每周、每月和每年 GFS 轮转计划

| 周次 | 完整 | 第 1 天增量备份 | 第 2 天增量备份 | 第 3 天增量备份 | 第 4 天增量备份 | 第 5 天增量备份 |
|---|---|---|---|---|---|---|
| 第 1 周 | 本地 RAID 卷  | 本地 RAID 卷 | 本地 RAID 卷 | 本地 RAID 卷 | 本地 RAID 卷 | 本地 RAID 卷 |
| 第 2 周 | StorSimple（第 2-4 周） |   |   |   |   |   |
| 第 3 周 | StorSimple（第 2-4 周） |   |   |   |   |   |
| 第 4 周 | StorSimple（第 2-4 周） |   |   |   |   |   |
| 每月 | StorSimple 每月备份 |   |   |   |   |   |
| 每年 | StorSimple 每年备份  |   |   |   |   |   |   |


## <a name="assign-storsimple-volumes-to-a-netbackup-archive-and-duplication-job"></a>将 StorSimple 卷分配到 NetBackup 存档和重复数据删除作业

由于 NetBackup 提供众多的存储和媒体管理选项，因此，我们建议咨询 Veritas 或 NetBackup 架构师来正确评估存储生命周期策略 (SLP) 要求。

定义初始磁盘池后，需要额外定义&3; 个存储生命周期策略，因此总共有&4; 个策略：
* LocalRAIDVolume
* StorSimpleWeek2-4
* StorSimpleMonthlyFulls
* StorSimpleYearlyFulls

### <a name="to-assign-storsimple-volumes-to-a-netbackup-archive-and-duplication-job"></a>将 StorSimple 卷分配到 NetBackup 存档和重复数据删除作业

1.  在 NetBackup 管理控制台中，选择“存储” > “存储生命周期策略” > “新建存储生命周期策略”。

    ![NetBackup 管理控制台，新建存储生命周期策略](./media/storsimple-configure-backup-target-using-netbackup/nbimage20.png)

2.  输入快照的名称，然后选择“添加”。

3.  在“新建操作”对话框中的“属性”选项卡上，为“操作”选择“备份”。 为“目标存储”、“保留类型”和“保留期”选择所需的值。 选择“确定”。

    ![NetBackup 管理控制台，“新建操作”对话框](./media/storsimple-configure-backup-target-using-netbackup/nbimage22.png)

    现已定义第一个备份操作和存储库。

4.  突出显示前一个操作将其选中，然后选择“添加”。 在“更改存储操作”对话框中，为“目标存储”、“保留类型”和“保留期”选择所需的值。

    ![NetBackup 管理控制台，“更改存储操作”对话框](./media/storsimple-configure-backup-target-using-netbackup/nbimage23.png)

5.  突出显示前一个操作将其选中，然后选择“添加”。 在“新建存储生命周期策略”对话框框中，添加一年的每月备份。

    ![NetBackup 管理控制台，“新建存储生命周期策略”对话框](./media/storsimple-configure-backup-target-using-netbackup/nbimage24.png)

6.  重复步骤 4-5，直到已创建所需的全面 SLP 保留策略。

    ![NetBackup 管理控制台，在“新建存储生命周期策略”对话框中添加策略](./media/storsimple-configure-backup-target-using-netbackup/nbimage25.png)

7.  定义完 SLP 保留策略后，请在“策略”下面，根据[将 StorSimple 卷分配到 NetBackup 备份作业](#assigning-storsimple-volumes-to-a-netbackup-backup-job)中详述的步骤定义备份策略。

8.  在“更改计划”对话框中的“计划”下面，右键单击“完整”，然后选择“更改”。

    ![NetBackup 管理控制台，“更改计划”对话框](./media/storsimple-configure-backup-target-using-netbackup/nbimage26.png)

9.  选择“覆盖策略存储选择”，然后选择在步骤 1-6 中创建的 SLP 保留策略。

    ![NetBackup 管理控制台，覆盖策略存储选择](./media/storsimple-configure-backup-target-using-netbackup/nbimage27.png)

10.  选择“确定”，然后针对增量备份计划重复上述步骤。

    ![NetBackup 管理控制台，增量备份的“更改计划”对话框](./media/storsimple-configure-backup-target-using-netbackup/nbimage28.png)


| 每种备份类型的保留期 | 大小 (TiB) | GFS 乘数\* | 总容量 (TiB)  |
|---|---|---|---|
| 每周完整备份 |  1  |  4 | 4  |
| 每日增量备份  | 0.5  | 20（周期等于每月的周数） | 12（使用&2; 个以提高配额） |
| 每月完整备份  | 1 | 12 | 12 |
| 每年完整备份 | 1  | 10 | 10 |
| GFS 要求  |     |     | 38 |
| 提高的配额  | 4  |    | 总共&42;，满足 GFS 要求 |
\* GFS 乘数是需要根据备份策略要求保护和保留的副本数。

## <a name="storsimple-cloud-snapshots"></a>StorSimple 云快照

StorSimple 云快照可保护 StorSimple 设备中的数据。 创建云快照相当于将本地备份磁带寄送到场外设施。 如果使用 Azure 异地冗余存储，则创建云快照就相当于将备份磁带寄送到多个站点。 如果发生灾难后需要还原设备，可将另一个 StorSimple 设备联机，然后执行故障转移。 故障转移后，可从最新的云快照访问数据（以云的速度）。

以下部分介绍如何创建一个简短脚本，用于在备份后处理过程中启动和删除 StorSimple 云快照。

> [!NOTE]
> 手动或以编程方式创建的快照不遵循 StorSimple 快照过期策略。 必须手动或以编程方式删除这些快照。

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>使用脚本启动和删除云快照

> [!NOTE]
> 在删除 StorSimple 快照之前，请仔细评估造成的合规性与数据保留后果。 有关如何运行备份后脚本的详细信息，请参阅 [NetBackup 文档](http://www.veritas.com/docs/000094423)。

### <a name="backup-lifecycle"></a>备份生命周期

![备份生命周期示意图](./media/storsimple-configure-backup-target-using-netbackup/backuplifecycle.png)

### <a name="requirements"></a>要求

-   运行脚本的服务器必须能够访问 Azure 云资源。
-   用户帐户必须拥有所需的权限。
-   必须设置但不要启用具有关联 StorSimple 卷的 StorSimple 备份策略。
-   需要 StorSimple 资源名称、注册密钥、设备名称和备份策略 ID。

### <a name="to-start-or-delete-a-cloud-snapshot"></a>启动或删除云快照

1.  [安装 Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azureps-cmdlets-docs/#install-and-configure)。
2.  [下载并导入发布设置和订阅信息](https://msdn.microsoft.com/library/dn385850.aspx)。
3.  在 Azure 经典门户中获取资源名称和 [StorSimple Manager 服务的注册密钥](storsimple-deployment-walkthrough-u2.md#step-2-get-the-service-registration-key)。
4.  在运行脚本的服务器上，以管理员身份运行 PowerShell。 键入以下命令：

    `Get-AzureStorSimpleDeviceBackupPolicy –DeviceName <device name>`

    记下备份策略 ID。
5.  在记事本中，使用以下代码创建新的 PowerShell 脚本。

    复制并粘贴以下代码片段：
    ```powershell
    Import-AzurePublishSettingsFile "c:\\CloudSnapshot Snapshot\\myAzureSettings.publishsettings"
    Disable-AzureDataCollection
    $ApplianceName = <myStorSimpleApplianceName>
    $RetentionInDays = 20
    $RetentionInDays = -$RetentionInDays
    $Today = Get-Date
    $ExpirationDate = $Today.AddDays($RetentionInDays)
    Select-AzureStorSimpleResource -ResourceName "myResource" –RegistrationKey
    Start-AzureStorSimpleDeviceBackupJob –DeviceName $ApplianceName -BackupType CloudSnapshot -BackupPolicyId <BackupId> -Verbose
    $CompletedSnapshots =@()
    $CompletedSnapshots = Get-AzureStorSimpleDeviceBackup -DeviceName $ApplianceName
    Write-Host "The Expiration date is " $ExpirationDate
    Write-Host

    ForEach ($SnapShot in $CompletedSnapshots)
    {
        $SnapshotStartTimeStamp = $Snapshot.CreatedOn
        if ($SnapshotStartTimeStamp -lt $ExpirationDate)

        {
            $SnapShotInstanceID = $SnapShot.InstanceId
            Write-Host "This snpashotdate was created on " $SnapshotStartTimeStamp.Date.ToShortDateString()
            Write-Host "Instance ID " $SnapShotInstanceID
            Write-Host "This snpashotdate is older and needs to be deleted"
            Write-host "\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#"
            Remove-AzureStorSimpleDeviceBackup -DeviceName $ApplianceName -BackupId $SnapShotInstanceID -Force -Verbose
        }
    }
    ```
      将该 PowerShell 脚本保存到 Azure 发布设置所在的同一位置。 例如，另存为 C:\CloudSnapshot\StorSimpleCloudSnapshot.ps1。
6.  将该脚本添加到 NetBackup 中的备份作业。 为此，请编辑 NetBackup 作业选项的前处理和后处理命令。

> [!NOTE]
> 建议在每日备份作业结束时，以后处理脚本的形式运行 StorSimple 云快照备份策略。 有关如何备份和还原备份应用程序环境以帮助满足 RPO 和 RTO 要求的详细信息，请咨询备份架构师。

## <a name="storsimple-as-a-restore-source"></a>用作还原源的 StorSimple

从 StorSimple 设备还原的过程类似于从任何块存储设备还原。 还原已在云中分层的数据将以云的速度进行。 对于本地数据，还原将以设备本地磁盘的速度进行。 有关如何执行还原的信息，请参阅 [NetBackup 文档](http://www.veritas.com/docs/000094423)。 我们建议遵循 NetBackup 还原最佳实践。

## <a name="storsimple-failover-and-disaster-recovery"></a>StorSimple 故障转移和灾难恢复

> [!NOTE]
> 对于备份目标方案，不支持将 StorSimple 云设备用作恢复目标。

灾难的发生可能会出于多种因素。 下表列出了常见的灾难恢复方案。

| 方案 | 影响 | 如何恢复 | 说明 |
|---|---|---|---|
| StorSimple 设备故障 | 备份和还原操作将会中断。 | 更换有故障的设备，然后执行 [StorSimple 故障转移和灾难恢复](storsimple-device-failover-disaster-recovery.md)。 | 如果在恢复设备后需要执行还原，则需要将云中的完整工作集检索到新设备。 所有操作都以云的速度进行。 索引和目录重新扫描过程可能会导致扫描所有备份集并将其从云层提取到本地设备层，因此可能非常耗时。 |
| NetBackup 服务器故障 | 备份和还原操作将会中断。 | 重新构建备份服务器并执行数据库还原。 | 必须在灾难恢复站点重建或还原 NetBackup 服务器。 将数据库还原到最近的时间点。 如果还原的 NetBackup 数据库未与最新的备份作业同步，则需要编制索引和目录。 这种索引和目录重新扫描过程可能会导致扫描所有备份集并将其从云层提取到本地设备层。 这会进一步消耗时间。 |
| 站点发生故障，导致备份服务器和 StorSimple 丢失 | 备份和还原操作将会中断。 | 首先还原 StorSimple，然后还原 NetBackup。 | 首先还原 StorSimple，然后还原 NetBackup。 如果在恢复设备后需要执行还原，则需要将云中的完整工作集检索到新设备。 所有操作都以云的速度进行。 |

## <a name="references"></a>参考

本文参考了以下文档：

- [StorSimple multipath I/O setup](storsimple-configure-mpio-windows-server.md)（StorSimple 多路径 I/O 设置）
- [Storage scenarios: Thin provisioning](http://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)（存储方案：精简预配）
- [Using GPT drives](http://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)（使用 GPT 驱动器）
- [Set up shadow copies for shared folders](http://technet.microsoft.com/library/cc771893.aspx)（设置共享文件夹的卷影副本）

## <a name="next-steps"></a>后续步骤

- 详细了解如何[从备份集还原](storsimple-restore-from-backup-set-u2.md)。
- 详细了解如何执行[设备故障转移和灾难恢复](storsimple-device-failover-disaster-recovery.md)。

