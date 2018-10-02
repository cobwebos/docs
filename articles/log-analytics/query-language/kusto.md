---
title: Azure Monitor Log Analytics 语言参考 | Microsoft Docs
description: Log Analytics 使用的 Kusto 语言的参考信息。 包括特定于 Log Analytics 的附加元素以及 Log Analytics 查询中不支持的元素。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 5173790436a29fa9947346d711da1a2ddb32bf62
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2018
ms.locfileid: "47451062"
---
# <a name="log-analytics-query-language-reference"></a>Log Analytics 查询语言参考
[Log Analytics 查询](../log-analytics-queries.md)使用 [Azure 数据资源管理器](/azure/data-explorer/)使用的同一查询语言和引擎。 可以从以下位置访问语言参考以及有关该语言的其他详细信息：[Kusto 语言参考](/azure/kusto/query)



## <a name="kusto-elements-not-support-in-log-analytics"></a>Log Analytics 中不支持的 Kusto 元素
虽然 Log Analytics 查询使用了 Kusto 实现，但它不支持某些 Kusto 元素，如以下各部分中所述。

### <a name="statements-not-supported-in-log-analytics"></a>Log Analytics 中不支持的语句

* [Alias](/kusto/query/aliasstatement)
* [Query parameters](/azure/kusto/query/queryparametersstatement)

### <a name="functions-not-supported-in-log-analytics"></a>Log Analytics 中不支持的函数

* [cluster()](/azure/kusto/query/clusterfunction)
* [cursor_after()](/azure/kusto/query/cursorafterfunction)
* [cursor_before_or_at()](/azure/kusto/query/cursorbeforeoratfunction)
* [cursor_current()、current_cursor()](/azure/kusto/query/cursorcurrent)
* [database()](/azure/kusto/query/databasefunction)
* [current_principal()](/azure/kusto/query/current-principalfunction)
* [extent_id()](/azure/kusto/query/extentidfunction)
* [extent_tags()](/azure/kusto/query/extenttagsfunction)

### <a name="operators-not-supported-in-log-analytics"></a>Log Analytics 中不支持的运算符

* [跨群集加入](/azure/kusto/query/joincrosscluster)
* [externaldata 运算符](/azure/kusto/query/externaldata-operator)

### <a name="plugins-not-supported-in-log-analytics"></a>Log Analytics 中不支持的插件

* [sql_request 插件](/azure/kusto/query/sqlrequestplugin)


## <a name="additional-operators-in-log-analytics"></a>Log Analytics 中的附加运算符
为了支持特定的 Log Analytics 功能，提供了在 Log Analytics 外部不可用的以下附加 Kusto 运算符。 

* [app()](app-expression.md)
* [workspace()](workspace-expression.md)

## <a name="next-steps"></a>后续步骤

- 阅读有关 [Log Analytics](../log-analytics-queries.md) 中的查询的内容。
- 完成有关编写 [Log Analytics 查询](/log-analytics/query-language/get-started-queries.md)的一节课程。
- 访问完整的 [Kusto 参考文档](/azure/kusto/query/)。