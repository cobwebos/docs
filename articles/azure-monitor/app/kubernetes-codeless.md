---
title: 使用 Application Insights 监视 Azure Kubernetes 服务 (AKS) 上的应用程序 - Azure Monitor | Microsoft Docs
description: Azure Monitor 与 Kubernetes 上运行的应用程序无缝集成，可用来立即查明应用中的问题。
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 05/13/2020
ms.openlocfilehash: 52bd6d2a98e5126ff2463de1ef99da03ca555567
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87075296"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes---azure-monitor-application-insights"></a>针对 Kubernetes 的零检测应用程序监视 - Azure Monitor Application Insights

> [!IMPORTANT]
>  目前，无需对代码进行检测，即可为 Kubernetes 上运行的 Java 应用启用监视 - 为此，可以使用 [Java 独立代理](./java-in-process-agent.md)。 针对其他语言的用于无缝实现应用程序监视的解决方案正在开发中，暂时可以使用 SDK 来监视 AKS 上运行的应用：[ASP.NET Core](./asp-net-core.md)、[ASP.NET](./asp-net.md)、[Node.js](./nodejs.md)、[JavaScript](./javascript.md) 和 [Python](./opencensus-python.md)。

## <a name="application-monitoring-without-instrumenting-the-code"></a>无需对代码进行检测即可监视应用程序
目前，只有 Java 允许在不检测代码的情况下启用应用程序监视。 若要监视以其他语言编写的应用程序，请使用 SDK。 

## <a name="java"></a>Java
启用后，Java 代理将自动从最广泛使用的库和框架收集大量请求、依赖项、日志和指标。

请按照[详细说明](./java-in-process-agent.md)监视 Kubernetes 应用及其他环境中运行的 Java 应用。 

## <a name="other-languages"></a>其他语言

对于以其他语言编写的应用程序，我们目前建议使用 SDK：
* [ASP.NET Core](./asp-net-core.md)
* [ASP.NET](./asp-net.md)
* [Node.js](./nodejs.md) 
* [JavaScript](./javascript.md)
* [Python](./opencensus-python.md)

## <a name="next-steps"></a>后续步骤

* 详细了解 [Azure Monitor](../overview.md) 和 [Application Insights](./app-insights-overview.md)
* 查看[分布式跟踪](./distributed-tracing.md)的概述，并了解[应用程序映射](./app-map.md?tabs=net)能够对业务起到的作用
