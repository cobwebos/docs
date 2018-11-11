---
title: Azure Monitor Log Analytics 语言参考 | Microsoft Docs
description: Log Analytics 使用的数据资源管理器查询语言的参考信息。 包括特定于 Log Analytics 的附加元素以及 Log Analytics 查询中不支持的元素。
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
ms.date: 10/29/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: e6d097749dae49cf6f1d710bcf01cf99dcd98a4c
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2018
ms.locfileid: "50243892"
---
# <a name="log-analytics-query-language-differences"></a>Log Analytics 查询语言差异

虽然 [Log Analytics](../log-analytics-queries.md) 是基于 [Azure 数据资源管理器](/azure//data-explorer)构建的并使用[相同的查询语言](/azure/kusto/query)，但该语言的版本确实有一些差异。 本文介绍了在数据资源管理器使用的语言版本与 Log Analytics 查询使用的版本之间存在差异的元素。

## <a name="data-explorer-elements-not-supported-in-log-analytics"></a>Log Analytics 中不支持的数据资源管理器元素
以下各部分介绍了 Log Analytics 不支持的数据资源管理器查询语言的元素。

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
下列运算符支持特定的 Log Analytics 功能并且在 Log Analytics 外部不可用。

* [app()](app-expression.md)
* [workspace()](workspace-expression.md)

## <a name="next-steps"></a>后续步骤

<<<<<<< HEAD:articles/log-analytics/query-language/data-explorer-difference.md
- 获取[用于编写 Log Analytics 查询的不同资源](kusto.md)的参考信息。
- 访问完整的[数据资源管理器查询语言参考文档](/azure/kusto/query/)。
=======
- 阅读有关 [Log Analytics](../log-analytics-queries.md) 中的查询的内容。
- 完成有关编写 [Log Analytics 查询](/log-analytics/query-language/get-started-queries.md)的一节课程。
- 访问完整的 [Kusto 参考文档](/azure/kusto/query/)。
>>>>>>> 4bccab5ecb17c887658a4d2ed1bab6b22bf29ffd:articles/log-analytics/query-language/kusto.md
