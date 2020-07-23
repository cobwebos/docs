---
title: 设置警报和通知（Azure 门户）
description: 使用 Azure 门户可以创建警报，在满足指定条件时，可以触发通知或自动化。
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
ms.date: 05/04/2020
ms.openlocfilehash: d03c1cad9692058c47dd643be9770284b207bb74
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85553494"
---
# <a name="create-alerts-for-azure-sql-database-and-azure-synapse-analytics-using-the-azure-portal"></a>使用 Azure 门户创建 Azure SQL 数据库和 Azure Synapse Analytics 的警报
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


## <a name="overview"></a>概述

本文介绍如何使用 Azure 门户为 Azure SQL 数据库和 Azure Synapse Analytics （以前称为 Azure SQL 数据仓库）中的数据库设置警报。 当某些指标（例如数据库大小或 CPU 使用率）达到阈值时，警报可以向你发送电子邮件或调用 Webhook。

> [!NOTE]
> 有关 Azure SQL 托管实例特定说明，请参阅为[AZURE sql 托管实例创建警报](../managed-instance/alerts-create.md)。

可以根据监控指标或事件接收 Azure 服务的警报。

* **指标值** - 当指定指标的值在任一方向越过了指定的阈值时警报将触发。 也就是说，当条件先是满足以及之后不再满足该条件时，警报都会触发。
* **活动日志事件** - 警报可以在发生 *每个* 事件时都触发，也可以仅在发生特定数量的事件时触发。

可以配置警报以在其触发时执行以下操作：

* 向服务管理员和共同管理员发送电子邮件通知
* 将电子邮件发送到指定的其他电子邮件地址。
* 调用 Webhook

可以使用以下工具配置和获取关于警报的信息：

* [Azure 门户](../../azure-monitor/platform/alerts-classic-portal.md)
* [PowerShell](../../azure-monitor/platform/alerts-classic-portal.md)
* [命令行接口（CLI）](../../azure-monitor/platform/alerts-classic-portal.md)
* [Azure 监视器 REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>使用 Azure 门户创建指标的警报规则

1. 在此[门户](https://portal.azure.com/)，查找想要监视的资源并选中它。
2. 在“监视”部分中选择“警报”。**** 对于不同的资源，文本和图标可能会略有不同。  

   ![监视](./media/alerts-insights-configure-portal/Alerts.png)
  
3. 选择“新建警报规则”按钮以打开“创建规则”页。**** ****
  ![创建规则](./media/alerts-insights-configure-portal/create-rule.png)

4. 在“条件”部分中，单击“添加”。**** ****
  ![定义条件](./media/alerts-insights-configure-portal/create-rule.png)
5. 在“配置信号逻辑”页中，选择一个信号。 
  ![选择信号](./media/alerts-insights-configure-portal/select-signal.png)
6. 在选择某个信号（例如“CPU 百分比”  ）后，将会显示“配置信号逻辑”  页。
  ![配置信号逻辑](./media/alerts-insights-configure-portal/configure-signal-logic.png)
7. 在此页面上，配置该阈值类型、运算符、聚合类型、阈值、聚合粒度和评估频率。 然后单击“完成”。****
8. 在“创建规则”中，选择一个现有的“操作组”或创建一个新组。**** **** 操作组用于定义在发生警报条件时要执行的操作。
  ![定义操作组](./media/alerts-insights-configure-portal/action-group.png)

9. 定义规则的名称，提供可选说明，为规则选择严重性级别，选择是否在创建规则时启用规则，然后单击“创建规则警报”**** 以创建指标规则警报。

在 10 分钟后，警报将如前所述激活并触发。

## <a name="next-steps"></a>后续步骤

* 了解[在警报中配置 Webhook](../../azure-monitor/platform/alerts-webhooks.md)的详细信息。
