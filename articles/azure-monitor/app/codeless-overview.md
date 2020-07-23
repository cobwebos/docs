---
title: 监视应用而不进行代码更改-自动检测 Azure Monitor Application Insights |Microsoft Docs
description: Azure Monitor Application Insights 应用程序性能管理的自动检测概述
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 05/31/2020
ms.reviewer: mbullwin
ms.openlocfilehash: 87342dcd316b0364522baa01e632b704665c998e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85363720"
---
# <a name="what-is-auto-instrumentation-or-codeless-attach---azure-monitor-application-insights"></a>什么是自动检测或无代码置备的 Azure Monitor Application Insights？

自动检测或无代码置备 attach 允许使用 Application Insights 启用应用程序监视，而无需更改代码。  

Application Insights 与各种资源提供程序集成，并在不同的环境中工作。 实质上，你只需启用和-在某些情况下-配置代理，该代理将收集现成的遥测数据。 在任何时候，你都可以在 Application Insights 资源中看到指标、数据和依赖项，这使你能够在出现潜在问题之前发现潜在问题的根源，并通过端到端事务视图分析根本原因。

## <a name="supported-environments-languages-and-resource-providers"></a>支持的环境、语言和资源提供程序

随着我们添加越来越多的集成，自动检测功能矩阵变得很复杂。 下表显示了对各种资源提供程序、语言和环境的支持的当前状态。

|环境/资源提供程序 | .NET            | .NET Core       | Java            | Node.js         |
|------------------------------|-----------------|-----------------|-----------------|-----------------|
|Windows 上的 Azure App Service  | GA，OnBD *       | GA，选择加入      | 个人预览版 | 个人预览版 |
|Linux 上的 Azure 应用服务    | 不适用             | 不支持   | 公共预览版  | 公共预览版  |
|AKS 上的 Azure App Service      | 不适用             | 设计中       | 设计中       | 设计中       |
|Azure Functions-基本       | GA，OnBD *       | GA，OnBD *       | GA，OnBD *       | GA，OnBD *       |
|Azure Functions-依赖项| 不支持   | 不支持   | 公共预览版  | 不支持   |
|Azure Kubernetes 服务      | 不适用             | 设计中       | 通过代理   | 设计中       |
|Azure Vm Windows             | 公共预览版  | 不支持   | 不支持   | 不支持   |
|本地 Vm Windows       | GA，选择加入      | 不支持   | 通过代理   | 不支持   |
|独立代理-任何 env。   | 不支持   | 不支持   | 公共预览版  | 不支持   |

* 默认情况下，OnBD 为打开状态-在受支持的环境中部署应用后，将自动启用 Application Insights。 

## <a name="azure-app-service"></a>Azure 应用服务

### <a name="windows"></a>Windows

[Azure App Service 上的应用程序监视](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps?tabs=net)适用于 .net 应用程序，并且在默认情况下已启用，可以通过一次单击来启用 .net Core，Java 和 Node.js 处于个人预览状态。

### <a name="linux"></a>Linux 

在应用服务中监视 Java 和 Node.js 应用程序是公共预览版，可以在所有区域提供 Azure 门户中启用。

## <a name="azure-functions"></a>Azure Functions

默认情况下，启用了基本监视 Azure Functions 以收集日志、性能、错误数据和 HTTP 请求。 对于 Java 应用程序，可以使用分布式跟踪来启用更丰富的监视，并获取端到端事务的详细信息。 此 Java 功能以公共预览版提供，你可以[在 Azure 门户中启用它](https://docs.microsoft.com/azure/azure-monitor/app/monitor-functions)。

## <a name="azure-kubernetes-service"></a>Azure Kubernetes 服务

Azure Kubernetes 服务的无代码置备检测目前可通过[独立代理](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent)在 Java 应用程序中使用。 

## <a name="azure-windows-vms-and-virtual-machine-scale-set"></a>Azure Windows Vm 和虚拟机规模集

适用于[Azure vm 和虚拟机规模集的自动检测](https://docs.microsoft.com/azure/azure-monitor/app/azure-vm-vmss-apps)适用于 .net 应用程序 

## <a name="on-premises-servers"></a>本地服务器
你可以轻松为[适用于 .net 应用程序的本地 Windows 服务器](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview)和[Java 应用](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent)启用监视。

## <a name="other-environments"></a>其他环境
通用 Java 独立代理适用于任何环境，无需检测代码。 [按照指南进行操作](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent)，启用 Application Insights，并阅读有关 Java 代理的惊人功能的信息。 代理以公共预览版提供，在所有区域都可用。 

## <a name="next-steps"></a>后续步骤

* [Application Insights 概述](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)
* [应用程序映射](./../../azure-monitor/app/app-map.md)
* [端到端性能监视](./../../azure-monitor/learn/tutorial-performance.md)
