---
title: 使用 Application Insights 分析实时 Azure 云服务 | Microsoft Docs
description: 对 Azure 云服务启用 Application Insights Profiler。
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: 2e13f1f09fcdfb68a99e705511e3659f1632132e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "57895475"
---
# <a name="profile-live-azure-cloud-services-with-application-insights"></a>使用 Application Insights 分析实时 Azure 云服务

Application Insights Profiler 也可以部署在以下服务上：
* [Azure 应用服务](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric 应用程序](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Azure 虚拟机](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler 随 Azure 诊断扩展一同安装。 只需将 Azure 诊断配置为安装 Profiler 并将配置文件发送到 Application Insights 资源即可。

## <a name="enable-profiler-for-azure-cloud-services"></a>对 Azure 云服务启用 Profiler
1. 请检查以确保使用的是 [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) 或更高版本。 只需要确认 *ServiceConfiguration.\*.cscfg* 文件的 `osFamily` 值至少为“5”即可。

1. [将 Application Insights SDK 添加到 Azure 云服务](../../azure-monitor/app/cloudservices.md?toc=/azure/azure-monitor/toc.json)。

   >**是探查器在 WAD 的最新版本中随附的云服务中的 bug。** 若要使用探查器与云服务，它仅支持最高版本 2.7.2 AI SDK。 如果使用 AI SDK 的较新版本，您必须返回 2.7.2，它才能使用探查器。 如果使用 Visual Studio App Insights SDK 的版本降级，可能会在运行时获取绑定重定向错误。 这是因为 Microsoft.ApplicationInsights 的 web.config 文件中的"newVersion"应设置为"2.7.2.0"后降级 AI SDK，但它不会自动更新。

1. 使用 Application Insights 跟踪请求：

    * 对于 ASP.NET Web 角色，Application Insights 可以自动跟踪请求。

    * 对于辅助角色，请[添加代码以跟踪请求](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json)。

1. 执行以下操作，以将 Azure 诊断扩展配置为启用 Profiler：

    a. 找到应用程序角色的 [Azure 诊断](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) diagnostics.wadcfgx 文件，如下所示：  

      ![诊断配置文件的位置](./media/profiler-cloudservice/cloudservice-solutionexplorer.png)  

      如果找不到该文件，请参阅[针对 Azure 云服务和虚拟机设置诊断](https://docs.microsoft.com/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines)。

    b. 添加以下 `SinksConfig` 部分作为 `WadCfg` 的子元素：  

      ```xml
      <WadCfg>
        <DiagnosticMonitorConfiguration>...</DiagnosticMonitorConfiguration>
        <SinksConfig>
          <Sink name="MyApplicationInsightsProfiler">
            <!-- Replace with your own Application Insights instrumentation key. -->
            <ApplicationInsightsProfiler>00000000-0000-0000-0000-000000000000</ApplicationInsightsProfiler>
          </Sink>
        </SinksConfig>
      </WadCfg>
      ```

    > [!NOTE]
    > 如果 *diagnostics.wadcfgx* 文件中还包含类型为 ApplicationInsights 的其他接收器，则必须匹配以下所有三个检测密钥：  
    > * 应用程序使用的密钥。 
    > * ApplicationInsights 接收器使用的密钥。 
    > * ApplicationInsightsProfiler 接收器使用的密钥。 
    >
    > 可以在 *ServiceConfiguration.\*.cscfg* 文件中找到 `ApplicationInsights` 接收器使用的实际检测密钥值。 
    > 继 Visual Studio 15.5 Azure SDK 版本之后，只有该应用程序和 ApplicationInsightsProfiler 接收器使用的检测密钥才需相互匹配。

1. 为你的服务部署新的诊断配置，Application Insights Profiler 就可以配置为在该服务上运行了。
 
## <a name="next-steps"></a>后续步骤

* 生成到应用程序的流量（例如，启动[可用性测试](monitor-web-app-availability.md)）。 然后等待 10 到 15 分钟，这样跟踪就会开始发送到 Application Insights 实例。
* 请参阅 Azure 门户中的 [Profiler 跟踪](profiler-overview.md?toc=/azure/azure-monitor/toc.json)。
* 若要排查 Profiler 问题，请参阅 [Profiler 故障排除](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)。
