---
title: "使用 Azure 门户创建 SQL 数据库警报 | Microsoft 文档"
description: "使用 Azure 门户创建 SQL 数据库警报，可在满足指定条件时触发通知或自动化。"
author: CarlRabeler
manager: jhubbard
editor: 
services: sql-database
documentationcenter: 
ms.assetid: f7457655-ced6-4102-a9dd-7ddf2265c0e2
ms.service: sql-database
ms.custom: monitor & manage
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2017
ms.author: carlrab
ms.translationtype: Human Translation
ms.sourcegitcommit: 2679681c77dd6a3410bbe6ddbcf562924b13bfe6
ms.openlocfilehash: afa21052281200768db24ce35a94097f23f23efe
ms.contentlocale: zh-cn
ms.lasthandoff: 11/17/2016


---
# <a name="use-azure-portal-to-create-alerts-for-azure-sql-database"></a>使用 Azure 门户为 Azure SQL 数据库创建警报

## <a name="overview"></a>概述
本文介绍如何使用 Azure 门户设置 Azure SQL 数据库警报。 本文还提供了值和阈值的最佳实践。    

可以根据监视指标或事件接收 Azure 服务的警报。

* **指标值** - 指定指标的值超过在任一方向分配的阈值时，将触发警报。 也就是说，在以下两种情况下会触发警报：首先是满足条件时，然后是满足条件后，不再满足条件时。    
* **活动日志事件** - 每个事件发生，或仅当出现一定次数的事件时，触发警报。

可配置警报，使警报触发时执行以下操作：

* 向服务管理员和共同管理员发送电子邮件通知
* 将电子邮件发送到指定的其他电子邮件。
* 调用 Webhook
* 开始执行 Azure Runbook（仅从 Azure 门户）

可使用以下项配置并获取有关警报规则的信息

* [Azure 门户](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../monitoring-and-diagnostics/insights-alerts-powershell.md)
* [命令行接口 (CLI)](../monitoring-and-diagnostics/insights-alerts-command-line-interface.md)
* [Azure 监视器 REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>使用 Azure 门户创建指标的警报规则
1. 在此[门户](https://portal.azure.com/)，查找想要监视的资源并选中它。
2. 在“监视”部分下，选择“警报”或“警报规则”。 对于不同的资源，文本和图标可能会略有不同。  
   
    ![监视](../monitoring-and-diagnostics/media/insights-alerts-portal/AlertRulesButton.png)
3. 选择“添加通知”命令，并填写字段。
   
    ![添加警报](../monitoring-and-diagnostics/media/insights-alerts-portal/AddAlertOnlyParamsPage.png)
4. **命名**警报规则，并选择也在通知电子邮件中显示的“说明”。
5. 选择想要监视的“指标”为该指标选择一个“条件”和“阈值”。 还选择了触发警报前指标规则必须满足的时间**段**。 例如，如果使用时间段"PT5M"，且警报针对 CPU 高于 80% 的情况，则 CPU 持续高于 80% 达到 5 分钟时触发警报。 第一次触发后，CPU 5 分钟内持续低于 80% 时将再次触发警报。 每 1 分钟对 CPU 进行一次测量。   
6. 如果触发警报时希望向管理员和共同管理员发送电子邮件，则选择“向所有者发送电子邮件...”。
7. 触发警报时，如果希望其他电子邮件收到通知，请将其添加到“其他管理员电子邮件”字段下。 用分号隔开多个电子邮件 - *email@contoso.com;email2@contoso.com*
8. 触发警报时，如果希望调用有效的 URI，请将其放入“Webhook”字段。
9. 如果使用 Azure 自动化时，则触发警报时可选择要运行的 Runbook。
10. 警报创建完成后，选择“确定”。   

几分钟后，警报将处于活动状态，并按前面所述进行触发。

## <a name="managing-your-alerts"></a>管理你的警报
创建警报后，可选择它并：

* 查看显示指标的阈值和前一天实际值的关系图。
* 编辑或删除其。
* 如果想要暂时停止或恢复接收该警报的通知，可**禁用**或**启用**它。


## <a name="sql-database-alert-values-and-thresholds"></a>SQL 数据库警报值和阈值

| 资源类型 | 指标名称 | 友好名称 | 聚合类型 | 最小警报时间窗口|
| --- | --- | --- | --- | --- |
| SQL 数据库 | cpu_percent | CPU 百分比 | 平均值 | 5 分钟 |
| SQL 数据库 | physical_data_read_percent | 数据 IO 百分比 | 平均值 | 5 分钟 |
| SQL 数据库 | log_write_percent | 日志 IO 百分比 | 平均值 | 5 分钟 |
| SQL 数据库 | dtu_consumption_percent | DTU 百分比 | 平均值 | 5 分钟 |
| SQL 数据库 | storage | 数据库总大小 | 最大值 | 30 分钟 |
| SQL 数据库 | connection_successful | 成功的连接数 | 总计 | 10 分钟 |
| SQL 数据库 | connection_failed | 失败的连接数 | 总计 | 10 分钟 |
| SQL 数据库 | blocked_by_firewall | 被防火墙阻止 | 总计 | 10 分钟 |
| SQL 数据库 | deadlock | 死锁数 | 总计 | 10 分钟 |
| SQL 数据库 | storage_percent | 数据库大小百分比 | 最大值 | 30 分钟 |
| SQL 数据库 | xtp_storage_percent | In-Memory OLTP 存储百分比（预览） | 平均值 | 5 分钟 |
| SQL 数据库 | workers_percent | 辅助角色百分比 | 平均值 | 5 分钟 |
| SQL 数据库 | sessions_percent | 会话百分比 | 平均值 | 5 分钟 |
| SQL 数据库 | dtu_limit | DTU 限制 | 平均值 | 5 分钟 |
| SQL 数据库 | dtu_used | 已用的 DTU | 平均值 | 5 分钟 |
||||||           
| SQL 数据仓库 | cpu_percent | CPU 百分比 | 平均值 | 10 分钟 |
| SQL 数据仓库 | physical_data_read_percent | 数据 IO 百分比 | 平均值 | 10 分钟 |
| SQL 数据仓库 | storage | 数据库总大小 | 最大值 | 10 分钟 |
| SQL 数据仓库 | connection_successful | 成功的连接数 | 总计 | 10 分钟 |
| SQL 数据仓库 | connection_failed | 失败的连接数 | 总计 | 10 分钟 |
| SQL 数据仓库 | blocked_by_firewall | 被防火墙阻止 | 总计 | 10 分钟 |
| SQL 数据仓库 | service_level_objective | 数据库的服务级别目标 | 总计 | 10 分钟 |
| SQL 数据仓库 | dwu_limit | dwu 限制 | 最大值 | 10 分钟 |
| SQL 数据仓库 | dwu_consumption_percent | DWU 百分比 | 平均值 | 10 分钟 |
| SQL 数据仓库 | dwu_used | 已用的 DWU | 平均值 | 10 分钟 |
||||||               
| 弹性池 | cpu_percent | CPU 百分比 | 平均值 | 5 分钟 |
| 弹性池 | physical_data_read_percent | 数据 IO 百分比 | 平均值 | 5 分钟 |
| 弹性池 | log_write_percent | 日志 IO 百分比 | 平均值 | 5 分钟 |
| 弹性池 | dtu_consumption_percent | DTU 百分比 | 平均值 | 5 分钟 |
| 弹性池 | storage_percent | 存储百分比 | 平均值 | 5 分钟 |
| 弹性池 | workers_percent | 辅助角色百分比 | 平均值 | 5 分钟 |
| 弹性池 | eDTU_limit | eDTU 限制 | 平均值 | 5 分钟 |
| 弹性池 | storage_limit | 存储限制 | 平均值 | 5 分钟 |
| 弹性池 | eDTU_used | 已用的 eDTU | 平均值 | 5 分钟 |
| 弹性池 | storage_used | 已用的存储量 | 平均值 | 5 分钟 |
||||||


## <a name="next-steps"></a>后续步骤
* [获取 Azure 监视概述](../monitoring-and-diagnostics/monitoring-overview.md)，包括可收集和监视的信息的类型。
* 了解[在警报中配置 Webhook](../monitoring-and-diagnostics/insights-webhooks-alerts.md)的详细信息。
* 详细了解 [Azure 自动化 Runbook](../automation/automation-starting-a-runbook.md)。
* 获取[诊断日志概述](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)收集有关服务的详细高频率指标。
* 获取[指标集合概述](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)以确保你的服务可用且响应迅速。


