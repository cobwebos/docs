---
title: 查询性能洞察 - MySQL 的 Azure 数据库
description: 本文介绍 MySQL Azure 数据库中的查询性能洞察功能
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: a924f51fac6d43ae4a4530ac4f61f2e8b1f4e8c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537051"
---
# <a name="query-performance-insight-in-azure-database-for-mysql"></a>Azure Database for MySQL 中的 Query Performance Insight

**适用于：** MySQL 5.7 的 Azure 数据库

Query Performance Insight 可帮助你快速确定运行时间最长的查询、它们如何随时间的推移而变化，以及哪些等待因素会影响它们。

## <a name="common-scenarios"></a>常见方案

### <a name="long-running-queries"></a>长时间运行的查询

- 标识过去 X 小时内运行时间最长的查询
- 标识正在等待资源的前 N 个查询
 
### <a name="wait-statistics"></a>等待统计信息

- 了解查询的等待性质
- 了解资源等待趋势以及发生资源争用的位置

## <a name="permissions"></a>权限

查看 Query Performance Insight 中查询文本所需的“所有者”**** 或“参与者”**** 权限。 读者**** 可以查看图表和表格，但不能查看查询文本。

## <a name="prerequisites"></a>先决条件

若要运行 Query Performance Insight，数据必须存在于[查询存储](concepts-query-store.md)中。

## <a name="viewing-performance-insights"></a>查看性能见解

Azure 门户中的 [Query Performance Insight](concepts-query-performance-insight.md) 视图将显示来自查询存储的关键信息的可视化效果。

在 MySQL 服务器 Azure 数据库的门户页中，在菜单栏的**智能性能**部分下选择 **"查询性能洞察**"。

### <a name="long-running-queries"></a>长时间运行的查询

**"长时间运行查询"** 选项卡按每次执行的平均持续时间显示前 5 个查询，以 15 分钟间隔聚合。 您可以通过从"**查询数**"下拉列表中选择查看更多查询。 执行此操作时，特定查询 ID 的图表颜色可能会更改。

可以在图表中单击并拖动以缩小到特定的时间窗口。 或者，使用放大和缩小图标分别查看较小或更大的时间段。

![Query Performance Insight 长时间运行的查询](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png) 

### <a name="wait-statistics"></a>等待统计信息

> [!NOTE]
> 等待统计信息用于排除查询性能问题。 建议仅出于故障排除目的打开。 <br>如果在 Azure 门户中收到错误消息 *""Microsoft.DBforMySQL"遇到的问题;如果收到错误消息。"Microsoft.DBforMySQL"遇到的问题。无法满足请求。如果此问题仍然存在或出乎意料，请与支持人员联系，了解此信息。* 查看等待统计信息时，请使用较小的时间段。

Wait 统计信息提供执行特定查询期间发生的等待事件的视图。 在[MySQL 引擎文档中](https://go.microsoft.com/fwlink/?linkid=2098206)了解有关等待事件类型的更多内容。

选择“等待统计信息”**** 选项卡以查看服务器中等待次数的相应可视化效果。

等待统计信息视图中显示的查询按在指定时间间隔内显示最大等待的查询分组。

![查询性能洞察等待统计信息](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="next-steps"></a>后续步骤

- 了解有关 MySQL Azure 数据库中[监视和调优](concepts-monitoring.md)的详细信息。