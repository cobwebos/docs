---
title: 监视 Azure 容器概述 | Microsoft Docs
description: 本文概述 Azure 提供的用于监视 Azure 容器以快速了解群集运行状况和可用性的不同方法。
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2018
ms.author: magoedte
ms.openlocfilehash: 0d511c1f6dfd482e5754741da15b2852ee77c11e
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2018
---
# <a name="overview-of-monitoring-containers-in-azure"></a>监视 Azure 容器的概述
借助 Azure，可以有效地监视和管理运行 Kubernetes 或 Docker 的 Azure 容器上部署的工作负载。 请务必了解如何执行包含多个微服务应用程序的容器，以大规模提供可靠的服务并支持监视计划。 本文简要概述 Azure 的管理和监视功能，有助于了解每个功能，从而帮助你根据需求选择合适的功能。

使用 [Azure Monitor 容器运行状况](monitoring-container-health.md)，可以一目了然地查看 Linux 容器基础结构的性能和运行状况并快速调查问题。 遥测数据存储在 Log Analytics 工作区中并集成在 Azure 门户中，在 Azure 门户中，可以借助仪表板浏览、筛选和分隔聚合数据以监视负载、性能和运行状况。  

对于在托管的 Azure Kubernetes 服务外运行的容器，Log Analytics [Windows 和 Docker 容器解决方案](../log-analytics/log-analytics-containers.md)有助于查看和管理 Windows 和 Docker 容器主机。 从 Log Analytics 工作区，可以在环境中查看节点和容器的清单详细信息、性能和事件。 可以查看详细的审核信息，显示用于容器的命令，并且可以通过查看和搜索集中式日志对容器进行故障排除，而无需远程访问 Docker 或 Windows 主机。

若要实现整体或端到端的应用程序监视，应该借助 Azure Monitor 或 Log Analytics 监视任意依赖关系（无论是 Azure 还是本地资源）。  使用 Application Insights 的平台和应用程序级别都应包含应用层，以添加额外的运行状况感知层。 在平台级别，有适用于 [Kubernetes]( https://github.com/Microsoft/ApplicationInsights-Kubernetes)、[Docker](https://hub.docker.com/r/microsoft/applicationinsights/) 和 [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-appinsights) 的 Application Insights SDK。 对于微服务应用程序，有对 [Java](../application-insights/app-insights-java-get-started.md)、[Node.js](../application-insights/app-insights-nodejs-quick-start.md)、[.Net](../application-insights/app-insights-asp-net.md)、[.Net Core](../application-insights/app-insights-asp-net-core.md) 以及大量其他[语言/框架](../application-insights/app-insights-platforms.md)的支持。 

否则，将无法识别问题，这可能影响应用程序的可用性并且将无法满足服务级别目标。  
