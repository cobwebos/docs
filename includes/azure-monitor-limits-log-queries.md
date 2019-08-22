---
title: include 文件
description: include 文件
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 07/22/2019
ms.author: bwren
ms.custom: include file
ms.openlocfilehash: f007cf0d46d6cbee39a950b9784bbc9bde702ff5
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657729"
---
| 限制 | 描述 |
|:---|:---|
| 查询语言 | Azure Monitor 使用与 Azure 数据资源管理器相同的[Kusto 查询语言](/azure/kusto/query/)。 请参阅 Azure Monitor 中不支持的 KQL 语言元素[Azure Monitor 日志查询语言差异](../articles/azure-monitor/log-query/data-explorer-difference.md)。 |
| Azure 区域 | 当数据跨越多个 Azure 区域中的工作区 Log Analytics 时, 日志查询可能会遇到过多的开销。 有关详细信息, 请参阅[查询限制](../articles/azure-monitor/log-query/scope.md#query-limits)。 |
| 跨资源查询 | 单个查询中 Application Insights 资源和 Log Analytics 工作区的最大数目限制为100。<br>视图设计器不支持跨资源查询。<br>新的 scheduledQueryRules API 支持日志警报中的跨资源查询。<br>有关详细信息, 请参阅[跨资源查询限制](../articles/azure-monitor/log-query/cross-workspace-query.md#cross-resource-query-limits)。 |