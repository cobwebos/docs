---
title: Azure Service Fabric 监视最佳实践
description: 使用 Azure Service Fabric 监视群集和应用程序的最佳实践和设计注意事项。
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: af03223e8b007cbd2a00d54c3076056cd110ecc9
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2019
ms.locfileid: "75551810"
---
# <a name="monitoring-and-diagnostics"></a>监视和诊断

在任何云环境中开发、测试和部署工作负荷时，[监视和诊断](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-overview)至关重要。 例如，可以跟踪应用程序的使用方式、Service Fabric 平台所采取的操作、带性能计数器的资源利用率以及群集的总体运行状况。 可以使用此信息来诊断和更正问题，避免将来发生此类问题。

## <a name="application-monitoring"></a>应用程序监视

应用程序监视跟踪应用程序的功能和组件的使用方式。 监视应用程序可以确保捕获影响用户的问题。 应用程序监视是开发应用程序及其服务的人员的责任，因为它特定于应用程序的业务逻辑。 建议使用 [Application Insights](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-monitoring-aspnet)（Azure 的应用程序监视工具）来设置应用程序监视。

## <a name="cluster-monitoring"></a>群集监视

Service Fabric 的目标之一是使应用程序能够灵活应对硬件故障。 为了实现此目的，可以通过平台的系统服务功能检测基础结构问题，并快速将工作负荷故障转移到群集中的其他节点。 但是，系统服务本身出现问题该怎么办？ 或者，在尝试部署或移动工作负荷时，如果违反服务位置的规则该怎么办？ Service Fabric 为这些问题以及其他问题提供诊断，确保你了解 Service Fabric 平台与应用程序、服务、容器和节点的交互情况。

对于 Windows 群集，建议使用[诊断代理](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-wad)和[Azure Monitor 日志](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-setup)设置群集监视。

对于 Linux 群集，还建议使用 Azure Monitor 日志来监视 Azure 平台和基础结构。 Linux 平台诊断要求不同的配置，详见 [Syslog 中的 Service Fabric Linux 群集事件](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-syslog)。

## <a name="infrastructure-monitoring"></a>基础结构监视

建议使用[Azure Monitor 日志](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-agent)来监视群集级别事件。 使用上一链接中介绍的工作区配置 Log Analytics 代理以后，即可收集性能指标，例如：CPU 使用率、.NET 性能计数器（例如进程级别的 CPU 使用率）、Service Fabric 性能计数器（例如来自 Reliable Service 的异常数），以及容器指标（例如 CPU 使用率）。  需要将容器日志写入 stdout 或 stderr，以便它们在 Azure Monitor 日志中可用。

## <a name="watchdogs"></a>监视器

监视器通常是一个独立的服务，用于监视各个服务的运行状况和负载、ping 终结点，以及报告群集中的异常运行状况事件。 这有助于防止出现仅基于单个服务的性能所检测不到的错误。 监视器也是一个托管代码的好选择，无需用户交互即可执行补救措施，例如每隔特定时间就清理一次存储中的日志文件。 若要查看示例监视器服务实现，请参阅 [Syslog 中的 Service Fabric Linux 群集事件](https://github.com/Azure-Samples/service-fabric-watchdog-service)。

## <a name="next-steps"></a>后续步骤

* 开始检测应用程序：[应用程序级别事件和日志生成](service-fabric-diagnostics-event-generation-app.md)。
* 通过[在 Service Fabric 上监视和诊断 ASP.NET Core 应用程序](service-fabric-tutorial-monitoring-aspnet.md)，完成为应用程序设置 Application Insights 的步骤。
* 详细了解监视平台以及 Service Fabric 提供的事件：[平台级别事件和日志生成](service-fabric-diagnostics-event-generation-infra.md)。
* 配置 Azure Monitor 日志与 Service Fabric 的集成：[设置群集的 Azure Monitor 日志](service-fabric-diagnostics-oms-setup.md)
* 了解如何为监视容器设置 Azure Monitor 日志： [Azure 中 Windows 容器的监视和诊断 Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md)。
* 查看 Service Fabric 的示例诊断问题和解决方案：[诊断常见方案](service-fabric-diagnostics-common-scenarios.md)
* 了解 Azure 资源的一般监视建议：[最佳做法-监视和诊断](https://docs.microsoft.com/azure/architecture/best-practices/monitoring)。