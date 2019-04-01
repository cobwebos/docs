---
title: Azure Service Fabric 监视合作伙伴 | Microsoft Docs
description: 了解如何使用合作伙伴监视解决方案监视 Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/16/2018
ms.author: srrengar
ms.openlocfilehash: e9bfb835a1dea240573c21ccbbe13088561851ab
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58661405"
---
# <a name="azure-service-fabric-monitoring-partners"></a>Azure Service Fabric 监视合作伙伴

本文演示如何使用几个合作伙伴解决方案来监视 Service Fabric 应用程序、群集和基础结构。 我们与以下每家合作伙伴协作，以创建适用于 Service Fabric 的集成产品/服务。

## <a name="dynatrace"></a>Dynatrace

与 Dynatrace 的集成可提供许多现成的功能用于监视 Service Fabric 群集。 在 VMSS 实例上安装 Dynatrace OneAgent 可以提供性能计数器，并实现应用级别的 Service Fabric 部署拓扑。 此外，Dynatrace 非常适合用于本地监视。 请查看详细信息中列出的功能[公告](https://www.dynatrace.com/news/blog/automatic-end-to-end-service-fabric-monitoring-with-dynatrace/)并[说明](https://www.dynatrace.com/news/blog/automatic-end-to-end-service-fabric-monitoring-with-dynatrace/)若要在群集上启用 Dynatrace。 

## <a name="datadog"></a>Datadog

Datadog 包含适用于 Windows 和 Linux 实例的 VMSS 扩展。 使用 Datadog 可以收集 Windows 事件日志，因此可以收集 Windows 上的 Service Fabric 平台事件。 请在[此处](https://www.datadoghq.com/blog/azure-monitoring-enhancements/#integrate-with-azure-service-fabric)查看有关如何将诊断数据发送到 Datadog 的说明。

## <a name="appdynamics"></a>AppDynamics

Service Fabric 与 AppDynamics 的集成是在应用程序级别实现的。 通过更新环境变量并使用 App Dynamics NuGet，可将应用程序遥测数据发送到 AppDynamics。 请参阅这些[说明](https://docs.appdynamics.com/display/AZURE/Install+AppDynamics+for+Azure+Service+Fabric)，了解如何将 .NET Service Fabric 应用程序与 AppDynamics 集成。

## <a name="new-relic"></a>New Relic

New Relic 是与 Service Fabric 应用程序完美集成的另一个应用程序性能管理工具。 可以安装 New Relic NuGet 包，并在清单文件中添加特定的环境变量，以便将应用程序遥测数据发送到 New Relic。 请查看这些[说明](https://docs.newrelic.com/docs/agents/net-agent/azure-installation/install-net-agent-azure-service-fabric)，了解如何为 .NET Service Fabric 应用程序启用 New Relic 遥测。

## <a name="elk"></a>ELK 

ELK 堆栈是以下开源技术的集合：Elasticsearch、Logstash 和 Kibana。 结合使用这些技术可以收集、存储和分析 Service Fabric 监视与诊断数据。 我们在[此处](service-fabric-tutorial-java-elk.md)提供了有关如何对 Service Fabric 本机 Java 应用程序执行此操作的教程。 


## <a name="next-steps"></a>后续步骤

* 参阅 Service Fabric 中[监视和诊断的概述](service-fabric-diagnostics-overview.md)
* 了解如何使用我们的第一方工具[诊断常见问题](service-fabric-diagnostics-common-scenarios.md)
