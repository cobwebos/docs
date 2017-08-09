---
title: "Log Analytics 新日志搜索常见问题解答 | Microsoft Docs"
description: "本文提供有关将 Log Analytics 升级到新的查询语言的常见问题解答。"
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: 137671152878e6e1ee5ba398dd5267feefc435b7
ms.openlocfilehash: b4031e5ec5ab38863e1e74f5b6ebb117f23a327e
ms.contentlocale: zh-cn
ms.lasthandoff: 07/28/2017

---

# <a name="log-analytics-new-log-search-frequently-asked-questions"></a>Log Analytics 新日志搜索常见问题解答

本文提供有关[将 Log Analytics 升级到新的查询语言](log-analytics-log-search-upgrade.md)的常见问题解答。



## <a name="upgrade-process"></a>升级过程

### <a name="i-have-several-workspaces-can-i-upgrade-all-workspaces-at-the-same-time"></a>我有多个工作区。 是否可以同时升级所有工作区？  
答：不可以。  升级适用于单次单个工作区。 当前没有方法可以一次性升级多个工作区。 请注意，所升级的工作区的其他用户也会受影响。  

### <a name="will-existing-log-data-collected-in-my-workspace-be-modified-if-i-upgrade"></a>如果升级，是否将修改工作区中收集的现有日志数据？  
不能。 可用于工作区搜索的日志数据不受升级影响。 已保存的搜索、警报和视图将自动转换为新的搜索语言。  

### <a name="what-happens-if-i-dont-upgrade-my-workspace"></a>如果不升级工作区，会发生什么情况？  
旧的日志搜索将在几个月后弃用。 系统会自动升级此时间前仍未升级的工作区。

### <a name="i-didnt-choose-to-upgrade-but-my-workspace-has-been-upgraded-anyway-what-happened"></a>未选择升级，但工作区仍进行了升级！ 发生了什么情况？  
此工作区的另一个管理员可能升级了该工作区。 请注意，正式发布新语言后，系统会自动升级所有工作区。  

### <a name="i-have-upgraded-by-mistake-and-now-need-to-cancel-it-and-restore-everything-back-what-should-i-do"></a>不小心进行了升级，现在需要将其取消并还原所有内容。 我该怎么办？  
没问题。  由于升级之前创建了工作区快照，所以可将其还原。 请记住，升级后会丢失已保存的搜索、警报或视图。  若要还原工作区环境，请按照[升级后是否可以还原？](log-analytics-log-search-upgrade.md#can-i-go-back-after-i-upgrade)中的步骤进行操作。



## <a name="log-searches"></a>日志搜索

### <a name="i-have-saved-searches-outside-of-my-upgraded-workspace-can-i-convert-them-to-the-new-search-language-automatically"></a>已将搜索保存到升级的工作区之外。 是否可以自动将这些搜索转换到新的搜索语言？
可以在“日志搜索”页使用语言转换器工具转换每个搜索。  目前无法在不升级工作区的情况下自动转换多个搜索。



## <a name="alerts"></a>警报

### <a name="i-have-a-lot-of-alert-rules-do-i-need-to-create-them-again-in-the-new-language-after-i-upgrade"></a>我有大量的警报规则。 升级后，是否需要采用新的语言再次创建这些规则？  
不需要，在升级过程中，警报规则会自动转换为新的搜索语言。  

## <a name="power-bi"></a>Power BI

### <a name="does-anything-change-with-powerbi-integration"></a>PowerBI 集成会发生改变吗？
是的。  升级工作区后，Log Analytics 数据导出到 Power BI 的过程将停止。  将禁用升级前创建的任何现有计划。  升级后，Azure Log Analytics 会将同一平台用作 Application Insights，将 Log Analytics 查询导出到 Power BI 的过程与[将 Application Insights 查询导出到 Power BI 的过程](../application-insights/app-insights-export-power-bi.md#export-analytics-queries)相同。



## <a name="next-steps"></a>后续步骤

- 详细了解[将工作区升级到新的 Log Analytics 查询语言](log-analytics-log-search-upgrade.md)。

