---
title: 在 Azure Stack 上备份 SQL Server 工作负荷
description: 使用 Azure 备份服务器在 Azure Stack 上保护 SQL Server 工作负荷。
services: backup
author: pvrk
manager: Shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 6/8/2018
ms.author: pullabhk
ms.openlocfilehash: ca7da7ab048b6f7bfdba81aac9bc7702b20ff967
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2018
ms.locfileid: "36751791"
---
# <a name="back-up-sql-server-on-stack"></a>在 Stack 上备份 SQL Server
使用本文配置 Microsoft Azure 备份服务器 (MABS) 以在 Azure Stack 上保护 SQL Server 数据库。

向 Azure 备份以及从 Azure 恢复 SQL Server 数据库的管理工作涉及三个步骤：

1. 创建备份策略来保护 SQL Server 数据库
2. 创建按需备份副本
3. 从磁盘和 Azure 恢复数据库

## <a name="before-you-start"></a>开始之前

[安装并准备 Azure 备份服务器](backup-mabs-install-azure-stack.md)。

## <a name="create-a-backup-policy-to-protect-sql-server-databases-to-azure"></a>创建备份策略以保护要备份到 Azure 的 SQL Server 数据库
1. 在 Azure 备份服务器 UI 中，单击“保护”工作区。

2. 单击“**新建**”创建新的保护组。

    ![创建保护组](./media/backup-azure-backup-sql/protection-group.png)

    Azure 备份服务器将启动“保护组”向导，该向导将引导你完成创建**保护组**的过程。 单击“下一步”。

3. 在“选择保护组类型”屏幕上，选择“服务器”。

    ![选择保护组类型 -“服务器”](./media/backup-azure-backup-sql/pg-servers.png)

4. 在“选择组成员”屏幕中，“可用成员”列表中显示了各种数据源。 单击 **+** 可展开文件夹并显示子文件夹。 单击复选框可选择项。

    ![选择 SQL DB](./media/backup-azure-backup-sql/pg-databases.png)

    所有选定的成员将出现在“所选成员”列表中。 选择想要保护的服务器或数据库后，单击“下一步”。

5. 在“选择数据保护方法”屏幕中，为保护组提供名称，并选中“我需要在线保护”复选框。

    ![数据保护方法 - 短期磁盘和在线 Azure](./media/backup-azure-backup-sql/pg-name.png)

6. 在“指定短期目标”屏幕中，提供在磁盘上创建备份点所需的输入，并单击“下一步”。

    在示例中，“保持期”为 **5 天**，“同步频率”为 **15 分钟**一次，这是备份频率。 “**快速完整备份**”设置为“**晚上 8:00**”。

    ![短期目标](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > 在显示的示例中，每天晚上 8:00 将通过传输在上一天的晚上 8:00 备份点之后已修改的数据来创建备份点。 此过程称为“**快速完整备份**”。 事务日志每 15 分钟同步一次。 如果需要在晚上 9:00 恢复数据库，则会基于日志从上次的快速完整备份点（在本例中为晚上 8 点）创建恢复点。
   >
   >

7. 在“检查磁盘分配”屏幕上，验证可用的总存储空间以及能够使用的磁盘空间。 单击“下一步”。

8. 在“选择副本创建方法”中，选择如何创建第一个恢复点。 可以选择手动传输初始备份（脱离网络），以免网络出现带宽拥塞现象。 如果选择等待传输第一个备份，则可以指定初始传输的时间。 单击“下一步”。

    ![初始复制方法](./media/backup-azure-backup-sql/pg-manual.png)

    初始备份复制要求将整个数据源（SQL Server 数据库）从生产服务器（SQL Server 计算机）传输到 Azure 备份服务器。 此类数据可能会非常大，通过网络传输此类数据可能会超过带宽限制。 出于这个原因，可以选择通过以下方式传输初始备份：“**手动**”（使用可移动媒体），以免网络出现带宽拥塞现象；或“**自动通过网络**”（于指定时间）。

    初始备份完成后，其余的备份都是初始备份副本的增量备份。 增量备份往往比较小，能轻松地通过网络传输。

9. 选择需要运行一致性检查的时间，并单击“**下一步**”。

    ![一致性检查](./media/backup-azure-backup-sql/pg-consistent.png)

    Azure 备份服务器可以通过执行一致性检查来检查备份点的完整性。 Azure 备份服务器会计算生产服务器（在本方案中为 SQL Server 计算机）上的备份文件和该文件的已备份数据的校验和。 如果存在冲突，则会认为 Azure 备份服务器上的备份文件已损坏。 Azure 备份服务器 会发送与校验和不匹配部分相对应的块来纠正备份的数据。 由于一致性检查对性能要求较高，因此你可以计划一致性检查或者自动运行它。

10. 要指定对数据源进行在线保护，请选择要通过 Azure 进行保护的数据库，并单击“**下一步**”。

    ![选择数据源](./media/backup-azure-backup-sql/pg-sqldatabases.png)

11. 选择适合组织策略的备份计划和保留策略。

    ![计划和保留](./media/backup-azure-backup-sql/pg-schedule.png)

    在本示例中，备份会在一天的中午 12:00 和晚上 8:00 各进行一次（参见屏幕底部）

    > [!NOTE]
    > 最好是在磁盘上设置几个短期恢复点，以便进行快速恢复。 这些恢复点适用于“操作恢复”。 Azure 具有较高的 SLA，其可用性也可以得到保证，因此可作为理想的非现场位置。
    >
    >

    **最佳做法**：如果将目的地为 Azure 的备份计划为在本地磁盘备份完成后启动，则始终会将最新的磁盘备份复制到 Azure。

12. 选择保留策略计划。 有关保留策略工作原理的详细信息，请参阅[使用 Azure 备份来取代磁带基础结构文章](backup-azure-backup-cloud-as-tape.md)。

    ![保留策略](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    在本示例中：

    * 备份会在一天的中午 12:00 和晚上 8:00 各进行一次（参见屏幕底部），并且会保留 180 天。
    * 在星期六中午 12:00 进行的备份 会保留 104 周
    * 在最后一个星期六中午 12:00 进行的备份 会保留 60 个月
    * 在 3 月的最后一个星期六中午 12:00 进行的备份 会保留 10 年
13. 单击“**下一步**”，选择相应的选项将初始备份副本传输到 Azure。 可以选择“自动通过网络”

14. 在“摘要”屏幕中复查策略详细信息后，单击“创建组”以完成工作流。 可以单击“关闭”，然后在“监视”工作区中监视作业进度。

    ![保护组创建进度](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="on-demand-backup-of-a-sql-server-database"></a>SQL Server 数据库的按需备份
虽然前述步骤创建了备份策略，但“恢复点”仅在进行首个备份的时候创建。 如果不想等待计划程序进行计划，则以下步骤可触发手动创建恢复点。

1. 在创建恢复点之前，请等待数据库的保护组状态显示为“**正常**”。

    ![保护组成员](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
2. 右键单击该数据库，并选择“**创建恢复点**”。

    ![创建在线恢复点](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
3. 在下拉菜单中选择“在线保护”，然后单击“确定”开始在 Azure 中创建恢复点。

    ![创建恢复点](./media/backup-azure-backup-sql/sqlbackup-azure.png)
4. 在“监视”工作区中查看作业进度。

    ![监视控制台](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>从 Azure 恢复 SQL Server 数据库
若要从 Azure 中恢复受保护的实体（SQL Server 数据库），必须执行以下步骤。

1. 打开 Azure 备份服务器管理控制台。 导航到“恢复”工作区，可以在其中看到受保护的服务器。 浏览所需的数据库（在本示例中为 ReportServer$MSDPM2012）。 选择指定为**在线**点的一个**恢复开始**时间。

    ![选择恢复点](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
2. 右键单击数据库名称，并单击“**恢复**”。

    ![从 Azure 恢复](./media/backup-azure-backup-sql/sqlbackup-recover.png)
3. MABS 会显示恢复点的详细信息。 单击“下一步”。 选择恢复类型“**恢复到 SQL Server 的原始实例**”。 单击“下一步”。

    ![恢复到原始位置](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    在此示例中，MABS 将数据库恢复到另一个 SQL Server 实例或独立的网络文件夹。

4. 在“**指定恢复选项**”屏幕上，可以选择恢复选项（例如“网络带宽使用限制”），以便限制恢复操作所使用的带宽。 单击“下一步”。

5. 在“**摘要**”屏幕上，会看到目前提供的所有恢复配置。 单击“**恢复**”。

    恢复状态显示数据库正在恢复。 可以单击“**关闭**”关闭向导，然后在“**监视**”工作区中查看进度。

    ![启动恢复过程](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    完成恢复操作后，还原的数据库在应用程序级别将是一致的。

## <a name="next-steps"></a>后续步骤

请参阅[备份文件和应用程序](backup-mabs-files-applications-azure-stack.md)一文。
请参阅[在 Azure Stack 上备份 SharePoint](backup-mabs-sharepoint-azure-stack.md) 一文。
