---
title: 查看和分析 Azure Log Analytics 中的数据 | Microsoft Docs
description: 本文介绍可用于在 Azure Log Analytics 中创建和编辑日志搜索的门户。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 4677b8d5601dcafbf4760e6f185ef70393229b1a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46961053"
---
# <a name="viewing-and-analyzing-data-in-log-analytics"></a>查看和分析 Log Analytics 中的数据
Azure 门户中提供了两个选项用于分析 Log Analytics 中存储的数据，以及为即席分析创建查询。 使用这些门户创建的查询可用于其他功能，例如警报和仪表板。

## <a name="log-analytics-page"></a>Log Analytics 页
通过 Log Analytics 菜单中的“日志”打开 Log Analytics 页。 这是用于处理日志数据和创建查询的新体验。 [Azure 门户中的 Log Analytics 页入门](query-language/get-started-analytics-portal.md)中介绍了此门户及其功能。

Log Analytics 页在[日志搜索（经典版）](#log-search-classic)体验的基础上提供了以下改进。

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
> Log Analytics 页的功能与高级分析门户（Azure 门户外部的工具）相同。 高级分析门户仍然可用，但在 Azure 门户中对它的链接和其他引用即将由此新页取代。

![高级分析门户](media/log-analytics-log-search-portals/advanced-analytics-portal.png)

### <a name="resource-logs"></a>资源日志
新的 Log Analytics 体验与各种 Azure 资源（如虚拟机）集成在一起。 这意味着你可以直接通过资源的监控菜单打开 Log Analytics 页面，而无需切换到 Azure Monitor 或 Log Analytics 而导致丢失资源上下文。 所有 Azure 资源都尚未启用日志，但日志将开始出现在门户菜单中，记录不同的资源类型。

在从特定资源打开 Log Analytics 时，它会自动限定为只记录该资源的记录。   如果要编写包含其他记录的查询，则需要从 Log Analytics 或 Azure Monitor 菜单中打开该查询。

Log Analytics 的资源视图中尚不提供以下选项：

- 保存
- 设置警报
- 查询资源管理器
- 切换到不同的工作区/资源（目前未计划）


### <a name="firewall-requirements"></a>防火墙要求
浏览器需要访问以下地址才能访问 Log Analytics 页和高级分析门户。  如果浏览器通过防火墙访问 Azure 门户，则必须允许访问这些地址。

| Uri | IP | 端口 |
|:---|:---|:---|
| portal.loganalytics.io | 动态 | 80,443 |
| api.loganalytics.io    | 动态 | 80,443 |
| docs.loganalytics.io   | 动态 | 80,443 |


## <a name="log-search-classic"></a>日志搜索（经典）
通过 Log Analytics 菜单中的“日志(经典)”或者 Azure Monitor 菜单中的“Log Analytics”打开日志搜索页。 这是用于处理 Log Analytics 查询的经典页面，该页面缺少上面 [Log Analytics 页面](#log-analytics-page)列出的附加功能。



![日志搜索页面](media/log-analytics-log-search-portals/log-search-portal.png)


## <a name="next-steps"></a>后续步骤

- 完成有关使用[日志搜索](log-analytics-tutorial-viewdata.md)的教程，了解如何使用查询语言创建查询
- 完成[有关使用高级分析门户的课程](query-language/get-started-analytics-portal.md)，该门户提供与 Log Analytics 页相同的体验。

