---
title: 设置警报和通知（Azure 门户）
description: 使用 Azure 门户创建 SQL 数据库警报，可在满足指定条件时触发通知或自动化。
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
ms.date: 11/02/2018
ms.openlocfilehash: c2b889d4013abb60c9ad7bb4bcdc4e6546cfa37c
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2020
ms.locfileid: "75745950"
---
# <a name="create-alerts-for-azure-sql-database-and-data-warehouse-using-azure-portal"></a>使用 Azure 门户为 Azure SQL 数据库和数据仓库创建警报

## <a name="overview"></a>概述
本文介绍如何使用 Azure 门户设置 Azure SQL 数据库和数据仓库警报。 当某些指标（例如数据库大小或 CPU 使用率）达到阈值时，警报可以向你发送电子邮件或调用 Webhook。 本文还提供了有关设置警报期限的最佳做法。    

> [!IMPORTANT]
> 此功能在托管实例中尚不可用。 作为替代方法，你可以使用 SQL 代理根据[动态管理视图](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views)发送某些指标的电子邮件警报。

可以根据监控指标或事件接收 Azure 服务的警报。

* **指标值** - 指定指标的值超过在任一方向分配的阈值时，将触发警报。 也就是说，当首次满足条件时，以及之后不再满足条件时，都会触发此警报。    
* **活动日志事件** - 每个事件发生，或仅当出现一定次数的事件时，触发警报。

可配置警报，使警报触发时执行以下操作：

* 向服务管理员和共同管理员发送电子邮件通知
* 将电子邮件发送到指定的其他电子邮件。
* 调用 Webhook

可使用以下项配置并获取有关警报规则的信息

* [Azure 门户](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../azure-monitor/platform/alerts-classic-portal.md)
* [命令行接口 (CLI)](../azure-monitor/platform/alerts-classic-portal.md)
* [Azure 监视器 REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>使用 Azure 门户创建指标的警报规则
1. 在此[门户](https://portal.azure.com/)，查找想要监视的资源并选中它。
2. 在“监视”部分下，选择“警报(经典)”。 对于不同的资源，文本和图标可能会略有不同。  
   
     ![监视](media/sql-database-insights-alerts-portal/AlertsClassicButton.JPG)
  
   - **仅限 SQL DW** ：单击“DWU 使用情况”图表。 选择“查看经典警报”

3. 选择“添加指标警报(经典)”按钮，并填写字段。
   
    ![添加警报](media/sql-database-insights-alerts-portal/AddDBAlertPageClassic.JPG)
4. **命名**警报规则，并选择也在通知电子邮件中显示的“说明”。
5. 选择想要监视的“指标”为该指标选择一个“条件”和“阈值”。 还选择触发警报前指标规则必须满足的时间段。 例如，如果使用时间段"PT5M"，且警报针对 CPU 高于 80% 的情况，则平均 CPU 高于 80% 达到 5 分钟时触发警报。 第一次触发结束后，当平均 CPU 低于 80% 的时间超过 5 分钟时，将再次触发。 每 1 分钟对 CPU 进行一次测量。 请参阅下表，了解支持的时间窗口和每个警报使用的聚合类型（并非所有警报都使用平均值）。   
6. 如果触发警报时希望向管理员和共同管理员发送电子邮件，则选择“向所有者发送电子邮件...”。
7. 触发警报时，如果希望其他电子邮件收到通知，请将其添加到“其他管理员电子邮件”字段下。 用分号分隔多个电子邮件- *email\@contoso .com; email2\@contoso.com*
8. 触发警报时，如果希望调用有效的 URI，请将其放入“Webhook”字段。
9. 警报创建完成后，选择“确定”。   

几分钟后，警报将处于活动状态，并按前面所述进行触发。

## <a name="managing-your-alerts"></a>管理警报
创建警报后，可选择它并：

* 查看显示指标的阈值和前一天实际值的关系图。
* 编辑或删除其。
* 如果想要暂时停止或恢复接收该警报的通知，可**禁用**或**启用**它。


## <a name="sql-database-alert-values"></a>SQL 数据库警报值

| 资源类型 | 标准名称 | 友好名称 | 聚合类型 | 最小警报时间窗口|
| --- | --- | --- | --- | --- |
| SQL 数据库 | cpu_percent | CPU 百分比 | 平均值 | 5 分钟 |
| SQL 数据库 | physical_data_read_percent | 数据 IO 百分比 | 平均值 | 5 分钟 |
| SQL 数据库 | log_write_percent | 日志 IO 百分比 | 平均值 | 5 分钟 |
| SQL 数据库 | dtu_consumption_percent | DTU 百分比 | 平均值 | 5 分钟 |
| SQL 数据库 | 存储 | 数据库总大小 | 最大值 | 30 分钟 |
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
| 弹性池 | cpu_percent | CPU 百分比 | 平均值 | 10 分钟 |
| 弹性池 | physical_data_read_percent | 数据 IO 百分比 | 平均值 | 10 分钟 |
| 弹性池 | log_write_percent | 日志 IO 百分比 | 平均值 | 10 分钟 |
| 弹性池 | dtu_consumption_percent | DTU 百分比 | 平均值 | 10 分钟 |
| 弹性池 | storage_percent | 存储百分比 | 平均值 | 10 分钟 |
| 弹性池 | workers_percent | 辅助角色百分比 | 平均值 | 10 分钟 |
| 弹性池 | eDTU_limit | eDTU 限制 | 平均值 | 10 分钟 |
| 弹性池 | storage_limit | 存储限制 | 平均值 | 10 分钟 |
| 弹性池 | eDTU_used | 已用的 eDTU | 平均值 | 10 分钟 |
| 弹性池 | storage_used | 已用存储 | 平均值 | 10 分钟 |
||||||               
| SQL 数据仓库 | cpu_percent | CPU 百分比 | 平均值 | 10 分钟 |
| SQL 数据仓库 | physical_data_read_percent | 数据 IO 百分比 | 平均值 | 10 分钟 |
| SQL 数据仓库 | connection_successful | 成功的连接数 | 总计 | 10 分钟 |
| SQL 数据仓库 | connection_failed | 失败的连接数 | 总计 | 10 分钟 |
| SQL 数据仓库 | blocked_by_firewall | 被防火墙阻止 | 总计 | 10 分钟 |
| SQL 数据仓库 | service_level_objective | 数据库服务层 | 总计 | 10 分钟 |
| SQL 数据仓库 | dwu_limit | dwu 限制 | 最大值 | 10 分钟 |
| SQL 数据仓库 | dwu_consumption_percent | DWU 百分比 | 平均值 | 10 分钟 |
| SQL 数据仓库 | dwu_used | 已用的 DWU | 平均值 | 10 分钟 |
||||||


## <a name="next-steps"></a>后续步骤
* [获取 Azure 监视概述](../monitoring-and-diagnostics/monitoring-overview.md)，包括可收集和监视的信息的类型。
* 了解[在警报中配置 Webhook](../azure-monitor/platform/alerts-webhooks.md)的详细信息。
* 获取[诊断日志概述](../azure-monitor/platform/platform-logs-overview.md)收集有关服务的详细高频率指标。
* 获取[指标集合概述](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)以确保服务可用且响应迅速。
