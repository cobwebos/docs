---
title: 使用 Azure 备份服务器备份 SQL 服务器
description: 在本文中，了解使用 Microsoft Azure 备份服务器 （MABS） 备份 SQL Server 数据库的配置。
ms.topic: conceptual
ms.date: 03/24/2017
ms.openlocfilehash: 4a4d4b7e70e2df0e014ea4b4d23027aa7c48f2fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77505941"
---
# <a name="back-up-sql-server-to-azure-by-using-azure-backup-server"></a>使用 Azure 备份服务器将 SQL 服务器备份到 Azure

本文可帮助您使用 Microsoft Azure 备份服务器 （MABS） 设置 SQL Server 数据库的备份。

要备份 SQL Server 数据库并从 Azure 恢复它，它：

1. 创建备份策略以保护 Azure 中的 SQL Server 数据库。
1. 在 Azure 中创建按需备份副本。
1. 在 Azure 中恢复数据库。

## <a name="before-you-start"></a>开始之前

开始之前，请确保已[安装并准备了 Azure 备份服务器](backup-azure-microsoft-azure-backup.md)。

## <a name="create-a-backup-policy"></a>创建备份策略 

要保护 Azure 中的 SQL Server 数据库，请先创建备份策略：

1. 在 Azure 备份服务器中，选择**保护**工作区。
1. 选择 **"新建"** 以创建保护组。

    ![在 Azure 备份服务器中创建保护组](./media/backup-azure-backup-sql/protection-group.png)
1. 在起始页上，查看有关创建保护组的指导。 然后选择 **"下一步**"。
1. 对于保护组类型，选择 **"服务器**"。

    ![选择服务器保护组类型](./media/backup-azure-backup-sql/pg-servers.png)
1. 展开要备份的数据库所在的 SQL Server 计算机。 您将看到可以从该服务器备份的数据源。 展开**所有 SQL 共享**，然后选择要备份的数据库。 在此示例中，我们选择报表服务器$MSDPM2012 和报表服务器$MSDPM2012TempDB。 选择“下一步”。

    ![选择 SQL Server 数据库](./media/backup-azure-backup-sql/pg-databases.png)
1. 命名保护组，然后选择 **"我想要联机保护**"。

    ![选择数据保护方法 - 短期磁盘保护或在线 Azure 保护](./media/backup-azure-backup-sql/pg-name.png)
1. 在 **"指定短期目标"** 页上，包括创建磁盘备份点所需的输入。

    在此示例中，**保留范围**设置为*5 天*。 备份**同步频率**设置为每*15 分钟*一次。 **快速完整备份**设置为晚上*8：00。*

    ![设置备份保护的短期目标](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > 在此示例中，每天晚上 8：00 创建一个备份点。 已修改的数据，因为前一天的晚上 8：00 备份点已传输。 此过程称为“**快速完整备份**”。 尽管事务日志每 15 分钟同步一次，但如果我们需要在晚上 9：00 恢复数据库，则通过重播最后一个快速完整备份点的日志来创建该点，在此示例中为 8：00 PM。
   >
   >

1. 选择“下一步”。 MABS 显示可用的总体存储空间。 它还显示了潜在的磁盘空间利用率。

    ![在 MABS 中设置磁盘分配](./media/backup-azure-backup-sql/pg-storage.png)

    默认情况下，MABS 每个数据源（SQL Server 数据库）创建一个卷。 该卷用于初始备份副本。 在此配置中，逻辑磁盘管理器 （LDM） 将 MABS 保护限制为 300 个数据源（SQL Server 数据库）。 若要避免此限制，请选择“在 DPM 存储池中共置数据”。**** 如果使用此选项，MABS 会对多个数据源使用单个卷。 此设置允许 MABS 保护多达 2，000 个 SQL Server 数据库。

    如果选择 **"自动增长卷**"，则 MABS 可以随着生产数据的增长考虑增加的备份量。 如果不选择 **"自动增长卷**"，则 MABS 将备份存储限制为保护组中的数据源。
1. 如果您是管理员，您可以选择**通过网络自动**传输此初始备份，并选择传输时间。 或者选择**手动**传输备份。 然后选择 **"下一步**"。

    ![在 MABS 中选择副本创建方法](./media/backup-azure-backup-sql/pg-manual.png)

    初始备份副本需要传输整个数据源（SQL Server 数据库）。 备份数据从生产服务器（SQL Server 计算机）移动到 MABS。 如果此备份较大，则通过网络传输数据可能会导致带宽拥塞。 因此，管理员可以选择使用可移动媒体**手动**传输初始备份。 或者，他们可以在指定时间**通过网络自动**传输数据。

    初始备份完成后，备份在初始备份副本上以增量方式继续。 增量备份往往比较小，能轻松地通过网络传输。
1. 选择何时运行一致性检查。 然后选择 **"下一步**"。

    ![选择何时运行一致性检查](./media/backup-azure-backup-sql/pg-consistent.png)

    MABS 可以对备份点的完整性运行一致性检查。 它计算生产服务器上的备份文件的校验和（本示例中的 SQL Server 计算机）和 MABS 中该文件的备份数据。 如果检查发现冲突，则假定 MABS 中的备份文件已损坏。 MABS 通过发送对应于校验和不匹配的块来修复备份的数据。 由于一致性检查是一项性能密集型操作，因此管理员可以选择计划一致性检查或自动运行一致性检查。
1. 选择要在 Azure 中保护的数据源。 然后选择 **"下一步**"。

    ![选择要在 Azure 中保护的数据源](./media/backup-azure-backup-sql/pg-sqldatabases.png)
1. 如果您是管理员，则可以选择适合组织策略的备份计划和保留策略。

    ![选择计划和保留策略](./media/backup-azure-backup-sql/pg-schedule.png)

    在此示例中，备份每天在 12：00 PM 和 8：00 PM 进行。

    > [!TIP]
    > 要快速恢复，请保留一些短期恢复点。 这些恢复点适用于“操作恢复”。 Azure 充当良好的异地位置，提供更高的 SL 和保证的可用性。
    >
    > 使用数据保护管理器 （DPM） 在本地磁盘备份完成后安排 Azure 备份。 遵循这种做法后，最新的磁盘备份将复制到 Azure。
    >


1. 选择保留策略计划。 有关保留策略的工作原理的详细信息，请参阅使用 Azure[备份替换磁带基础结构](backup-azure-backup-cloud-as-tape.md)。

    ![在 MABS 中选择保留策略](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    在此示例中：

    * 备份每天在中午 12：00 PM 和 8：00 PM 进行。 他们被保存了180天
    * 周六下午 12：00 的备份将保留 104 周。
    * 每月最后一个星期六的备份在中午 12：00 保留 60 个月。
    * 从3月的最后一个星期六下午12：00备份保存10年。

    选择保留策略后，选择 **"下一步**"。
1. 选择如何将初始备份副本传输到 Azure。

    * **"通过网络自动"** 选项遵循备份计划将数据传输到 Azure。
    * 有关**脱机备份**的详细信息，请参阅[脱机备份概述](offline-backup-overview.md)。

    选择传输机制后，选择 **"下一步**"。
1. 在 **"摘要"** 页上，查看策略详细信息。 然后选择 **"创建组**"。 您可以在 **"监视"** 工作区中选择 **"关闭**"并监视作业进度。

    ![保护组创建的进展](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="create-on-demand-backup-copies-of-a-sql-server-database"></a>创建 SQL Server 数据库的按需备份副本

发生第一次备份时，将创建一个恢复点。 您可以手动触发恢复点的创建，而不是等待计划运行：

1. 在保护组中，请确保数据库状态**正常**。

    ![显示数据库状态的保护组](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
1. 右键单击数据库，然后选择 **"创建恢复点**"。

    ![选择创建联机恢复点](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
1. 在下拉菜单中，选择 **"联机保护**"。 然后选择 **"确定"** 以开始在 Azure 中创建恢复点。

    ![开始在 Azure 中创建恢复点](./media/backup-azure-backup-sql/sqlbackup-azure.png)
1. 您可以在 **"监视"** 工作区中查看作业进度。 

    ![在监视控制台中查看作业进度](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>从 Azure 恢复 SQL Server 数据库

要从 Azure 恢复受保护的实体（如 SQL Server 数据库）：：

1. 打开 DPM 服务器管理控制台。 转到**恢复**工作区以查看 DPM 备份的服务器。 选择数据库（在此示例中，报表服务器$MSDPM2012）。 选择以**联机**结尾**的恢复时间**。

    ![选择恢复点](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
1. 右键单击数据库名称并选择 **"恢复**"。

    ![从 Azure 恢复数据库](./media/backup-azure-backup-sql/sqlbackup-recover.png)
1. DPM 会显示恢复点的详细信息。 选择“下一步”。 选择恢复类型“**恢复到 SQL Server 的原始实例**”。 然后选择 **"下一步**"。

    ![将数据库恢复到其原始位置](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    在此示例中，DPM 允许将数据库恢复到另一个 SQL Server 实例或独立网络文件夹。
1. 在 **"指定恢复选项"** 页上，您可以选择恢复选项。 例如，您可以选择**网络带宽使用限制**以限制恢复使用的带宽。 然后选择 **"下一步**"。
1. 在 **"摘要"** 页上，您将看到当前恢复配置。 选择 **"恢复**"。

    恢复状态显示正在恢复的数据库。 您可以选择 **"关闭"** 以关闭向导，并在 **"监视"** 工作区中查看进度。

    ![启动恢复过程](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    恢复完成后，还原的数据库与应用程序一致。

### <a name="next-steps"></a>后续步骤

有关详细信息，请参阅[Azure 备份常见问题解答](backup-azure-backup-faq.md)。
