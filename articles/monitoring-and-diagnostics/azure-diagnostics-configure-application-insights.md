---
title: "配置 Azure 诊断以将数据发送到 Application Insights | Microsoft Docs"
description: "更新 Azure 诊断公共配置，以将数据发送到 Application Insights。"
services: monitoring-and-diagnostics
documentationcenter: .net
author: rboucher
manager: carmonm
editor: 
ms.assetid: f9e12c3e-c307-435e-a149-ef0fef20513a
ms.service: monitoring-and-diagnostics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2016
ms.author: robb
ms.openlocfilehash: 67dc2d5bbfa2012e4e098616edda593d023c4c1e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="send-cloud-service-virtual-machine-or-service-fabric-diagnostic-data-to-application-insights"></a>将云服务、虚拟机或 Service Fabric 诊断数据发送到 Application Insights
云服务、虚拟机、虚拟机规模集和 Service Fabric 都使用 Azure 诊断扩展来收集数据。  Azure 诊断将数据发送到 Azure 存储表。  但是，也可以 Azure 诊断扩展 1.5 或更高版本，通过管道将所有或一部分数据发送到其他位置。

本文介绍如何将数据从 Azure 诊断扩展发送到 Application Insights。

## <a name="diagnostics-configuration-explained"></a>诊断配置说明
Azure 诊断扩展 1.5 引入了接收器 - 可将诊断数据发送到的附加位置。

Application Insights 接收器的示例配置：

```XML
<SinksConfig>
    <Sink name="ApplicationInsights">
      <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
      <Channels>
        <Channel logLevel="Error" name="MyTopDiagData"  />
        <Channel logLevel="Verbose" name="MyLogData"  />
      </Channels>
    </Sink>
</SinksConfig>
```
```JSON
"SinksConfig": {
    "Sink": [
        {
            "name": "ApplicationInsights",
            "ApplicationInsights": "{Insert InstrumentationKey}",
            "Channels": {
                "Channel": [
                    {
                        "logLevel": "Error",
                        "name": "MyTopDiagData"
                    },
                    {
                        "logLevel": "Error",
                        "name": "MyLogData"
                    }
                ]
            }
        }
    ]
}
```
- **接收器**的 *name* 属性是用于唯一标识该接收器的字符串值。

- **ApplicationInsights** 元素指定要将 Azure 诊断数据发送到的 Application Insights 资源的检测键。
    - 如果没有 Application Insights 资源，请参阅[创建新的 Application Insights 资源](../application-insights/app-insights-create-new-resource.md)，了解有关创建资源和获取检测键的详细信息。
    - 如果使用 Azure SDK 2.8 和更高版本开发云服务，系统会自动填充此检测键。 该值基于打包云服务项目时指定的 **APPINSIGHTS_INSTRUMENTATIONKEY** 服务配置设置。 请参阅[将 Application Insights 与 Azure 诊断配合使用以排查云服务问题](../cloud-services/cloud-services-dotnet-diagnostics-applicationinsights.md)。

- **Channels** 元素包含一个或多个 **Channels** 元素。
    - *name* 属性唯一引用该通道。
    - 使用 *loglevel* 属性可以指定通道允许的日志级别。 可用日志级别从最多信息到最少信息的顺序依次为：
        - 详细
        - 信息
        - 警告
        - 错误
        - 严重

通道的作用类似于筛选器，可让你选择要发送到目标接收器的特定日志级别。 例如，可以收集详细日志并将其发送到存储，但只将“错误”日志发送到接收器。

下图显示了这种关系。

![诊断公共配置](./media/azure-diagnostics-configure-applicationinsights/AzDiag_Channels_App_Insights.png)

下图汇总了配置值及其工作原理。 还可以在层次结构中不同级别下的配置中包含多个接收器。 位于顶层的接收器用作全局设置，在单个元素中指定的接收器类似于该全局设置的重写。

![使用 Application Insights 配置诊断接收器](./media/azure-diagnostics-configure-applicationinsights/Azure_Diagnostics_Sinks.png)

## <a name="complete-sink-configuration-example"></a>完整的接收器配置示例
下面是公共配置文件的完整示例。该文件
1. 将所有错误都发送到 Application Insights（在 **DiagnosticMonitorConfiguration** 节点中指定）
2. 此外，还会发送日志应用程序日志的详细级别日志（在 **Logs** 节点中指定）。

```XML
<WadCfg>
  <DiagnosticMonitorConfiguration overallQuotaInMB="4096"
       sinks="ApplicationInsights.MyTopDiagData"> <!-- All info below sent to this channel -->
    <DiagnosticInfrastructureLogs />
    <PerformanceCounters>
      <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />
      <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
    </PerformanceCounters>
    <WindowsEventLog scheduledTransferPeriod="PT1M">
      <DataSource name="Application!*" />
    </WindowsEventLog>
    <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose"
            sinks="ApplicationInsights.MyLogData"/> <!-- This specific info sent to this channel -->
  </DiagnosticMonitorConfiguration>

<SinksConfig>
    <Sink name="ApplicationInsights">
      <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
      <Channels>
        <Channel logLevel="Error" name="MyTopDiagData"  />
        <Channel logLevel="Verbose" name="MyLogData"  />
      </Channels>
    </Sink>
  </SinksConfig>
</WadCfg>
```
```JSON
"WadCfg": {
    "DiagnosticMonitorConfiguration": {
        "overallQuotaInMB": 4096,
        "sinks": "ApplicationInsights.MyTopDiagData", "_comment": "All info below sent to this channel",
        "DiagnosticInfrastructureLogs": {
        },
        "PerformanceCounters": {
            "PerformanceCounterConfiguration": [
                {
                    "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                    "sampleRate": "PT3M"
                },
                {
                    "counterSpecifier": "\\Memory\\Available MBytes",
                    "sampleRate": "PT3M"
                }
            ]
        },
        "WindowsEventLog": {
            "scheduledTransferPeriod": "PT1M",
            "DataSource": [
                {
                    "name": "Application!*"
                }
            ]
        },
        "Logs": {
            "scheduledTransferPeriod": "PT1M",
            "scheduledTransferLogLevelFilter": "Verbose",
            "sinks": "ApplicationInsights.MyLogData", "_comment": "This specific info sent to this channel"
        }
    },
    "SinksConfig": {
        "Sink": [
            {
                "name": "ApplicationInsights",
                "ApplicationInsights": "{Insert InstrumentationKey}",
                "Channels": {
                    "Channel": [
                        {
                            "logLevel": "Error",
                            "name": "MyTopDiagData"
                        },
                        {
                            "logLevel": "Verbose",
                            "name": "MyLogData"
                        }
                    ]
                }
            }
        ]
    }
}
```
在上面的配置中，以下各行的含义如下所述：

### <a name="send-all-the-data-that-is-being-collected-by-azure-diagnostics"></a>发送 Azure 诊断收集的所有数据

```XML
<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights">
```
```JSON
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights",
}
```

### <a name="send-only-error-logs-to-the-application-insights-sink"></a>只将错误日志发送到 Application Insights 接收器

```XML
<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights.MyTopDiagdata">
```
```JSON
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights.MyTopDiagData",
}
```

### <a name="send-verbose-application-logs-to-application-insights"></a>将详细的应用程序日志发送到 Application Insights

```XML
<Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose" sinks="ApplicationInsights.MyLogData"/>
```
```JSON
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights.MyLogData",
}
```

## <a name="limitations"></a>限制

- **通道只能记录类型，而不能记录性能计数器。** 如果对性能计数器元素指定通道，将忽略该通道。
- **通道的日志级别不能超过 Azure 诊断所要收集的日志级别。** 例如，不能在 Logs 元素中收集应用程序日志错误，并且不能尝试向 Application Insight 接收器发送详细日志。 *scheduledTransferLogLevelFilter* 属性收集的日志数始终必须等于或大于尝试发送到接收器的日志数。
- **无法将 Azure 诊断扩展收集的 Blob 数据发送到 Application Insights。** 例如，*Directories* 节点下指定的任何数据。 对于故障转储，实际故障转储将发送到 Blob 存储，并只会将生成了故障转储的通知发送到 Application Insights。

## <a name="next-steps"></a>后续步骤
* 了解如何在 Application Insights 中[查看 Azure 诊断信息](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-cloudservices#view-azure-diagnostic-events)。
* 使用 [PowerShell](../cloud-services/cloud-services-diagnostics-powershell.md) 为应用程序启用 Azure 诊断扩展。
* 使用 [Visual Studio](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md) 为应用程序启用 Azure 诊断扩展。
