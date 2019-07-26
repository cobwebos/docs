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
ms.openlocfilehash: ed840352096f1a1739bc8f655be42096456d3c33
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405669"
---
| 限制 | 描述 |
|:---|:---|
| 查询语言 | Azure Monitor 使用与 Azure 数据资源管理器相同的[Kusto 查询语言](/azure/kusto/query/)。 请参阅 Azure Monitor 中不支持的 KQL 语言元素[Azure Monitor 日志查询语言差异](../articles/azure-monitor/log-query/data-explorer-difference.md)。 |
| Azure 区域 | 当数据跨越多个 Azure 区域中的工作区 Log Analytics 时, 日志查询可能会遇到过多的开销。 有关详细信息, 请参阅[查询限制](../articles/azure-monitor/log-query/scope.md#query-limits)。 |
