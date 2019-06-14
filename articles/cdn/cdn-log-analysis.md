---
title: 分析 Azure CDN 使用模式 |Microsoft Docs
description: 本文介绍适用于 Azure CDN 产品的不同类型的分析报告。
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 95e18b3c-b987-46c2-baa8-a27a029e3076
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: magattus
ms.openlocfilehash: ef713c954d6eab05259547a277db12a1e9036bcf
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60636190"
---
# <a name="analyze-azure-cdn-usage-patterns"></a>分析 Azure CDN 使用模式

为应用程序启用 CDN 后，可以监视 CDN 使用情况、检查交付内容的运行状况以及排除潜在问题。 Azure CDN 通过以下方式提供这些功能： 

## <a name="core-analytics-via-azure-diagnostic-logs"></a>通过 Azure 诊断日志进行核心分析

核心分析可用于所有定价层的 CDN 终结点。 Azure 诊断日志使核心分析导出到 Azure 存储中，事件中心或 Azure Monitor 日志。 Azure Monitor 日志提供了图形的用户配置和自定义解决方案。 有关 Azure 诊断日志的详细信息，请参阅 [Azure 诊断日志](cdn-azure-diagnostic-logs.md)。

## <a name="verizon-core-reports"></a>Verizon 核心报表

作为具有 **Verizon 提供的标准 Azure CDN** 或 **Verizon 提供的高级 Azure CDN** 配置文件的 Azure CDN 用户，可以在 Verizon 补充门户中查看 Verizon 核心报告。 可通过 Azure 门户的“管理”  选项访问 Verizon 核心报表，其中提供了各种各样的图和视图。 有关详细信息，请参阅 [Verizon 中的核心报表](cdn-analyze-usage-patterns.md)。

## <a name="verizon-custom-reports"></a>Verizon 自定义报表

作为具有 **Verizon 提供的标准 Azure CDN** 或 **Verizon 提供的高级 Azure CDN** 配置文件的 Azure CDN 用户，可以在 Verizon 补充门户中查看 Verizon 自定义报告。 可通过 Azure 门户的“管理”  选项访问 Verizon 自定义报表。 Verizon 自定义报表页显示属于 Azure CDN 配置文件的各个边缘 CName 的点击量或传输数据。 可以按照任意时间段内的 HTTP 响应代码或缓存状态将数据分组。 有关详细信息，请参阅 [Verizon 中的自定义报表](cdn-verizon-custom-reports.md)。

## <a name="azure-cdn-premium-from-verizon-reports"></a>来自 Verizon 的高级 Azure CDN 报告

使用 **Verizon 提供的 Azure CDN 高级版**，还可以访问以下报告：
   * [高级 HTTP 报告](cdn-advanced-http-reports.md)
   * [实时统计信息](cdn-real-time-stats.md)
   * [边缘节点性能](cdn-edge-performance.md)

