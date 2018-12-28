---
title: 使用 Application Insights 探查实时 Azure Service Fabric 应用程序 | Microsoft Docs
description: 为 Service Fabric 应用程序启用 Profiler
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
ms.openlocfilehash: 3eb5b2300ea2af7bc778e0831d105f286eab247c
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2018
ms.locfileid: "52721402"
---
# <a name="profile-live-azure-service-fabric-applications-with-application-insights"></a>使用 Application Insights 探查实时 Azure Service Fabric 应用程序

还可以在以下服务上部署 Application Insights Profiler：
* [Azure Web 应用](app-insights-profiler.md?toc=/azure/azure-monitor/toc.json)
* [云服务](app-insights-profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [虚拟机](app-insights-profiler-vm.md?toc=/azure/azure-monitor/toc.json)


## <a name="set-up-the-environment-deployment-definition"></a>设置环境部署定义

Windows Azure 诊断 (WAD) 中包括了 Application Insights Profiler。 可以使用 Azure RM 模板为你的 Service Fabric 群集安装 WAD 扩展。 此处有一个示例模板：[**用于在 Service Fabric 群集上安装 WAD 的模板。**](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json)

若要设置环境，请执行以下操作：
1. 若要确保使用的是 [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) 或更高版本，只需确认部署的 OS 是 `Windows Server 2012 R2` 或更高版本。

1. 在部署模板文件中搜索 [Azure 诊断](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics)扩展，然后添加下面的 `SinksConfig` 节作为 `WadCfg` 的子元素。 使用自己的 Application Insights 检测密钥替换 `ApplicationInsightsProfiler` 属性值：  

      ```json
      "SinksConfig": {
        "Sink": [
          {
            "name": "MyApplicationInsightsProfilerSink",
            "ApplicationInsightsProfiler": "00000000-0000-0000-0000-000000000000"
          }
        ]
      }
      ```

      若要了解如何将诊断扩展添加到部署模板，请参阅[将监视和诊断与 Windows VM 和 Azure 资源管理器模板配合使用](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
1. 使用 Azure 资源管理器模板部署 Service Fabric 群集。 如果你的设置正确，则在安装 WAD 扩展时将安装并启用 Application Insights Profiler。 
1. 将 Application Insights 添加到你的 Service Fabric 应用程序。 你的应用程序必须将请求数据发送到 Application Insights，Profiler 才能为你的请求收集配置文件。 可以在[此处](https://github.com/Microsoft/ApplicationInsights-ServiceFabric)找到说明。
1. 重新部署应用程序。

> [提示] 对于虚拟机，上述基于 json 的步骤的替代方法是在 Azure 门户中导航到“虚拟机”>“诊断设置”>“接收器”，将向 Application Insights 发送诊断数据设置为“已启用”，然后选择 Application Insights 帐户或特定的 ikey。

## <a name="next-steps"></a>后续步骤

- 生成到应用程序的流量（例如，启动[可用性测试](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability)）。 然后等待 10 到 15 分钟，这样跟踪就会开始发送到 Application Insights 实例。
- 请参阅 Azure 门户中的 [Profiler 跟踪](https://docs.microsoft.com/azure/application-insights/app-insights-profiler-overview?toc=/azure/azure-monitor/toc.json)。
- 在 [Profiler 故障排除](app-insights-profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)中获得有关排查 Profiler 问题的帮助。