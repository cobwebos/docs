---
title: 使用 Azure 备份服务器备份 SQL Server
description: 本文介绍使用 Microsoft Azure 备份 Server （MABS）备份 SQL Server 数据库的配置。
ms.topic: conceptual
ms.date: 03/24/2017
ms.openlocfilehash: 4a4d4b7e70e2df0e014ea4b4d23027aa7c48f2fe
ms.sourcegitcommit: 934776a860e4944f1a0e5e24763bfe3855bc6b60
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77505941"
---
# <a name="back-up-sql-server-to-azure-by-using-azure-backup-server"></a>使用 Azure 备份服务器将 SQL Server 备份到 Azure

本文将帮助你使用 Microsoft Azure 备份 Server （MABS）设置 SQL Server 数据库的备份。

若要备份 SQL Server 数据库并从 Azure 中恢复，请执行以下操作：

1. 创建备份策略来保护 Azure 中的 SQL Server 数据库。
1. 在 Azure 中创建按需备份副本。
1. 恢复 Azure 中的数据库。

## <a name="before-you-start"></a>开始之前

在开始之前，请确保已[安装并准备好 Azure 备份服务器](backup-azure-microsoft-azure-backup.md)。

## <a name="create-a-backup-policy"></a>创建备份策略 

若要在 Azure 中保护 SQL Server 数据库，请先创建备份策略：

1. 在 Azure 备份服务器中，选择 "**保护**" 工作区。
1. 选择 "**新建**" 以创建保护组。

    ![在 Azure 备份服务器中创建保护组](./media/backup-azure-backup-sql/protection-group.png)
1. 在 "开始" 页上，查看有关创建保护组的指南。 然后，选择“下一步”。
1. 对于保护组类型，选择 "**服务器**"。

    ![选择服务器保护组类型](./media/backup-azure-backup-sql/pg-servers.png)
1. 展开要备份的数据库所在的 SQL Server 计算机。 你将看到可从该服务器备份的数据源。 展开 "**所有 SQL 共享**"，然后选择要备份的数据库。 在此示例中，我们选择了 ReportServer $ MSDPM2012 和 ReportServer $ MSDPM2012TempDB。 选择“**下一页**”。

    ![选择 SQL Server 数据库](./media/backup-azure-backup-sql/pg-databases.png)
1. 为保护组命名，然后选择 "**我需要在线保护**"。

    ![选择数据保护方法-短期磁盘保护或在线 Azure 保护](./media/backup-azure-backup-sql/pg-name.png)
1. 在 "**指定短期目标**" 页上，包含创建磁盘备份点所需的输入。

    在此示例中，"**保持期**" 设置为*5 天*。 备份**同步频率**设置为每*15 分钟*一次。 "**快速完整备份**" 设置为*8:00 PM*。

    ![为备份保护设置短期目标](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > 在此示例中，将在每天下午8:00 创建一个备份点。 传输自前一天的 8:00 PM 备份点以来已修改的数据。 此过程称为“**快速完整备份**”。 尽管事务日志每15分钟同步一次，但如果需要在下午9:00 恢复数据库，则会通过从上一个快速完整备份点（在此示例中为 8:00 PM）重播日志来创建该点。
   >
   >

1. 选择“**下一页**”。 MABS 显示可用的总存储空间。 它还显示了可能的磁盘空间使用率。

    ![在 MABS 中设置磁盘分配](./media/backup-azure-backup-sql/pg-storage.png)

    默认情况下，MABS 为每个数据源创建一个卷（SQL Server 数据库）。 卷用于初始备份副本。 在此配置中，逻辑磁盘管理器（LDM）将 MABS 保护限制为300数据源（SQL Server 数据库）。 若要避免此限制，请选择“在 DPM 存储池中共置数据”。 如果使用此选项，MABS 将为多个数据源使用单个卷。 此设置允许 MABS 保护最多 2000 SQL Server 数据库。

    如果选择 "**自动增大卷**"，则在生产数据增长时，MABS 可以考虑增加的备份量。 如果未选择 **"自动增大卷**"，则 MABS 会将备份存储限制为保护组中的数据源。
1. 如果你是管理员，则可以选择**通过网络自动**传输此初始备份，并选择传输时间。 或者选择**手动**传输备份。 然后，选择“下一步”。

    ![在 MABS 中选择副本创建方法](./media/backup-azure-backup-sql/pg-manual.png)

    初始备份副本需要传输整个数据源（SQL Server 数据库）。 备份数据从生产服务器（SQL Server 计算机）移动到 MABS。 如果此备份较大，则通过网络传输数据可能会导致带宽堵塞。 出于此原因，管理员可以选择使用可移动媒体**手动**传输初始备份。 或者，他们可以在指定时间**通过网络自动**传输数据。

    初始备份完成后，备份将以增量方式在初始备份副本上继续。 增量备份往往比较小，能轻松地通过网络传输。
1. 选择运行一致性检查的时间。 然后，选择“下一步”。

    ![选择运行一致性检查的时间](./media/backup-azure-backup-sql/pg-consistent.png)

    MABS 可以运行一致性检查来检查备份点的完整性。 它会计算生产服务器（在本示例中为 SQL Server 机）上的备份文件的校验和，以及 MABS 中该文件的备份数据。 如果检查发现冲突，则假定 MABS 中的备份文件已损坏。 MABS 通过发送与校验和不匹配对应的块来修复备份的数据。 由于一致性检查是一项性能密集型操作，因此管理员可以选择安排一致性检查或自动运行一致性检查。
1. 选择要在 Azure 中保护的数据源。 然后，选择“下一步”。

    ![选择要在 Azure 中保护的数据源](./media/backup-azure-backup-sql/pg-sqldatabases.png)
1. 如果你是管理员，则可以选择适合组织策略的备份计划和保留策略。

    ![选择计划和保留策略](./media/backup-azure-backup-sql/pg-schedule.png)

    在此示例中，将在每天下午12:00 到 8:00 PM 进行备份。

    > [!TIP]
    > 对于快速恢复，请将几个短期恢复点保留在磁盘上。 这些恢复点适用于“操作恢复”。 Azure 作为一个良好的场外位置，提供更高的 Sla 和保证的可用性。
    >
    > 在本地磁盘备份完成后，使用 Data Protection Manager （DPM）计划 Azure 备份。 执行此操作时，会将最新磁盘备份复制到 Azure。
    >


1. 选择保留策略计划。 有关保留策略工作原理的详细信息，请参阅[使用 Azure 备份来替换磁带基础结构](backup-azure-backup-cloud-as-tape.md)。

    ![选择 MABS 中的保留策略](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    在本示例中：

    * 每日备份的时间为下午12:00 到 8:00 PM。 它们保留180天。
    * 星期六下午12:00 的备份保留104周。
    * 每月最后一个星期六下午12:00 的备份保留60个月。
    * 12:00 年3月最后一个星期六的备份保留10年。

    选择保留策略后，选择 "**下一步**"。
1. 选择如何将初始备份副本传输到 Azure。

    * "**自动通过网络**" 选项遵循你的备份计划，以将数据传输到 Azure。
    * 有关**脱机备份**的详细信息，请参阅[脱机备份概述](offline-backup-overview.md)。

    选择传输机制后，选择 "**下一步**"。
1. 在 "**摘要**" 页上，查看策略详细信息。 然后选择 "**创建组**"。 你可以选择 "**关闭**" 并在 "**监视**" 工作区中查看作业进度。

    ![保护组创建进度](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="create-on-demand-backup-copies-of-a-sql-server-database"></a>创建 SQL Server 数据库的按需备份副本

第一次备份时，将创建恢复点。 您可以手动触发创建恢复点，而不是等待计划运行：

1. 在保护组中，确保数据库状态为 **"正常"** 。

    ![显示数据库状态的保护组](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
1. 右键单击该数据库，然后选择 "**创建恢复点**"。

    ![选择创建在线恢复点](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
1. 在下拉菜单中，选择 "**联机保护**"。 然后选择 **"确定"** 以开始在 Azure 中创建恢复点。

    ![开始在 Azure 中创建恢复点](./media/backup-azure-backup-sql/sqlbackup-azure.png)
1. 你可以在 "**监视**" 工作区中查看作业进度。 

    ![在监视控制台中查看作业进度](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>从 Azure 恢复 SQL Server 数据库

若要从 Azure 中恢复受保护的实体，例如 SQL Server 数据库，请执行以下操作：

1. 打开 DPM 服务器管理控制台。 请访问 "**恢复**" 工作区，查看 DPM 备份的服务器。 选择数据库（在本示例中为 ReportServer $ MSDPM2012）。 选择以**联机状态**结束的**恢复时间**。

    ![选择恢复点](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
1. 右键单击数据库名称，然后选择 "**恢复**"。

    ![从 Azure 恢复数据库](./media/backup-azure-backup-sql/sqlbackup-recover.png)
1. DPM 会显示恢复点的详细信息。 选择“**下一页**”。 选择恢复类型“**恢复到 SQL Server 的原始实例**”。 然后，选择“下一步”。

    ![将数据库恢复到其原始位置](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    在此示例中，DPM 允许将数据库恢复到另一个 SQL Server 实例或独立的网络文件夹。
1. 在 "**指定恢复选项**" 页上，可以选择恢复选项。 例如，可以选择**网络带宽使用限制**来限制恢复使用的带宽。 然后，选择“下一步”。
1. 在 "**摘要**" 页上，可以看到当前的恢复配置。 选择 "**恢复**"。

    "恢复状态" 显示要恢复的数据库。 你可以选择 "**关闭**" 以关闭向导，并在 "**监视**" 工作区中查看进度。

    ![启动恢复过程](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    恢复完成后，还原的数据库将与应用程序保持一致。

### <a name="next-steps"></a>后续步骤

有关详细信息，请参阅[Azure 备份常见问题解答](backup-azure-backup-faq.md)。
