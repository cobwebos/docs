---
title: 通过 Application Insights 分析实时 Azure Service Fabric 应用
description: 为 Service Fabric 应用程序启用 Profiler
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 162a8c3f90b7adab356ebb07c80b8de283fd818d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75432407"
---
# <a name="profile-live-azure-service-fabric-applications-with-application-insights"></a>使用 Application Insights 探查实时 Azure Service Fabric 应用程序

Application Insights Profiler 也可以部署在以下服务上：
* [Azure 应用服务](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure 云服务](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure 虚拟机](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="set-up-the-environment-deployment-definition"></a>设置环境部署定义

Azure 诊断中包括了 Application Insights Profiler。 可以使用 Azure 资源管理器模板为 Service Fabric 群集安装 Azure 诊断扩展。 获取[在 Service Fabric 群集上安装 Azure 诊断的模板](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json)。

若要设置环境，请执行以下操作：

1. 探查器支持 .NET Framework 和 .Net Core。 如果使用 .NET Framework，请确保使用[.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed)或更高版本。 只需确认部署的 OS `Windows Server 2012 R2` 或更高版本即可。 探查器支持 .NET Core 2.1 和更高版本的应用程序。

1. 在部署模板文件中搜索 [Azure 诊断](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics)。

1. 添加以下 `SinksConfig` 部分作为 `WadCfg` 的子元素。 使用自己的 Application Insights 检测密钥替换 `ApplicationInsightsProfiler` 属性值：  

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

1. 使用 Azure 资源管理器模板部署 Service Fabric 群集。  
  如果你的设置正确，则在安装 Azure 诊断扩展时将安装并启用 Application Insights Profiler。 

1. 将 Application Insights 添加到你的 Service Fabric 应用程序。  
  为了使 Profiler 为你的请求收集配置文件，你的应用程序必须跟踪 Application Insights 的操作。 对于无状态 Api，你可以参考用于[跟踪分析请求的](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json)说明。 有关在其他类型的应用中跟踪自定义操作的详细信息，请参阅[跟踪 Application Insights .NET SDK 的自定义操作](custom-operations-tracking.md?toc=/azure/azure-monitor/toc.json)。

1. 重新部署应用程序。


## <a name="next-steps"></a>后续步骤

* 生成到应用程序的流量（例如，启动[可用性测试](monitor-web-app-availability.md)）。 然后等待 10 到 15 分钟，这样跟踪就会开始发送到 Application Insights 实例。
* 请参阅 Azure 门户中的 [Profiler 跟踪](profiler-overview.md?toc=/azure/azure-monitor/toc.json)。
* 排查 Profiler 问题时如需帮助，请参阅 [Profiler 故障排除](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)。
