---
title: Azure Database for MySQL 中的 Query Performance Insight
description: 本文介绍中的 Query Performance Insight 功能 Azure Database for MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 38299a8ffd72722c2d2c1dda9227b7fbf76c0386
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2019
ms.locfileid: "73603112"
---
# <a name="query-performance-insight-in-azure-database-for-mysql"></a>Azure Database for MySQL 中的 Query Performance Insight

**适用于：** Azure Database for MySQL 5。7

Query Performance Insight 可帮助你快速确定运行时间最长的查询、它们如何随时间的推移而变化，以及哪些等待因素会影响它们。

## <a name="common-scenarios"></a>常见方案

### <a name="long-running-queries"></a>长时间运行的查询

- 标识过去 X 小时内运行时间最长的查询
- 标识正在等待资源的前 N 个查询
 
### <a name="wait-statistics"></a>等待统计信息

- 了解查询的等待性质
- 了解资源等待趋势以及发生资源争用的位置

## <a name="permissions"></a>权限

查看 Query Performance Insight 中查询文本所需的“所有者”或“参与者”权限。 读者可以查看图表和表格，但不能查看查询文本。

## <a name="prerequisites"></a>先决条件

若要运行 Query Performance Insight，数据必须存在于[查询存储](concepts-query-store.md)中。

## <a name="viewing-performance-insights"></a>查看性能见解

Azure 门户中的 [Query Performance Insight](concepts-query-performance-insight.md) 视图将显示来自查询存储的关键信息的可视化效果。

在 Azure Database for MySQL 服务器的 "门户" 页上，选择菜单栏的 "**智能性能**" 部分下的 " **Query Performance Insight** "。

### <a name="long-running-queries"></a>长时间运行的查询

"**长时间运行的查询**" 选项卡按每次执行的平均持续时间显示前5个查询，按15分钟的间隔进行聚合。 您可以通过从 "**查询数**" 下拉箭头中进行选择来查看更多查询。 执行此操作时，特定查询 ID 的图表颜色可能会更改。

可以在图表中单击并拖动以缩小到特定的时间窗口。 或者，使用 "放大" 和 "缩小" 图标分别查看较小或更大的时间段。

![Query Performance Insight 长时间运行的查询](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png) 

### <a name="wait-statistics"></a>等待统计信息

> [!NOTE]
> 等待统计信息用于排查查询性能问题。 建议仅出于故障排除目的启用此功能。 <br>如果收到错误消息，请在 Azure 门户 ""*DBforMySQL 遇到的问题 ";无法完成请求。如果此问题仍然存在或意外出现，请与支持人员联系以了解此信息。* " 查看等待统计信息时，请使用较短的时间段。

"等待统计信息" 提供执行特定查询期间发生的等待事件的视图。 详细了解[MySQL 引擎文档](https://go.microsoft.com/fwlink/?linkid=2098206)中的等待事件类型。

选择“等待统计信息”选项卡以查看服务器中等待次数的相应可视化效果。

"等待统计信息" 视图中显示的查询按指定时间间隔内显示最大等待的查询进行分组。

![Query Performance Insight 等待统计信息](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="next-steps"></a>后续步骤

- 详细了解 Azure Database for MySQL 中的[监视和优化](concepts-monitoring.md)。