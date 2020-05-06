---
title: 为托管实例设置警报和通知（Azure 门户）
description: 使用 Azure 门户创建 SQL 托管实例警报，该警报可在满足指定条件时触发通知或自动化操作。
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 05/04/2020
ms.openlocfilehash: 0e7c4cde684f393fd98ada46393948c5a62efa2f
ms.sourcegitcommit: c8a0fbfa74ef7d1fd4d5b2f88521c5b619eb25f8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82800774"
---
# <a name="create-alerts-for-azure-sql-managed-instance-using-the-azure-portal"></a>使用 Azure 门户为 Azure SQL 托管实例创建警报

## <a name="overview"></a>概述

本文说明如何使用 Azure 门户为 Azure SQL 托管实例数据库中的数据库设置警报。 警报可以向你发送电子邮件，呼叫 web 挂钩，执行 Azure 功能，runbook，调用外部 ITSM 兼容票证系统，在手机上呼叫你，或在某个指标（例如实例存储大小或 CPU 使用率）达到预定义阈值时发送短信。 本文还提供了设置警报期限的最佳做法。

可以根据监控指标或事件接收 Azure 服务的警报。

* **指标值** - 当指定指标的值在任一方向越过了指定的阈值时警报将触发。 也就是说，当条件先是满足以及之后不再满足该条件时，警报都会触发。

可以配置警报以在其触发时执行以下操作：

* 向服务管理员和共同发送电子邮件通知
* 将电子邮件发送到指定的其他电子邮件地址。
* 使用语音提示拨打电话号码
* 将短信发送到电话号码
* 调用 Webhook
* 调用 Azure 函数
* 调用 Azure runbook
* 调用外部票证 ITSM 兼容系统

您可以使用以下接口配置和获取有关警报规则的信息：

* [Azure 门户](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../azure-monitor/platform/alerts-classic-portal.md)
* [命令行接口（CLI）](../azure-monitor/platform/alerts-classic-portal.md)
* [Azure 监视器 REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="alerting-metrics-available-for-managed-instance"></a>可用于托管实例的警报指标

> [!IMPORTANT]
> 警报指标仅适用于托管实例。 托管实例中的单个数据库的警报指标不可用。 数据库诊断遥测以[诊断日志](sql-database-metrics-diag-logging.md#diagnostic-telemetry-for-export-for-azure-sql-database)形式提供。 诊断日志的警报可以使用托管实例的[日志警报脚本](../azure-monitor/insights/azure-sql.md#creating-alerts-for-managed-instances)在[SQL Analytics](../azure-monitor/insights/azure-sql.md)产品内进行设置。

以下托管实例指标可用于报警配置：

| 指标 | 说明 | 度量单位 \ 可能值 |
| :--------- | --------------------- | ----------- |
| CPU 平均百分比 | 所选时间段内的 CPU 使用率的平均百分比。 | 0-100 （百分比） |
| 已读取的 IO 字节数 | 在选定时间段内读取的 IO 字节数。 | 字节 |
| 已写入的 IO 字节数 | 所选时间段内写入的 IO 字节数。 | 字节 |
| IO 请求计数 | 所选时间段内的 IO 请求计数。 | 数值 |
| 预留的存储空间 | 当前最大值。为托管实例保留的存储空间。 资源缩放操作的更改。 | MB （mb） |
| 已使用的存储空间 | 所选时间段内使用的存储空间。 按数据库和实例的存储占用情况变化。 | MB （mb） |
| 虚拟核心计数 | 已为托管实例预配 Vcore。 资源缩放操作的更改。 | 4-80 （Vcore） |

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>使用 Azure 门户创建指标的警报规则

1. 在 Azure[门户](https://portal.azure.com/)中，找到你想要监视的 SQL 托管实例，并将其选中。

2. 在 "监视" 部分选择 "**指标**" 菜单项。

   ![监视](media/sql-database-managed-instance-alerts/mi-alerting-menu-annotated.png)
  
3. 在下拉菜单上，选择想要设置警报的指标之一（示例中显示了 "使用的存储空间"）。

4. 选择聚合周期-在给定的时间段（Avg、Min 或 Max）达到的平均、最小值或最大值。 

5. 选择**新警报规则**

6. 在 "创建警报规则" 窗格中，单击 "**条件名称**" （示例中显示 "已使用存储空间"）

   ![定义条件](media/sql-database-managed-instance-alerts/mi-create-metrics-alert-smaller-annotated.png)

7. 在 "配置信号逻辑" 窗格上，定义运算符、聚合类型和阈值

   * 运算符类型选项大于、等于且小于（阈值）
   * 聚合类型选项是最小值、最大值或平均值（在聚合粒度期间内）
   * 阈值是将基于运算符和聚合条件计算的警报值
   
   ![Configure_signal_logic](media/sql-database-managed-instance-alerts/mi-configure-signal-logic-annotated.png)
   
   在屏幕截图中显示的示例中，使用值 1840876 MB，表示阈值为 1.8 TB。 当示例中的运算符设置为大于时，如果托管实例上的存储空间消耗超过 1.8 TB，则会创建警报。 请注意，存储空间度量值的阈值必须以 MB 表示。

8. 设置评估周期-聚合粒度（分钟和评估频率）。 评估频率表示警报系统将在达到阈值条件后定期检查的时间。

9. 选择 "操作组"。 操作组窗格将显示，您可以在其中选择现有的或创建新的操作。 此操作定义将在触发警报时（例如，发送电子邮件、通过电话呼叫你、执行 webhook、Azure 函数或 runbook）。

   ![Select_action_group](media/sql-database-managed-instance-alerts/mi-select-action-group-smaller-annotated.png)

   * 若要创建新的操作组，请选择 " **+ 创建操作组**"

      ![Create_action_group_alerts](media/sql-database-managed-instance-alerts/mi-create-alert-action-group-smaller-annotated.png)
   
   * 定义要如何发出警报：输入操作组名称、短名称、操作名称和选择操作类型。 操作类型定义是否通过电子邮件、短信和语音呼叫通知你，如果可能，将执行 webhook、Azure 函数、runbook 或将在兼容的系统中创建 ITSM 票证。

      ![Define_how_to_be_alerted](media/sql-database-managed-instance-alerts/mi-add-alerts-action-group-annotated.png)

10. 填写记录的警报规则详细信息，选择严重性类型。

      ![Rule_description](media/sql-database-managed-instance-alerts/mi-rule-details-complete-smaller-annotated.png)

   * 单击 "**创建警报规则**" 按钮，完成创建警报规则。

新警报规则将在几分钟内变为活动状态，并将根据你的设置触发。

## <a name="verifying-alerts"></a>验证警报

> [!NOTE]
> 若要抑制干扰警报，请参阅[使用操作规则禁止显示警报](../azure-monitor/platform/alerts-action-rules.md#suppression-of-alerts)。

设置警报规则时，请验证是否对警报触发器及其频率感到满意。 对于在此页上显示的用于设置所用存储空间的警报的示例，如果警报选项为 "电子邮件"，则可能会收到如下所示的电子邮件。

   ![alert_example](media/sql-database-managed-instance-alerts/mi-email-alert-example-smaller-annotated.png)

该电子邮件将显示警报名称、阈值的详细信息以及触发警报的原因，以帮助你验证警报并对其进行故障排除。 可以使用 "**查看 Azure 门户**" 按钮查看 Azure 门户中通过电子邮件收到的警报。 

## <a name="view-suspend-activate-modify-and-delete-existing-alert-rules"></a>查看、暂停、激活、修改和删除现有警报规则

> [!NOTE]
> 需要通过 Azure 门户仪表板中的 "警报" 菜单来管理现有警报。 无法从托管实例资源 "边栏选项卡修改现有警报。

查看、挂起、激活、修改和删除现有警报：

1. 使用 Azure 门户搜索搜索警报。 单击 "警报"。

   ![find_alerts](media/sql-database-managed-instance-alerts/mi-manage-alerts-browse-smaller-annotated.png)

   此外，还可以在 Azure 导航栏上单击 "警报" （如果已配置）。

2. 在 "警报" 窗格中，选择 "管理警报规则"。

   ![modify_alerts](media/sql-database-managed-instance-alerts/mi-manage-alert-rules-smaller-annotated.png)

   将显示现有警报的列表。 选择单个现有警报规则进行管理。 可以修改现有活动规则并将其调整为首选项。 活动规则也可以在未删除的情况下挂起。 

## <a name="next-steps"></a>后续步骤

* 了解 Azure Monitor 警报系统的详细信息，请参阅[Microsoft Azure 中的警报概述](../azure-monitor/platform/alerts-overview.md)
* 了解有关指标警报的详细信息，请参阅[了解指标警报在 Azure Monitor 中的工作原理](../azure-monitor/platform/alerts-metric-overview.md)
* 了解在警报中配置 webhook 的详细信息，请参阅[使用经典指标警报调用 webhook](../azure-monitor/platform/alerts-webhooks.md)
* 了解如何使用 PowerShell 配置和管理警报，请参阅[操作规则](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrulev2)
* 了解如何使用 API 配置和管理警报，请参阅[Azure Monitor REST API 参考](https://docs.microsoft.com/rest/api/monitor/) 
