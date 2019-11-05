---
title: 配置 Azure Database for PostgreSQL-超大规模（Citus）的指标警报
description: 本文介绍如何配置和访问 Azure Database for PostgreSQL-超大规模（Citus）的指标警报
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: d5f45df16ac294f50e99cc7e05ab6eba43c0ae85
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73516014"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql---hyperscale-citus"></a>使用 Azure 门户为 Azure Database for PostgreSQL 超大规模（Citus）的指标设置警报

本文介绍如何使用 Azure 门户设置 Azure Database for PostgreSQL 警报。 可根据监视指标接收 Azure 服务的警报。

我们将设置一个警报，以便在指定指标的值超过阈值时触发。 当首次满足条件时，将触发警报，并在以后继续触发。

可配置警报，使警报触发时执行以下操作：
* 向服务管理员和共同发送电子邮件通知。
* 将电子邮件发送到指定的其他电子邮件。
* 调用 Webhook。

可使用以下项配置并获取有关警报规则的信息：
* [Azure 门户](../azure-monitor/platform/alerts-metric.md#create-with-azure-portal)
* [Azure CLI](../azure-monitor/platform/alerts-metric.md#with-azure-cli)
* [Azure 监视器 REST API](https://docs.microsoft.com/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>通过 Azure 门户针对指标创建预警规则
1. 在 [Azure 门户](https://portal.azure.com/)中，选择要监视的 Azure Database for PostgreSQL 服务器。

2. 在边栏的“监视”部分，选择“警报”，如下所示：

   ![选择“警报规则”](./media/howto-hyperscale-alert-on-metric/2-alert-rules.png)

3. 选择“新建警报规则”（+ 图标）。

4. 随即打开“创建规则”页面，如下所示。 填写所需信息：

   ![添加指标警报窗体](./media/howto-hyperscale-alert-on-metric/4-add-rule-form.png)

5. 在 "**条件**" 部分中，选择 "**添加**"。

6. 从要发出警报的信号列表中选择一个指标。 在此示例中，选择“存储百分比”。
   
   ![选择指标](./media/howto-hyperscale-alert-on-metric/6-configure-signal-logic.png)

7. 配置警报逻辑：

    * **运算符**（例如 "大于"）
    * **阈值**（例如 85%）
    * **聚合粒度**在触发警报前必须满足指标规则的时间长度（例如 "过去30分钟"）
    * 和**评估频率**（例如 "1 分钟"）
   
   完成后选择“完成”。

   ![选择指标](./media/howto-hyperscale-alert-on-metric/7-set-threshold-time.png)

8. 在“操作组”部分中，选择“新建”创建新组以接收有关警报的通知。

9. 使用名称、短名称、订阅和资源组填写“添加操作组”表单。

    ![操作组](./media/howto-hyperscale-alert-on-metric/9-add-action-group.png)

10. 配置“电子邮件/短信/推送/语音”操作类型。
    
    选择 "电子邮件 Azure 资源管理器角色"，将通知发送到订阅所有者、参与者和读者。
   
    完成后选择“确定”。

    ![操作组](./media/howto-hyperscale-alert-on-metric/10-action-group-type.png)

11. 指定预警规则名称、说明和严重性。

    ![操作组](./media/howto-hyperscale-alert-on-metric/11-name-description-severity.png) 

12. 选择“创建警报规则”可以创建警报。

    几分钟后，警报将处于活动状态，并按前面所述进行触发。

## <a name="manage-your-alerts"></a>管理警报

创建警报后，可选择它并执行以下操作：

* 查看图，了解与此警报相关的指标阈值和前一天实际值。
* 编辑或删除预警规则。
* 如果要暂时停止或恢复接收通知，可禁用或启用警报。

## <a name="next-steps"></a>后续步骤
* 了解[在警报中配置 Webhook](../azure-monitor/platform/alerts-webhooks.md)的详细信息。
* [大致了解指标收集](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)以确保服务可用且响应迅速。
