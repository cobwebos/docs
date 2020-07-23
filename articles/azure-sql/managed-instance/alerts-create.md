---
title: 为托管实例设置警报和通知（Azure 门户）
description: 使用 Azure 门户创建 SQL 托管实例警报，该警报可在满足指定条件时触发通知或自动化操作。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 05/04/2020
ms.openlocfilehash: ae139dd65242be9456f3498c494e1a7c5a29402f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84695688"
---
# <a name="create-alerts-for-azure-sql-managed-instance-using-the-azure-portal"></a>使用 Azure 门户为 Azure SQL 托管实例创建警报
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

本文说明如何使用 Azure 门户为 Azure SQL 托管实例数据库中的数据库设置警报。 警报可以向你发送电子邮件，呼叫 web 挂钩，执行 Azure 功能，runbook，调用外部 ITSM 兼容票证系统，在手机上呼叫你，或在某个指标（例如实例存储大小或 CPU 使用率）达到预定义阈值时发送短信。 本文还提供了设置警报期限的最佳做法。


## <a name="overview"></a>概述

可以根据监控指标或事件接收 Azure 服务的警报。

* **指标值** - 当指定指标的值在任一方向越过了指定的阈值时警报将触发。 也就是说，当条件先是满足以及之后不再满足该条件时，警报都会触发。

可以配置警报以在其触发时执行以下操作：

* 向服务管理员和共同管理员发送电子邮件通知
* 将电子邮件发送到指定的其他电子邮件地址。
* 使用语音提示拨打电话号码
* 向组发送电话号码
* 调用 Webhook
* 调用 Azure Function
* 调用 Azure runbook
* 调用外部票证 ITSM 兼容系统

您可以使用[Azure 门户、PowerShell 或 Azure CLI](../../azure-monitor/platform/alerts-classic-portal.md)或[Azure Monitor REST API](/rest/api/monitor/alertrules)来配置和获取有关警报规则的信息。 

## <a name="alerting-metrics-available-for-managed-instance"></a>适用于托管实例的警报指标

> [!IMPORTANT]
> 警报指标仅适用于托管实例。 警报指标不适用于托管实例中的单个数据库。 另一方面，数据库诊断遥测可按[诊断日志](../database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md#diagnostic-telemetry-for-export)的形式提供。 诊断日志的警报可以使用托管实例的[日志警报脚本](../../azure-monitor/insights/azure-sql.md#creating-alerts-for-sql-managed-instance)在[SQL Analytics](../../azure-monitor/insights/azure-sql.md)产品内进行设置。

以下托管实例指标可用于警报配置：

| 指标 | 说明 | 度量单位 \ 可能值 |
| :--------- | --------------------- | ----------- |
| CPU 平均百分比 | 选定时间段内 CPU 使用率的平均百分比。 | 0-100（百分比） |
| 已读取的 IO 字节数 | 选定时间段内已读取的 IO 字节数。 | 字节 |
| 已写入的 IO 字节数 | 选定时间段内已写入的 IO 字节。 | 字节 |
| IO 请求计数 | 选定时间段内的 IO 请求计数。 | 数值 |
| 预留的存储空间 | 当前为托管实例预留的最大存储空间。 随资源缩放操作所发生的变化。 | MB（兆字节） |
| 已使用的存储空间 | 选定时间段内已使用的存储空间。 随数据库和实例的存储消耗所发生的变化。 | MB（兆字节） |
| 虚拟核心计数 | 为托管实例预配的 vCore 数量。 随资源缩放操作所发生的变化。 | 4-80 (vCore) |

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>使用 Azure 门户创建指标的警报规则

1. 在 Azure[门户](https://portal.azure.com/)中，找到你想要监视的托管实例，并将其选中。

2. 在监视部分中选择“指标”菜单项****。

   ![监视](./media/alerts-create/mi-alerting-menu-annotated.png)
  
3. 在下拉菜单上，选择要设置警报的指标之一（示例中显示了已使用的存储空间）。

4. 选择聚合时间段 - 在给定时间段内达到的平均值、最小值或最大值（Avg、Min 或 Max）。 

5. 选择“新建警报规则”****

6. 在“创建警报规则”窗格中，单击“条件名称”（示例中显示了已使用的存储空间）****

   ![定义条件](./media/alerts-create/mi-create-metrics-alert-smaller-annotated.png)

7. 在“配置信号逻辑”窗格中，定义运算符、聚合类型和阈值

   * 运算符类型选项有“大于”、“等于”和“小于”（阈值）
   * 聚合类型选项有“最小值”、“最大值”或“平均值”（在聚合粒度期间内）
   * 阈值是根据运算符和聚合条件计算的警报值
   
   ![Configure_signal_logic](./media/alerts-create/mi-configure-signal-logic-annotated.png)
   
   在屏幕截图所示的示例中，值 1840876 MB 表示阈值为 1.8 TB。 由于示例中的操作符设置为“大于”，所以如果托管实例上的存储空间消耗量超过 1.8 TB，就会创建警报。 请注意，存储空间指标的阈值必须以 MB 表示。

8. 设置评估期 - 聚合粒度（以分钟为单位）和评估频率。 评估频率指示警报系统定期检查阈值条件是否满足的检查时间。

9. 选择操作组。 随即显示“操作组”窗格，通过该窗格可以选择现有操作或创建新操作。 此操作定义将在触发警报时（例如，发送电子邮件、通过电话呼叫你、执行 webhook、Azure 函数或 runbook）。

   ![Select_action_group](./media/alerts-create/mi-select-action-group-smaller-annotated.png)

   * 若要创建新的操作组，请选择“+ 创建操作组”****

      ![Create_action_group_alerts](./media/alerts-create/mi-create-alert-action-group-smaller-annotated.png)
   
   * 定义警报方式：输入操作组名称、短名称、操作名称并选择操作类型。 操作类型定义是否通过电子邮件、短信和语音呼叫通知你，如果可能，将执行 webhook、Azure 函数、runbook 或将在兼容的系统中创建 ITSM 票证。

      ![Define_how_to_be_alerted](./media/alerts-create/mi-add-alerts-action-group-annotated.png)

10. 填写用于记录的警报规则详细信息，选择严重性类型。

      ![Rule_description](./media/alerts-create/mi-rule-details-complete-smaller-annotated.png)

   * 单击“创建警报规则”按钮，完成警报规则的创建****。

新的警报规则将在几分钟内激活，并根据设置触发。

## <a name="verifying-alerts"></a>验证警报

> [!NOTE]
> 若要抑制干扰警报，请参阅[使用操作规则抑制警报](../../azure-monitor/platform/alerts-action-rules.md#suppression-of-alerts)。

设置警报规则后，请验证警报触发情况及其频率是否符合需求。 对于此页上显示的关于针对已使用的存储空间设置警报的示例，如果警报选项是电子邮件，则可能会收到电子邮件，如下所示。

   ![alert_example](./media/alerts-create/mi-email-alert-example-smaller-annotated.png)

电子邮件显示警报名称、阈值的详细信息以及触发警报的原因，可帮助你验证警报并对警报进行故障排除。 可以使用“在 Azure 门户中查看”按钮查看通过 Azure 门户中的电子邮件接收的警报****。 

## <a name="view-suspend-activate-modify-and-delete-existing-alert-rules"></a>查看、暂停、激活、修改和删除现有警报规则

> [!NOTE]
> 需要通过 Azure 门户面板的“警报”菜单管理现有警报。 无法从托管实例资源边栏选项卡修改现有警报。

若要查看、暂停、激活、修改和删除现有警报：

1. 使用 Azure 门户搜索搜索警报。 单击“警报”。

   ![find_alerts](./media/alerts-create/mi-manage-alerts-browse-smaller-annotated.png)

   或者，也可以单击 Azure 导航栏上的警报（如果已配置）。

2. 在“警报”窗格中，选择“管理警报规则”。

   ![modify_alerts](./media/alerts-create/mi-manage-alert-rules-smaller-annotated.png)

   随即显示现有警报的列表。 选择单个现有警报规则进行管理。 可以根据你的偏好修改和调整现有的活动规则。 也可以不删除活动规则而是将其挂起。 

## <a name="next-steps"></a>后续步骤

* 了解 Azure Monitor 警报系统的详细信息，请参阅 [Microsoft Azure 中的警报概述](../../azure-monitor/platform/alerts-overview.md)
* 了解有关指标警报的详细信息，请参阅[了解指标警报在 Azure Monitor 中的工作原理](../../azure-monitor/platform/alerts-metric-overview.md)
* 了解如何在警报中配置 webhook 的详细信息，请参阅[使用经典指标警报调用 webhook](../../azure-monitor/platform/alerts-webhooks.md)
* 了解如何使用 PowerShell 配置和管理警报，请参阅[操作规则](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrulev2)
* 了解如何使用 API 配置和管理警报，请参阅 [Azure Monitor REST API 参考](https://docs.microsoft.com/rest/api/monitor/) 
