---
title: 使用 MARS 代理备份 Windows 计算机
description: 使用 Microsoft Azure 恢复服务 (MARS) 代理来备份 Windows 计算机。
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: d2cdd5d1fa98462a70d72fd9f8723685952b665a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90052216"
---
# <a name="back-up-windows-server-files-and-folders-to-azure"></a>将 Windows Server 文件和文件夹备份到 Azure

本文介绍如何使用 [Azure 备份](backup-overview.md) 服务和 MICROSOFT AZURE 恢复服务 (MARS) 代理来备份 Windows 计算机。 MARS 也称为 Azure 备份代理。

本文将指导如何进行以下操作：

> [!div class="checklist"]
>
> * 验证先决条件
> * 创建备份策略和计划。
> * 执行按需备份。

## <a name="before-you-start"></a>开始之前

* 了解 [Azure 备份如何使用 MARS 代理备份 Windows 计算机](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)。
* 了解在辅助 MABS 或 Data Protection Manager 服务器上运行 MARS 代理的[备份体系结构](backup-architecture.md#architecture-back-up-to-dpmmabs)。
* 查看 MARS 代理[支持的操作以及可备份的内容](backup-support-matrix-mars-agent.md)。
* 在要备份的计算机上[验证 Internet 访问权限](install-mars-agent.md#verify-internet-access)。
* 如果未安装 MARS 代理，请在[此处](install-mars-agent.md)了解如何安装它。

## <a name="create-a-backup-policy"></a>创建备份策略

备份策略指定何时创建数据快照来创建恢复点。 它还指定恢复点的保留期限。 使用 MARS 代理配置备份策略。

Azure 备份不会自动考虑夏令时 (DST)。 此默认设置可能会导致实际时间与计划的备份时间存在一定的偏差。

创建备份策略：

1. 下载并注册 MARS 代理后，打开代理控制台。 可以通过在计算机中搜索 **Microsoft Azure 备份**找到该代理。  

1. 在“操作”下，选择“计划备份”。 

    ![计划 Windows Server 备份](./media/backup-configure-vault/schedule-first-backup.png)
1. 在计划备份向导中，选择“开始” > “下一步”。 
1. 在“选择要备份的项”下，选择“添加项”。 

    ![添加要备份的项](./media/backup-azure-manage-mars/select-item-to-backup.png)

1. 在“选择项”框中，选择要备份的项，然后选择“确定”。 

    ![选择要备份的项](./media/backup-azure-manage-mars/selected-items-to-backup.png)

1. 在“选择要备份的项”页上，选择“下一步”。 
1. 在“指定备份计划”页上，指定何时创建每日或每周备份。 然后，选择“下一步”。

    * 创建备份时会创建一个恢复点。
    * 在环境中创建的恢复点数目取决于备份计划。
    * 可以计划每日备份，每天最多备份三次。 在以下示例中，每日会执行两次备份，一次发生在午夜，另一次发生在下午 6:00。

        ![设置每日备份计划](./media/backup-configure-vault/day-schedule.png)

    * 也可以运行每周备份。 在以下示例中，将每隔两周在星期日和星期三上午 9:30 和凌晨 1:00 创建备份。

        ![设置每周备份计划](./media/backup-configure-vault/week-schedule.png)

1. 在“选择保留策略”页上，指定如何存储数据的历史副本。 然后，选择“下一步”。

    * 保留设置指定要存储哪些恢复点，以及要存储多长时间。
    * 对于每日保留设置，可以指明在针对每日保留指定的时间，要将最新恢复点保留指定的天数。 或者，可以指定每月保留策略，指明在每个月的 30 日创建的恢复点应保留 12 个月。
    * 每日和每周恢复点的保留期通常与备份计划相一致。 因此，当计划触发备份时，备份创建的恢复点的存储持续时间与每日或每周保留策略指定的持续时间相一致。
    * 在以下示例中：

        * 在午夜和下午 6:00 创建的每日备份将保留 7 天。
        * 在星期六的午夜和下午 6:00 创建的备份将保留 4 周。
        * 在当月最后一个星期六的午夜和下午 6:00 创建的备份将保留 12 个月。
        * 在三月份最后一个星期六创建的备份将保留 10 年。

        ![保留策略的示例](./media/backup-configure-vault/retention-example.png)

1. 在“选择初始备份类型”页上，确定如何通过网络或使用脱机备份创建初始备份。 若要通过网络创建初始备份，请选择“自动通过网络” > “下一步”。 

    有关脱机备份的详细信息，请参阅[使用 Azure Data Box 进行脱机备份](offline-backup-azure-data-box.md)。

    ![选择初始备份类型](./media/backup-azure-manage-mars/choose-initial-backup-type.png)

1. 在“确认”页上复查信息，然后选择“完成”。 

    ![确认备份类型](./media/backup-azure-manage-mars/confirm-backup-type.png)

1. 在向导完成创建备份计划后，选择“关闭”。

    ![查看备份计划的进度](./media/backup-azure-manage-mars/confirm-modify-backup-process.png)

在安装了代理的每台计算机上创建一个策略。

### <a name="do-the-initial-backup-offline"></a>执行脱机初始备份

可以通过网络自动运行初始备份，也可以使用脱机备份。 如果需要消耗大量的网络带宽来传输大量的数据，则初始备份的脱机种子设定非常有用。

若要执行脱机传输：

1. 将备份数据写入暂存位置。
1. 使用 AzureOfflineBackupDiskPrep 工具将暂存位置中的数据复制到一个或多个 SATA 磁盘。

    该工具会创建 Azure 导入作业。 有关详细信息，请参阅[什么是 Azure 导入/导出服务](../storage/common/storage-import-export-service.md)。
1. 将 SATA 磁盘寄送到 Azure 数据中心。

    在数据中心，磁盘数据将复制到 Azure 存储帐户。 Azure 备份将数据从存储帐户复制到保管库，并计划增量备份。

有关脱机种子设定的详细信息，请参阅[使用 Azure Data Box 进行脱机备份](offline-backup-azure-data-box.md)。

### <a name="enable-network-throttling"></a>启用网络限制

可以通过启用网络限制，来控制 MARS 代理使用网络带宽的方式。 如果你需要在工作时间备份数据，但想要控制备份和还原活动占用的带宽量，则限制会很有帮助。

Azure 备份中的网络限制在本地操作系统上使用[服务质量 (QoS)](/windows-server/networking/technologies/qos/qos-policy-top)。

针对备份的网络限制适用于 Windows Server 2012 和更高版本，以及 Windows 8 和更高版本。 操作系统应该运行最新的服务包。

若要启用网络限制：

1. 在 MARS 代理中，选择“更改属性”。
1. 在“限制”选项卡上，选择“为备份操作启用 Internet 带宽使用限制”。

    ![针对备份操作设置网络限制](./media/backup-configure-vault/throttling-dialog.png)
1. 指定在工作时间和下班时间允许的带宽。 带宽值最小为 512 Kbps，最大为 1,023 Mbps。 然后选择“确定”。

## <a name="run-an-on-demand-backup"></a>运行按需备份

1. 在 MARS 代理中，选择“立即备份”。

    ![在 Windows Server 中立即备份](./media/backup-configure-vault/backup-now.png)

1. 如果 MARS 代理版本为 2.0.9169.0 或更高，则你可以设置自定义保留日期。 在“备份保留截止日期”部分，从日历中选择一个日期。

   ![使用日历自定义保留日期](./media/backup-configure-vault/mars-ondemand.png)

1. 在“确认”页上复查设置，然后选择“备份”。 
1. 选择“关闭”以关闭向导。 如果在备份完成之前关闭向导，向导将继续在后台运行。

完成初始备份后，备份控制台中显示“作业已完成”状态。

## <a name="set-up-on-demand-backup-policy-retention-behavior"></a>设置按需备份策略保留行为

> [!NOTE]
> 此信息仅适用于低于 2.0.9169.0 的 MARS 代理版本。
>

| 备份计划选项 | 数据保留持续时间
| -- | --
| 日期 | **默认保留期**：相当于“每日备份的保留期(以天为单位)”。 <br/><br/> **例外**：如果为长期保留（周、月、年）设置的每日计划备份失败，则会考虑将失败后立即触发的按需备份进行长期保留。 否则，将考虑进行下一次计划备份以进行长期保留。<br/><br/> **示例方案**：在星期四上午 8:00 进行的计划备份失败。 考虑每周、每月或每年保留此备份。 因此，在下一次于星期五上午 8:00 执行计划备份之前触发的第一个按需备份将自动标记为每周、每月或每年保留。 此备份会替代星期四上午 8:00 的备份。
| Week | **默认保留期**：一天。 为使用每周备份策略的数据源创建的按需备份将在第二天删除。 即使它们是该数据源的最新备份，也仍会将其删除。 <br/><br/> **例外**：如果为长期保留（周、月、年）设置的每周计划备份失败，则会考虑将失败后立即触发的按需备份进行长期保留。 否则，将考虑进行下一次计划备份以进行长期保留。 <br/><br/> **示例方案**：在星期四上午 8:00 进行的计划备份失败。 考虑每月或每年保留此备份。 因此，在下一次于星期四上午 8:00 执行计划备份之前触发的第一个按需备份将自动标记为每月或每年保留。 此备份会替代星期四上午 8:00 的备份。

有关详细信息，请参阅[创建备份策略](#create-a-backup-policy)。

## <a name="next-steps"></a>后续步骤

* 了解如何[在 Azure 中还原文件](backup-azure-restore-windows-server.md)。
* 查找[与文件和文件夹备份相关的常见问题](backup-azure-file-folder-backup-faq.md)
