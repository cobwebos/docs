---
title: Azure Service Fabric - 使用 Windows Azure 诊断扩展进行性能监视 | Microsoft Docs
description: 使用 Windows Azure 诊断来收集有关 Azure Service Fabric 群集的性能计数器。
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/26/2018
ms.author: dekapur; srrengar
ms.openlocfilehash: a9e8ef7243fcef990dae6ddc6509cd31b3f36e3d
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2018
---
# <a name="performance-monitoring-with-the-windows-azure-diagnostics-extension"></a>使用 Windows Azure 诊断扩展进行性能监视

本文档介绍通过 Windows 群集的 Windows Azure 诊断 (WAD) 扩展设置性能计数器集合所需的步骤。 对于 Linux 群集，设置 [OMS 代理](service-fabric-diagnostics-oms-agent.md)来收集节点的性能计数器。 

 > [!NOTE]
> 应针对适用于你的这些步骤在群集上部署 WAD 扩展。 如果还未设置，请查看[使用 Windows Azure 诊断的事件聚合和集合](service-fabric-reliable-serviceremoting-diagnostics.md#list-of-performance-counters)。

## <a name="collect-performance-counters-via-the-wadcfg"></a>通过 WadCfg 收集性能计数器

若要通过 WAD 收集性能计数器，则需要修改群集“资源管理器”模板中的相应配置。 请按照下列步骤将要收集的性能计数器添加到模板，并运行资源管理器资源升级。

1. 在群集模板中找到 WAD 配置 - 查找 `WadCfg`。 你将添加性能计数器以在 `DiagnosticMonitorConfiguration` 下进行收集。

2. 通过将以下部分添加到 `DiagnosticMonitorConfiguration`，设置你的配置，以收集性能计数器。 

    ```json
    "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": []
    }
    ```

    `scheduledTransferPeriod` 定义所收集的计数器值被传输到 Azure 存储表和任何已配置接收器的频率。 

3. 添加要收集到上一步中声明的 `PerformanceCounterConfiguration` 中的性能计数器。 要收集的每个计数器都通过 `counterSpecifier`、`sampleRate`、`unit`、`annotation` 和任何相关的 `sinks` 来定义。

下面是一个配置示例，其中有总处理器时间（CPU 处理操作所用的时间）的计数器和每秒 Service Fabric 执行组件方法调用数的计数器（Service Fabric 的自定义性能计数器之一）。 若要获取 Service Fabric 自定义性能计数器的完整列表，请参考 [Reliable Actor 性能计数器](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters)和 [Reliable Service 性能计算器](service-fabric-reliable-serviceremoting-diagnostics.md#list-of-performance-counters)。

 ```json
 "WadCfg": {
         "DiagnosticMonitorConfiguration": {
           "overallQuotaInMB": "50000",
           "EtwProviders": {
             "EtwEventSourceProviderConfiguration": [
               {
                 "provider": "Microsoft-ServiceFabric-Actors",
                 "scheduledTransferKeywordFilter": "1",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricReliableActorEventTable"
                 }
               },
               {
                 "provider": "Microsoft-ServiceFabric-Services",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricReliableServiceEventTable"
                 }
               }
             ],
             "EtwManifestProviderConfiguration": [
               {
                 "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                 "scheduledTransferLogLevelFilter": "Information",
                 "scheduledTransferKeywordFilter": "4611686018427387904",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricSystemEventTable"
                 }
               }
             ]
           },
           "PerformanceCounters": {
                 "scheduledTransferPeriod": "PT1M",
                 "PerformanceCounterConfiguration": [
                     {
                         "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                         "sampleRate": "PT1M",
                         "unit": "Percent",
                         "annotation": [
                         ],
                         "sinks": ""
                     },
                     {
                         "counterSpecifier": "\\Service Fabric Actor Method(*)\\Invocations/Sec",
                         "sampleRate": "PT1M",
                     }
                 ]
             }
         }
       },
  ```

 计数器的采样率可根据需要进行修改。 其格式为 `PT<time><unit>`，如果想要每秒收集一次计数器，则应设置 `"sampleRate": "PT15S"`。

 >[!NOTE]
 >虽然可以使用 `*` 来指定名称类似的性能计数器组，但通过接收器发送任何计数器（到 Application Insights）都需要它们单独声明。 

4. 在添加需要收集的适当性能计数器后，需要升级群集资源，以让这些更改反映到正在运行的群集中。 保存已修改的 `template.json` 并打开 PowerShell。 可以使用 `New-AzureRmResourceGroupDeployment` 升级群集。 该调用需要资源组的名称、更新的模板文件和参数文件，并提示资源管理器对你更新的资源进行相应更改。 一旦登录到帐户并在正确的订阅中，使用以下命令来运行升级：

    ```sh
    New-AzureRmResourceGroupDeployment -ResourceGroupName <ResourceGroup> -TemplateFile <PathToTemplateFile> -TemplateParameterFile <PathToParametersFile> -Verbose
    ```

5. 在升级完成回滚后（需要 15-45 分钟），WAD 应收集性能计数器，并将其发送到与群集关联的存储帐户中的 WADPerformanceCountersTable 表中。 通过[将 AI 接收器添加到资源管理器模板](service-fabric-diagnostics-event-analysis-appinsights.md#add-the-ai-sink-to-the-resource-manager-template)，查看 Application Insights 中的性能计数器。

## <a name="next-steps"></a>后续步骤
* 收集群集的更多性能计数器。 有关应收集的计数器列表，请参阅[性能指标](service-fabric-diagnostics-event-generation-perf.md)。
* [将监视和诊断与 Windows VM 和 Azure 资源管理器模板配合使用](../virtual-machines/windows/extensions-diagnostics-template.md)，以进一步修改 `WadCfg`，包括配置向其发送诊断数据的其他存储帐户。
