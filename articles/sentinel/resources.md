---
title: 使用 Azure 哨兵时的有用资源*微软文档
description: 本文档为您提供了使用 Azure Sentinel 时的有用资源的列表。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 9b4c8e38-c986-4223-aa24-a71b01cb15ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/02/2019
ms.author: yelevin
ms.openlocfilehash: 2110d3319cebf693ef06deec26a29fa655e35035
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77585265"
---
# <a name="useful-resources-for-working-with-azure-sentinel"></a>用于使用 Azure 哨兵的有用资源



本文列出了可帮助您获取有关使用 Azure Sentinel 的详细信息的资源。

Azure 逻辑应用连接器：<https://docs.microsoft.com/connectors/>


## <a name="auditing-and-reporting"></a>审核和报告
Azure Sentinel 的审核日志在[Azure 活动日志](../azure-monitor/platform/platform-logs-overview.md)中维护。

可以审核以下支持的操作。

|操作名称|    资源类型|
|----|----|
|创建或更新工作簿  |微软.见解/工作簿|
|删除工作簿    |微软.见解/工作簿|
|设置工作流   |Microsoft.Logic/workflows|
|删除工作流    |Microsoft.Logic/workflows|
|创建保存的搜索    |微软.操作见解/工作区/保存的搜索|
|删除保存的搜索    |微软.操作见解/工作区/保存的搜索|
|更新警报规则 |Microsoft.SecurityInsights/alertRules|
|删除警报规则 |Microsoft.SecurityInsights/alertRules|
|更新警报规则响应操作 |微软.安全见解/警报规则/操作|
|删除警报规则响应操作 |微软.安全见解/警报规则/操作|
|更新书签   |Microsoft.SecurityInsights/bookmarks|
|删除书签   |Microsoft.SecurityInsights/bookmarks|
|更新案例   |微软.安全见解/案例|
|更新案例调查  |微软.安全见解/案例/调查|
|创建案例注释   |微软.安全见解/案例/评论|
|更新数据连接器 |Microsoft.SecurityInsights/dataConnectors|
|删除数据连接器 |Microsoft.SecurityInsights/dataConnectors|
|更新设置    |Microsoft.SecurityInsights/settings|

### <a name="view-audit-and-reporting-data-in-azure-sentinel"></a>在 Azure 哨兵中查看审核和报告数据

可以通过从 Azure 活动日志流式传输到 Azure Sentinel 来查看此数据，然后可以对这些数据执行研究和分析。

1. 连接[Azure 活动](connect-azure-activity.md)数据源。 执行此操作后，审核事件将流式传输到**日志**屏幕中称为 AzureActivity 的新表中。
2. 然后，使用 KQL 查询数据，就像使用任何其他表一样。



## <a name="vendor-documentation"></a>供应商文档

| **供应商**  | **在 Azure 哨兵中使用事件** | **链接**|
|----|----|----|
| GitHub| 用于访问社区页面| <https://github.com/Azure/Azure-Sentinel> |
| 帕洛阿尔托| 配置 CEF| <https://www.paloaltonetworks.com/documentation/misc/cef.html>|
| 复数视觉 | Kusto 查询语言课程| [https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch](https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch)|

## <a name="blogs-and-forums"></a>博客和论坛

在 Azure 哨兵[的技术社区空间](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bd-p/AzureSentinel)上发布您的问题。

查看[来自 Tech社区和](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bg-p/AzureSentinelBlog) [Microsoft Azure](https://azure.microsoft.com/blog/tag/azure-sentinel/)的 Azure Sentinel 博客文章。


## <a name="next-steps"></a>后续步骤
在本文档中，您得到了一个在使用 Azure Sentinel 时有用的资源列表。 您可以在[Microsoft Azure 安全和合规性博客上](https://blogs.msdn.com/b/azuresecurity/)找到有关 Azure 安全性和合规性的其他信息。
