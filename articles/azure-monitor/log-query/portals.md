---
title: 查看和分析 Azure Monitor 中的日志数据 | Microsoft Docs
description: 本文介绍了如何使用 Azure 门户中的 Log Analytics 在 Azure Monitor 中创建和编辑日志查询。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: bwren
ms.openlocfilehash: 0e5b9b43e528b37fd994f9131f145abadb33c53b
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2019
ms.locfileid: "59259025"
---
# <a name="viewing-and-analyzing-log-data-in-azure-monitor"></a>查看和分析 Azure Monitor 中的日志数据
Log Analytics 是用于在 Azure Monitor 中处理日志数据以及创建查询的主要体验。 从 **Azure Monitor** 菜单中的“日志”打开 Log Analytics。 可以在 [Azure 门户中的 Log Analytics 入门](get-started-portal.md)中了解此门户及其功能。

Log Analytics 提供了用于处理日志查询的以下功能。

* 多个选项卡 - 创建单独的选项卡来处理多个查询。
* 丰富的可视化效果 – 各种图表选项。
* 改进了 Intellisense 和语言自动补全。
* 语法突出显示 - 改进了查询的易读性。 
* 查询资源管理器 – 访问已保存的查询和函数。
* 架构视图 - 查看数据的结构以帮助编写查询。
* 共享 – 创建查询的链接，或者将查询固定到任何共享的 Azure 仪表板。
* 智能分析 - 在图表中识别高峰，快速分析原因。
* 列选择 – 对查询结果中的列进行排序和分组。

> [!NOTE]
> Log Analytics 的功能与高级分析门户（Azure 门户外的一个外部工具）相同。 高级分析门户仍然可用，但在 Azure 门户中对它的链接和其他引用即将由此新页取代。

![Log Analytics](media/portals/log-analytics.png)

## <a name="resource-logs"></a>资源日志
Log Analytics 集成了各种 Azure 资源（例如虚拟机）。 这意味着你可以直接通过资源的监控菜单打开 Log Analytics，而无需切换到 Azure Monitor 以致丢失资源上下文。 所有 Azure 资源都尚未启用日志，但日志将开始出现在门户菜单中，记录不同的资源类型。

在从特定资源打开 Log Analytics 时，它会自动限定为只记录该资源的记录。   如果要编写包含其他记录的查询，则需要从 Azure Monitor 菜单中打开该查询。

Log Analytics 的资源视图中尚不提供以下选项：

- 保存
- 设置警报
- 查询资源管理器
- 切换到不同的工作区/资源（目前未计划）


## <a name="firewall-requirements"></a>防火墙要求
浏览器需要访问以下地址才能访问 Log Analytics。  如果浏览器通过防火墙访问 Azure 门户，则必须允许访问这些地址。

| Uri | IP | 端口 |
|:---|:---|:---|
| portal.loganalytics.io | 动态 | 80,443 |
| api.loganalytics.io    | 动态 | 80,443 |
| docs.loganalytics.io   | 动态 | 80,443 |


## <a name="next-steps"></a>后续步骤

- 完成[有关使用 Log Analytics 的教程](../../azure-monitor/log-query/get-started-portal.md)。
- 完成[有关使用日志搜索的教程](../../azure-monitor/learn/tutorial-viewdata.md)。

