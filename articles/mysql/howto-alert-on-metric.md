---
title: 在 Azure 门户中为 Azure Database for MySQL 配置指标警报
description: 本文介绍了如何通过 Azure 门户配置和访问针对 Azure Database for MySQL 的指标警报。
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 3accc31f433e6db40c7d1de2b56dfbd4180b4933
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35265160"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-mysql"></a>使用 Azure 门户设置针对 Azure Database for MySQL 指标的警报 

本文介绍了如何使用 Azure 门户设置 Azure Database for MySQL 警报。 你可以收到基于 Azure 服务的监视指标的警报。

当指定的指标值越过了分配的阈值时，就会触发此警报。 首次满足条件时，以及之后不再满足条件时，都会触发此警报。 

可配置警报，使警报触发时执行以下操作：
* 向服务管理员和共同管理员发送电子邮件通知
* 将电子邮件发送到指定的其他电子邮件地址。
* 调用 Webhook

可使用以下项配置并获取预警规则相关信息：
* [Azure 门户](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../monitoring-and-diagnostics/insights-alerts-powershell.md)
* [命令行接口 (CLI)](../monitoring-and-diagnostics/insights-alerts-command-line-interface.md)
* [Azure 监视器 REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>通过 Azure 门户针对指标创建警报规则
1. 在 [Azure 门户](https://portal.azure.com/)中，选择要监视的 Azure Database for MySQL 服务器。

2. 在侧栏的“监视”部分下，选择“警报规则”，如下图所示：

   ![选择“警报规则”](./media/howto-alert-on-metric/1-alert-rules.png)

3. 选择“添加指标警报”（“+”图标）。 

4. 随即打开如下所示的“添加规则”页面。  填写所需信息：

   ![添加指标警报窗体](./media/howto-alert-on-metric/2-add-rule-form.png)

   | 设置 | 说明  |
   |---------|---------|
   | 名称 | 为警报规则提供一个名称。 此值将在警报通知电子邮件中发送。 |
   | 说明 | 提供警报规则的简短说明。 此值将在警报通知电子邮件中发送。 |
   | 警报对象 | 对于此类警报，请选择“指标”。 |
   | 订阅 | 此字段预填充了托管着 Azure Database for MySQL 的订阅。 |
   | 资源组 | 此字段预填充了 Azure Database for MySQL 的资源组。 |
   | 资源 | 此字段预填充了 Azure Database for MySQL 的名称。 |
   | 指标 | 选择要针对其发出警报的指标。 例如，存储百分比。 |
   | 条件 | 选择要与指标进行比较的条件。 例如，大于。 |
   | 阈值 | 指标的阈值，例如 85（百分比）。 |
   | 周期 | 触发警报前必须满足指标规则的时间段。 例如，过去 30 分钟。 |

   基于示例，警报查找超过 85% 的时间超过 30 分钟的存储百分比。 当平均存储百分比超过 85% 的时间超过 30 分钟时，触发该警报。 第一次触发结束后，当平均存储百分比低于 85% 的时间超过 30 分钟时，将再次触发。

5. 为警报规则选择所需通知方式。 

   如果希望在警报触发时向订阅管理员和共同管理员发送电子邮件，请选择“电子邮件所有者、参与者和读者”。

   如果希望在警报触发时有其他电子邮件收到通知，请将其添加到“其他管理员电子邮件”字段。 用分号隔开多个电子邮件 - *email@contoso.com;email2@contoso.com*

   （可选）如果希望在警报触发时调用有效的 URI，请将其放入“Webhook”字段。

6. 选择“确定”以创建警报。

   几分钟后，警报将处于活动状态，并按前面所述进行触发。

## <a name="manage-your-alerts"></a>管理警报
创建警报后，可选择它并执行以下操作：

* 查看图，了解与此警报相关的指标阈值和前一天实际值。
* 编辑或删除预警规则。
* 如果要暂时停止或恢复接收通知，可禁用或启用警报。


## <a name="next-steps"></a>后续步骤
* 了解[在警报中配置 Webhook](../monitoring-and-diagnostics/insights-webhooks-alerts.md)的详细信息。
* 获取[指标集合概述](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)以确保服务可用且响应迅速。
