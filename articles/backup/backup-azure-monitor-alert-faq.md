---
title: Azure 备份监视警报常见问题解答
description: 本文介绍有关 Azure 备份监视警报和 Azure 备份报表的常见问题的解答。
ms.reviewer: srinathv
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: dacurwin
ms.openlocfilehash: dc0225092d23371ca97dfedd48a2d3ffcf85a9be
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747394"
---
# <a name="azure-backup-monitoring-alert---faq"></a>Azure 备份监视警报 - 常见问题解答

本文解答有关 Azure 备份监视警报的常见问题。

## <a name="configure-azure-backup-reports"></a>配置 Azure 备份报表

### <a name="how-do-i-check-if-reporting-data-has-started-flowing-into-a-storage-account"></a>如何确定报表数据是否已开始流向存储帐户？

转到配置的存储帐户，并选择容器。 如果容器包含 insights-logs-azurebackupreport 条目，则表示报表数据已开始流向存储帐户。

### <a name="what-is-the-frequency-of-data-push-to-a-storage-account-and-the-azure-backup-content-pack-in-power-bi"></a>数据多久向存储帐户和 Power BI 中的 Azure 备份内容包推送一次？

  在用户配置存储帐户当天，数据大约需要 24 小时才能推送到存储帐户。 在这一次初始推送完成后，将按下图中所示频率刷新数据。

* 与“作业”、“警报”、“备份项”、“保管库”、“受保护的服务器”和“策略”相关的数据：在客户登录时推送到客户存储帐户。

* 与“存储”相关的数据：每 24 小时推送到客户存储帐户一次。

    ![Azure 备份报表数据推送频率](./media/backup-azure-configure-reports/reports-data-refresh-cycle.png)

* Power BI [计划每天刷新一次](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/#what-can-be-refreshed)。 对于内容包，可以在 Power BI 中手动刷新数据。

### <a name="how-long-can-i-retain-reports"></a>报表可以保留多长时间？

配置存储帐户时，可以在存储帐户中选择报表数据的保留期。 请按照[配置报表的存储帐户](backup-azure-configure-reports.md#configure-storage-account-for-reports)部分中的步骤 6 执行操作。 此外，还可以[在 Excel 中分析报表](https://powerbi.microsoft.com/documentation/powerbi-service-analyze-in-excel/)，并保存报表以根据需要延长保持期。

### <a name="will-i-see-all-my-data-in-reports-after-i-configure-the-storage-account"></a>配置存储帐户后，报表中是否会显示我的所有数据？

 配置存储帐户后生成的所有数据都会推送到存储帐户，并会显示在报表中。 不会为报表推送正在进行的作业。 作业完成或失败后，会将其发送到报表。

### <a name="if-i-already-configured-the-storage-account-to-view-reports-can-i-change-the-configuration-to-use-another-storage-account"></a>如果我已配置存储帐户来查看报表，能否更改配置以使用其他存储帐户？

能，可以将配置更改为指向不同的存储帐户。 连接到 Azure 备份内容包时，可使用新配置的存储帐户。 此外，配置其他存储帐户后，新数据便会流向此存储帐户。 更改配置前的旧数据仍会保留在旧存储帐户中。

### <a name="can-i-view-reports-across-vaults-and-subscriptions"></a>能否跨保管库和订阅查看报表？

能，可以跨各种保管库配置同一存储帐户，以便跨保管库查看报表。 此外，还可以跨订阅为保管库配置同一存储帐户。 然后，可以在连接到 Power BI 中的 Azure 备份内容包时使用此存储帐户来查看报表。 所选存储帐户必须位于与恢复服务保管库相同的区域。

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>多长时间后，门户中会反映 Azure 备份代理作业状态？

最长可能需要 15 分钟，Azure 门户才会反映 Azure 备份代理作业状态。

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>备份作业失败后，需要多长时间才会引发警报？

Azure 备份失败后，会在 20 分钟内引发警报。

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>是否存在配置了通知却不发送电子邮件的情况？

是的。 在以下情况下，不会发送通知：

* 已将通知配置为每小时发送，并且在一小时内引发并解决了警报
* 取消了作业
* 由于原始备份作业正在进行，另一个备份作业失败

## <a name="recovery-services-vault"></a>恢复服务保管库

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>多长时间后，门户中会反映 Azure 备份代理作业状态？

最长可能需要 15 分钟，Azure 门户才会反映 Azure 备份代理作业状态。

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>备份作业失败后，需要多长时间才会引发警报？

Azure 备份失败后，会在 20 分钟内引发警报。

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>是否存在配置了通知却不发送电子邮件的情况？

是的。 在以下情况下，不发送通知：

* 已将通知配置为每小时发送，并且在一小时内引发并解决了警报
* 取消了作业
* 由于原始备份作业正在进行，另一个备份作业失败

## <a name="next-steps"></a>后续步骤

阅读其他常见问题：

* 有关 Azure VM 备份的[常见问题](backup-azure-vm-backup-faq.md)。
* 有关 Azure 备份代理的[常见问题](backup-azure-file-folder-backup-faq.md)
