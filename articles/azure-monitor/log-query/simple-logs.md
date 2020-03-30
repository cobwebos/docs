---
title: Azure 监视器（预览）中的简单日志体验 |微软文档
description: 通过简单日志体验，您可以在 Azure 监视器中创建基本查询，而无需直接与 KQL 交互。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/12/2019
ms.openlocfilehash: 59bcb42edaf7d46498a3514b4f1c919c6e8cc0c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77660251"
---
# <a name="simple-logs-experience-in-azure-monitor-preview"></a>Azure 监视器中的简单日志体验（预览）
Azure 监视器为使用 KQL 语言创建[日志查询](log-query-overview.md)提供了[丰富的体验](get-started-portal.md)。 不过，您可能不需要 KQL 的全部功能，并且更喜欢简化基本查询要求的体验。 通过简单日志体验，您可以创建基本查询，而无需直接与 KQL 交互。 您还可以使用简单日志作为 KQL 的学习工具，因为您需要更复杂的查询。

> [!NOTE]
> 简单日志当前仅作为参数数据库和密钥保管库的测试实现。 请通过[用户语音](https://feedback.azure.com/forums/913690-azure-monitor)与 Microsoft 分享您的体验，以帮助我们确定是否会扩展和发布此功能。


## <a name="scope"></a>范围
简单日志体验从所选资源的 Azure*诊断**、AzureMetrics*和*Azure 活动*表检索数据。 

## <a name="using-simple-logs"></a>使用简单日志
导航到 Azure 订阅中的任何 Cosmos DB 或密钥保管库，并[配置诊断设置以在日志分析工作区中收集日志](../platform/resource-logs-collect-storage.md)。 单击 **"监视"** 菜单中的**日志**以打开简单日志体验。

![菜单](media/simple-logs/menu.png)

选择**字段**和**运算符**并指定**用于比较的值**。 单击**+** 并指定**和/或**以添加其他条件。

![条件](media/simple-logs/criteria.png)

单击 **"运行"** 以查看查询结果。

## <a name="view-and-edit-kql"></a>查看和编辑 KQL
选择**查询编辑器**以在完整的日志分析体验中打开由简单日志查询生成的 KQL。 

![查询编辑器](media/simple-logs/query-editor.png)

您可以直接编辑 KQL，并使用日志分析中的其他功能（如筛选器）来进一步优化结果。

![编辑 KQL](media/simple-logs/edit-kql.png)


## <a name="next-steps"></a>后续步骤

- 在 Azure[门户中完成有关使用日志分析的](get-started-portal.md)教程。
- 完成有关[编写日志查询的](get-started-portal.md)教程。
