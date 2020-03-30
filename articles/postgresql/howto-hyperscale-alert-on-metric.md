---
title: 配置警报 - 超大规模（Citus） - 用于后格雷SQL的 Azure 数据库
description: 本文介绍如何为后格雷SQL - 超大规模 （Citus） 的 Azure 数据库配置和访问指标警报
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 3/16/2020
ms.openlocfilehash: 80c061f72ce827df8f8354a5881c032c6f874fe1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063144"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql---hyperscale-citus"></a>使用 Azure 门户为后格雷SQL - 超大规模 （Citus） 的 Azure 数据库的指标设置警报

本文介绍如何使用 Azure 门户设置 Azure Database for PostgreSQL 警报。 您可以根据 Azure 服务的[监视指标](concepts-hyperscale-monitoring.md)接收警报。

我们将设置警报，以便当指定指标的值超过阈值时触发。 当首次遇到条件时，警报将触发，并在之后继续触发。

可配置警报，使警报触发时执行以下操作：
* 向服务管理员和共同管理员发送电子邮件通知。
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

5. 在 **"条件"** 部分中，选择 **"添加**"。

6. 从要发出警报的信号列表中选择一个指标。 在此示例中，选择“存储百分比”。
   
   ![选择指标](./media/howto-hyperscale-alert-on-metric/6-configure-signal-logic.png)

7. 配置警报逻辑：

    * **操作员**（例如 "大于"）
    * **阈值**（例如 85%）
    * 在警报触发之前必须满足指标规则的**聚合粒度**量（例如。 "过去 30 分钟"）
    * 和评估**频率**（例如 "1 分钟"）
   
   完成后选择“完成”****。

   ![选择指标](./media/howto-hyperscale-alert-on-metric/7-set-threshold-time.png)

8. 在“操作组”部分中，选择“新建”创建新组以接收有关警报的通知********。

9. 使用名称、短名称、订阅和资源组填写“添加操作组”表单。

    ![操作组](./media/howto-hyperscale-alert-on-metric/9-add-action-group.png)

10. 配置“电子邮件/短信/推送/语音”操作类型****。
    
    选择"电子邮件 Azure 资源管理器角色"以向订阅所有者、参与者和读者发送通知。
   
    完成后选择“确定”****。

    ![操作组](./media/howto-hyperscale-alert-on-metric/10-action-group-type.png)

11. 指定预警规则名称、说明和严重性。

    ![操作组](./media/howto-hyperscale-alert-on-metric/11-name-description-severity.png) 

12. 选择“创建警报规则”可以创建警报****。

    几分钟后，警报将处于活动状态，并按前面所述进行触发。

### <a name="managing-alerts"></a>管理警报

创建警报后，您可以选择它并执行以下操作：

* 查看图，了解与此警报相关的指标阈值和前一天实际值。
* 编辑或删除预警规则********。
* 如果要暂时停止或恢复接收通知，可禁用或启用警报********。

## <a name="suggested-alerts"></a>建议警报

### <a name="disk-space"></a>磁盘空间

监视和警报对于每个生产超大规模 （Citus） 服务器组都很重要。 基础 PostgreSQL 数据库需要可用磁盘空间才能正常运行。 如果磁盘已满，数据库服务器节点将脱机并拒绝启动，直到空间可用。 此时，它需要 Microsoft 支持请求来修复这种情况。

我们建议在每个服务器组的每个节点上设置磁盘空间警报，即使对于非生产使用情况也是如此。 磁盘空间使用警报提供干预和保持节点正常运行所需的提前警告。 为获得最佳效果，请尝试以 75%、85% 和 95% 的使用量进行一系列警报。 选择的百分比取决于数据引入速度，因为快速的数据引入会更快地填满磁盘。

当磁盘接近其空间限制时，请尝试这些技术以获得更多的可用空间：

* 查看数据保留策略。 如果可行，将较旧的数据移动到冷存储。
* 请考虑[将节点添加到](howto-hyperscale-scaling.md#add-worker-nodes)服务器组并重新平衡分片。 重新平衡将数据分布在更多计算机上。
* 考虑[增加](howto-hyperscale-scaling.md#increase-or-decrease-vcores-on-nodes)辅助节点的容量。 每个工作人员最多只能有 2 个 TiB 的存储空间。 但是，在调整节点大小之前，应尝试添加节点，因为添加节点完成得更快。

### <a name="cpu-usage"></a>CPU 使用率

监视 CPU 使用率对于建立性能基准非常有用。 例如，您可能会注意到 CPU 使用率通常约为 40-60%。 如果 CPU 使用率突然开始徘徊在 95% 左右，您可以识别异常。 CPU 使用率可能反映有机增长，但它也可能显示杂散查询。 创建 CPU 警报时，请设置较长的聚合粒度以捕获长时间增加并忽略瞬时峰值。

## <a name="next-steps"></a>后续步骤
* 了解[在警报中配置 Webhook](../azure-monitor/platform/alerts-webhooks.md)的详细信息。
* 获取[指标集合概述](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)以确保服务可用且响应迅速。
