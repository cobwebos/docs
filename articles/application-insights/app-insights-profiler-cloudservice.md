---
title: 使用 Application Insights 探查实时 Azure 云服务 | Microsoft Docs
description: 为云服务启用 Application Insights Profiler。
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: 28e21180fde50d19154830694cd4959795ae9d5c
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/27/2018
ms.locfileid: "53789375"
---
# <a name="profile-live-azure-cloud-services-with-application-insights"></a>使用 Application Insights 探查实时 Azure 云服务

还可以在以下服务上部署 Application Insights Profiler：
* [Azure 应用服务](app-insights-profiler.md?toc=/azure/azure-monitor/toc.json)
* [Service Fabric 应用程序](app-insights-profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [虚拟机](app-insights-profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler 是随 Windows Azure 诊断 (WAD) 扩展安装的。 你只需要配置 WAD 来安装 Profiler 并将配置文件发送到 Application Insights 资源。

## <a name="enable-profiler-for-your-azure-cloud-service"></a>为 Azure 云服务启用 Profiler
1. 请检查你使用的是否为 [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) 或更高版本。  只需要确认 *ServiceConfiguration.\*.cscfg* 文件的 `osFamily` 值至少为“5”即可。
1. [将 Application Insights SDK 添加到云服务](../azure-monitor/app/cloudservices.md?toc=/azure/azure-monitor/toc.json)。
1. 使用 Application Insights 跟踪请求：

    对于 ASP.Net Web 角色，Application Insights 可以自动跟踪请求。

    对于辅助角色，请[添加代码来跟踪请求。](app-insights-profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json)

    

1. 配置 Windows Azure 诊断 (WAD) 扩展来启用 Profiler。



    1. 找到应用程序角色的 [Azure 诊断](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) diagnostics.wadcfgx 文件，如下所示：  

       ![诊断配置文件的位置](./media/enable-profiler-compute/cloudservice-solutionexplorer.png)  

        如果找不到该文件，若要了解如何在 Azure 云服务项目中启用诊断扩展，请参阅[为 Azure 云服务和虚拟机设置诊断](https://docs.microsoft.com/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#enable-diagnostics-in-cloud-service-projects-before-deploying-them)。

    1. 添加以下 `SinksConfig` 部分作为 `WadCfg` 的子元素：  

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

    >   **注意：** 如果 diagnostics.wadcfgx* 文件还包含 `ApplicationInsights` 类型的其他接收器，则以下三个检测密钥都必须匹配：  
    >  * 应用程序使用的密钥。  
    >  * `ApplicationInsights` 接收器使用的密钥。  
    >  * `ApplicationInsightsProfiler` 接收器使用的密钥。  
    >
    > 可以在 *ServiceConfiguration.\*.cscfg* 文件中找到 `ApplicationInsights` 接收器使用的实际检测密钥值。  
    > 在 Visual Studio 15.5 Azure SDK 版本之后，只有应用程序和 `ApplicationInsightsProfiler` 接收器使用的检测密钥需要相互匹配。
1. 使用新的诊断配置部署你的服务，Application Insights Profiler 将配置为在你的服务上运行。
 
## <a name="next-steps"></a>后续步骤

- 生成到应用程序的流量（例如，启动[可用性测试](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability)）。 然后等待 10 到 15 分钟，这样跟踪就会开始发送到 Application Insights 实例。
- 请参阅 Azure 门户中的 [Profiler 跟踪](https://docs.microsoft.com/azure/application-insights/app-insights-profiler-overview?toc=/azure/azure-monitor/toc.json)。
- 在 [Profiler 故障排除](app-insights-profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)中获得有关排查 Profiler 问题的帮助。