---
title: 配置警报-超大规模（Citus）-Azure Database for PostgreSQL
description: 本文介绍如何配置和访问 Azure Database for PostgreSQL-超大规模（Citus）的指标警报
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 3/16/2020
ms.openlocfilehash: 8bf887b8d86131e0b358056fc1744a8d144be3fc
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82584109"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql---hyperscale-citus"></a>使用 Azure 门户为 Azure Database for PostgreSQL 超大规模（Citus）的指标设置警报

本文介绍如何使用 Azure 门户设置 Azure Database for PostgreSQL 警报。 你可以收到基于 Azure 服务的[监视指标](concepts-hyperscale-monitoring.md)的警报。

我们将设置一个警报，以便在指定指标的值超过阈值时触发。 当首次满足条件时，将触发警报，并在以后继续触发。

可配置警报，使警报触发时执行以下操作：
* 向服务管理员和共同发送电子邮件通知。
* 将电子邮件发送到指定的其他电子邮件地址。
* 调用 Webhook。

可使用以下项配置并获取预警规则相关信息：
* [Azure 门户](../azure-monitor/platform/alerts-metric.md#create-with-azure-portal)
* [Azure CLI](../azure-monitor/platform/alerts-metric.md#with-azure-cli)
* [Azure 监视器 REST API](https://docs.microsoft.com/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>通过 Azure 门户针对指标创建警报规则
1. 在 [Azure 门户](https://portal.azure.com/)中，选择要监视的 Azure Database for PostgreSQL 服务器。

2. 在边栏的“监视”部分，选择“警报”，如下所示********：

   ![选择“警报规则”](./media/howto-hyperscale-alert-on-metric/2-alert-rules.png)

3. 选择“新建警报规则”（+ 图标）。****

4. 随即打开“创建规则”页面，如下所示****。 填写所需信息：

   ![添加指标警报窗体](./media/howto-hyperscale-alert-on-metric/4-add-rule-form.png)

5. 在 "**条件**" 部分中，选择 "**添加**"。

6. 从要发出警报的信号列表中选择一个指标。 在此示例中，选择“存储百分比”。
   
   ![选择指标](./media/howto-hyperscale-alert-on-metric/6-configure-signal-logic.png)

7. 配置警报逻辑：

    * **运算符**（例如 "大于"）
    * **阈值**（例如 85%）
    * **聚合粒度**在触发警报前必须满足指标规则的时间长度（例如 "过去30分钟"）
    * 和**评估频率**（例如 "1 分钟"）
   
   完成后选择“完成”****。

   ![选择指标](./media/howto-hyperscale-alert-on-metric/7-set-threshold-time.png)

8. 在“操作组”部分中，选择“新建”创建新组以接收有关警报的通知********。

9. 使用名称、短名称、订阅和资源组填写“添加操作组”表单。

    ![操作组](./media/howto-hyperscale-alert-on-metric/9-add-action-group.png)

10. 配置“电子邮件/短信/推送/语音”操作类型****。
    
    选择 "电子邮件 Azure 资源管理器角色"，将通知发送到订阅所有者、参与者和读者。
   
    完成后选择“确定”****。

    ![操作组](./media/howto-hyperscale-alert-on-metric/10-action-group-type.png)

11. 指定预警规则名称、说明和严重性。

    ![操作组](./media/howto-hyperscale-alert-on-metric/11-name-description-severity.png) 

12. 选择“创建警报规则”可以创建警报****。

    几分钟后，警报将处于活动状态，并按前面所述进行触发。

### <a name="managing-alerts"></a>管理警报

创建警报后，可选择它并执行以下操作：

* 查看图，了解与此警报相关的指标阈值和前一天实际值。
* 编辑或删除预警规则********。
* 如果要暂时停止或恢复接收通知，可禁用或启用警报********。

## <a name="suggested-alerts"></a>建议的警报

### <a name="disk-space"></a>磁盘空间

对于每个生产超大规模（Citus）服务器组，监视和警报都非常重要。 底层 PostgreSQL 数据库要求可用磁盘空间才能正常运行。 如果磁盘已满，则数据库服务器节点将处于脱机状态，并拒绝启动，直到空间可用。 此时，它需要 Microsoft 支持请求来解决这种情况。

建议在每个服务器组中的每个节点上设置磁盘空间警报，即使对于非生产使用也是如此。 磁盘空间使用情况警报提供干预并使节点保持正常运行所需的高级警告。 为获得最佳结果，请尝试在75%、85% 和95% 使用的一系列警报。 选择的百分比取决于数据引入速度，因为快速数据引入会使磁盘更快地填满。

磁盘接近其空间限制时，可以尝试以下方法获取更多的可用空间：

* 查看数据保留策略。 如果可行，请将较旧的数据移到冷存储。
* 考虑向服务器组[添加节点](howto-hyperscale-scaling.md#add-worker-nodes)并重新平衡分片。 重新平衡跨多台计算机分散数据。
* 考虑[增加](howto-hyperscale-scaling.md#increase-or-decrease-vcores-on-nodes)辅助角色节点的容量。 每个工作线程最多可以有 2 TiB 的存储空间。 但是，应在调整节点大小时尝试添加节点，因为添加节点的速度更快。

### <a name="cpu-usage"></a>CPU 使用率

监视 CPU 使用率有助于建立性能基线。 例如，你可能会注意到，CPU 使用率通常约为40-60%。 如果 CPU 使用率突然开始停留在95%，则可以识别异常情况。 CPU 使用率可能反映了随机增长，但它也可能会暴露出不需要的查询。 创建 CPU 警报时，请设置一个长聚合粒度来捕获长时间的增加，并忽略暂时峰值。

## <a name="next-steps"></a>后续步骤
* 了解[在警报中配置 Webhook](../azure-monitor/platform/alerts-webhooks.md)的详细信息。
* 获取[指标集合概述](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)以确保服务可用且响应迅速。
