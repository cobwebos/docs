---
title: 监视警报和报告常见问题
description: 本文介绍有关 Azure 备份监视警报和 Azure 备份报表的常见问题的解答。
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 07/08/2019
ms.openlocfilehash: f5be97458ba658f315c31ae34e540842b64e3ec4
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989563"
---
# <a name="azure-backup-monitoring-alert---faq"></a>Azure 备份监视警报-常见问题

本文解答了有关 Azure 备份监视和报告的常见问题。

## <a name="configure-azure-backup-reports"></a>配置 Azure 备份报表

### <a name="how-do-i-check-if-reporting-data-has-started-flowing-into-a-log-analytics-la-workspace"></a>如何实现检查报告数据是否已开始流入 Log Analytics （LA）工作区？

导航到已配置的 "LA" 工作区，导航到 "**日志**" 菜单项，然后运行查询 CoreAzureBackup |请使用1。 如果看到返回的记录，则表示数据已开始流入工作区。 初始数据推送可能需要长达24小时。

### <a name="what-is-the-frequency-of-data-push-to-an-la-workspace"></a>向 LA 工作区推送数据的频率如何？

保管库中的诊断数据会抽取到 Log Analytics 工作区，但有一些延迟。 每个事件从恢复服务保管库推送到 Log Analytics 工作区20到30分钟。 下面是有关延迟的更多详细信息：

* 在所有解决方案中，备份服务的内置警报会在创建后立即推送。 因此，它们通常会在20到30分钟后显示在 Log Analytics 的工作区中。
* 在所有解决方案中，按需备份作业和还原作业将在完成后立即推送。
* 对于除 SQL 备份之外的所有解决方案，计划的备份作业在完成后立即推送。
* 对于 SQL 备份，因为日志备份每15分钟发生一次，所有已完成的计划备份作业（包括日志）的信息每隔6小时进行批处理和推送。
* 在所有解决方案中，其他信息（如备份项、策略、恢复点、存储等）每日推送至少一次。
* 备份配置中的更改（例如更改策略或编辑策略）触发了所有相关备份信息的推送。

### <a name="how-long-can-i-retain-reporting-data"></a>报告数据可以保留多长时间？

创建 LA 工作区后，可以选择保留最多2年的数据。 默认情况下，LA 工作区会将数据保留31天。

### <a name="will-i-see-all-my-data-in-reports-after-i-configure-the-la-workspace"></a>配置 LA 工作区后，是否会在报表中看到所有数据？

 配置诊断设置后生成的所有数据都将推送到 "LA" 工作区，并在报表中提供。 不会为报表推送正在进行的作业。 作业完成或失败后，会将其发送到报告。

### <a name="can-i-view-reports-across-vaults-and-subscriptions"></a>能否跨保管库和订阅查看报表？

是的，你可以查看跨保管库和订阅以及区域的报表。 你的数据可能驻留在单 LA 工作区或一组 LA 工作区中。

### <a name="can-i-view-reports-across-tenants"></a>能否跨租户查看报表？

如果你是有权访问客户订阅或 LA 工作区的[Azure Lighthouse](https://azure.microsoft.com/services/azure-lighthouse/)用户，则可以使用备份报表查看所有租户中的数据。

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>多长时间后，门户中会反映 Azure 备份代理作业状态？

最长可能需要 15 分钟，Azure 门户才会反映 Azure 备份代理作业状态。

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>备份作业失败后，需要多长时间才会引发警报？

Azure 备份失败后，会在 20 分钟内引发警报。

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>是否存在配置了通知却不发送电子邮件的情况？

可以。 在以下情况下，不会发送通知：

* 已将通知配置为每小时发送，并且在一小时内引发并解决了警报
* 取消了作业
* 由于原始备份作业正在进行，另一个备份作业失败

## <a name="recovery-services-vault"></a>恢复服务保管库

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>多长时间后，门户中会反映 Azure 备份代理作业状态？

最长可能需要 15 分钟，Azure 门户才会反映 Azure 备份代理作业状态。

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>备份作业失败后，需要多长时间才会引发警报？

Azure 备份失败后，会在 20 分钟内引发警报。

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>是否存在配置了通知却不发送电子邮件的情况？

可以。 在以下情况下，不发送通知：

* 已将通知配置为每小时发送，并且在一小时内引发并解决了警报
* 取消了作业
* 由于原始备份作业正在进行，另一个备份作业失败

## <a name="next-steps"></a>后续步骤

阅读其他常见问题：

* 有关 Azure VM 备份的[常见问题](backup-azure-vm-backup-faq.md)。
* 有关 Azure 备份代理的[常见问题](backup-azure-file-folder-backup-faq.md)
