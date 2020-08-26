---
title: Azure Monitor 中的见解概述 | Microsoft Docs
description: 见解在 Azure Monitor 中针对特定的应用程序和服务提供自定义的监视体验。 本文简要介绍了当前提供的每种见解。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/22/2019
ms.openlocfilehash: e3eefb97c85b3865abbf116b9912dc45f6db8ce8
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87326862"
---
# <a name="overview-of-insights-in-azure-monitor"></a>Azure Monitor 中的见解概述
见解针对特定的应用程序和服务提供自定义的监视体验。 它们将数据存储在 [Azure Monitor 数据平台](../platform/data-platform.md)中，并利用其他 Azure Monitor 功能分析数据和发出警报，但可以收集更多数据，并在 Azure 门户中提供独特的用户体验。 可以通过 Azure 门户中“Azure Monitor”菜单的“见解”部分访问见解。


以下部分简要介绍了 Azure Monitor 中当前提供的见解。 有关每种见解的详细信息，请参阅详细文档。

## <a name="application-insights"></a>Application Insights
Application Insights 是多个平台上面向 Web 开发人员的可扩展应用程序性能管理 (APM) 服务。 使用它可以监视实时 Web 应用程序。 它适用于本地云、混合云或任何公有云中托管的各种平台（包括 .NET、Node.js 和 Java EE）中的应用程序。 它还与 DevOps 流程集成，并提供了与各种开发工具的连接点。

请参阅[什么是 Application Insights？](../app/app-insights-overview.md)。

![Application Insights](media/insights-overview/app-insights.png)

## <a name="azure-monitor-for-containers"></a>用于容器的 Azure Monitor
用于容器的 Azure Monitor 可监视部署到 Azure 容器实例或 Azure Kubernetes 服务 (AKS) 上托管的托管 Kubernetes 群集的容器工作负荷的性能。 监视容器至关重要，特别是在大规模运行包含多个应用程序的生产群集时。

请参阅[用于容器的 Azure Monitor 概述](./container-insights-overview.md)。

![用于容器的 Azure Monitor](media/insights-overview/container-insights.png)

## <a name="azure-monitor-for-resource-groups-preview"></a>用于资源组的 Azure Monitor（预览版）
用于资源组的 Azure Monitor 有助于会审和诊断各资源出现的任何问题，同时提供有关资源组整体运行状况和性能的上下文。

请参阅[使用 Azure Monitor 监视资源组（预览版）](./resource-group-insights.md)。

![用于资源组的 Azure Monitor](media/insights-overview/resource-group-insights.png)

## <a name="azure-monitor-for-vms-preview"></a>用于 VM 的 Azure Monitor（预览版）
用于 VM 的 Azure Monitor 可以大规模监视 Azure 虚拟机 (VM) 和虚拟机规模集。 它分析 Windows 和 Linux VM 的性能和运行状况，并监视它们的进程及其对其他资源和外部进程的依赖关系。

请参阅[什么是用于 VM 的 Azure Monitor？](vminsights-overview.md)

![用于 VM 的 Azure Monitor](media/insights-overview/vm-insights.png)

## <a name="azure-monitor-for-networks-preview"></a>Azure 网络监视器（预览版）
[Azure 网络监视器](network-insights-overview.md)提供所有网络资源的运行状况和指标的综合视图。 高级搜索功能通过便捷搜索网站名称，帮助你识别资源依赖关系，从而能够支持多种方案，例如识别用于托管网站的资源等。

![用于网络的 Azure Monitor](media/insights-overview/network-insights.png)

## <a name="next-steps"></a>后续步骤
* 详细了解见解利用的 [Azure Monitor 数据平台](../platform/data-platform.md)。
* 了解 [Azure Monitor 使用的不同数据源](../platform/data-sources.md)，以及每种见解收集的不同类型的数据。

