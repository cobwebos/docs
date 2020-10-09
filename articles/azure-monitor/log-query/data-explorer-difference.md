---
title: Azure Monitor 日志查询语言差异 | Microsoft Docs
description: Azure Monitor 使用的 Kusto 查询语言的参考信息。 包括特定于 Azure Monitor 的附加元素以及 Azure Monitor 日志查询中不支持的元素。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/01/2020
ms.openlocfilehash: 265179909c8ae4a6fa630b835bc9993f042d6460
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "80585699"
---
# <a name="azure-monitor-log-query-language-differences"></a>Azure Monitor 日志查询语言差异

虽然 [Azure Monitor 中的日志](log-query-overview.md)是基于 [Azure 数据资源管理器](/azure/data-explorer)构建的并使用相同的 [Kusto 查询语言](/azure/kusto/query)，但该语言的版本确实有一些差异。 本文介绍了在数据资源管理器使用的语言版本与 Azure Monitor 日志查询使用的版本之间存在差异的元素。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="kql-elements-not-supported-in-azure-monitor"></a>Azure Monitor 中不支持的 KQL 元素
以下各部分介绍了 Azure Monitor 不支持的 Kusto 查询语言的元素。

### <a name="statements-not-supported-in-azure-monitor"></a>Azure Monitor 中不支持的语句

* [Alias](/azure/kusto/query/aliasstatement)
* [Query parameters](/azure/kusto/query/queryparametersstatement)

### <a name="functions-not-supported-in-azure-monitor"></a>Azure Monitor 中不支持的函数

* [cluster()](/azure/kusto/query/clusterfunction)
* [cursor_after()](/azure/kusto/query/cursorafterfunction)
* [cursor_before_or_at()](/azure/kusto/query/cursorbeforeoratfunction)
* [cursor_current()、current_cursor()](/azure/kusto/query/cursorcurrent)
* [database()](/azure/kusto/query/databasefunction)
* [current_principal()](/azure/kusto/query/current-principalfunction)
* [extent_id()](/azure/kusto/query/extentidfunction)
* [extent_tags()](/azure/kusto/query/extenttagsfunction)

### <a name="operators-not-supported-in-azure-monitor"></a>Azure Monitor 中不支持的运算符

* [跨群集加入](/azure/kusto/query/joincrosscluster)

### <a name="plugins-not-supported-in-azure-monitor"></a>Azure Monitor 中不支持的插件

* [Python 插件](/azure/kusto/query/pythonplugin)
* [sql_request 插件](/azure/kusto/query/sqlrequestplugin)


## <a name="additional-operators-in-azure-monitor"></a>Azure Monitor 中的附加运算符
下列运算符支持特定的 Azure Monitor 功能并且在 Azure Monitor 外部不可用。

* [app()](app-expression.md)
* [workspace()](workspace-expression.md)

## <a name="next-steps"></a>后续步骤

- 获取[用于编写 Azure Monitor 日志查询的不同资源](query-language.md)的参考信息。
- 访问完整的 [Kusto 查询语言的参考文档](/azure/kusto/query/)。
