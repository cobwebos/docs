---
title: "将 Azure 诊断数据发送到 Application Insights | Microsoft 文档"
description: "配置发送到 Application Insights 门户的 Azure 云服务诊断日志的详细信息。"
services: application-insights
documentationcenter: .net
author: sbtron
manager: douge
ms.assetid: a67dd20a-fc5d-4391-ba63-bfe164fb62f7
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/17/2015
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2843ff25c118afe5d5421b322b529e9a9f2d5036


---
# <a name="configure-azure-diagnostic-logging-to-application-insights"></a>配置 Azure 诊断以将日志记录到 Application Insights
在 Microsoft Azure 中设置云服务项目或虚拟机时，[Azure 可以生成诊断日志](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md)。 可以将此日志发送到 Application Insights，以便分析它与 Application Insights SDK 从应用内发送的诊断和使用情况遥测数据。 Azure 日志包含应用管理的事件，例如启动、停止、崩溃，以及性能计数器。 日志还包含应用中对 System.Diagnostics.Trace 的调用。

本文详细介绍诊断捕获配置。

需在 Visual Studio 中安装 Azure SDK 2.8 或更高版本。

## <a name="get-an-application-insights-resource"></a>获取 Application Insights 资源
为获得最佳体验，请[将 Application Insights SDK 添加到云服务应用的每个角色](app-insights-cloudservices.md)，或[添加到 VM 中运行的每个应用](app-insights-overview.md)。 然后可以发送要分析的诊断数据并在相同的 Application Insights 资源中显示。

或者，如果不想使用 SDK - 例如，如果应用已处于活动状态 - 只能在 Azure 门户中[创建新的 Application Insights 资源](app-insights-create-new-resource.md)。 选择“Azure 诊断”作为应用程序类型。

## <a name="send-azure-diagnostics-to-application-insights"></a>将 Azure 诊断数据发送到 Application Insights
如果可以更新应用项目，请在 Visual Studio 中选择每个角色及其属性，然后在“配置”选项卡中，选择“将诊断数据发送到 Application Insights”。

或者，在使用“发布”命令上载应用时，可以在“诊断”页上选择“Application Insights”选项。

如果应用程序已处于活动状态，请使用 Visual Studio 的服务器资源管理器或云服务资源管理器打开应用的属性。 选择“将 Azure 诊断数据发送到 Application Insights”。

在每种情况下，系统都会要求提供所创建的 Application Insights 资源的详细信息。

建议将不同角色的数据发送到不同的资源。 可以通过创建[仪表板](app-insights-dashboards.md)，在门户中并列显示资源的指标图表。

[详细了解如何设置云服务应用的 Application Insights](app-insights-cloudservices.md)。

## <a name="configuring-the-azure-diagnostics-adapter"></a>配置 Azure 诊断适配器
仅当你想要选择发送到 Application Insights 的日志部分时，才参阅本部分。 默认情况下，将发送所有内容，包括：Microsoft Azure 事件；性能计数器；跟踪从应用对 System.Diagnostics.Trace 的调用。

在角色属性编辑器中或发布向导中更改诊断选项时，实际上更改的是两组文件的内容：

* 角色[诊断配置文件](https://msdn.microsoft.com/library/azure/dn782207.aspx)。 可在解决方案资源管理器中的 `<Your Service>/Roles/*/diagnostics.wadcfgx` 下面找到这些文件
* 服务配置文件：`ServiceConfiguration.*.cscfg`。

直接编辑这些文件可以影响比向导允许的更多的特定选项。 阅读更多详细信息。 

## <a name="separate-development-and-production-resources"></a>将开发资源和生产资源隔离开来
可以将应用开发和生产戳记中的遥测数据发送到不同的 Application Insights 资源。 这样可以避免实时遥测数据使开发遥测数据变得混乱。 

1. 为每个戳记[创建新的 Application Insights 资源](app-insights-create-new-resource.md)。 从每个资源的“概要”选项卡获取检测密钥。
2. 编辑两个.cscfg 文件并插入不同的检测密钥。

## <a name="choose-the-priority-levels-to-send"></a>选择发送优先级
在每个角色的诊断配置文件 `diagnostics.wadcfgx` 中，可以按级别筛选日志消息。

### <a name="define-a-sink"></a>定义接收器
`<SinksConfig>` 定义可将 Azure 诊断数据发送到的其他接收器位置。  示例 `SinksConfig` 如下：

```xml

    <SinksConfig>
     <Sink name="ApplicationInsights">
      <ApplicationInsights/>
      <Channels>
        <Channel logLevel="Error" name="MyTopDiagData"  />
        <Channel logLevel="Verbose" name="MyLogData"  />
      </Channels>
     </Sink>
    </SinksConfig>

```

`Channels` 命名要发送到接收器的数据流。 通道的作用类似于筛选器。 使用 `loglevel` 属性可以指定通道发送的日志级别。 可用值为：`{Verbose, Information, Warning, Error, Critical}`。

### <a name="send-data-to-the-sink"></a>将数据发送到接收器
将数据发送到 Application Insights 接收器，方法是在 DiagnosticMonitorConfiguration 节点下面添加接收器属性。 将 sinks 元素添加到每个节点可以指定要从该节点及其下的任何节点收集数据，并发送到指定的接收器。

例如，由 Azure SDK 所创建的默认值是发送所有的 Azure 诊断数据：

```xml

    <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights">
```

但是，如果只想要发送错误日志，请将接收器名称限定为通道名称：

```xml

    <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights.MyTopDiagdata">
```

请注意，这里使用了我们定义的接收器名称，以及上面定义的通道名称。

如果只想将详细应用程序日志发送到 Application Insights，请将 sinks 属性添加到 `Logs` 节点。

```xml

    <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose" sinks="ApplicationInsights.MyLogData"/>
```

你还可以在层次结构中不同级别下的配置中包含多个接收器。 在此情况下，在层次结构顶层指定的接收器将用作全局设置，而在单个元素中指定的接收器将用作该全局设置的覆盖。

以下公共配置文件的完整示例会将所有错误发送到 Application Insights（在 `DiagnosticMonitorConfiguration` 节点中指定），此外，还会发送应用程序日志的详细信息级别记录（在 `Logs` 节点中指定）。

```xml

    <WadCfg>
     <DiagnosticMonitorConfiguration overallQuotaInMB="4096"
       sinks="ApplicationInsights.MyTopDiagData">
       <!-- All info below sent to this channel -->
      <DiagnosticInfrastructureLogs />
      <PerformanceCounters>
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" sinks="ApplicationInsights.MyLogData/>
        <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
        <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
      </PerformanceCounters>
      <WindowsEventLog scheduledTransferPeriod="PT1M">
        <DataSource name="Application!*" />
      </WindowsEventLog>
      <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose"
            sinks="ApplicationInsights.MyLogData"/>
       <!-- This specific info sent to this channel -->
     </DiagnosticMonitorConfiguration>

     <SinksConfig>
      <Sink name="ApplicationInsights">
        <ApplicationInsights/>
        <Channels>
          <Channel logLevel="Error" name="MyTopDiagData"  />
          <Channel logLevel="Verbose" name="MyLogData"  />
        </Channels>
      </Sink>
     </SinksConfig>
    </WadCfg>
```


需要注意此项功能的一些限制：

* 通道只能用于处理日志类型，而不能处理性能计数器。 如果对性能计数器元素指定通道，将会忽略该通道。
* 通道的日志级别不能超过 Azure 诊断所要收集的日志级别。 例如：不能在 Logs 元素中收集应用程序日志错误，并且不能尝试向 Application Insight 同步发送详细日志。 scheduledTransferLogLevelFilter 属性收集的日志数始终必须等于或大于尝试发送到接收器的日志数。
* 无法将 Azure 诊断扩展收集的任何 Blob 数据发送到 Application Insights。 例如，“目录”节点下指定的任何数据。 对于故障转储，实际故障转储仍会发送到 Blob 存储，并只会将生成了故障转储的通知发送到 Application Insights。

## <a name="next-steps"></a>后续步骤
* [使用 Application Insights 监视 Azure 云服务](app-insights-cloudservices.md)
* [使用 PowerShell 将 Azure 诊断数据发送到 Application Insights](app-insights-powershell-azure-diagnostics.md)
* [Azure 诊断配置文件](https://msdn.microsoft.com/library/azure/dn782207.aspx)




<!--HONumber=Nov16_HO3-->


