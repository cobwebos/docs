---
title: 在不更改代码的情况下监视应用 - Azure Monitor Application Insights 自动检测功能 | Microsoft Docs
description: Azure Monitor Application Insights 自动检测功能概述 - 无代码应用程序性能管理
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 05/31/2020
ms.reviewer: mbullwin
ms.openlocfilehash: 234f772b4a77b780f0b0c1faf307c93847e5caf2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87310712"
---
# <a name="what-is-auto-instrumentation-or-codeless-attach---azure-monitor-application-insights"></a>什么是自动检测或无代码附加 - Azure Monitor Application Insights？

自动检测或无代码附加允许使用 Application Insights 来启用应用程序监视，无需更改代码。  

Application Insights 与各种资源提供程序集成，可在不同的环境中工作。 事实上，你只需启用代理并在某些情况下对其进行配置，该代理就会直接收集遥测数据。 你会立刻在 Application Insights 资源中看到指标、数据和依赖项，因此可以在潜在问题出现之前发现其来源，并通过端到端事务视图分析根本原因。

## <a name="supported-environments-languages-and-resource-providers"></a>支持的环境、语言和资源提供程序

随着我们添加越来越多的集成，自动检测功能矩阵变得很复杂。 下表显示了与各种资源提供程序、语言和环境的支持相对应的项目的当前状态。

|环境/资源提供程序 | .NET            | .NET Core       | Java            | Node.js         |
|------------------------------|-----------------|-----------------|-----------------|-----------------|
|Windows 上的 Azure 应用服务  | GA，OnBD*       | GA，选择加入      | 个人预览版 | 个人预览版 |
|Linux 上的 Azure 应用服务    | 空值             | 不支持   | 公共预览版  | 公共预览版  |
|AKS 上的 Azure 应用服务      | 空值             | 设计中       | 设计中       | 设计中       |
|Azure Functions - 基本       | GA，OnBD*       | GA，OnBD*       | GA，OnBD*       | GA，OnBD*       |
|Azure Functions - 依赖项| 不支持   | 不支持   | 公共预览版  | 不支持   |
|Azure Kubernetes 服务      | 空值             | 设计中       | 通过代理   | 设计中       |
|Azure VM (Windows)             | 公共预览版  | 不支持   | 不支持   | 不支持   |
|本地 VM (Windows)       | GA，选择加入      | 不支持   | 通过代理   | 不支持   |
|独立代理 - 任何环境   | 不支持   | 不支持   | 公共预览版  | 不支持   |

*OnBD 是 On by Default（默认启用）的缩写 - 当你在受支持的环境中部署应用后，系统会自动启用 Application Insights。 

## <a name="azure-app-service"></a>Azure 应用服务

### <a name="windows"></a>Windows

[Azure 应用服务上的应用程序监视](./azure-web-apps.md?tabs=net)适用于 .NET 应用程序，在默认情况下已启用。单击一次即可启用 .NET Core。Java 和 Node.js 均为个人预览版。

### <a name="linux"></a>Linux 

在应用服务中监视 Java 和 Node.js 应用程序的功能为公共预览版，可以在 Azure 门户中启用，在所有区域中提供。

## <a name="azure-functions"></a>Azure Functions

默认启用对 Azure Functions 的基本监视功能，该功能可以收集日志、性能、错误数据和 HTTP 请求。 对于 Java 应用程序，可以使用分布式跟踪来启用更丰富的监视功能，并获取端到端事务详细信息。 适用于 Java 的此功能为公共预览版，你可以[在 Azure 门户中启用它](./monitor-functions.md)。

## <a name="azure-kubernetes-service"></a>Azure Kubernetes 服务

目前可以通过[独立代理](./java-in-process-agent.md)为 Java 应用程序提供 Azure Kubernetes 服务的无代码检测。 

## <a name="azure-windows-vms-and-virtual-machine-scale-set"></a>Azure Windows VM 和虚拟机规模集

[适用于 Azure VM 和虚拟机规模集的自动检测](./azure-vm-vmss-apps.md)可用于 .NET 应用程序 

## <a name="on-premises-servers"></a>本地服务器
可以轻松地为[适用于 .NET 应用程序的本地 Windows 服务器](./status-monitor-v2-overview.md)和 [Java 应用](./java-in-process-agent.md)启用监视功能。

## <a name="other-environments"></a>其他环境
通用 Java 独立代理适用于任何环境，无需检测代码。 [按指南](./java-in-process-agent.md)启用 Application Insights 并了解 Java 代理的强大功能。 此代理为公共预览版，在所有区域提供。 

## <a name="next-steps"></a>后续步骤

* [Application Insights 概述](./app-insights-overview.md)
* [应用程序映射](./app-map.md)
* [端到端性能监视](../learn/tutorial-performance.md)

