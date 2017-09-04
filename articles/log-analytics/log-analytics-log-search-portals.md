---
title: "用于在 Azure Log Analytics 中创建和编辑日志查询的门户 | Microsoft Docs"
description: "本文介绍可用于在 Azure Log Analytics 中创建和编辑日志搜索的门户。"
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
ms.sourcegitcommit: 7456da29aa07372156f2b9c08ab83626dab7cc45
ms.openlocfilehash: 29dfa31d38f85574f84ed351bc5c26224b1a7e16
ms.contentlocale: zh-cn
ms.lasthandoff: 08/28/2017

---
# <a name="portals-for-creating-and-editing-log-queries-in-azure-log-analytics"></a>用于在 Azure Log Analytics 中创建和编辑日志查询的门户

> [!NOTE]
> 本文介绍 Azure Log Analytics 中使用新查询语言的门户。  若要了解有关新语言的详细信息和工作区升级过程，请参阅[将 Azure Log Analytics 工作区升级到新日志搜索](log-analytics-log-search-upgrade.md)。  
>
> 如果尚未将工作区升级到新查询语言，应参考[在 Log Analytics 中使用日志搜索查找数据](log-analytics-log-searches.md)，了解有关日志搜索门户当前版本的信息。

在 Log Analytics 中多处使用日志搜索来从工作区检索数据。  除交互处理返回数据外，实际创建和编辑查询有如下所示两种方法。  

## <a name="log-search-portal"></a>日志搜索门户
日志搜索门户可从 Azure 门户或 OMS 门户进行访问。  它适合创建可通过单行创建的基本查询。  可在不启动外部门户的情况下使用日志搜索门户，并可将其用于执行日志搜索的多种函数，例如创建警报规则、创建计算机组和导出查询结果。  

日志搜索门户提供的多种功能可在无需完全掌握查询语言的情况下编辑查询。  有关这些功能概要，请参阅[在 Azure Log Analytics 中使用日志搜索门户创建日志搜索](log-analytics-log-search-log-search-portal.md)。


![日志搜索门户](media/log-analytics-log-search-portals/log-search-portal.png)

## <a name="advanced-analytics-portal"></a>高级分析门户
高级分析门户是一个提供日志搜索门户中不可用的高级功能的专用门户。  这些功能包括在多行上编辑查询、选择性地执行代码、上下文相关 Intellisense 和智能分析。  高级分析门户最适合设计保存为日志搜索或复制粘贴到其他 Log Analytics 元素的复杂查询。  通过日志搜索中的链接可启动高级分析门户。

![高级分析门户](media/log-analytics-log-search-portals/advanced-analytics-portal.png)


这些高级功能使得高级分析门户通常作为创建和编辑查询的主要工具。  确定查询按预期方式运行后，可将其粘贴复制到日志搜索门户或视图设计器等其他地方。  由于高级分析门户支持多行查询，因此从此门户中复制查询需要注意以下事项。

- 将查询粘贴复制到另一位置前必须将其中的注释删除。  通过在行前添加两个斜杠 (//) 可对行进行注释。  将多行查询粘贴到单行时会删除换行符。  如果包含注释，则第一个注释后所有的字符会被视为此注释的一部分。


## <a name="next-steps"></a>后续步骤

- 完成有关使用[日志搜索门户](log-analytics-log-search-log-search-portal.md)或[高级分析门户](https://go.microsoft.com/fwlink/?linkid=856587)创建查询的教程。
- 查看使用新查询语言的[查询编写教程](https://go.microsoft.com/fwlink/?linkid=856078)。

