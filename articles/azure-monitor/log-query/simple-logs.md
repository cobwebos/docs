---
title: Azure Monitor（预览版）中的简单日志体验 | Microsoft Docs
description: 简单日志体验允许你在 Azure Monitor 中创建基本查询，无需直接与 KQL 交互。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/12/2019
ms.openlocfilehash: 7bdb0ae813600f53df44ee1a61a4cbbb56bb0bfe
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87067496"
---
# <a name="simple-logs-experience-in-azure-monitor-preview"></a>Azure Monitor（预览版）中的简单日志体验
Azure Monitor 提供了使用 KQL 语言创建[日志查询](log-query-overview.md)的[丰富体验](get-started-portal.md)。 但是，你可能不需要 KQL 的全部功能，并且更喜欢符合基本查询要求的简化体验。 简单日志体验允许你创建基本查询，无需直接与 KQL 交互。 当你需要更复杂的查询时，也可以将“简单日志”用作 KQL 的学习工具。

> [!NOTE]
> 简单日志当前仅作为 Cosmos DB 和 Key Vault 的测试来实现。 请通过 [User Voice](https://feedback.azure.com/forums/913690-azure-monitor)（用户之声）与 Microsoft 分享你的体验，帮助我们确定是否要扩展和发布此功能。


## <a name="scope"></a>作用域
简单日志体验从 AzureDiagnostics、AzureMetrics和 AzureActivity 表中检索所选资源的数据。 

## <a name="using-simple-logs"></a>使用简单日志
使用[配置为在 Log Analytics 工作区中收集日志的诊断设置](../platform/resource-logs.md#send-to-azure-storage)导航到 Azure 订阅中的任何 Cosmos DB 或 Key Vault。 单击“监视”菜单中的“日志”，打开简单日志体验。

![菜单](media/simple-logs/menu.png)

选择字段和**运算符**并指定要比较的值。 单击“+”并指定“And”/“Or”以添加其他条件。

![条件](media/simple-logs/criteria.png)

单击“运行”以查看查询结果。

## <a name="view-and-edit-kql"></a>查看和编辑 KQL
在完整的 Log Analytics 体验中，选择“查询编辑器”以打开简单日志查询生成的 KQL。 

![查询编辑器](media/simple-logs/query-editor.png)

可以直接编辑 KQL，并使用 Log Analytics 中的其他功能（例如筛选器）进一步细化结果。

![编辑 KQL](media/simple-logs/edit-kql.png)


## <a name="next-steps"></a>后续步骤

- 完成有关[在 Azure 门户中使用 Log Analytics](get-started-portal.md) 的教程。
- 完成有关[编写日志查询](get-started-portal.md)的教程。
