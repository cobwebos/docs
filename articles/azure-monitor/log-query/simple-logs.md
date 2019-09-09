---
title: Azure Monitor 中的简单日志体验（预览版） |Microsoft Docs
description: 简单的日志体验允许您在 Azure Monitor 中创建基本查询，无需直接与 KQL 交互。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/14/2019
ms.author: bwren
ms.openlocfilehash: 76133e0f5c94b9734b44d726ee83557d9063b92c
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2019
ms.locfileid: "70814702"
---
# <a name="simple-logs-experience-in-azure-monitor-preview"></a>Azure Monitor 中的简单日志体验（预览版）
Azure Monitor 提供了使用 KQL 语言创建[日志查询](log-query-overview.md)的[丰富体验](get-started-portal.md)。 不过，您可能不需要 KQL 的全部功能，并且更喜欢基本查询要求的简化体验。 简单的日志体验允许您创建基本查询，无需直接与 KQL 交互。 你还可以使用简单日志作为 KQL 的学习工具，因为你需要执行更复杂的查询。

> [!NOTE]
> 简单日志当前仅作为 Cosmos DB 和 Key vault 的测试实现。 请通过[用户语音](https://feedback.azure.com/forums/913690-azure-monitor)与 Microsoft 共享你的体验，以帮助我们确定是否将扩展和发布此功能。


## <a name="scope"></a>范围
简单日志体验从所选资源的*AzureDiagnostics*、 *AzureMetrics*和*AzureActivity*表中检索数据。 

## <a name="using-simple-logs"></a>使用简单日志
使用将[诊断设置配置为在 Log Analytics 工作区收集日志的诊断设置](../platform/diagnostic-logs-stream-log-store.md)，导航到 Azure 订阅中的任何 Cosmos DB 或 Key Vault。 单击 "**监视**" 菜单中的 "**日志**" 以打开简单日志体验。

![菜单](media/simple-logs/menu.png)

选择**字段**和**运算符**，并指定一个**值**进行比较。 单击 **+** 并指定**和/或**以添加其他条件。

![条件](media/simple-logs/criteria.png)

单击 "**运行**" 以查看查询结果。

## <a name="view-and-edit-kql"></a>查看和编辑 KQL
选择 "**查询编辑器**"，以在完全 Log Analytics 体验中打开简单日志查询生成的 "KQL"。 

![查询编辑器](media/simple-logs/query-editor.png)

可以直接编辑 KQL，并使用 Log Analytics （例如筛选器）中的其他功能进一步优化你的结果。

![编辑 KQL](media/simple-logs/edit-kql.png)


## <a name="next-steps"></a>后续步骤

- 完成有关在[Azure 门户中使用 Log Analytics](get-started-portal.md)的教程。
- 完成[编写日志查询](get-started-portal.md)的教程。
