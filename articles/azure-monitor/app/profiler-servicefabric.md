---
title: 使用 Application Insights 探查实时 Azure Service Fabric 应用程序 | Microsoft Docs
description: 为 Service Fabric 应用程序启用 Profiler
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
ms.openlocfilehash: 0b1a06d181fc4d2a44d389d47d1f9480c2fdcb40
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401108"
---
# <a name="profile-live-azure-service-fabric-applications-with-application-insights"></a>使用 Application Insights 探查实时 Azure Service Fabric 应用程序

Application Insights Profiler 也可以部署在以下服务上：
* [Azure 应用服务](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure 云服务](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure 虚拟机](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="set-up-the-environment-deployment-definition"></a>设置环境部署定义

Azure 诊断中包括了 Application Insights Profiler。 可以使用 Azure 资源管理器模板为 Service Fabric 群集安装 Azure 诊断扩展。 获取[在 Service Fabric 群集上安装 Azure 诊断的模板](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json)。

若要设置环境，请执行以下操作：

1. 若要确保使用的是 [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) 或更高版本，只需确认部署的 OS 是 `Windows Server 2012 R2` 或更高版本。

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
  Profiler 收集你的请求的配置文件，为你的应用程序必须跟踪使用 Application Insights 的操作。 对于无状态 Api，您可以参考的说明[进行分析跟踪请求](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json)。 有关跟踪中其他类型的应用程序的自定义操作的详细信息，请参阅[跟踪使用 Application Insights.NET SDK 的自定义操作](custom-operations-tracking.md?toc=/azure/azure-monitor/toc.json)。

1. 重新部署应用程序。

> [提示] 对于虚拟机，上述基于 JSON 的步骤的替代方法是在 Azure 门户中导航到“虚拟机”>“诊断设置”>“接收器”，将向 Application Insights 发送诊断数据设置为“已启用”，然后选择 Application Insights 帐户或特定的 ikey>。

## <a name="next-steps"></a>后续步骤

* 生成到应用程序的流量（例如，启动[可用性测试](monitor-web-app-availability.md)）。 然后等待 10 到 15 分钟，这样跟踪就会开始发送到 Application Insights 实例。
* 请参阅 Azure 门户中的 [Profiler 跟踪](profiler-overview.md?toc=/azure/azure-monitor/toc.json)。
* 排查 Profiler 问题时如需帮助，请参阅 [Profiler 故障排除](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)。
