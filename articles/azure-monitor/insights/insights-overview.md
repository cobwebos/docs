---
title: Azure Monitor 中的见解概述 |Microsoft Docs
description: Insights 提供了针对特定应用程序和服务 Azure Monitor 的自定义监视体验。 本文简要介绍了当前可用的每个见解。
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/22/2019
ms.openlocfilehash: 3abf3d562b9ca4d6fc9907c065dbaf9a52d58c5f
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555341"
---
# <a name="overview-of-insights-in-azure-monitor"></a>Azure Monitor 中的见解概述
Insights 提供针对特定应用程序和服务的自定义监视体验。 它们将数据存储在[Azure Monitor 数据平台](../platform/data-platform.md)中，并利用其他 Azure Monitor 功能进行分析和警报，但可能会收集更多的数据，并在 Azure 门户提供独特的用户体验。 从 Azure 门户中的 "Azure Monitor" 菜单的 "**见解**" 部分访问 insights。

以下部分提供了 Azure Monitor 中当前可用的见解的简短说明。 有关每个的详细信息，请参阅详细文档。

## <a name="application-insights"></a>Application Insights
Application Insights 是多个平台上面向 Web 开发人员的可扩展应用程序性能管理 (APM) 服务。 使用它可以监视实时 Web 应用程序。 它适用于各种平台上的应用程序，包括 .NET、node.js 和 Java EE、托管在本地、混合或任何公有云。 它还与 DevOps 流程集成，并具有各种开发工具的连接点。

请参阅[什么是 Application Insights？](../app/app-insights-overview.md)。

![Application Insights](media/insights-overview/app-insights.png)

## <a name="azure-monitor-for-containers"></a>容器 Azure Monitor
用于容器的 Azure Monitor 可监视部署到 Azure 容器实例或 Azure Kubernetes 服务 (AKS) 上托管的托管 Kubernetes 群集的容器工作负荷的性能。 监视容器至关重要，特别是在大规模运行包含多个应用程序的生产群集时。

[有关容器的详细概述](../insights/container-insights-overview.md)，请参阅 Azure Monitor。

![容器 Azure Monitor](media/insights-overview/container-insights.png)

## <a name="azure-monitor-for-resource-groups-preview"></a>资源组的 Azure Monitor （预览）
资源组的 Azure Monitor 有助于诊断和诊断您的个别资源遇到的任何问题，同时将上下文视为整个资源组的运行状况和性能。

请参阅[监视具有 Azure Monitor （预览版）的资源组](../insights/resource-group-insights.md)。

![资源组的 Azure Monitor](media/insights-overview/resource-group-insights.png)

## <a name="azure-monitor-for-vms-preview"></a>用于 VM 的 Azure Monitor （预览）
用于 VM 的 Azure Monitor 可以大规模监视 Azure 虚拟机 (VM) 和虚拟机规模集。 它分析 Windows 和 Linux VM 的性能和运行状况，并监视它们的进程及其对其他资源和外部进程的依赖关系。

请参阅[什么是用于 VM 的 Azure Monitor？](vminsights-overview.md)

![用于 VM 的 Azure Monitor](media/insights-overview/vm-insights.png)

## <a name="next-steps"></a>后续步骤
* 详细了解见解利用的[Azure Monitor 数据平台](../platform/data-platform.md)。
* 了解[Azure Monitor 使用的不同数据源](../platform/data-sources.md)以及每个见解收集的不同类型的数据。
