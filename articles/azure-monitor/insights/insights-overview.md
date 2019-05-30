---
title: Azure Monitor 中的见解概述 |Microsoft Docs
description: Insights 提供 Azure Monitor 中为特定应用程序和服务的自定义监视体验。 本文提供了当前可用的见解的简短说明。
services: azure-monitor
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: bwren
ms.openlocfilehash: 8cb39a174c570b7019e872d731f49252a9505406
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2019
ms.locfileid: "66247224"
---
# <a name="overview-of-insights-in-azure-monitor"></a>Azure Monitor 中的见解概述
Insights 特定应用程序和服务提供的自定义的监视体验。 它们存储中的数据[Azure Monitor 数据平台](../platform/data-platform.md)和利用用于分析和发出警报的其他 Azure Monitor 功能，但可能会收集更多数据并提供在 Azure 门户中的独特用户体验。 访问的真知灼见**Insights**部分在 Azure 门户中的 Azure Monitor 菜单。

以下部分提供 Azure Monitor 中当前可用的见解的简短说明。 每个上，请参阅详细信息的详细的文档。

## <a name="application-insights"></a>Application Insights
Application Insights 是多个平台上面向 Web 开发人员的可扩展应用程序性能管理 (APM) 服务。 使用它可以监视实时 Web 应用程序。 它适用于应用程序上的各种平台包括.NET、 Node.js 和 Java EE 托管在本地、 混合或任何公共云中。 它还与 DevOps 流程集成，并具有与各种开发工具的连接点。

请参阅[什么是 Application Insights？](../app/app-insights-overview.md)。

![Application Insights](media/insights-overview/app-insights.png)

## <a name="azure-monitor-for-containers"></a>用于容器的 Azure Monitor
用于容器的 Azure Monitor 可监视部署到 Azure 容器实例或 Azure Kubernetes 服务 (AKS) 上托管的托管 Kubernetes 群集的容器工作负荷的性能。 监视容器至关重要，特别是在大规模运行包含多个应用程序的生产群集时。

请参阅[容器概述的 Azure Monitor](../insights/container-insights-overview.md)。

![用于容器的 Azure Monitor](media/insights-overview/container-insights.png)

## <a name="azure-monitor-for-resource-groups-preview"></a>Azure 监视的资源组 （预览）
Azure 监视的资源组可帮助会审和诊断单个资源时提供上下文，包括运行状况和性能的资源组作为一个整体遇到任何问题。

请参阅[使用 Azure Monitor （预览版） 监视资源组](../insights/resource-group-insights.md)。

![Azure 监视的资源组](media/insights-overview/resource-group-insights.png)

## <a name="azure-monitor-for-vms-preview"></a>Azure 监视的 Vm （预览版）
用于 VM 的 Azure Monitor 可以大规模监视 Azure 虚拟机 (VM) 和虚拟机规模集。 它分析 Windows 和 Linux VM 的性能和运行状况，并监视它们的进程及其对其他资源和外部进程的依赖关系。

请参阅[Vm 的 Azure Monitor 是什么？](vminsights-overview.md)

![用于 VM 的 Azure Monitor](media/insights-overview/vm-insights.png)

## <a name="next-steps"></a>后续步骤
* 详细了解如何[Azure Monitor 数据平台](../platform/data-platform.md)利用的见解。
* 了解有关不同[使用的 Azure Monitor 数据源](../platform/data-sources.md)和不同类型的每个 insights 收集的数据。
