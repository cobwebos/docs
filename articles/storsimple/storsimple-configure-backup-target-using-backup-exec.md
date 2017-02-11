---
title: "使用 Veritas Backup Exec 配置 StorSimple | Microsoft 文档"
description: "介绍如何使用 Veritas Backup Exec 配置 StorSimple 备份目标。"
services: storsimple
documentationcenter: 
author: hkanna
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
ms.sourcegitcommit: b7c6a9ffb2c598048bf0547420b1e7afac2c3e74
ms.openlocfilehash: 7a2fa4ccac6b2eda935477034c5f58a0ed90da37

---

# <a name="configure-storsimple-with-veritas-backup-exec8482"></a>使用 Veritas Backup Exec&#8482; 配置 StorSimple

## <a name="overview"></a>概述

Microsoft Azure StorSimple 是一种混合型云存储解决方案，可解决数据呈指数级增长时带来的复杂性。 此解决方案使用 Azure 存储帐户作为本地解决方案的扩展，可跨本地存储和云存储自动将数据分层。

本文介绍 StorSimple 与 Veritas Backup Exec 的集成，以及集成这两种解决方案时的最佳实践。 此外，还提供了有关如何配置 Veritas Backup Exec，以实现与 StorSimple 的最佳集成的建议。 我们将遵循 Veritas 最佳实践以及备份架构师与管理员的意见，最合理地配置 Veritas Backup Exec，旨在满足用户的备份要求和 SLA。

本文介绍了配置步骤和重要概念，但并未介绍循序渐进的配置，不能用作安装指南。 本文假设基本组件与基础结构工作正常，随时能够支持本文中所述的操作。

## <a name="why-storsimple-as-a-backup-target"></a>为何将 StorSimple 用作备份目标？

StorSimple 是极佳的备份目标，原因如下：

-   它为备份应用程序提供标准的本地存储，无需进行任何更改即可使用，提供快速的备份目标。 StorSimple 还可用于快速还原最近的备份。

-   它的云层与经济高效的 Microsoft Azure 云存储无缝集成。

-   它提供场外存储，实现灾难恢复。


## <a name="target-audience"></a>目标读者

本白皮书的读者包括具有存储、Windows Server 2012 R2、以太网、云服务和 Veritas Backup Exec 相关知识的备份管理员、存储管理员和存储架构师。

## <a name="supported-versions"></a>支持的版本

有关受支持版本的完整列表，请参阅：

-   [Backup Exec 16 和更高版本](http://backupexec.com/compatibility)。

-   [StorSimple Update 3 和更高版本](storsimple-overview.md#storsimple-workload-summary)。

## <a name="key-concepts"></a>关键概念

与其他任何存储解决方案一样，对解决方案的存储性能、SLA、更改率以及容量增长需求展开细致的评估是成功的关键所在。 主要思路是引入云层，要使 StorSimple 发挥其作用，云的访问时间与吞吐量发挥着至关重要的作用。

StorSimple 旨在为处理妥善定义的工作数据集（热数据）的应用程序提供存储。 在此模型中，工作数据集存储在本地层中，剩余的非工作/冷/存档数据集在云中分层。 下图演示了此模型。 平坦的绿线表示存储在 StorSimple 设备本地层中的数据。 红线表示 StorSimple 解决方案的所有层中存储的总数据量。 平坦绿线与呈指数级升高的红色曲线之间的空间表示存储在云中的总数据量。

**StorSimple 分层**
![Storsimple 分层示意图](./media/storsimple-configure-backup-target-using-backup-exec/image1.jpg)

知道这种体系结构后，将会发现 StorSimple 非常适合用作备份目标。 使用 StorSimple 可以：

-   通过本地工作数据集执行最常见的还原。

-   使用云实现场外灾难恢复，存储不经常还原的陈旧数据。

## <a name="storsimple-benefits"></a>StorSimple 的优势

StorSimple 提供与 Microsoft Azure 无缝集成的本地解决方案，可让用户无缝访问本地存储和云存储。

StorSimple 在包含固态设备 (SSD) 的本地设备、串行附加 SCSI (SAS) 存储与 Azure 存储之间使用自动分层。 自动分层将频繁访问的本地数据保留在 SSD 和 SAS 层中，将不经常访问的数据移到 Azure 存储。

StorSimple 提供以下优势：

-   独特的重复数据删除和压缩算法，使用云实现前所未有的重复数据删除能力

-   高可用性

-   利用 Azure 异地复制实现异地复制

-   Azure 集成

-   云中的数据加密

-   改进灾难恢复和合规性

尽管 StorSimple 提供两种主要部署方案（主备份目标和辅助备份目标），但请记住，它本质上是一个普通的块存储设备。 StorSimple 执行所有的压缩和重复数据删除操作，将数据从云无缝发送到应用程序和文件系统，以及从中检索数据。

有关 StorSimple 的详细信息，请参阅 [StorSimple 8000 系列：混合云存储解决方案](storsimple-overview.md)以及 [StorSimple 8000 系列技术规范](storsimple-technical-specifications-and-compliance.md)。

> [!IMPORTANT]
> 只有 StorSimple 8000 Update 3 或更高版本才支持使用 StorSimple 设备作为备份目标。

## <a name="architecture-overview"></a>体系结构概述

下表包含设备模型到体系结构的初步指导。

#### <a name="storsimple-capacities-for-local-and-cloud-storage"></a>用于本地存储和云存储的 StorSimple 容量


| 存储容量       | 8100          | 8600            |
|------------------------|---------------|-----------------|
| 本地存储容量 | &lt; 10 TiB\*  | &lt; 20 TiB\*  |
| 云存储容量 | &gt; 200 TiB\* | &gt; 500 TiB\* |

\* 未经过重复数据删除或压缩的存储大小。

#### <a name="storsimple-capacities-for-primary-and-secondary-backups"></a>用于主备份和辅助备份的 StorSimple 容量


| 备份方案  | 本地存储容量                                         | 云存储容量                      |
|------------------|----------------------------------------------------------------|---------------------------------------------|
| 主备份   | 最近的备份存储在本地存储中以加快恢复速度 (RPO) | 备份历史记录 (RPO) 占用云容量 |
| 辅助备份 | 备份数据的辅助副本可存储在云容量中  |

## <a name="storsimple-as-a-primary-backup-target"></a>用作主备份目标的 StorSimple

在此方案中，StorSimple 卷将提供给备份应用程序作为唯一的备份存储库。 下图演示了其中所有备份使用 StorSimple 分层卷进行备份和还原的解决方案体系结构。

![用作主备份目标的 StorSimple 的逻辑示意图](./media/storsimple-configure-backup-target-using-backup-exec/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>主目标备份逻辑步骤

1.  备份服务器与目标备份代理通信，备份代理将数据传输到备份服务器。

2.  备份服务器将数据写入 StorSimple 分层卷。

3.  备份服务器更新目录数据库，完成备份作业。

4.  快照脚本触发 StorSimple 云快照管理（启动-删除）。

5.  备份服务器根据保留策略删除过期的备份。

###  <a name="primary-target-restore-logical-steps"></a>主目标还原逻辑步骤

1.  备份服务器开始从存储库还原相应的数据。

2.  备份代理从备份服务器接收数据。

3.  备份服务器完成还原作业。

## <a name="storsimple-as-a-secondary-backup-target"></a>用作辅助备份目标的 StorSimple

在此方案中，StorSimple 卷主要用于长期保留或存档。

下图演示了其中的初始备份和还原针对高性能卷的体系结构。 这些备份将按指定的计划复制并存档到 StorSimple 分层卷。

必须调整高性能卷的大小，使其具有足够的空间和性能来应对保留策略容量和性能要求。

![用作辅助备份目标的 StorSimple 的逻辑示意图](./media/storsimple-configure-backup-target-using-backup-exec/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>辅助目标备份逻辑步骤

1.  备份服务器与目标备份代理通信，备份代理将数据传输到备份服务器。

2.  备份服务器将数据写入高性能存储。

3.  备份服务器更新目录数据库，完成备份作业。

4.  备份服务器根据保留策略将备份复制到 StorSimple。

5.  快照脚本触发 StorSimple 云快照管理（启动-删除）。

6.  备份服务器根据保留策略删除过期的备份。

### <a name="secondary-target-restore-logical-steps"></a>辅助目标还原逻辑步骤

1.  备份服务器开始从存储库还原相应的数据。

2.  备份代理从备份服务器接收数据。

3.  备份服务器完成还原作业。

## <a name="deploy-the-solution"></a>部署解决方案

此解决方案的部署包括三个步骤：准备网络基础结构，将 StorSimple 设备部署为备份目标，最后部署 Veritas Backup Exec。 以下部分详细介绍了这些步骤。

### <a name="configure-the-network"></a>配置网络

与 Azure 云集成的 StorSimple 解决方案需要与 Azure 云建立有效的连接。 此连接用于如下所述的操作：云快照、管理、元数据传输，以及在 Azure 云存储中将不经常访问的旧数据分层。

为使该解决方案保持最佳性能，我们建议遵守以下网络最佳实践：

-   用于将 StorSimple 层连接到 Azure 的链接必须通过向基础结构交换机应用适当的服务质量 (QoS) 来满足带宽要求以及 RPO/RTO SLA。

-   最大 Azure Blob 存储访问延迟应在 80 毫秒范围内。

### <a name="deploy-storsimple"></a>部署 StorSimple

有关 StorSimple 分步部署指南，请参阅[部署本地 StorSimple 设备](storsimple-deployment-walkthrough-u2.md)。

### <a name="deploy-veritas-backup-exec"></a>部署 Veritas Backup Exec

有关 Veritas Backup Exec 的安装最佳实践，请参阅 [Backup Exec 的安装最佳实践](https://www.veritas.com/support/en_US/article.000068207)。

## <a name="configure-the-solution"></a>配置解决方案

本部分演示一些配置示例。 以下示例/建议演示最基本的实现。 不一定能够根据具体的备份要求直接应用此实现。

### <a name="configure-storsimple"></a>配置 StorSimple

| StorSimple 部署任务                                                                                                                 | 附加备注                                                                                                                                                                                                                                                                                      |
|---------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 部署本地 StorSimple 设备。                                                                                                | 支持的版本：Update 3 和更高版本。                                                                                                                                                                                                                                                                 |
| 启用备份目标模式。                                                                                                                   | 使用以下命令启用/禁用和获取状态。 有关详细信息，请参阅[远程连接到 StorSimple](storsimple-remote-connect.md)。</br> 启用备份模式：`Set-HCSBackupApplianceMode -enable`</br>  禁用备份模式：`Set-HCSBackupApplianceMode -disable`</br> 备份模式设置的当前状态`Get-HCSBackupApplianceMode` |
| 为存储备份数据的卷创建通用卷容器。   卷容器中的所有数据都已删除重复数据。 | StorSimple 卷容器定义重复数据删除域。                                                                                                                                                                                                                                             |
| 创建 StorSimple 卷                                                                                                                 | 创建大小尽量接近预期用量的卷，因为卷大小会影响云快照持续时间。 有关如何调整卷大小的详细信息，请参阅[保留策略](#retention-policies)。</br> </br> 使用 StorSimple 分层卷，并选中“将此卷用于不常访问的存档数据”。 </br> 不支持本地固定卷。|
| 为所有备份目标卷创建唯一的 StorSimple 备份策略。                                                               | StorSimple 备份策略定义卷一致性组。                                                                                                                                                                                                                                       |
| 禁用计划为快照。                                                                                                    | 快照将作为后处理操作触发。                                                                                                                                                                                                                                                         |
|                                                                                                     |                                             |




### <a name="configure-host-backup-server-storage"></a>配置主机备份服务器存储

确保根据以下指导原则配置主机备份服务器存储。  

- 不支持跨区卷（由 Windows 磁盘管理器创建）。
- 使用 NTFS 和 64 KB 分配大小格式化卷。
- 将 StorSimple 卷直接映射到“Veeam”服务器。
    - 为物理服务器使用 iSCSI。
    - 为虚拟服务器使用直通磁盘。


## <a name="best-practices-for-storsimple-and-veritas-backup-exec"></a>StorSimple 和 Veritas Backup Exec 的最佳实践

根据以下指导原则配置解决方案。


### <a name="operating-system"></a>操作系统

-   在 NTFS 文件系统中禁用 Windows Server 加密和重复数据删除。

-   在 StorSimple 卷上禁用 Windows Server 碎片整理。

-   在 StorSimple 卷上禁用 Windows Server 索引编制。

-   在源主机上执行防病毒扫描（不是针对 StorSimple 卷）。

-   在任务管理器中禁用默认的“Windows Server 维护”。[](https://msdn.microsoft.com/library/windows/desktop/hh848037.aspx)

    - 在 Windows 任务计划程序中禁用“维护”配置器。

        或

    - 下载：[PSEXEC – Microsoft Sysinternals](https://technet.microsoft.com/sysinternals/bb897553.aspx)

      - 下载 PSEXEC 后，以管理员身份运行 Windows PowerShell，键入：

            `psexec \\%computername% -s schtasks /change /tn “MicrosoftWindowsTaskSchedulerMaintenance Configurator" /disable`

### <a name="storsimple"></a>StorSimple

-   确保将 StorSimple 设备更新到 [Update 3 或更高版本](storsimple-install-update-3.md)。

-   隔离 iSCSI 和云流量。 使用专用的 iSCSI 连接在 StorSimple 与备份服务器之间传送流量。

-   确保 StorSimple 设备是专用的备份目标。 不支持混合工作负荷，因为这会影响 RTO/RPO。

### <a name="veritas-backup-exec"></a>Veritas Backup Exec

-   Veritas Backup Exec 必须安装在服务器的本地驱动器上，而不能安装在 StorSimple 卷上。

-   将 Veritas Backup Exec 存储的**并发写入操作**设置为允许的最大值。

    -   应将 Veritas Backup Exec 存储的**块和缓冲区大小**设置为 512 KB。

    -   应启用 Veritas Backup Exec 存储的**缓冲读取和写入**。

-   支持 Veritas Backup Exec 完整和增量备份，但不建议执行合成与差异备份。

-   备份数据文件应只包含特定作业的数据。 例如，不允许跨不同的作业附加媒体。

-   禁用作业验证。 如果需要，应在最新的备份作业之后计划验证。 必须知道，此作业会影响备份持续时间。

-   在“存储”>“你的磁盘”>“详细信息”>“属性”中，禁用“预分配磁盘空间”。

有关最新的 Veritas Backup Exec 设置以及有关如何实现这些要求的最佳实践，请参阅 [www.veritas.com](https://www.veritas.com) 上的 Veritas Backup Exec 文档。

## <a name="retention-policies"></a>保留策略

最常用的备份保留策略之一为祖父、父和子 (GFS)。 在此策略中，增量备份每日执行。 完整备份每周和每月执行。 此策略会导致生成 6 个 StorSimple 分层卷。

-   一个卷包含每周、每月和每年完整备份。

-   另外 5 个卷存储每日增量备份。

以下示例使用 GFS 轮转。 该示例的假设条件如下：

-   使用未经过重复数据删除或压缩的数据。

-   每个完整备份的大小为 1 TiB。

-   每个每日增量备份的大小为 500 GiB。

-   4 个每周备份保留一个月。

-   12 个每月备份保留一年。

-   1 个每年备份保留 10 年。

根据前面的假设，将为每月和每年完整备份创建 26 TiB 的 StorSimple 分层卷。 将为每个每日增量备份创建 5 TiB 的 StorSimple 分层卷。

| 每种备份类型的保留期 | 大小 (TiB) | GFS 乘数\*                                       | 总容量 (TiB)          |
|-----------------------|----------|--------------------------------------------------------|-----------------------------|
| 每周完整备份           | 1        | 4                                                      | 4                           |
| 每日增量备份     | 0.5      | 20（周期等于每月周数） | 12（使用 2 个以提高配额） |
| 每月完整备份          | 1        | 12                                                     | 12                          |
| 每年完整备份           | 1        | 10                                                     | 10                          |
| GFS 要求       |          |                                                        | 38                          |
| 提高的配额      | 4        |                                                        | 总共 42，满足 GFS 要求   |

\*GFS 乘数是需要根据备份策略保护和保留的副本数。

## <a name="configure-veritas-backup-exec-storage"></a>配置 Veritas Backup Exec 存储

1.  在 Veritas Backup Exec 管理控制台中，选择“存储”&gt;“配置存储”&gt;“基于磁盘的存储”。 单击“下一步”。**&gt;**

    ![Veritas Backup Exec 管理控制台，配置存储屏幕](./media/storsimple-configure-backup-target-using-backup-exec/image4.png)

1.  单击“磁盘存储”转到下一页。

    ![Veritas Backup Exec 管理控制台，选择存储屏幕](./media/storsimple-configure-backup-target-using-backup-exec/image5.png)

1.  输入有代表性的名称（例如“星期六完整备份”）和附带的说明。 转到下一页。

    ![Veritas Backup Exec 管理控制台，名称和说明屏幕](./media/storsimple-configure-backup-target-using-backup-exec/image7.png)

1.  选择相应的磁盘，然后单击“下一步”。**&gt;** 例如，这里选择了 D:。

    ![Veritas Backup Exec 管理控制台，存储磁盘选择屏幕](./media/storsimple-configure-backup-target-using-backup-exec/image9.png)

1.  将写入操作数目增大到 16，然后单击“下一步”。**&gt;**

    ![Veritas Backup Exec 管理控制台，并发写入操作设置屏幕 ](./media/storsimple-configure-backup-target-using-backup-exec/image10.png)

1.  检查设置，然后单击“完成”。

    ![Veritas Backup Exec 管理控制台，存储配置摘要屏幕](./media/storsimple-configure-backup-target-using-backup-exec/image11.png)

1.  每次分配结束时，请根据最佳实践列表中的建议设置更改存储设备设置。

    ![Veritas Backup Exec 管理控制台，存储设备设置屏幕](./media/storsimple-configure-backup-target-using-backup-exec/image12.png)

1.  重复上述所有步骤，直至完成将 StorSimple 卷分配到 Veritas Backup Exec。

## <a name="storsimple-as-a-primary-backup-target"></a>用作主备份目标的 StorSimple

> [!NOTE]
> 请注意，如果需要从云中分层的备份还原数据，还原将以云的速度进行。

下图演示了典型卷到备份作业的映射。 在本例中，所有每周备份映射到星期六完整备份磁盘，增量备份映射到星期一至星期五增量备份磁盘。 所有备份和还原操作从 StorSimple 分层卷发生。

![主备份目标配置逻辑示意图 ](./media/storsimple-configure-backup-target-using-backup-exec/primarybackuptargetdiagram.png)

#### <a name="storsimple-as-a-primary-backup-target-grandfather-father-and-son-gfs-schedule-example"></a>用作主备份目标的 StorSimple 的祖父、父和子 (GFS) 计划示例

| 4 周、每月和每年的 GFS 轮转计划 |               |             |
|--------------------------------------------------------------------------|---------------|-------------|
| 频率/备份类型   | 完整          | 增量备份（第 1-5 天）  |
| 每周（第 1-4 周）    | 星期六 | 星期一至星期五 | 
| 每月     | 星期六  |             |
| 每年      | 星期六  |             |


### <a name="assigning-storsimple-volumes-to-a-veritas-backup-exec-backup-job"></a>将 StorSimple 卷分配到 Veritas Backup Exec 备份作业。

以下步骤顺序假设已根据 Backup Exec 代理指导原则配置 Backup Exec 和目标主机。

1.  在 Veritas Backup Exec 管理控制台中，转到“主机”&gt;“备份”&gt;“备份到磁盘”&gt;。

    ![Veritas Backup Exec 管理控制台，选择“主机”、“备份”、“备份到磁盘”](./media/storsimple-configure-backup-target-using-backup-exec/image14.png)

1.  在“备份定义属性”窗口中，选择“编辑”（在“备份”下面）。

    ![在“备份定义属性”窗口中选择“编辑”](./media/storsimple-configure-backup-target-using-backup-exec/image15.png)

1.  配置完整和增量备份，使其符合 RPO/RTO 要求以及 Veritas 最佳实践。

2.  在“备份选项”窗口中，选择“存储”。

    ![备份选项存储选项卡](./media/storsimple-configure-backup-target-using-backup-exec/image16.png)

1.  将相应的 StorSimple 卷分配到备份计划。

    > [!NOTE]
    > 将“压缩”和“加密类型”设置为“无”。

2.  在“验证”下面，选择“不要验证此作业的数据”，因为这可能会影响 StorSimple 分层。

    > [!NOTE]
    > 碎片整理、索引编制和后台验证会给 StorSimple 分层造成负面影响。

    ![备份选项中的验证设置](./media/storsimple-configure-backup-target-using-backup-exec/image17.png)

1.  根据要求配置剩余的备份选项后，请选择“确定”完成配置。

## <a name="storsimple-as-a-secondary-backup-target"></a>用作辅助备份目标的 StorSimple

> [!NOTE]
> 请注意，如果需要从云中分层的备份还原数据，还原将以云的速度进行。

在此模型中，必须使用一个存储媒体（不是 StorSimple）充当临时缓存。 例如，可以使用 RAID 卷来满足空间、I/O 和带宽要求。 建议使用 RAID 5、50 和 10。

下图演示了典型的短期保留本地卷（在服务器上）和长期保留存档卷。 在本例中，所有备份在服务器本地 RAID 卷上运行。 将定期删除这些备份中的重复数据并将其存档到存档卷。 必须调整服务器本地 RAID 卷的大小，使其能够应对短期保留容量和性能要求。

#### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>用作辅助备份目标的 StorSimple GFS 示例

![用作辅助备份目标的 StorSimple 的逻辑示意图 ](./media/storsimple-configure-backup-target-using-backup-exec/secondarybackuptargetdiagram.png)

下表演示如何将备份配置为在本地和 StorSimple 磁盘中运行，并列出了各个磁盘的要求以及总容量要求。

#### <a name="backup-configuration-and-capacity-requirements"></a>备份配置和容量要求

| 备份类型和保留期                    |配置的存储| 大小 (TiB) | GFS 乘数 | 总容量 (TiB)        |
|----------------------------------------------|-----|----------|----------------|------------------------|
| 第 1 周（完整和增量） |本地磁盘（短期）| 1        | 1              | 1           |
| StorSimple（第 2-4 周）           |StorSimple 磁盘（长期） | 1        | 4              | 4                   |
| 每月完整备份                                 |StorSimple 磁盘（长期） | 1        | 12             | 12                   |
| 每年完整备份                               |StorSimple 磁盘（长期） | 1        | 1              | 1                   |
|GFS 卷大小要求 | |          |                | 18*|

\* 总容量包括 17 TiB 的 StorSimple 磁盘和 1 TiB 的本地 RAID 卷。


#### <a name="gfs-example-schedule"></a>GFS 示例计划

|每周、每月和每年 GFS 轮转计划|                    |                   |                   |                   |                   |                   |
|--------------------------------------------------------------------------|--------------------|-------------------|-------------------|-------------------|-------------------|-------------------|
| 频率                                                                     | 完整               | 第 1 天增量备份        | 第 2 天增量备份        | 第 3 天增量备份        | 第 4 天增量备份        | 第 5 天增量备份        |
| 第 1 周                                                                   | 本地 RAID 卷  | 本地 RAID 卷 | 本地 RAID 卷 | 本地 RAID 卷 | 本地 RAID 卷 | 本地 RAID 卷 |
| 第 2 周                                                                   | StorSimple（第 2-4 周） |                   |                   |                   |                   |                   |
| 第 3 周                                                                   | StorSimple（第 2-4 周） |                   |                   |                   |                   |                   |
| 第 4 周                                                                   | StorSimple（第 2-4 周） |                   |                   |                   |                   |                   |
| 每月                                                                  | StorSimple 每月备份 |                   |                   |                   |                   |                   |
| 每年                                                                   | StorSimple 每年备份  |                   |                   |                   |                   |                   |


### <a name="assign-storsimple-volumes-to-backup-exec-archivededuplication-job"></a>将 StorSimple 卷分配到 Backup Exec 存档/重复数据删除作业

1.  将初始备份作业配置为使用 RAID 卷作为主备份目标后，请转到 Veritas Backup Exec 管理控制台。 选择要存档到 StorSimple 卷的作业，单击右键，然后选择“备份定义属性”。 单击“编辑”。

    ![Backup Exec 控制台，备份定义属性选项卡](./media/storsimple-configure-backup-target-using-backup-exec/image19.png)

1.  从下拉列表中选择“添加阶段”，然后选择“复制到磁盘”。 单击“编辑”。

    ![Backup Exec 控制台，备份定义 - 添加阶段](./media/storsimple-configure-backup-target-using-backup-exec/image20.png)

2.  在“复制选项”中，选择相应的**源**和**计划**。

    ![Backup Exec 控制台，备份定义属性，复制选项](./media/storsimple-configure-backup-target-using-backup-exec/image21.png)

1.  从“存储”下拉列表中选择存档作业要将数据存储到的 StorSimple 卷。 在本例中，源设置为“完整”，目标设置为 StorSimple 完整存档卷。

    ![Backup Exec 控制台，备份定义属性，复制选项](./media/storsimple-configure-backup-target-using-backup-exec/image22.png)

1.  转到“验证”，选择“不要验证此作业的数据”。

    ![Backup Exec 控制台，备份定义属性，复制选项](./media/storsimple-configure-backup-target-using-backup-exec/image23.png)

1.  单击 **“确定”**。

    ![Backup Exec 控制台，备份定义属性，复制选项](./media/storsimple-configure-backup-target-using-backup-exec/image24.png)

1.  在“备份”列中添加一个新阶段。 选择增量备份作为源，选择增量备份作业要存档到的 StorSimple 卷作为目标。 重复上述步骤。

## <a name="storsimple-cloud-snapshots"></a>StorSimple 云快照

StorSimple 云快照可保护 StorSimple 设备中的数据。 这就相当于将磁带寄送到场外设施；如果使用的是 Azure 异地冗余存储 (GRS)，则就相当于将磁带寄送到多个站点。 如果发生灾难时需要还原设备，可将另一个 StorSimple 设备联机，然后执行故障转移。 故障转移后，便可以从最新的云快照访问数据（以云的速度）。

以下部分演示如何创建一个简短脚本，用于在备份后处理过程中触发和删除 StorSimple 云快照。

> [!NOTE]
> 手动或以编程方式创建的快照不遵循 StorSimple 快照过期策略。 必须手动或以编程方式删除这些快照。

### <a name="start-and-delete-cloud-snapshots-with-a-script"></a>使用脚本启动和删除云快照

> [!NOTE]
> 在删除 StorSimple 快照之前，请仔细评估造成的合规性与数据保留后果。 有关如何运行备份后脚本的详细信息，请参阅 Veritas Backup Exec 文档。

#### <a name="backup-lifecycle"></a>备份生命周期

![备份生命周期示意图](./media/storsimple-configure-backup-target-using-backup-exec/backuplifecycle.png)

#### <a name="requirements"></a>要求：

-   运行脚本的服务器必须能够访问 Azure 云。

-   用户帐户必须拥有所需的权限。

-   已配置具有关联 StorSimple 卷的 StorSimple 备份策略，但尚未启用该策略。

-   StorSimple 资源名称、注册密钥、设备名称和备份策略 ID。

#### <a name="steps"></a>步骤：

1.  [安装 Azure PowerShell](/powershell-install-configure/)。

2.  [下载并导入发布设置和订阅信息](https://msdn.microsoft.com/library/dn385850.aspx)。

3.  在 Azure 经典门户中获取资源名称和 [StorSimple Manager 服务的注册密钥](storsimple-deployment-walkthrough-u2.md#step-2-get-the-service-registration-key)。

4.  在运行脚本的服务器上，以管理员身份运行 Windows PowerShell。 键入：

    -   `Get-AzureStorSimpleDeviceBackupPolicy –DeviceName <device name>`

    记下备份策略 ID。

5.  在记事本中创建新的 Windows PowerShell 脚本，并将其保存到 Azure 发布设置所在的同一位置。 例如，`C:\\CloudSnapshot\\StorSimpleCloudSnapshot.ps1`。

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
        Write-Host "This snapshotdate was created on " $SnapshotStartTimeStamp.Date.ToShortDateString()
        Write-Host "Instance ID " $SnapShotInstanceID
        Write-Host "This snpashotdate is older and needs to be deleted"
        Write-host "\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#"
        Remove-AzureStorSimpleDeviceBackup -DeviceName $ApplianceName -BackupId $SnapShotInstanceID -Force -Verbose
        }
    }   
    ```

1.  通过编辑 Veritas Backup Exec 作业选项前处理-后处理命令，将脚本添加到 Veritas Backup Exec 中的备份作业。

    ![Backup Exec 控制台，备份选项，前处理/后处理命令选项卡](./media/storsimple-configure-backup-target-using-backup-exec/image25.png)

> [!NOTE]
> 建议在每日备份作业结束时，以后处理脚本的形式运行 StorSimple 云快照备份策略。 有关如何根据 RPO/RTO 备份和还原备份应用程序环境的详细信息，请咨询备份架构师。

## <a name="storsimple-as-a-restore-source"></a>用作还原源的 StorSimple

从 StorSimple 还原的过程类似于从任何块存储设备还原。 还原已在云中分层的数据时，还原将以云的速度进行。 对于本地数据，还原将以设备本地磁盘的速度进行。 有关如何执行还原的信息，请参阅 Veritas Backup Exec 文档。请遵守 Veritas Backup Exec 还原最佳实践。

## <a name="storsimple-failover-and-disaster-recovery"></a>StorSimple 故障转移和灾难恢复

> [!NOTE]
> 对于备份目标方案，不支持将 StorSimple 云设备用作恢复目标。

可能会出于多种因素造成灾难。 下表列出了常见的灾难恢复 (DR) 方案。

| 方案                                                                    | 影响                                             | 如何恢复                                                                                                                                                                               | 说明                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|-----------------------------------------------------------------------------|----------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| StorSimple 设备故障。                                                | 备份和还原操作将会中断。 | 更换有故障的设备，然后执行 [StorSimple 故障转移和灾难恢复](storsimple-device-failover-disaster-recovery.md)。 | 如果在恢复设备后立即执行还原，云中的完整工作集将检索到新设备。 因此，所有操作都以云的速度进行。 此外，索引和目录重新扫描可能会导致扫描所有备份集并将其从云层提取到本地设备层。 这会进一步消耗时间。                                                               |
| Veritas Backup Exec 服务器故障。                                       | 备份和还原操作将会中断。 | 根据 [How to do a manual Backup and Restore of Backup Exec (BEDB) database](http://www.veritas.com/docs/000041083)（如何对 Backup Exec (BEDB) 数据库执行手动备份和还原）中所述，重新构建备份服务器并执行数据库还原。            | 需要在 DR 站点重新构建或还原 Veritas Backup Exec 服务器。 数据库需要还原到最近的时间点。 如果还原的 Veritas Backup Exec 数据库未与最新的备份作业同步，则需要编制索引和目录。 这种索引和目录重新扫描可能会导致扫描所有备份集并将其从云层提取到本地设备层。 这会进一步消耗时间。 |
| 站点发生故障，导致备份服务器和 StorSimple 丢失。 | 备份和还原操作将会中断。 | 首先还原 StorSimple，然后还原 Veritas Backup Exec。                                                                                                                                   | 首先还原 StorSimple，然后还原 Veritas Backup Exec。                                                                如果在恢复设备后需要执行还原，则需要将云中的完整工作集检索到新设备。 因此，所有操作都以云的速度进行。|


## <a name="references"></a>参考

本文参考了以下文档：

- [StorSimple MPIO setup](storsimple-configure-mpio-windows-server.md)（StorSimple MPIO 设置）

- [Storage scenarios: Thin provisioning](http://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)（存储方案：精简预配）

- [Using GPT drives](http://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)（使用 GPT 驱动器）

- [Enable and configure shadow copies for shared folders](http://technet.microsoft.com/library/cc771893.aspx)（启用和配置共享文件夹的卷影副本）

## <a name="next-steps"></a>后续步骤

了解更多：

- 如何[从备份集还原](storsimple-restore-from-backup-set-u2.md)。
- 如何执行[设备故障转移和灾难恢复](storsimple-device-failover-disaster-recovery.md)。


<!--HONumber=Dec16_HO2-->


