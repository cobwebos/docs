---
title: 托管实例（Azure 门户）的设置警报和通知
description: 使用 Azure 门户创建 SQL 托管实例警报，在满足指定条件时，可以触发通知或自动化。
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 04/02/2020
ms.openlocfilehash: a332627d149a36ba5d5beb2626023e58a221f0d6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80639193"
---
# <a name="create-alerts-for-azure-sql-managed-instance-using-the-azure-portal"></a>使用 Azure 门户为 Azure SQL 托管实例创建警报

## <a name="overview"></a>概述

本文说明如何使用 Azure 门户为 Azure SQL 托管实例数据库中的数据库设置警报。 警报可以向你发送电子邮件，或在某个指标（例如实例存储大小或 CPU 使用率）达到阈值时调用 web 挂钩。 本文还提供了有关设置警报期限的最佳做法。

可以根据监控指标或事件接收 Azure 服务的警报。

* **指标值** - 指定指标的值超过在任一方向分配的阈值时，将触发警报。 也就是说，当首次满足条件时，以及之后不再满足条件时，都会触发此警报。
* **活动日志事件** - 每个** 事件发生，或仅当出现一定次数的事件时，触发警报。

可配置警报，使警报触发时执行以下操作：

* 向服务管理员和共同管理员发送电子邮件通知
* 将电子邮件发送到指定的其他电子邮件地址。
* 调用 Webhook

可使用以下项配置并获取有关警报规则的信息

* [Azure 门户](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../azure-monitor/platform/alerts-classic-portal.md)
* [命令行接口（CLI）](../azure-monitor/platform/alerts-classic-portal.md)
* [Azure 监视器 REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>使用 Azure 门户创建指标的警报规则

1. 在此[门户](https://portal.azure.com/)，查找想要监视的资源并选中它。
2. 在 "监视" 部分选择**警报**。 对于不同的资源，文本和图标可能会略有不同。  

   ![监视](media/sql-database-insights-alerts-portal/Alerts.png)
  
3. 选择 "**新建警报规则**" 按钮，打开 "**创建规则**" 页。
   ![创建规则](media/sql-database-insights-alerts-portal/create-rule.png)

4. 在 "**条件**" 部分中，单击 "**添加**"。
   ![定义条件](media/sql-database-insights-alerts-portal/create-rule.png)
5. 在 "**配置信号逻辑**" 页上，选择一个信号。
   ![选择信号](media/sql-database-insights-alerts-portal/select-signal.png)
6. 选择信号（如**CPU 百分比**）后，将显示 "**配置信号逻辑**" 页。
   ![配置信号逻辑](media/sql-database-insights-alerts-portal/configure-signal-logic.png)
7. 在此页上，配置该阈值类型、运算符、聚合类型、阈值、聚合粒度和计算频率。 然后单击 "**完成**"。
8. 在 "**创建规则**" 中，选择一个现有**操作组**或创建一个新组。 操作组可用于定义在发生警报条件时要执行的操作。
  ![定义操作组](media/sql-database-insights-alerts-portal/action-group.png)

9. 定义规则的名称，提供可选说明，为规则选择严重性级别，选择是否在创建规则时启用规则，然后单击 "**创建规则警报**" 以创建指标规则警报。

在10分钟内，警报处于活动状态，并按前面所述的方式触发。

## <a name="next-steps"></a>后续步骤

* 了解[在警报中配置 Webhook](../azure-monitor/platform/alerts-webhooks.md)的详细信息。
