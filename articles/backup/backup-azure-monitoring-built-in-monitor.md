---
title: 监视 Azure 备份保护的工作负荷
description: 本文介绍如何使用 Azure 门户的 Azure 备份工作负荷的监视和通知功能。
ms.topic: conceptual
ms.date: 03/05/2019
ms.assetid: 86ebeb03-f5fa-4794-8a5f-aa5cbbf68a81
ms.openlocfilehash: ea5102a95a9bef17f25219e00dec4654bf7f06d6
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79273366"
---
# <a name="monitoring-azure-backup-workloads"></a>监视 Azure 备份工作负荷

Azure 备份根据备份要求和基础结构拓扑（本地与 Azure）提供多个备份解决方案。 任何备份用户或管理员都应能看到在所有解决方案中进行的操作，并且预计会在重要方案中收到通知。 本文详细介绍了 Azure 备份服务提供的监视和通知功能。

## <a name="backup-jobs-in-recovery-services-vault"></a>恢复服务保管库中的备份作业

Azure 备份为受 Azure 备份保护的工作负荷提供内置的监视和警报功能。 在 "恢复服务保管库设置" 中，"**监视**" 部分提供了生成的作业和警报。

![RS vault 内置监视](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltmonitoring.png)

执行配置备份、备份、还原、删除备份等操作时，将生成作业。

以下 Azure 备份解决方案中的作业如下所示：

- Azure VM 备份
- Azure 文件备份
- Azure 工作负载备份，如 SQL
- Azure 备份代理 (MAB)

不显示 System Center Data Protection Manager （SC-DPM） Microsoft Azure 备份 Server （MABS）中的作业。

> [!NOTE]
> Azure Vm 中的 azure 工作负荷（如 SQL 备份）具有大量的备份作业。 例如，日志备份可以每15分钟运行一次。 因此，对于此类 DB 工作负载，只显示用户触发的操作。 不显示计划的备份操作。

## <a name="backup-alerts-in-recovery-services-vault"></a>恢复服务保管库中的备份警报

警报主要是用户收到通知，以便他们可以执行相关操作的情况。 "**备份警报**" 部分显示 Azure 备份服务生成的警报。 这些警报由服务定义，用户无法自定义创建任何警报。

### <a name="alert-scenarios"></a>警报方案

以下方案由服务定义为可报警方案。

- 备份/还原失败
- 备份成功，但出现 Azure 备份代理 (MAB) 的警告
- 停止保护并保留数据/停止保护并删除数据

### <a name="exceptions-when-an-alert-is-not-raised"></a>不引发警报时的异常

如果在发生故障时未引发警报，则会出现一些例外：

- 用户显式取消正在运行的作业
- 作业失败，因为另一个备份作业正在进行中（因为我们只需等待之前的作业完成），
- VM 备份作业失败，因为备份的 Azure VM 不再存在

以上异常的设计从了解到这些操作的结果（主要是用户触发的）会立即显示在门户/PS/CLI 客户端上。 因此，用户会立即了解，无需通知。

### <a name="alerts-from-the-following-azure-backup-solutions-are-shown-here"></a>以下 Azure 备份解决方案中显示的警报如下所示

- Azure VM 备份
- Azure 文件备份
- Azure 工作负荷备份，例如 SQL
- Azure 备份代理 (MAB)

> [!NOTE]
> 此处未显示来自 System Center Data Protection Manager （SC-DPM） Microsoft Azure 备份 Server （MABS）的警报。

### <a name="alert-types"></a>警报类型

根据警报严重性，可以通过三种类型定义警报：

- **关键**：在原则上，任何备份或恢复失败（已计划或用户触发）都将导致生成警报，并且将显示为严重警报和破坏性操作，例如删除备份。
- **警告**：如果备份操作成功，但有几个警告，它们将作为警告警报列出。
- **信息**：目前，Azure 备份服务不会生成信息性警报。

## <a name="notification-for-backup-alerts"></a>备份警报的通知

> [!NOTE]
> 只能通过 Azure 门户来配置通知。 不支持 PS/CLI/REST API/Azure 资源管理器模板支持。

一旦发出警报，就会通知用户。 Azure 备份通过电子邮件提供内置通知机制。 可以指定要在生成警报时通知的单个电子邮件地址或通讯组列表。 你还可以选择是接收每个单个警报的通知，还是按小时摘要对它们进行分组，然后接收通知。

![RS Vault 内置电子邮件通知](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltnotification.png)

> [!NOTE]
> SQL 备份警报将合并，并且仅在第一次出现时发送电子邮件。 但如果用户停用了警报，则下一个事件会触发另一封电子邮件。

配置通知后，你将收到欢迎或介绍性电子邮件。 这可确认在引发警报时，Azure 备份可以向这些地址发送电子邮件。<br>

如果将频率设置为 "每小时摘要"，并在一小时内引发并解决了警报，则不会成为即将到来的每小时摘要。

> [!NOTE]
>
> - 如果执行了破坏性操作（如 "**停止保护并删除数据**"），则会发出警报，并将电子邮件发送给订阅所有者、管理员和共同管理员，即使没有为恢复服务保管库配置通知。
> - 若要配置成功作业的通知，请使用[Log Analytics](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-workspace)。

## <a name="inactivating-alerts"></a>停用警报

若要停用/解决活动警报，可以单击与要禁用的警报相对应的列表项。 此时将打开一个屏幕，其中显示有关警报的详细信息，顶部有一个 "停用" 按钮。 单击此按钮会将警报的状态更改为 "非活动"。 还可以通过右键单击对应于该警报的列表项并选择 "停用" 来停用警报。

![RS Vault 警报停用](media/backup-azure-monitoring-laworkspace/vault-alert-inactivation.png)

## <a name="next-steps"></a>后续步骤

[使用 Azure Monitor 监视 Azure 备份工作负荷](backup-azure-monitoring-use-azuremonitor.md)
