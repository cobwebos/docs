---
title: 使用 MARS 代理备份 Windows 计算机
description: 使用 Microsoft Azure 恢复服务 （MARS） 代理备份 Windows 计算机。
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 12463f33a6fa97b33e70b77fb2fcf6b0a27b5790
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408906"
---
# <a name="back-up-windows-machines-by-using-the-azure-backup-mars-agent"></a>使用 Azure 备份 MARS 代理备份 Windows 计算机

本文介绍如何使用[Azure 备份](backup-overview.md)服务和 Microsoft Azure 恢复服务 （MARS） 代理备份 Windows 计算机。 MARS 也称为 Azure 备份代理。

本文介绍如何执行以下操作：

> [!div class="checklist"]
>
> * 验证先决条件
> * 创建备份策略和计划。
> * 执行按需备份。

## <a name="before-you-start"></a>开始之前

* 了解[Azure 备份如何使用 MARS 代理备份 Windows 计算机](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)。
* 了解在辅助 MABS 或数据保护管理器服务器上运行 MARS 代理的[备份体系结构](backup-architecture.md#architecture-back-up-to-dpmmabs)。
* 查看支持[的内容以及 MARS 代理可以备份的内容](backup-support-matrix-mars-agent.md)。
* [验证](install-mars-agent.md#verify-internet-access)要备份的计算机上的 Internet 访问。
* 如果未安装 MARS 代理，请了解如何[在此处](install-mars-agent.md)安装它。

## <a name="create-a-backup-policy"></a>创建备份策略

备份策略指定何时拍摄数据的快照以创建恢复点。 它还指定保留恢复点的时间。 您可以使用 MARS 代理配置备份策略。

Azure 备份不会自动考虑夏令时 （DST）。 此默认值可能会导致实际时间和计划备份时间之间存在一些差异。

创建备份策略：

1. 下载并注册 MARS 代理后，打开代理控制台。 可以通过在计算机中搜索 **Microsoft Azure 备份**找到该代理。  

1. 在 **"操作"** 下，选择 **"计划备份**"。

    ![计划 Windows Server 备份](./media/backup-configure-vault/schedule-first-backup.png)
1. 在"计划备份向导"中，选择 **"下** > **一步**入门"。
1. 在 **"选择要备份的项目**"下，选择 **"添加项目**"。

    ![添加要备份的项目](./media/backup-azure-manage-mars/select-item-to-backup.png)

1. 在 **"选择项目"** 框中，选择要备份的项目，然后选择 **"确定**"。

    ![选择要备份的项](./media/backup-azure-manage-mars/selected-items-to-backup.png)

1. 在 **"选择要备份的项目"** 页上，选择 **"下一步**"。
1. 在 **"指定备份计划"** 页上，指定何时进行每日或每周备份。 然后选择 **"下一步**"。

    * 创建备份时会创建一个恢复点。
    * 在环境中创建的恢复点数取决于您的备份计划。
    * 您每天最多可以安排三次备份。 在下面的示例中，每天发生两次备份，一个在午夜，一个在下午 6：00。

        ![设置每日备份计划](./media/backup-configure-vault/day-schedule.png)

    * 也可以运行每周备份。 在下面的示例中，备份在每个备用星期日和周三上午 9：30 和凌晨 1：00 进行。

        ![设置每周备份计划](./media/backup-configure-vault/week-schedule.png)

1. 在 **"选择保留策略"** 页上，指定如何存储数据的历史副本。 然后选择 **"下一步**"。

    * 保留设置指定要存储的恢复点以及存储它们的时间。
    * 对于每日保留设置，您指示在为每日保留指定的时间，最新的恢复点将保留指定天数。 或者，您可以指定每月保留策略，以指示每月 30 日创建的恢复点应存储 12 个月。
    * 每日和每周恢复点的保留通常与备份计划一致。 因此，当计划触发备份时，备份创建的恢复点将存储在每日或每周保留策略指定的持续时间内。
    * 在以下示例中：

        * 在午夜和下午 6:00 创建的每日备份将保留 7 天。
        * 在星期六的午夜和下午 6:00 创建的备份将保留 4 周。
        * 在每月的最后一个星期六午夜和下午 6：00 进行的备份将保留 12 个月。
        * 在 3 月的最后一个星期六进行的备份将保留 10 年。

        ![保留策略的示例](./media/backup-configure-vault/retention-example.png)

1. 在 **"选择初始备份类型"** 页上，确定是通过网络进行初始备份还是使用脱机备份。 要通过网络进行初始备份，请在 > **网络上选择"下****一步**"。

    有关脱机备份的详细信息，请参阅使用[Azure 数据框进行脱机备份](offline-backup-azure-data-box.md)。

    ![选择初始备份类型](./media/backup-azure-manage-mars/choose-initial-backup-type.png)

1. 在**确认**页面上，查看信息，然后选择 **"完成**"。

    ![确认备份类型](./media/backup-azure-manage-mars/confirm-backup-type.png)

1. 向导完成创建备份计划后，选择 **"关闭**"。

    ![查看备份计划进度](./media/backup-azure-manage-mars/confirm-modify-backup-process.png)

在安装代理的每台计算机上创建策略。

### <a name="do-the-initial-backup-offline"></a>脱机执行初始备份

您可以通过网络自动运行初始备份，也可以脱机备份。 如果具有大量数据，需要传输大量网络带宽，则初始备份的脱机种子设定非常有用。

要执行脱机传输：

1. 将备份数据写入暂存位置。
1. 使用 AzureOffline 备份磁盘准备工具将数据从暂存位置复制到一个或多个 SATA 磁盘。

    该工具会创建 Azure 导入作业。 有关详细信息，请参阅什么是[Azure 导入/导出服务](https://docs.microsoft.com/azure/storage/common/storage-import-export-service)。
1. 将 SATA 磁盘发送到 Azure 数据中心。

    在数据中心，磁盘数据将复制到 Azure 存储帐户。 Azure 备份将数据从存储帐户复制到保管库，并计划增量备份。

有关脱机种子设定的详细信息，请参阅使用[Azure 数据框进行脱机备份](offline-backup-azure-data-box.md)。

### <a name="enable-network-throttling"></a>启用网络限制

您可以通过启用网络限制来控制 MARS 代理如何使用网络带宽。 如果需要在工作时间备份数据，但希望控制备份和还原活动使用的带宽，则限制非常有用。

Azure 备份中的网络限制在本地操作系统上使用[服务质量 （QoS）。](https://docs.microsoft.com/windows-server/networking/technologies/qos/qos-policy-top)

备份的网络限制在 Windows Server 2012 及更高版本以及 Windows 8 及更高版本上可用。 操作系统应该运行最新的服务包。

要启用网络限制：

1. 在 MARS 代理中，选择 **"更改属性**"。
1. 在 **"限制"** 选项卡上，选择**启用备份操作的互联网带宽使用限制**。

    ![为备份操作设置网络限制](./media/backup-configure-vault/throttling-dialog.png)
1. 指定工作时间和非工作时间的允许带宽。 带宽值最小为 512 Kbps，最大为 1,023 MBps。 然后选择 **"确定**"。

## <a name="run-an-on-demand-backup"></a>运行按需备份

1. 在 MARS 代理中，选择 **"立即备份**"。

    ![立即在 Windows 服务器中备份](./media/backup-configure-vault/backup-now.png)

1. 如果 MARS 代理版本为 2.0.9169.0 或较新，则可以设置自定义保留日期。 在"**保留备份直到"** 部分中，从日历中选择日期。

   ![使用日历自定义保留日期](./media/backup-configure-vault/mars-ondemand.png)

1. 在 **"确认"** 页上，查看设置，然后选择 **"备份**"。
1. 选择 **"关闭"** 以关闭向导。 如果在备份完成之前关闭向导，向导将继续在后台运行。

初始备份完成后，**作业完成**状态将显示在备份控制台中。

## <a name="set-up-on-demand-backup-policy-retention-behavior"></a>设置按需备份策略保留行为

> [!NOTE]
> 此信息仅适用于早于 2.0.9169.0 的 MARS 代理版本。
>

| 备份计划选项 | 数据保留期限
| -- | --
| 日期 | **默认保留**：相当于"每日备份的保留天数"。 <br/><br/> **例外情况**：如果设置为长期保留（数周、数月或数年）的每日计划备份失败，则在故障后立即触发的按需备份将被视为长期保留。 否则，将考虑进行下一次计划备份以进行长期保留。<br/><br/> **示例方案**：计划在星期四上午 8：00 备份失败。 此备份将考虑每周、每月或每年保留。 因此，在星期五上午 8：00 的下一次计划备份之前触发的第一个按需备份将自动标记为每周、每月或每年的保留。 此备份代替周四上午 8：00 备份。
| Week | **默认保留**：一天。 对于具有每周备份策略的数据源，为按需备份在第二天将被删除。 即使它们是数据源的最新备份，它们也会被删除。 <br/><br/> **例外情况**：如果设置为长期保留（数周、数月或数年）的每周计划备份失败，则在故障后立即触发的按需备份将被视为长期保留。 否则，将考虑进行下一次计划备份以进行长期保留。 <br/><br/> **示例方案**：计划在星期四上午 8：00 备份失败。 此备份将考虑每月或每年保留。 因此，在星期四上午 8：00 的下一次计划备份之前触发的第一个按需备份将自动标记为每月或每年保留。 此备份代替周四上午 8：00 备份。

有关详细信息，请参阅[创建备份策略](#create-a-backup-policy)。

## <a name="next-steps"></a>后续步骤

* 了解如何在[Azure 中还原文件](backup-azure-restore-windows-server.md)。
* 查找[有关备份文件和文件夹的常见问题](backup-azure-file-folder-backup-faq.md)

