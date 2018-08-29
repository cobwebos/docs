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
ms.date: 08/20/2018
ms.author: magoedte; bwren
ms.component: na
ms.openlocfilehash: 386aad94461fa3f2ceafb7564342797eefa2f086
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2018
ms.locfileid: "40246431"
---
# <a name="viewing-and-analyzing-data-in-log-analytics"></a>查看和分析 Log Analytics 中的数据
Azure 门户中提供了两个选项用于分析 Log Analytics 中存储的数据，以及为即席分析创建查询。 使用这些门户创建的查询可用于其他功能，例如警报和仪表板。

## <a name="log-analytics-page-preview"></a>Log Analytics 页（预览）
通过 Log Analytics 菜单中的“日志(预览版)”打开 Log Analytics 页。 这是用于处理日志数据和创建查询的新体验。 [Azure 门户中的 Log Analytics 页入门](query-language/get-started-analytics-portal.md)中介绍了此门户及其功能。

Log Analytics 页在[日志搜索](#log-search)体验的基础上提供了以下改进。

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


### <a name="firewall-requirements"></a>防火墙要求
浏览器需要访问以下地址才能访问 Log Analytics 页和高级分析门户。  如果浏览器通过防火墙访问 Azure 门户，则必须允许访问这些地址。

| Uri | IP | 端口 |
|:---|:---|:---|
| portal.loganalytics.io | 动态 | 80,443 |
| api.loganalytics.io    | 动态 | 80,443 |
| docs.loganalytics.io   | 动态 | 80,443 |


## <a name="log-search"></a>日志搜索
通过 Log Analytics 菜单中的“日志”或者 Azure Monitor 菜单中的“Log Analytics”打开日志搜索页。 使用基本查询分析日志数据，适合使用日志搜索。 无需完全掌握查询语言，就能借助日志搜索提供的多种功能编辑查询。  有关这些功能概要，请参阅[在 Azure Log Analytics 中使用日志搜索创建日志搜索](log-analytics-log-search-log-search-portal.md)。 


![日志搜索页面](media/log-analytics-log-search-portals/log-search-portal.png)


## <a name="next-steps"></a>后续步骤

- 完成有关使用[日志搜索](log-analytics-tutorial-viewdata.md)的教程，了解如何使用查询语言创建查询
- 完成[有关使用高级分析门户的课程](query-language/get-started-analytics-portal.md)，该门户提供与 Log Analytics 页相同的体验。

