---
title: Azure Database for MariaDB 中的查询性能见解
description: 本文介绍 Azure 数据库中的查询性能见解功能 for MariaDB
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/12/2019
ms.openlocfilehash: be9d5c4745cb03d9d3eaa324b7191d82b9d4a14e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079413"
---
# <a name="query-performance-insight-in-azure-database-for-mariadb"></a>Azure Database for MariaDB 中的查询性能见解

**适用于：**  Azure Database for MariaDB 10.2

> [!NOTE]
> 查询性能见解处于预览状态。 Query Performance Insight 支持在 Azure 门户中即将推出，并可能尚不可用你的区域中。

Query Performance Insight 可帮助你快速确定运行时间最长的查询、它们如何随时间的推移而变化，以及哪些等待因素会影响它们。

## <a name="permissions"></a>权限

**所有者** 或 **参与者** 查询性能见解中查看的查询文本所需的权限。 **读取器** 可以查看图表和表，但不是会查询文本。

## <a name="prerequisites"></a>必备组件

对函数的查询性能见解，数据必须存在于 [Query Store](concepts-query-store.md)。

## <a name="viewing-performance-insights"></a>查看性能见解

Azure 门户中的 [Query Performance Insight](concepts-query-performance-insight.md) 视图将显示来自查询存储的关键信息的可视化效果。

在 Azure Database for MariaDB 服务器门户上，选择 **Query Performance Insight** 下 **智能性能** 菜单条形的一部分。

![Query Performance Insight 长时间运行的查询](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png)

 **长时间运行的查询** 选项卡按每次执行，平均持续时间显示前 5 个查询在 15 分钟的时间间隔中聚合。 可以通过从选择查看更多的查询 **数的查询** 下拉列表。 执行此操作时，特定查询 ID 的图表颜色可能会更改。

可以在图表中单击并拖动以缩小到特定的时间窗口。 或者，使用放大和缩小图标分别查看的较小或更大的时间段。

选择 **Wait Statistics**选项卡以查看相应的可视化效果上等待的服务器中。

查询等待统计信息视图中显示按表现出的最大等待指定的时间间隔内的查询进行分组。

![查询性能见解等待统计信息](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="next-steps"></a>后续步骤

- 详细了解如何[监视和优化](concepts-monitoring.md)Azure Database for MariaDB 中。