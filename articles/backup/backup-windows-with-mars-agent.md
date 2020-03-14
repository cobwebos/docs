---
title: 使用 MARS 代理备份 Windows 计算机
description: 使用 Microsoft Azure 恢复服务（MARS）代理来备份 Windows 计算机。
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 0e1f7044c62bbaa9969b97690bf16b9ed446c27c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79247769"
---
# <a name="back-up-windows-machines-by-using-the-azure-backup-mars-agent"></a>使用 Azure 备份 MARS 代理备份 Windows 计算机

本文介绍如何使用[Azure 备份](backup-overview.md)服务和 MICROSOFT AZURE 恢复服务（MARS）代理备份 Windows 计算机。 MARS 也称为 Azure 备份代理。

本文介绍如何执行以下操作：

> [!div class="checklist"]
>
> * 验证先决条件
> * 创建备份策略和计划。
> * 执行按需备份。

## <a name="before-you-start"></a>开始之前

* 了解[Azure 备份如何使用 MARS 代理来备份 Windows 计算机](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)。
* 了解在辅助 MABS 或 Data Protection Manager 服务器上运行 MARS 代理的[备份体系结构](backup-architecture.md#architecture-back-up-to-dpmmabs)。
* 查看[支持的内容以及 MARS 代理可以备份](backup-support-matrix-mars-agent.md)的内容。
* 验证要备份的计算机上的[internet 访问权限](install-mars-agent.md#verify-internet-access)。
* 如果未安装 MARS 代理，请[在此处](install-mars-agent.md)了解如何安装它。

## <a name="create-a-backup-policy"></a>创建备份策略

备份策略指定何时拍摄数据快照以创建恢复点。 它还指定了保留恢复点的时间长度。 使用 MARS 代理来配置备份策略。

Azure 备份不会自动考虑夏令时（DST）。 此默认值可能会导致实际时间和计划的备份时间之间的一些差异。

创建备份策略：

1. 下载并注册 MARS 代理后，打开代理控制台。 可以通过在计算机中搜索 **Microsoft Azure 备份**找到该代理。  

1. 在 "**操作**" 下，选择 "**计划备份**"。

    ![计划 Windows Server 备份](./media/backup-configure-vault/schedule-first-backup.png)
1. 在计划备份向导中，选择 "**入门**" > "**下一步**"。
1. 在 "**选择要备份的项**" 下，选择 "**添加项**"。

    ![添加要备份的项](./media/backup-azure-manage-mars/select-item-to-backup.png)

1. 在 "**选择项**" 框中，选择要备份的项，然后选择 **"确定"** 。

    ![选择要备份的项](./media/backup-azure-manage-mars/selected-items-to-backup.png)

1. 在 "**选择要备份的项**" 页上，选择 "**下一步**"。
1. 在 "**指定备份计划**" 页上，指定每日或每周备份的时间。 然后，选择“下一步”。

    * 执行备份时，将创建恢复点。
    * 你的环境中创建的恢复点的数量取决于你的备份计划。
    * 每天最多可计划三个每日备份。 在下面的示例中，将发生两个每日备份，一个介于午夜，另一个在下午6:00。

        ![设置每日备份计划](./media/backup-configure-vault/day-schedule.png)

    * 还可以运行每周备份。 在下面的示例中，将在每个星期日和星期三的上午9:30 到 1:00 AM 创建备份。

        ![设置每周备份计划](./media/backup-configure-vault/week-schedule.png)

1. 在 "**选择保留策略**" 页上，指定如何存储数据的历史副本。 然后，选择“下一步”。

    * 保留设置指定要存储的恢复点和存储时间。
    * 对于每日保留设置，你指示在为每日保留指定的时间，将在指定的天数内保留最新的恢复点。 或者，你可以指定每月保留策略以指示每月30日创建的恢复点应存储12个月。
    * 每日和每周恢复点的保留期通常与备份计划一致。 因此，当计划触发备份时，将在每日或每周保留策略指定的持续时间内存储备份创建的恢复点。
    * 在以下示例中：

        * 每日午夜备份和 6:00 PM 保留7天。
        * 在星期六午夜和 6:00 PM 创建的备份将保留四周。
        * 在该月的最后一个星期六午夜和 6:00 PM 执行的备份保留12个月。
        * 在3月最后一个星期六创建的备份保留10年。

        ![保留策略示例](./media/backup-configure-vault/retention-example.png)

1. 在 "**选择初始备份类型**" 页上，决定是要通过网络进行初始备份还是使用 "脱机备份"。 若要通过网络进行初始备份，请选择 **"通过网络自动** >  **"** 。

    有关脱机备份的详细信息，请参阅[使用 Azure Data Box 进行脱机备份](offline-backup-azure-data-box.md)。

    ![选择初始备份类型](./media/backup-azure-manage-mars/choose-initial-backup-type.png)

1. 在 "**确认**" 页上，查看信息，然后选择 "**完成**"。

    ![确认备份类型](./media/backup-azure-manage-mars/confirm-backup-type.png)

1. 向导完成创建备份计划后，选择 "**关闭**"。

    ![查看备份计划进度](./media/backup-azure-manage-mars/confirm-modify-backup-process.png)

在安装了代理的每台计算机上创建一个策略。

### <a name="do-the-initial-backup-offline"></a>初始备份脱机

可以通过网络自动运行初始备份，也可以脱机备份。 如果有大量数据需要传输大量的网络带宽，则初始备份的脱机种子设定非常有用。

若要进行脱机传输：

1. 将备份数据写入暂存位置。
1. 使用 AzureOfflineBackupDiskPrep 工具将数据从暂存位置复制到一个或多个 SATA 磁盘。

    该工具会创建 Azure 导入作业。 有关详细信息，请参阅[什么是 Azure 导入/导出服务](https://docs.microsoft.com/azure/storage/common/storage-import-export-service)。
1. 将 SATA 磁盘发送到 Azure 数据中心。

    在数据中心，磁盘数据将复制到 Azure 存储帐户。 Azure 备份将数据从存储帐户复制到保管库，并计划增量备份。

有关脱机种子设定的详细信息，请参阅[使用 Azure Data Box 进行脱机备份](offline-backup-azure-data-box.md)。

### <a name="enable-network-throttling"></a>启用网络限制

可以通过启用网络限制来控制 MARS 代理使用网络带宽的方式。 如果需要在工作时间内备份数据，但需要控制备份和还原活动使用的带宽量，则限制会很有帮助。

Azure 备份中的网络限制在本地操作系统上使用[服务质量（QoS）](https://docs.microsoft.com/windows-server/networking/technologies/qos/qos-policy-top) 。

在 Windows Server 2012 和更高版本以及 Windows 8 及更高版本上，可以使用网络限制进行备份。 操作系统应运行最新的 service pack。

启用网络限制：

1. 在 MARS 代理中，选择 "**更改属性**"。
1. 在“限制”选项卡上，选择“为备份操作启用 Internet 带宽使用限制”。

    ![为备份操作设置网络限制](./media/backup-configure-vault/throttling-dialog.png)
1. 指定在工作时间和非小时内允许的带宽。 带宽值从 512 Kbps 开始，最高可达 1023 MBps。 然后选择“确定”。

## <a name="run-an-on-demand-backup"></a>运行按需备份

1. 在 MARS 代理中，选择 "**立即备份**"。

    ![在 Windows Server 中立即备份](./media/backup-configure-vault/backup-now.png)

1. 如果 MARS 代理版本为2.0.9169.0 或更高版本，则可以设置自定义保留日期。 在 "**保留备份截止**时间" 部分中，从日历中选择一个日期。

   ![使用日历自定义保留日期](./media/backup-configure-vault/mars-ondemand.png)

1. 在 "**确认**" 页上，查看设置，然后选择 "**备份**"。
1. 选择 "**关闭**" 关闭向导。 如果在备份完成之前关闭向导，向导将继续在后台运行。

初始备份完成后，备份控制台中会显示 "**作业已完成**" 状态。

## <a name="set-up-on-demand-backup-policy-retention-behavior"></a>设置按需备份策略保留行为

> [!NOTE]
> 此信息仅适用于早于2.0.9169.0 的 MARS 代理版本。
>

| 备份-计划选项 | 数据保持期
| -- | --
| 日期 | **默认保留期**：等效于 "每日备份的保留天数"。 <br/><br/> **异常**：如果为长期保留（周、月或年）设置的每日计划备份失败，则在发生故障后立即触发的按需备份将被视为长期保留。 否则，下一次计划的备份将被视为长期保留。<br/><br/> **示例方案**：星期四上午8:00 的计划备份失败。 此备份被视为每周、每月或每年保留。 因此，第一个按需备份在星期五凌晨8:00 点的下一个计划备份自动标记为每周、每月或每年保留。 此备份替换为周四 8:00 AM 的备份。
| Week | **默认保留期**：1天。 对于具有每周备份策略的数据源执行的按需备份将在下一天删除。 即使它们是数据源的最新备份，也会被删除。 <br/><br/> **例外**情况：如果设置为长期保留（周、月或年）的每周计划备份失败，则在出现故障后立即触发的按需备份将被视为长期保留。 否则，下一次计划的备份将被视为长期保留。 <br/><br/> **示例方案**：星期四上午8:00 的计划备份失败。 此备份被视为每月或每年保留。 因此，在星期四凌晨8:00 点的下一个计划备份之前触发的第一个按需备份将自动标记为每月或每年保留。 此备份替换为周四 8:00 AM 的备份。

有关详细信息，请参阅[创建备份策略](#create-a-backup-policy)。

## <a name="next-steps"></a>后续步骤

了解如何[在 Azure 中还原文件](backup-azure-restore-windows-server.md)。
