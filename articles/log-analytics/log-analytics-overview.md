---
title: "什么是 Azure 中的 Log Analytics？ | Microsoft Docs"
description: "Log Analytics 是 Azure 中的服务，可帮助收集和分析云和本地环境中资源生成的操作数据。  本文提供了 Log Analytics 不同组件的简要概述和指向详细内容的链接。"
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: bd90b460-bacf-4345-ae31-26e155beac0e
ms.service: log-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/24/2018
ms.author: bwren
ms.openlocfilehash: a95528f5bd259a36ea96c7bc0660ca082c09d6e6
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/25/2018
---
# <a name="what-is-log-analytics"></a>什么是 Log Analytics？
Log Analytics 是 Azure 中的一个服务，用于监视云和本地环境，使其保持较高的可用性和性能。  它可以收集云和本地环境中的资源生成的数据以及其他监视工具的数据，针对多个源提供分析。  本文简要介绍 Log Analytics 提供的价值及其工作原理的概述，并提供更详细内容的链接，帮助你深入了解此产品。

## <a name="is-log-analytics-for-you"></a>Log Analytics 是否适合你？
如果 Azure 环境中当前尚未部署监视机制，应该先使用 [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md) 来收集和分析 Azure 资源的监视数据。  Log Analytics 可[从 Azure Monitor 收集数据](log-analytics-azure-storage.md)，将这些数据与其他数据相关联并提供更多分析信息。

若要监视本地环境，或者部署了使用 Azure Monitor 或 System Center Operations Manager 等服务的现有监视机制，则 Log Analytics 可以大大提高其价值。  它能直接将代理中的数据以及其他此类工具中的数据收集到单个存储库。  Log Analytics 中的分析工具（例如日志搜索、视图和解决方案）可针对收集的所有数据运行，提供整个环境的集中分析信息。


## <a name="using-log-analytics"></a>使用 Log Analytics
可以通过在任意浏览器中运行的 Azure 门户访问 Log Analytics。在这些门户中，可以访问配置设置和多个工具来分析和处理收集的数据。  可以在该门户中使用[日志搜索](log-analytics-log-searches.md)，在此过程中，可以构造查询（分析收集的数据）、[仪表板](log-analytics-dashboards.md)（可以使用最有价值的搜索的图形视图自定义）和[解决方案](log-analytics-add-solutions.md)（提供其他功能和分析工具）。

下图显示“概览”屏幕，该屏幕显示了工作区中安装的[解决方案](#add-functionality-with-management-solutions)的摘要信息。  单击任一磁贴可以进一步深入到该解决方案的数据。

![OMS 门户](media/log-analytics-overview/portal.png)

Log Analytics 提供用于快速检索和整合存储库中数据的查询语言。  可以创建并保存[日志搜索](log-analytics-log-searches.md)，以便直接在门户中分析数据，或者自动运行日志搜索，在查询结果指示重要状况时创建警告。

![日志搜索](media/log-analytics-overview/log-search.png)

可以将数据导出到 [Power BI](log-analytics-powerbi.md) 或 Excel 等工具，以在 Log Analytics 外部分析数据。  还可以使用[日志搜索 API](log-analytics-log-search-api.md) 生成利用 Log Analytics 数据的自定义解决方案或与其他系统集成。

## <a name="add-functionality-with-management-solutions"></a>使用管理解决方案添加功能
[管理解决方案](log-analytics-add-solutions.md)可将功能添加到 Log Analytics，为 Log Analytics 提供更多数据和分析工具。  这些解决方案还可以定义要收集的新记录类型，并使用日志搜索或通过仪表板中的解决方案提供的其他用户界面进行分析。  以下示例截图显示了[更改跟踪解决方案](log-analytics-change-tracking.md)

![更改跟踪解决方案](media/log-analytics-overview/change-tracking.png)

解决方案适用于各种功能，我们将持续添加更多的解决方案。  可以轻松浏览可用的解决方案，并将其从 Azure Marketplace [添加到工作区](log-analytics-add-solutions.md)。  许多解决方案可自动部署并立即开始运行，还有一些解决方案可能需要经过适度的配置。

![解决方案库](media/log-analytics-overview/solution-gallery.png)

## <a name="log-analytics-components"></a>Log Analytics 组件
Log Analytics 的中心是托管在 Azure 云中的已收集数据的存储库。  通过配置数据源和向订阅添加解决方案，收集连接的源中的数据。  数据源和解决方案将分别创建具有自身属性集的不同记录类型，但是仍可在对存储库的查询中同时对它们进行分析。  这允许使用相同的工具和方法来处理不同资源收集的各种数据。

![Log Analytics 组件](media/log-analytics-overview/overview.png)

连接的源是生成 Log Analytics 收集的数据的计算机和其他资源。  其中可包括直接连接的 [Windows](log-analytics-windows-agent.md) 和 [Linux](log-analytics-linux-agents.md) 计算机上安装的代理或 [连接的 System Center Operations Manager 管理组](log-analytics-om-agents.md) 中的代理。  对于 Azure 资源，Log Analytics 将从 [Azure Monitor 和 Azure 诊断](log-analytics-azure-storage.md)收集数据。

[数据源](log-analytics-data-sources.md) 是从各个连接的源中收集的各种数据。  除 [IIS 日志](log-analytics-data-sources-iis-logs.md)和[自定义文本日志](log-analytics-data-sources-custom-logs.md)等源外，还包括 [Windows](log-analytics-data-sources-windows-events.md) 和 Linux 代理中的[事件](log-analytics-data-sources-windows-events.md)和[性能数据](log-analytics-data-sources-performance-counters.md)。  可以配置要收集的各个数据源，配置会自动传递到各个连接的源。

如果有自定义方面的要求，可以使用 [HTTP 数据收集器 API](log-analytics-data-collector-api.md) 将数据从 REST API 客户端写入存储库。

## <a name="log-analytics-architecture"></a>Log Analytics 体系结构
由于中心组件在 Azure 云中托管，因此 Log Analytics 的部署要求很少。  除允许关联和分析收集的数据的服务之外，其中还包括存储库。  门户可以通过任意浏览器进行访问，因此对客户端软件没有要求。

必须在 [Windows](log-analytics-windows-agent.md) 和 [Linux](log-analytics-linux-agents.md) 计算机上安装代理，但是已属于[连接的 System Center Operations Manager 管理组](log-analytics-om-agents.md)成员的计算机无需安装其他代理。  Operations Manager 代理将继续与管理服务器进行通信，该服务器会将通信数据转发到 Log Analytics。  但是一些解决方案需要代理才能直接与 Log Analytics 进行通信。  各解决方案的文档将指定解决方案的通信要求。

[注册 Log Analytics](log-analytics-get-started.md) 时，会创建一个工作区。  可将工作区视为独特的 Log Analytics 环境，其中包含自身的数据存储库、数据源和解决方案。 可以在订阅中创建多个工作区来支持生产和测试等多种环境。

![Log Analytics 体系结构](media/log-analytics-overview/architecture.png)

## <a name="next-steps"></a>后续步骤
* [注册免费的 Log Analytics 帐户](log-analytics-get-started.md) 以在自己的环境中进行测试。
* 查看各种可用的[数据源](log-analytics-data-sources.md)，以便将数据收集到 Log Analytics 中。
* [浏览解决方案库中的可用解决方案](log-analytics-add-solutions.md) 以向 Log Analytics 中添加功能。

