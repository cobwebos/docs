<properties
   pageTitle="配置 Azure 诊断以将数据发送到 Application Insights | Microsoft Azure"
   description="更新 Azure 诊断公共配置，以将数据发送到 Application Insights。"
   services="multiple"
   documentationCenter=".net"
   authors="sbtron"
   manager=""
   editor="" />
<tags
   ms.service="application-insights"
   ms.date="12/15/2015"
   wacn.date="" />

# 配置 Azure 诊断以将数据发送到 Application Insights

Azure 诊断会将数据存储到 Azure 存储空间表。不过，你也可以在使用 Azure 诊断扩展 1.5 或更高版本时，通过在配置中配置“接收器”和“通道”，将全部数据或数据的子集传送到 Application Insights。

本文说明如何为 Azure 诊断扩展创建公共配置，以便将其配置为向 Application Insights 发送数据。

## 将 Application Insights 配置为接收器

Azure 诊断扩展 1.5 在公共配置中引入了 **<SinksConfig>** 元素。此元素定义可将 Azure 诊断数据发送到的其他*接收器*位置。你可以指定要将 Azure 诊断数据作为此 **<SinksConfig>** 一部分发送到的 Application Insights 资源的详细信息。**SinksConfig** 的示例如下所示 -

	<SinksConfig>
        <Sink name="ApplicationInsights">
          <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
          <Channels>
            <Channel logLevel="Error" name="MyTopDiagData"  />
            <Channel logLevel="Verbose" name="MyLogData"  />
          </Channels>
        </Sink>
      </SinksConfig> 

**Sink** 元素的 *name* 属性指定用于唯一引用接收器的字符串值。**ApplicationInsights** 元素指定要将 Azure 诊断数据发送到的 Application Insights 资源的检测键。如果你没有 Application Insights 资源，请参阅[创建新的 Application Insights 资源](app-insights-create-new-resource.md)，以了解有关创建资源和获取检测键的详细信息。

如果你正在使用 Azure SDK 2.8 开发云服务项目，则在打包云服务项目时，系统会根据 **APPINSIGHTS\_INSTRUMENTATIONKEY** 服务配置设置，在公共配置中自动填充此检测键。请参阅[将 Application Insights 与 Azure 诊断配合使用以排查云服务问题](cloud-services-dotnet-diagnostics-applicationinsights.md)。

**Channels** 元素可让你针对要发送到接收器的数据定义一个或多个 **Channels** 元素。通道的作用类似于筛选器，可让你选择要发送到接收器的特定日志级别。例如，你可以收集详细日志并将其发送到存储，但你可以选择定义具有“错误”日志级别的通道，当你通过该通道发送日志时，只会将错误日志发送到该接收器。
**Channel** 的 *name* 属性用于唯一引用该通道。 
*loglevel* 属性可让你指定通道允许的日志级别。可用日志级别从最多信息到最少信息的顺序依次为：
 - 详细 
 - 信息
 - 警告
 - 错误
 - 关键

## 将数据发送到 Application Insights 接收器
定义 Application Insights 接收器之后，可以通过将 *sink* 属性添加到 **DiagnosticMonitorConfiguration** 节点下的元素，来向该接收器发送数据。将 *sinks* 元素添加到每个节点可以指定你要从该节点及其下的任何节点收集数据，并发送到指定的接收器。

例如，如果你要发送 Azure 诊断所要收集的所有数据，则可以将 *sink* 属性直接添加到 **DiagnosticMonitorConfiguration** 节点。将 *sinks* 的值设置为 **SinkConfig** 中指定的接收器名称。

	<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights">

如果你只想将错误日志发送到 Application Insights 接收器，则可以将 *sinks* 的值设置为接收器名称后接通道名称，并以句点（“.”）分隔。例如，若只要将错误日志发送到 Application Insights 接收器，请使用上述 SinksConfig 中定义的 MyTopDiagdata 通道。
 
	<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights.MyTopDiagdata">

如果你只想将详细应用程序日志发送到 Application Insights，请将 *sinks* 属性添加到 **Logs** 节点。
	
	<Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose" sinks="ApplicationInsights.MyLogData"/>

你还可以在层次结构中不同级别下的配置中包含多个接收器。在此情况下，在层次结构顶层指定的接收器将用作全局设置，而在单个元素中指定的接收器将用作该全局设置的覆盖。

以下公共配置文件的完整示例会将所有错误发送到 Application Insights（在 **DiagnosticMonitorConfiguration** 节点中指定），此外，还会发送应用程序日志的详细信息级别记录（在 **Logs** 节点中指定）。

    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096"
           sinks="ApplicationInsights.MyTopDiagData"> <!-- All info below sent to this channel -->
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

![诊断公共配置](./media/azure-diagnostics-configure-applicationinsights/diagnostics-publicconfig.png)

需要注意此项功能的一些限制

- 通道只能用于处理日志类型，而不能处理性能计数器。如果对性能计数器元素指定通道，将会忽略该通道。 
- 通道的日志级别不能超过 Azure 诊断所要收集的日志级别。例如：不能在 Logs 元素中收集应用程序日志错误，并且不能尝试向 Application Insight 接收器发送详细日志。*scheduledTransferLogLevelFilter* 属性收集的日志数始终必须等于或大于你尝试发送到接收器的日志数。 
- 无法将 Azure 诊断扩展收集的任何 Blob 数据发送到 Application Insights。例如，*Directories* 节点下指定的任何数据。对于故障转储，实际故障转储仍会发送到 Blob 存储，并只会将生成了故障转储的通知发送到 Application Insights。 


## 后续步骤

- 使用 [PowerShell](/documentation/articles/cloud-services-diagnostics-powershell) 为应用程序启用 Azure 诊断扩展。 
- 使用 [Visual Studio](/documentation/articles/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines) 为应用程序启用 Azure 诊断扩展。 

<!---HONumber=Mooncake_0104_2016-->