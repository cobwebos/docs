---
title: 监视 Azure 备份保护的工作负荷
description: 本文介绍使用 Azure 门户对 Azure 备份工作负荷执行的监视和通知功能。
ms.topic: conceptual
ms.date: 03/05/2019
ms.assetid: 86ebeb03-f5fa-4794-8a5f-aa5cbbf68a81
ms.openlocfilehash: 645bf701a8f8561b2cb4f19378036ada82c4bedd
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87054542"
---
# <a name="monitoring-azure-backup-workloads"></a>监视 Azure 备份工作负荷

Azure 备份根据备份要求和基础结构拓扑（本地或 Azure）提供多个备份解决方案。 任何备份用户或管理员都会看到所有解决方案中发生的情况，并会在出现重大情况时收到通知。 本文详细介绍了 Azure 备份服务提供的监视和通知功能。

## <a name="backup-jobs-in-recovery-services-vault"></a>恢复服务保管库中的 Azure 备份作业

Azure 备份针对 Azure 备份保护的工作负荷提供内置的监视和警报功能。 在恢复服务保管库设置中，“监视”部分提供了内置的作业和警报。

![恢复服务保管库内置监视](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltmonitoring.png)

执行配置备份、备份、还原、删除备份等操作时，会生成作业。

此处会显示以下 Azure 备份解决方案中的作业：

- Azure VM 备份
- Azure 文件备份
- Azure 工作负荷备份，例如 SQL 和 SAP HANA 备份
- Microsoft Azure 恢复服务 (MARS) 代理

不会显示 System Center Data Protection Manager (SC-DPM) 和 Microsoft Azure 备份服务器 (MABS) 中的作业。

> [!NOTE]
> Azure VM 中的 Azure 工作负荷（例如 SQL 和 SAP HANA 备份）包含大量的备份作业。 例如，日志备份可能每隔 15 分钟运行一次。 因此，对于此类数据库工作负荷，只会显示用户触发的操作。 不显示计划的备份操作。

## <a name="backup-alerts-in-recovery-services-vault"></a>恢复服务保管库中的备份警报

警报主要用于通知用户，让他们采取相关的措施。 “备份警报”部分显示 Azure 备份服务生成的警报。 这些警报由服务定义，用户无法以自定义方式创建任何警报。

### <a name="alert-scenarios"></a>警报方案

以下方案由服务定义为可发出警报的方案。

- 备份/还原失败
- 备份成功，Microsoft Azure 恢复服务（MARS）代理的警告
- 停止保护并保留数据/停止保护并删除数据

### <a name="alerts-from-the-following-azure-backup-solutions-are-shown-here"></a>此处会显示以下 Azure 备份解决方案中的警报

- Azure VM 备份
- Azure 文件备份
- Azure 工作负荷备份，例如 SQL 备份、SAP HANA 备份
- Microsoft Azure 恢复服务 (MARS) 代理 

> [!NOTE]
> 此处不会显示 System Center Data Protection Manager (SC-DPM) 和 Microsoft Azure 备份服务器 (MABS) 中的警报。

### <a name="consolidated-alerts"></a>合并的警报

对于 Azure 工作负荷备份解决方案（例如 SQL 和 SAP HANA），系统可以非常频繁地生成日志备份（根据策略，最高可达每 15 分钟 1 次）。 因此，也可能会出现日志备份失败也很频繁（高达每 15 分钟一次）的情况。 在这种情况下，如果每次失败都引发一次警报，最终用户将会不堪其扰。 因此，系统会在第一次失败时发送警报，以后由于同一根本原因而失败时则不会生成警报。 将在第一个警报中更新失败计数。 但如果该警报被用户停用，则下一次失败会触发另一警报，系统会将其视为该情况的第一个警报。 这是 Azure 备份针对 SQL 和 SAP HANA 备份执行警报合并的方式。

### <a name="exceptions-when-an-alert-is-not-raised"></a>未引发警报时生成异常

在一些例外情况下，失败时不会引发警报。 它们具有以下特点：

- 用户显式取消了正在运行的作业
- 作业失败，因为另一个备份作业正在进行（在此情况下，无需采取任何措施，因为只需等待前一个作业完成即可）
- VM 备份作业失败，因为备份的 Azure VM 不再存在
- [合并的警报](#consolidated-alerts)

之所以设计上述异常，是因为我们知道，这些操作的结果（主要是用户触发的操作）会立即显示在门户/PS/CLI 客户端中。 因此，用户会立即了解相关情况，不需要通知。

### <a name="alert-types"></a>警报类型

根据警报严重性，可以定义三种类型的警报：

- **严重**：原则上，发生任何备份或恢复失败（计划的或用户触发的）都会导致生成警报，并显示为严重警报以及破坏性操作（例如删除备份）。
- **警告**：如果备份操作成功但出现了几条警告，则会将这些警报列为“警告”警报。 “警告”警报目前仅适用于 Azure 备份代理备份。
- **信息性**：目前，Azure 备份服务不会生成任何信息性警报。

## <a name="notification-for-backup-alerts"></a>备份警报的通知

> [!NOTE]
> 只能通过 Azure 门户配置通知。 不支持使用 PS/CLI/REST API/Azure 资源管理器模板。

一旦引发警报，用户就会收到通知。 Azure 备份通过电子邮件提供内置通知机制。 可以指定在生成警报时接收通知的个人电子邮件地址或通讯组列表。 还可以选择是要接收每个警报的通知，还是将这些警报分组成按小时摘要，然后接收通知。

![恢复服务保管库内置电子邮件通知](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltnotification.png)

配置通知后，你会收到一封欢迎电子邮件或简介电子邮件。 由此可以确认，在引发警报时，Azure 备份可向这些地址发送电子邮件。<br>

如果将频率设置为“每小时摘要”，然后引发了警报，但在一小时内解决了该警报，那么，后续的每小时摘要中不会包括该警报。

> [!NOTE]
>
> - 如果执行了破坏性操作（例如“停止保护并删除数据”），那么，即使未针对恢复服务保管库配置通知，也会引发警报，并向订阅所有者、管理员和共同管理员发送电子邮件。
> - 若要针对成功的作业配置通知，请使用 [Log Analytics](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-workspace)。

## <a name="inactivating-alerts"></a>停用警报

若要停用/解决某个活动警报，可以单击与要停用的警报相对应的列表项。 这将打开一个屏幕，其中会显示有关警报的详细信息，顶部有一个“停用”按钮。 单击该按钮会将警报的状态更改为“非活动”。 还可以通过以下方式停用警报：右键单击与警报对应的列表项并选择“停用”。

![停用恢复服务保管库警报](media/backup-azure-monitoring-laworkspace/vault-alert-inactivation.png)

## <a name="next-steps"></a>后续步骤

[使用 Azure Monitor 监视 Azure 备份工作负荷](backup-azure-monitoring-use-azuremonitor.md)
