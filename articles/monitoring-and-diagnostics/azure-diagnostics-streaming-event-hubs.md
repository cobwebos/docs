---
title: 将 Azure 诊断数据流式传输到事件中心
description: 使用事件中心从头到尾配置 Azure 诊断，包括对常见方案的指导。
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 07/13/2017
ms.author: robb
ms.component: diagnostic-extension
ms.openlocfilehash: 98e788d87b0ce03eece35868391aadd5233217b0
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35267707"
---
# <a name="streaming-azure-diagnostics-data-in-the-hot-path-by-using-event-hubs"></a>使用事件中心流式处理热路径中的 Azure 诊断数据
Azure 诊断提供了灵活的方法用于收集来自云服务虚拟机 (VM) 的指标和日志，并将结果传输到 Azure 存储。 从 2016 年 3 月 (SDK 2.9) 这一时间范围开始，可以将诊断发送到自定义数据源，并使用 [Azure 事件中心](https://azure.microsoft.com/services/event-hubs/)在数秒内传输热路径数据。

支持的数据类型包括：

* Windows 事件跟踪 (ETW) 事件
* 性能计数器
* Windows 事件日志
* 应用程序日志
* Azure 诊断基础结构日志

本文说明如何使用事件中心从头到尾配置 Azure 诊断。 另针对以下常见方案提供指导：

* 如何自定义发送到事件中心的日志和指标
* 如何更改每个环境中的配置
* 如何查看事件中心流数据
* 如何排查连接问题  

## <a name="prerequisites"></a>先决条件
从 Azure SDK 2.9 和相应的 Azure Tools for Visual Studio 开始，云服务、VM、虚拟机规模集和 Service Fabric 将支持事件中心接收来自 Azure 诊断的数据。

* Azure 诊断扩展 1.6（[Azure SDK for .NET 2.9 或更高版本](https://azure.microsoft.com/downloads/)默认以此为目标）
* [Visual Studio 2013 或更高版本](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* 在应用程序中使用 *.wadcfgx* 文件和以下任一方法的 Azure 诊断的现有配置：
  * Visual Studio：[为 Azure 云服务和虚拟机配置诊断](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md)
  * Windows PowerShell：[使用 PowerShell 在 Azure 云服务中启用诊断](../cloud-services/cloud-services-diagnostics-powershell.md)
* 根据文章[事件中心入门](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)预配的事件中心命名空间

## <a name="connect-azure-diagnostics-to-event-hubs-sink"></a>将 Azure 诊断连接到事件中心接收器
默认情况下，Azure 诊断始终将日志和指标发送到 Azure 存储帐户。 应用程序也可以将数据发送到事件中心，方法是在 *.wadcfgx* 文件的 **PublicConfig** / **WadCfg** 元素下添加一个新的 **Sinks** 节。 在 Visual Studio 中，*.wadcfgx* 文件存储在以下路径中：“云服务项目” > “角色” > “(RoleName)” > “diagnostics.wadcfgx”文件。

```xml
<SinksConfig>
  <Sink name="HotPath">
    <EventHub Url="https://diags-mycompany-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" />
  </Sink>
</SinksConfig>
```
```JSON
"SinksConfig": {
    "Sink": [
        {
            "name": "HotPath",
            "EventHub": {
                "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
                "SharedAccessKeyName": "SendRule"
            }
        }
    ]
}
```

在本示例中，事件中心 URL 设为事件中心的完全限定的命名空间：事件中心命名空间 +“/”+ 事件中心名称。  

事件中心 URL 在 [Azure 门户](http://go.microsoft.com/fwlink/?LinkID=213885)中的“事件中心”仪表板上显示。  

“接收器”名称可以设置为任何有效的字符串，前提是在整个配置文件中一致地使用相同的值。

> [!NOTE]
> 该部分中可能配置了其他接收器，例如 *applicationInsights*。 如果 **PrivateConfig** 部分中还对每个接收器进行了声明，Azure 诊断将允许定义一个或多个接收器。  
>
>

此外，必须在 *.wadcfgx* 配置文件的 **PrivateConfig** 部分中声明并定义事件中心接收器。

```XML
<PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <StorageAccount name="{account name}" key="{account key}" endpoint="{optional storage endpoint}" />
  <EventHub Url="https://diags-mycompany-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />
</PrivateConfig>
```
```JSON
{
    "storageAccountName": "{account name}",
    "storageAccountKey": "{account key}",
    "storageAccountEndPoint": "{optional storage endpoint}",
    "EventHub": {
        "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "{base64 encoded key}"
    }
}
```

`SharedAccessKeyName` 值必须与已在**事件中心**命名空间中定义的共享访问签名 (SAS) 密钥和策略匹配。 浏览到 [Azure 门户](https://portal.azure.com)中的“事件中心”仪表板，单击“配置”选项卡，并设置具有“发送”权限的命名策略（例如“SendRule”）。 **StorageAccount** 也已在 **PrivateConfig** 中声明。 如果这里的值有效，就不需要更改。 在本示例中，我们将值保留为空，这表示下游资产将设置这些值。 例如，*ServiceConfiguration.Cloud.cscfg* 环境配置文件会设置适合环境的名称和密钥。  

> [!WARNING]
> 事件中心 SAS 密钥以纯文本形式存储在 *.wadcfgx* 文件中。 通常，系统会将此密钥签入源代码管理，或作为生成服务器中的资产提供，因此应该适当地保护它。 建议在此处使用具有“仅发送”权限的 SAS 密钥，使恶意用户只能写入事件中心，而无法侦听或进行管理。
>
>

## <a name="configure-azure-diagnostics-to-send-logs-and-metrics-to-event-hubs"></a>配置 Azure 诊断以将日志和指标发送到事件中心
如前所述，所有默认和自定义诊断数据（即指标和日志）会在配置的时间间隔自动发送到 Azure 存储。 使用事件中心及任何其他接收器，可以指定层次结构中要发送到事件中心的任何根节点或叶节点。 这包括 ETW 事件、性能计数器、Windows 事件日志和应用程序日志。   

请务必考虑实际上应该将多少数据点传输到事件中心。 通常情况下，开发人员会传输必须快速使用和解释的低延迟热路径数据。 监视警报或自动缩放规则的系统即是一例。 开发人员也会配置备用的分析存储或搜索存储 — 例如，Azure 流分析、Elasticsearch、自定义监视系统或偏好的第三方监视系统。

以下是一些示例配置。

```xml
<PerformanceCounters scheduledTransferPeriod="PT1M" sinks="HotPath">
  <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
</PerformanceCounters>
```
```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "sinks": "HotPath",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        }
    ]
}
```

在以上示例中，接收器将应用到层次结构中的父级 PerformanceCounters 节点，这意味着所有子级 PerformanceCounters 将发送到事件中心。  

```xml
<PerformanceCounters scheduledTransferPeriod="PT1M">
  <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" sinks="HotPath" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" sinks="HotPath"/>
  <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" sinks="HotPath"/>
</PerformanceCounters>
```
```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M",
            "sinks": "HotPath"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Rejected",
            "sampleRate": "PT3M",
            "sinks": "HotPath"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Queued",
            "sampleRate": "PT3M",
            "sinks": "HotPath"
        }
    ]
}
```

在上述示例中，接收器只应用到三个计数器：“已排队的请求数”、“拒绝的请求数”和“处理器时间百分比”。  

以下示例演示开发人员如何限制发送的数据量，这些数据将作为此服务运行状况的关键指标。  

```XML
<Logs scheduledTransferPeriod="PT1M" sinks="HotPath" scheduledTransferLogLevelFilter="Error" />
```
```JSON
"Logs": {
    "scheduledTransferPeriod": "PT1M",
    "scheduledTransferLogLevelFilter": "Error",
    "sinks": "HotPath"
}
```

在此示例中，接收器已应用到日志，并且只筛选为错误级别跟踪。

## <a name="deploy-and-update-a-cloud-services-application-and-diagnostics-config"></a>部署和更新云服务应用程序与诊断配置
Visual Studio 提供最简单的路径供你部署应用程序和事件中心接收器配置。 要查看和编辑文件，请在 Visual Studio 中打开 *.wadcfgx* 文件，然后编辑并保存它。 路径为“云服务项目” > “角色” > “(RoleName)”“ > diagnostics.wadcfgx”。  

此时，Visual Studio、Visual Studio Team System 中的所有部署和部署更新操作，以及所有基于 MSBuild 并使用 **/t:publish** 目标的命令或脚本，都会在打包过程中纳入 *.wadcfgx*。 此外，部署和更新会使用 VM 上适当的 Azure 诊断代理扩展将文件部署到 Azure。

在部署应用程序与 Azure 诊断配置后，将立即在事件中心的仪表板中看到活动。 这意味着可以继续在侦听器客户端或选择的分析工具中查看热路径数据。  

在下图中，事件中心仪表板显示从晚上 11 点之后开始发送到事件中心且状态良好的诊断数据发送操作。 也就是使用更新的 *.wadcfgx* 文件部署应用程序，而且正确配置接收器的时候。

![][0]  

> [!NOTE]
> 更新 Azure 诊断配置文件 (.wadcfgx) 时，建议使用 Visual Studio 发布或 Windows PowerShell 脚本将更新推送到整个应用程序以及配置。  
>
>

## <a name="view-hot-path-data"></a>查看热路径数据
如前文所述，侦听和处理事件中心数据有许多用例。

一种简单的方法是创建小型测试控制台应用程序，用于侦听事件中心并打印输出流。 可在控制台应用程序中插入以下代码（[事件中心入门](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)中已详细说明）。  

请注意，控制台应用程序必须包含[事件处理器主机 NuGet 包](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/)。  

请记得将 **Main** 函数中尖括号内的值替换为资源的值。   

```csharp
//Console application code for EventHub test client
using System;
using System.Collections.Generic;
using System.Diagnostics;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using Microsoft.ServiceBus.Messaging;

namespace EventHubListener
{
    class SimpleEventProcessor : IEventProcessor
    {
        Stopwatch checkpointStopWatch;

        async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
            if (reason == CloseReason.Shutdown)
            {
                await context.CheckpointAsync();
            }
        }

        Task IEventProcessor.OpenAsync(PartitionContext context)
        {
            Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
            this.checkpointStopWatch = new Stopwatch();
            this.checkpointStopWatch.Start();
            return Task.FromResult<object>(null);
        }

        async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (EventData eventData in messages)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                    Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
                        context.Lease.PartitionId, data));

                foreach (var x in eventData.Properties)
                {
                    Console.WriteLine(string.Format("    {0} = {1}", x.Key, x.Value));
                }
            }

            //Call checkpoint every 5 minutes, so that worker can resume processing from 5 minutes back if it restarts.
            if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
            {
                await context.CheckpointAsync();
                this.checkpointStopWatch.Restart();
            }
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            string eventHubConnectionString = "Endpoint= <your connection string>”
            string eventHubName = "<Event hub name>";
            string storageAccountName = "<Storage account name>";
            string storageAccountKey = "<Storage account key>”;
            string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);

            string eventProcessorHostName = Guid.NewGuid().ToString();
            EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
            Console.WriteLine("Registering EventProcessor...");
            var options = new EventProcessorOptions();
            options.ExceptionReceived += (sender, e) => { Console.WriteLine(e.Exception); };
            eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>(options).Wait();

            Console.WriteLine("Receiving. Press enter key to stop worker.");
            Console.ReadLine();
            eventProcessorHost.UnregisterEventProcessorAsync().Wait();
        }
    }
}
```

## <a name="troubleshoot-event-hubs-sinks"></a>排查事件中心接收器问题
* 事件中心不按预期显示传入或传出事件活动。

    检查是否已成功预配事件中心。 *.wadcfgx* 中 **PrivateConfig** 部分的所有连接信息必须与门户中显示的资源值匹配。 请确保已在门户中定义 SAS 策略（本示例中为“SendRule”），并为其授予“发送”权限。  
* 进行更新后，事件中心不再显示传入或传出事件活动。

    首先，确保事件中心和配置信息如先前所述的那样准确无误。 有时，系统会在部署更新时重置 **PrivateConfig**。 建议的解决方法是在项目中对 *.wadcfgx* 进行所有更改，并推送完整的应用程序更新。 如果不可行，请确保诊断更新推送完整的 **PrivateConfig**，包括 SAS 密钥。  
* 我试过了上述建议，但事件中心仍无法正常运行。

    请尝试查看 Azure 存储表，其中包含日志和 Azure 诊断本身的错误：**WADDiagnosticInfrastructureLogsTable**。 一个选项是使用 [Azure 存储资源管理器](http://www.storageexplorer.com)等工具连接到此存储帐户，查看此表，并添加过去 24 小时的时间戳查询。 可以使用此工具导出 .csv 文件，并在 Microsoft Excel 之类的应用程序中打开它。 Excel 可以轻松地搜索电话卡字符串（如 **EventHubs**），以便查看系统报告了哪些错误。  

## <a name="next-steps"></a>后续步骤
•    [了解有关事件中心的详细信息](https://azure.microsoft.com/services/event-hubs/)

## <a name="appendix-complete-azure-diagnostics-configuration-file-wadcfgx-example"></a>附录：完整的 Azure 诊断配置文件 (.wadcfgx) 示例
```xml
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="applicationInsights.errors">
        <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" />
        <Directories scheduledTransferPeriod="PT1M">
          <IISLogs containerName="wad-iis-logfiles" />
          <FailedRequestLogs containerName="wad-failedrequestlogs" />
        </Directories>
        <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="HotPath">
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />
        </PerformanceCounters>
        <WindowsEventLog scheduledTransferPeriod="PT1M">
          <DataSource name="Application!*" />
        </WindowsEventLog>
        <CrashDumps>
          <CrashDumpConfiguration processName="WaIISHost.exe" />
          <CrashDumpConfiguration processName="WaWorkerHost.exe" />
          <CrashDumpConfiguration processName="w3wp.exe" />
        </CrashDumps>
        <Logs scheduledTransferPeriod="PT1M" sinks="HotPath" scheduledTransferLogLevelFilter="Error" />
      </DiagnosticMonitorConfiguration>
      <SinksConfig>
        <Sink name="HotPath">
          <EventHub Url="https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py" SharedAccessKeyName="SendRule" />
        </Sink>
        <Sink name="applicationInsights">
          <ApplicationInsights />
          <Channels>
            <Channel logLevel="Error" name="errors" />
          </Channels>
        </Sink>
      </SinksConfig>
    </WadCfg>
    <StorageAccount>ACCOUNT_NAME</StorageAccount>
  </PublicConfig>
  <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <StorageAccount name="{account name}" key="{account key}" endpoint="{storage endpoint}" />
    <EventHub Url="https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py" SharedAccessKeyName="SendRule" SharedAccessKey="YOUR_KEY_HERE" />
  </PrivateConfig>
  <IsEnabled>true</IsEnabled>
</DiagnosticsConfiguration>
```

本示例的补充 *ServiceConfiguration.Cloud.cscfg* 如下所示。

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceConfiguration serviceName="MyFixItCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="3" osVersion="*" schemaVersion="2015-04.2.6">
  <Role name="MyFixIt.WorkerRole">
    <Instances count="1" />
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="YOUR_CONNECTION_STRING" />
    </ConfigurationSettings>
  </Role>
</ServiceConfiguration>
```

虚拟机基于 Json 的等效设置如下所示：
```JSON
"settings": {
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 4096,
            "sinks": "applicationInsights.errors",
            "DiagnosticInfrastructureLogs": {
                "scheduledTransferLogLevelFilter": "Error"
            },
            "Directories": {
                "scheduledTransferPeriod": "PT1M",
                "IISLogs": {
                    "containerName": "wad-iis-logfiles"
                },
                "FailedRequestLogs": {
                    "containerName": "wad-failedrequestlogs"
                }
            },
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
                "sinks": "HotPath",
                "PerformanceCounterConfiguration": [
                    {
                        "counterSpecifier": "\\Memory\\Available MBytes",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\Web Service(_Total)\\Bytes Total/Sec",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\ASP.NET Applications(__Total__)\\Requests/Sec",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\ASP.NET Applications(__Total__)\\Errors Total/Sec",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\ASP.NET\\Requests Queued",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\ASP.NET\\Requests Rejected",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
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
                "scheduledTransferLogLevelFilter": "Error",
                "sinks": "HotPath"
            }
        },
        "SinksConfig": {
            "Sink": [
                {
                    "name": "HotPath",
                    "EventHub": {
                        "Url": "https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py",
                        "SharedAccessKeyName": "SendRule"
                    }
                },
                {
                    "name": "applicationInsights",
                    "ApplicationInsights": "",
                    "Channels": {
                        "Channel": [
                            {
                                "logLevel": "Error",
                                "name": "errors"
                            }
                        ]
                    }
                }
            ]
        }
    },
    "StorageAccount": "{account name}"
}


"protectedSettings": {
    "storageAccountName": "{account name}",
    "storageAccountKey": "{account key}",
    "storageAccountEndPoint": "{storage endpoint}",
    "EventHub": {
        "Url": "https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "YOUR_KEY_HERE"
    }
}
```

## <a name="next-steps"></a>后续步骤
访问以下链接可以了解有关事件中心的详细信息：

* [事件中心概述](../event-hubs/event-hubs-what-is-event-hubs.md)
* [创建事件中心](../event-hubs/event-hubs-create.md)
* [事件中心常见问题](../event-hubs/event-hubs-faq.md)

<!-- Images. -->
[0]: ../event-hubs/media/event-hubs-streaming-azure-diags-data/dashboard.png
