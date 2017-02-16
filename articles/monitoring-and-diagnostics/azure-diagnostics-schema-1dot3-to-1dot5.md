---
title: "Azure 诊断 1.3、1.4、1.5 配置架构 | Microsoft Docs"
description: "Microsoft Azure SDK 中附带 Azure 诊断 1.3、1.4 和 1.5 版本架构。"
services: multiple
documentationcenter: .net
author: rboucher
manager: carmonm
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: 8c9c9dea1248205aa6303e11e1166d5d38786c1b
ms.openlocfilehash: 550f328ddd03f67419a339aa995920b1feef8d4e


---
# <a name="azure-diagnostics-13-14-15-configuration-schema"></a>Azure 诊断 1.3、1.4 和 1.5 配置架构
> [!NOTE]
> Azure 诊断是一个组件，用于从 Azure 虚拟机、虚拟机规模集、Service Fabric 和云服务收集性能计数器和其他统计信息。  在使用以下某个服务时，才需要参阅此页。
>

Azure 诊断可以与其他 Microsoft 诊断产品（例如 Azure Monitor、Application Insights 和 Log Analytics）结合使用。

Azure 诊断配置文件用于在诊断监视器启动时设置诊断配置设置。  

此页面适用于版本 1.3、1.4 和 1.5（Azure SDK 2.4 及更高版本）。 已对较新的配置节进行注释，以显示其添加于哪一版本。  

 通过执行以下 PowerShell 命令来下载公共配置文件架构定义：  

```powershell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  

 有关使用 Azure 诊断的详细信息，请参阅 [Enabling Diagnostics in Azure Cloud Services](http://azure.microsoft.com/documentation/articles/cloud-services-dotnet-diagnostics/)（在 Azure 云服务中启用诊断）。  

## <a name="example-of-the-diagnostics-configuration-file"></a>诊断配置文件示例  
 以下示例演示典型的诊断配置文件：  

```xml  
<?xml version="1.0" encoding="utf-8"?>  
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">   
  <PublicConfig>  
    <WadCfg>  
      <DiagnosticMonitorConfiguration overallQuotaInMB="10000">  

        <PerformanceCounters scheduledTransferPeriod="PT1M">  
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />  
        </PerformanceCounters>  

        <Directories scheduledTransferPeriod="PT5M">  
          <IISLogs containerName="iislogs" />  
          <FailedRequestLogs containerName="iisfailed" />  

          <DataSources>  
            <DirectoryConfiguration containerName="mynewprocess">  
              <Absolute path="C:\MyNewProcess" expandEnvironment="false" />  
            </DirectoryConfiguration>  
            <DirectoryConfiguration containerName="badapp">  
              <Absolute path="%SYSTEMDRIVE%\BadApp" expandEnvironment="true" />  
            </DirectoryConfiguration>  
            <DirectoryConfiguration containerName="goodapp">  
              <LocalResource name="Skippy" relativePath="..\PeanutButter"/>  
            </DirectoryConfiguration>  
          </DataSources>  

        </Directories>  

        <EtwProviders>  
          <EtwEventSourceProviderConfiguration   
                       provider="MyProviderClass"   
                       scheduledTransferPeriod="PT5M">  
            <Event id="0"/>  
            <Event id="1" eventDestination="errorTable"/>  
            <DefaultEvents />  
          </EtwEventSourceProviderConfiguration>  
          <EtwManifestProviderConfiguration provider="5974b00b-84c2-44bc-9e58-3a2451b4e3ad" scheduledTransferLogLevelFilter="Information" scheduledTransferPeriod="PT2M">  
            <Event id="0"/>  
            <DefaultEvents eventDestination="defaultTable"/>  
          </EtwManifestProviderConfiguration>  
        </EtwProviders>  

        <WindowsEventLog scheduledTransferPeriod="PT5M">  
          <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>  
          <DataSource name="System!*[System[Provider[@Name='NTFS'] and (EventID=55)]]" />  
          <DataSource name="System!*[System[Provider[@Name='disk'] and (EventID=7 or EventID=52 or EventID=55)]]" />  
        </WindowsEventLog>  

        <Logs  bufferQuotaInMB="1024"   
             scheduledTransferPeriod="PT1M"   
             scheduledTransferLogLevelFilter="Verbose"   
             sinks="ApplicationInsights.AppLogs"/>  <!-- sinks attribute added in 1.5 -->  

        <CrashDumps containerName="wad-crashdumps" directoryQuotaPercentage="30" dumpType="Mini">  
          <CrashDumpConfiguration processName="mynewprocess.exe" />  
          <CrashDumpConfiguration processName="badapp.exe"/>  
        </CrashDumps>  

      </DiagnosticMonitorConfiguration>  

      <SinksConfig>   <!-- Added in 1.5 -->  
        <Sink name="ApplicationInsights">   
          <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>   
          <Channels>   
            <Channel logLevel="Error" name="Errors"  />   
            <Channel logLevel="Verbose" name="AppLogs"  />   
          </Channels>   
        </Sink>   
      </SinksConfig>   

    </WadCfg>  
  </PublicConfig>  

  <PrivateConfig>  <!-- Added in 1.3 -->  
    <StorageAccount name="" key="" endpoint="" />  
  </PrivateConfig>  
  <IsEnabled>true</IsEnabled>  
</DiagnosticsConfiguration>  

```  

## <a name="reading-this-page"></a>阅读此页  
 以下标记大致按上述示例中显示的顺序。  如果未看到预期的完整说明，请在页面中搜索元素或属性。  

## <a name="common-attribute-types"></a>常见的属性类型  
 **scheduledTransferPeriod** 属性显示在多个元素中。 它是到存储空间的计划传输之间的间隔，向上舍入为最接近的分钟数。 值是 [XML“持续时间数据类型。”](http://www.w3schools.com/schema/schema_dtypes_date.asp)

## <a name="diagnostics-configuration-namespace"></a>诊断配置命名空间  
 诊断配置文件的 XML 命名空间是：  

```  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  
```  

## <a name="diagnosticsconfiguration-element"></a>DiagnosticsConfiguration 元素  
 在版本 1.3 中添加。  

 诊断配置文件的顶级元素。  

|子元素|说明|  
|--------------------|-----------------|  
|**PublicConfig**|必需。 在此页的其他位置查看说明。|  
|**PrivateConfig**|可选。 在此页的其他位置查看说明。|  
|**IsEnabled**|布尔值。 在此页的其他位置查看说明。|  

## <a name="publicconfig-element"></a>PublicConfig 元素  
 描述公共诊断配置。  

|子元素|说明|  
|--------------------|-----------------|  
|**WadCfg**|必需。 在此页的其他位置查看说明。|  
|**StorageAccount**|用于存储数据的 Azure 存储帐户的名称。 执行 Set-AzureServiceDiagnosticsExtension cmdlet 时，还可能将其指定为参数。|  
|**LocalResourceDirectory**|Monitoring Agent 在其中存储事件数据的虚拟机上的目录。 如果不设置，则使用默认目录：<br /><br /> 对于辅助角色/Web 角色：`C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> 对于虚拟机：`C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> 必需属性：<br /><br /> - **path** - Azure 诊断将使用的系统上的目录。<br /><br /> - **expandEnvironment** - 控制是否可在路径名称中扩展环境变量。|  

## <a name="wadcfg-element"></a>WadCFG 元素  
 标识并配置要收集的遥测数据。  

|子元素|说明|  
|--------------------|-----------------|  
|**DiagnosticMonitorConfiguration**|必需元素。<br /><br /> 可选属性：<br /><br /> - **overallQuotaInMB** - 由 Azure 诊断收集的各类诊断数据使用的最大本地磁盘空间量。 默认设置是 5120 MB。<br /><br /> - **useProxyServer** - 将 Azure 诊断配置为使用在 IE 设置中设置的代理服务器设置。|  
|**CrashDumps**|在此页的其他位置查看说明。|  
|**DiagnosticInfrastructureLogs**|启用收集 Azure 诊断生成的日志。 诊断基础结构日志可用于解决诊断系统本身的故障。 可选属性：<br /><br /> - **scheduledTransferLogLevelFilter** - 配置收集的日志的最低严重级别。<br /><br /> - **scheduledTransferPeriod** - 到存储空间的计划传输之间的时间间隔，向上舍入为最接近的分钟数。 值是 [XML“持续时间数据类型。”](http://www.w3schools.com/schema/schema_dtypes_date.asp) |  
|**Directories**|在此页的其他位置查看说明。|  
|**EtwProviders**|在此页的其他位置查看说明。|  
|**指标**|在此页的其他位置查看说明。|  
|**PerformanceCounters**|在此页的其他位置查看说明。|  
|**WindowsEventLog**|在此页的其他位置查看说明。|  

## <a name="crashdumps-element"></a>CrashDumps 元素  
 启用故障转储收集。  

|属性|说明|  
|----------------|-----------------|  
|**containerName**|可选。 Azure 存储帐户中用于存储故障转储的 blob 容器的名称。|  
|**crashDumpType**|可选。  将 Azure 诊断配置为收集少量或完整故障转储。|  
|**directoryQuotaPercentage**|可选。  配置为 VM 上故障转储保留的 **overallQuotaInMB** 的百分比。|  

|子元素|说明|  
|--------------------|-----------------|  
|**CrashDumpConfiguration**|必需。 定义每个进程的配置值。<br /><br /> 还必需以下属性：<br /><br /> **processName** - 希望 Azure 诊断为其收集故障转储的进程的名称。|  

## <a name="directories-element"></a>Directories 元素  
 启用收集目录内容、IIS 失败的访问请求日志和/或 IIS 日志。  

 可选的 **scheduledTransferPeriod** 属性。 请参阅前面的说明。  

|子元素|说明|  
|--------------------|-----------------|  
|**DataSources**|要监视的目录的列表。|  
|**FailedRequestLogs**|在配置中包括此元素可启用收集有关对 IIS 站点或应用程序的失败请求日志。 还必须在 **Web.config** 文件中的 **system.WebServer** 下启用跟踪选项。|  
|**IISLogs**|在配置中包括此元素可启用收集 IIS 日志：<br /><br /> **containerName** - Azure 存储帐户中用于存储 IIS 日志的 blob 容器的名称。|  

## <a name="datasources-element"></a>DataSources 元素  
 要监视的目录的列表。  

|子元素|说明|  
|--------------------|-----------------|  
|**DirectoryConfiguration**|必需。 必需属性：<br /><br /> **containerName** - Azure 存储帐户中用于存储日志文件的 blob 容器的名称。|  

## <a name="directoryconfiguration-element"></a>DirectoryConfiguration 元素  
 可能包括 **Absolute** 或 **LocalResource** 元素，但不能同时包含两者。  

|子元素|说明|  
|--------------------|-----------------|  
|**Absolute**|要监视的目录的绝对路径。 需要以下属性：<br /><br /> - **Path** - 要监视的目录的绝对路径。<br /><br /> - **expandEnvironment** - 配置是否在路径中扩展环境变量。|  
|**LocalResource**|要监视的本地资源的相对路径。 必需属性：<br /><br /> - **Name** - 包含要监视的目录的本地资源<br /><br /> - **relativePath** - 包含要监视的目录的名称的相对路径|  

## <a name="etwproviders-element"></a>EtwProviders 元素  
 配置从基于 EventSource 和/或 ETW 清单的提供程序收集 ETW 事件。  

|子元素|说明|  
|--------------------|-----------------|  
|**EtwEventSourceProviderConfiguration**|配置收集从 [EventSource 类](http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx)生成的事件。 必需属性：<br /><br /> **provider** - EventSource 事件的类名称。<br /><br /> 可选属性：<br /><br /> - **scheduledTransferLogLevelFilter** - 要传输到存储帐户的最低严重级别。<br /><br /> - **scheduledTransferPeriod** - 到存储空间的计划传输之间的时间间隔，向上舍入为最接近的分钟数。 值是 [XML“持续时间数据类型。”](http://www.w3schools.com/schema/schema_dtypes_date.asp) |  
|**EtwManifestProviderConfiguration**|必需属性：<br /><br /> **provider** - 事件提供程序的 GUID<br /><br /> 可选属性：<br /><br /> - **scheduledTransferLogLevelFilter** - 要传输到存储帐户的最低严重级别。<br /><br /> - **scheduledTransferPeriod** - 到存储空间的计划传输之间的时间间隔，向上舍入为最接近的分钟数。 值是 [XML“持续时间数据类型。”](http://www.w3schools.com/schema/schema_dtypes_date.asp) |  

## <a name="etweventsourceproviderconfiguration-element"></a>EtwEventSourceProviderConfiguration 元素  
 配置收集从 [EventSource 类](http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx)生成的事件。  

|子元素|说明|  
|--------------------|-----------------|  
|**DefaultEvents**|可选属性：<br/><br/> **eventDestination** -存储事件的表的名称|  
|**Event**|必需属性：<br /><br /> **id** - 事件 ID。<br /><br /> 可选属性：<br /><br /> **eventDestination** -存储事件的表的名称|  

## <a name="etwmanifestproviderconfiguration-element"></a>EtwManifestProviderConfiguration 元素  

|子元素|说明|  
|--------------------|-----------------|  
|**DefaultEvents**|可选属性：<br /><br /> **eventDestination** -存储事件的表的名称|  
|**Event**|必需属性：<br /><br /> **id** - 事件 ID。<br /><br /> 可选属性：<br /><br /> **eventDestination** -存储事件的表的名称|  

## <a name="metrics-element"></a>Metrics 元素  
 可以生成针对快速查询进行优化的性能计数器表。 在 **PerformanceCounters** 元素中定义的每个性能计数器除存储在性能计数器表内外，还存储在度量值表中。  

 必需 **resourceId** 属性。  这是要在其中部署 Azure 诊断的虚拟机的资源 ID。 从 [Azure 门户](https://portal.azure.com)获取 **resourceID**。 选择“浏览” -> “资源组” -> “<名称\>”。 单击“属性”磁贴，并从“ID”字段复制值。  

|子元素|说明|  
|--------------------|-----------------|  
|**MetricAggregation**|必需属性：<br /><br /> **scheduledTransferPeriod** - 到存储空间的计划传输之间的时间间隔，向上舍入为最接近的分钟数。 值是 [XML“持续时间数据类型。”](http://www.w3schools.com/schema/schema_dtypes_date.asp) |  

## <a name="performancecounters-element"></a>PerformanceCounters 元素  
 启用性能计数器收集。  

 可选属性：  

 可选的 **scheduledTransferPeriod** 属性。 请参阅前面的说明。

|子元素|说明|  
|-------------------|-----------------|  
|**PerformanceCounterConfiguration**|需要以下属性：<br /><br /> - **counterSpecifier** - 性能计数器的名称。 例如，`\Processor(_Total)\% Processor Time`。 若要获取性能计数器列表，请在主机上运行 `typeperf` 命令。<br /><br /> - **sampleRate** - 对计数器进行采样的频率。<br /><br /> 可选属性：<br /><br /> **unit** - 计数器的度量单位。|  

## <a name="windowseventlog-element"></a>WindowsEventLog 元素  
 启用收集 Windows 事件日志。  

 可选的 **scheduledTransferPeriod** 属性。 请参阅前面的说明。  

|子元素|说明|  
|-------------------|-----------------|  
|**DataSource**|要收集的 Windows 事件日志。 必需属性：<br /><br /> **name** - 描述要收集的 Windows 事件的 XPath 查询。 例如：<br /><br /> `Application!*[Application[(Level <=3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[Security[(Level <= 3)]`<br /><br /> 若要收集所有事件，请指定“*”。|  

## <a name="logs-element"></a>Logs 元素  
 在 1.0 和 1.1 版本中提供。 1.2 中不提供。 在 1.3 中重新添加。  

 定义基本 Azure 日志的缓冲区配置。  

|属性|类型|说明|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|**unsignedInt**|可选。 指定可用于存储指定数据的文件系统存储最大容量。<br /><br /> 默认值为 0。|  
|**scheduledTransferLogLevelFilterr**|**string**|可选。 指定传输的日志条目的最低严重级别。 默认值是“未定义”，这将传输所有日志。 其他可能的值是（按信息严重级别从高到低排序）“详细”、“信息”、“警告”、“错误”和“严重”。|  
|**scheduledTransferPeriod**|**duration**|可选。 指定计划的数据传输之间的时间间隔，向上舍入为最接近的分钟数。<br /><br /> 默认是 PT0S。|  
|**sinks** 在 1.5 中添加|**string**|可选。 指向同时要发送诊断数据的接收器位置。 例如，Application Insights。|  

## <a name="sinksconfig-element"></a>SinksConfig 元素  
 向其中发送诊断数据的位置的列表以及与这些位置关联的配置。  

|元素名称|说明|  
|------------------|-----------------|  
|**接收器**|在此页的其他位置查看说明。|  

## <a name="sink-element"></a>Sink 元素  
 在版本 1.5 中添加。  

 定义向其中发送诊断数据的位置。 例如，Application Insights 服务。  

|属性|类型|说明|  
|---------------|----------|-----------------|  
|**name**|字符串|标识 sinkname 的字符串。|  

|元素|类型|说明|  
|-------------|----------|-----------------|  
|**Application Insights**|字符串|仅在将数据发送到 Application Insights 时使用。 包含有权访问的有效 Application Insights 帐户的检测密钥。|  
|**通道**|字符串|每个对应一个流处理的其他筛选|  

## <a name="channels-element"></a>Channels 元素  
 在版本 1.5 中添加。  

 定义通过接收器的日志数据流的筛选器。  

|元素|类型|说明|  
|-------------|----------|-----------------|  
|**Channel**|字符串|在此页的其他位置查看说明。|  

## <a name="channel-element"></a>Channel 元素  
 在版本 1.5 中添加。  

 定义向其中发送诊断数据的位置。 例如，Application Insights 服务。  

|属性|类型|说明|  
|----------------|----------|-----------------|  
|**logLevel**|**string**|指定传输的日志条目的最低严重级别。 默认值是“未定义”，这将传输所有日志。 其他可能的值是（按信息严重级别从高到低排序）“详细”、“信息”、“警告”、“错误”和“严重”。|  
|**name**|**string**|要引用的通道的唯一名称|  

## <a name="privateconfig-element"></a>PrivateConfig 元素  
 在版本 1.3 中添加。  

 可选  

 存储存储帐户的私有详细信息（名称、密钥和终结点）。 此信息发送到虚拟机，但不能从中检索。  

|子元素|说明|  
|--------------------|-----------------|  
|**StorageAccount**|要使用的存储帐户。 需要以下属性<br /><br /> - **name** - 存储帐户的名称。<br /><br /> - **key** - 存储帐户的密钥。<br /><br /> - **endpoint** - 用于访问存储帐户的终结点。|  

## <a name="isenabled-element"></a>IsEnabled 元素  
 布尔值。 使用 `true` 启用诊断或使用 `false` 禁用诊断。



<!--HONumber=Jan17_HO5-->


