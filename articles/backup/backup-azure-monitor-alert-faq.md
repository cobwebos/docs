---
title: 监视警报和报告常见问题解答
description: 本文介绍有关 Azure 备份监视警报和 Azure 备份报告的常见问题解答。
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 07/08/2019
ms.openlocfilehash: cf6929b9b926a6e6469f3fa789a19e60d5883d21
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89181487"
---
# <a name="azure-backup-monitoring-alert---faq"></a>Azure 备份监视警报 - 常见问题解答

本文解答了有关 Azure 备份监视和报告的常见问题。

## <a name="configure-azure-backup-reports"></a>配置 Azure 备份报表

### <a name="how-do-i-check-if-reporting-data-has-started-flowing-into-a-log-analytics-la-workspace"></a>如何确定报告数据是否已开始流向 Log Analytics (LA) 工作区？

导航到已配置的 "LA" 工作区。 导航到 " **日志** " 菜单项，然后运行查询 `CoreAzureBackup | take 1` 。 如果看到返回了记录，则表示数据已开始流入工作区。 初始数据推送可能需要长达 24 小时。

### <a name="what-is-the-frequency-of-data-push-to-an-la-workspace"></a>向 LA 工作区推送数据的频率是怎样的？

保管库中的诊断数据将传送到 Log Analytics 工作区，但会出现一定的延迟。 从恢复服务保管库推送每个事件 20 到 30 分钟后，这些事件将抵达 Log Analytics 工作区。 下面是有关延迟的更多详细信息：

* 在所有解决方案中，一旦创建备份服务的内置警报，就会立即推送这些警报。 因此，它们通常会在 20 到 30 分钟后显示在 Log Analytics 工作区中。
* 在所有解决方案中，在完成按需备份作业和还原作业后，会立即推送这些作业。
* 对于除 SQL 备份以外的所有解决方案，在完成计划的备份作业后，会立即推送这些作业。
* 对于 SQL 备份，由于日志备份可每隔 15 分钟发生，所有已完成的计划备份作业的信息（包括日志）将每隔 6 小时进行批处理和推送。
* 在所有解决方案中，备份项、策略、恢复点、存储等其他信息每天至少推送一次。
* 备份配置发生更改（例如更改策略或编辑策略）会触发所有相关备份信息的推送。

### <a name="how-long-can-i-retain-reporting-data"></a>报告数据可以保留多长时间？

创建 LA 工作区后，可以选择将数据保留最多 2 年。 默认情况下，LA 工作区将数据保留 31 天。

### <a name="will-i-see-all-my-data-in-reports-after-i-configure-the-la-workspace"></a>配置 LA 工作区后，报告中是否会显示我的所有数据？

 在你配置诊断设置后生成的所有数据都会推送到 LA 工作区，并会显示在报告中。 不会为报表推送正在进行的作业。 作业完成或失败后，会将其发送到报表。

### <a name="can-i-view-reports-across-vaults-and-subscriptions"></a>能否跨保管库和订阅查看报表？

能，你可以跨保管库、订阅以及区域查看报告。 你的数据可能驻留在单个 LA 工作区或一组 LA 工作区中。

### <a name="can-i-view-reports-across-tenants"></a>能否跨租户查看报表？

如果你是有权访问客户订阅或 LA 工作区的 [Azure Lighthouse](https://azure.microsoft.com/services/azure-lighthouse/) 用户，则可以使用备份报表查看所有租户中的数据。

## <a name="recovery-services-vault"></a>恢复服务保管库

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Azure 备份代理作业状态需要多长时间才能在门户中反映出来？

Azure 门户最多可能需要15分钟才能反映 Azure 备份代理作业状态。

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>备份作业失败后，需要多长时间才会引发警报？

在 Azure 备份失败的20分钟内引发警报。

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>是否存在配置了通知却不发送电子邮件的情况？

是的。 在以下情况下，不发送通知：

* 已将通知配置为每小时发送，并且在一小时内引发并解决了警报
* 取消了作业
* 由于原始备份作业正在进行，另一个备份作业失败

## <a name="next-steps"></a>后续步骤

阅读其他常见问题：

* 有关 Azure VM 备份的[常见问题](backup-azure-vm-backup-faq.md)。
* 有关 Azure 备份代理的[常见问题](backup-azure-file-folder-backup-faq.md)
