---
title: "Azure 诊断 1.2 配置架构 | Microsoft Docs"
description: "只有在用户将 Azure SDK 2.5 与 Azure 虚拟机、虚拟机规模集、Service Fabric 或云服务配合使用时，才相关。"
services: monitoring-and-diagnostics
documentationcenter: .net
author: rboucher
manager: carmonm
editor: 
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/09/2017
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: 66f733d7602a8d26627fcc205f357e7a4a266d11
ms.openlocfilehash: b76ef954d8a00e190817e3d7f8e2b064210d0357
ms.lasthandoff: 02/22/2017


---
# <a name="azure-diagnostics-12-configuration-schema"></a>Azure 诊断 1.2 配置架构
> [!NOTE]
> Azure 诊断是一个组件，用于从 Azure 虚拟机、虚拟机规模集、Service Fabric 和云服务收集性能计数器和其他统计信息。  在使用以下某个服务时，才需要参阅此页。
>

Azure 诊断可以与其他 Microsoft 诊断产品（例如 Azure Monitor、Application Insights 和 Log Analytics）结合使用。

此架构定义了多种可能的值，可以使用这些值在诊断监视器启动时初始化诊断配置设置。  


 通过执行以下 PowerShell 命令来下载公共配置文件架构定义：  

```PowerShell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  

 有关使用 Azure 诊断的详细信息，请参阅 [Enabling Diagnostics in Azure Cloud Services](http://azure.microsoft.com/documentation/articles/cloud-services-dotnet-diagnostics/)（在 Azure 云服务中启用诊断）。  

## <a name="example-of-the-diagnostics-configuration-file"></a>诊断配置文件示例  
 以下示例演示典型的诊断配置文件：  

```xml
<?xml version="1.0" encoding="utf-8"?>  
<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">  
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
        <EtwEventSourceProviderConfiguration provider="MyProviderClass" scheduledTransferPeriod="PT5M">  
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
      <CrashDumps containerName="wad-crashdumps" directoryQuotaPercentage="30" dumpType="Mini">  
        <CrashDumpConfiguration processName="mynewprocess.exe" />  
        <CrashDumpConfiguration processName="badapp.exe"/>  
      </CrashDumps>  
    </DiagnosticMonitorConfiguration>  
  </WadCfg>  
</PublicConfig>  

```  

## <a name="diagnostics-configuration-namespace"></a>诊断配置命名空间  
 诊断配置文件的 XML 命名空间是：  

```  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  
```  

## <a name="publicconfig-element"></a>PublicConfig 元素  
 诊断配置文件的顶级元素。 下表介绍配置文件的元素。  

|元素名称|说明|  
|------------------|-----------------|  
|**WadCfg**|必需。 待收集的遥测数据的配置设置。|  
|**StorageAccount**|用于存储数据的 Azure 存储帐户的名称。 执行 Set-AzureServiceDiagnosticsExtension cmdlet 时，还可能将其指定为参数。|  
|**LocalResourceDirectory**|Monitoring Agent 要用于存储事件数据的虚拟机上的目录。 如果不设置，则使用默认目录：<br /><br /> 对于辅助角色/Web 角色：`C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> 对于虚拟机：`C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> 必需属性：<br /><br /> -                      **path** - Azure 诊断将使用的系统上的目录。<br /><br /> -                      **expandEnvironment** - 控制是否在路径名称中扩展环境变量。|  

## <a name="wadcfg-element"></a>WadCFG 元素  
定义待收集的遥测数据的配置设置。 下表介绍子元素：  

|元素名称|说明|  
|------------------|-----------------|  
|**DiagnosticMonitorConfiguration**|必需。 可选属性：<br /><br /> -                     **overallQuotaInMB** - 由 Azure 诊断收集的各类诊断数据使用的最大本地磁盘空间量。 默认设置是 5120 MB。<br /><br /> -                     **useProxyServer** - 将 Azure 诊断配置为使用在 IE 设置中设置的代理服务器设置。|  
|**CrashDumps**|启用故障转储收集。 可选属性：<br /><br /> -                     **containerName** - Azure 存储帐户中用于存储故障转储的 Blob 容器的名称。<br /><br /> -                     **crashDumpType** - 将 Azure 诊断配置为收集少量或完整故障转储。<br /><br /> -                     **directoryQuotaPercentage** - 配置 VM 上为故障转储保留的 **overallQuotaInMB** 的百分比。|  
|**DiagnosticInfrastructureLogs**|启用收集 Azure 诊断生成的日志。 诊断基础结构日志可用于解决诊断系统本身的故障。 可选属性：<br /><br /> -                     **scheduledTransferLogLevelFilter** - 配置收集的日志的最低严重级别。<br /><br /> -                     **scheduledTransferPeriod** - 到存储空间的计划传输之间的时间间隔，向上舍入为最接近的分钟数。 值是 [XML“持续时间数据类型。”](http://www.w3schools.com/schema/schema_dtypes_date.asp)|  
|**Directories**|启用收集目录内容、IIS 失败的访问请求日志和/或 IIS 日志。 可选属性：<br /><br /> **scheduledTransferPeriod** - 到存储空间的计划传输之间的时间间隔，向上舍入为最接近的分钟数。 值是 [XML“持续时间数据类型。”](http://www.w3schools.com/schema/schema_dtypes_date.asp)|  
|**EtwProviders**|配置从基于 EventSource 和/或 ETW 清单的提供程序收集 ETW 事件。|  
|**指标**|此元素可以生成针对快速查询进行优化的性能计数器表。 在 **PerformanceCounters** 元素中定义的每个性能计数器除存储在性能计数器表内外，还存储在度量值表中。 必需属性：<br /><br /> **resourceId** - 这是要将 Azure 诊断部署到的虚拟机的资源 ID。 从 [Azure 门户](https://portal.azure.com)获取 **resourceID**。 选择“浏览” -> “资源组” -> “<名称\>”。 单击“属性”磁贴，并从“ID”字段复制值。|  
|**PerformanceCounters**|启用性能计数器收集。 可选属性：<br /><br /> **scheduledTransferPeriod** - 到存储空间的计划传输之间的时间间隔，向上舍入为最接近的分钟数。 值是 [XML“持续时间数据类型。”](http://www.w3schools.com/schema/schema_dtypes_date.asp)|  
|**WindowsEventLog**|启用收集 Windows 事件日志。 可选属性：<br /><br /> **scheduledTransferPeriod** - 到存储空间的计划传输之间的时间间隔，向上舍入为最接近的分钟数。 值是 [XML“持续时间数据类型。”](http://www.w3schools.com/schema/schema_dtypes_date.asp)|  

## <a name="crashdumps-element"></a>CrashDumps 元素  
 启用故障转储收集。 下表介绍子元素：  

|元素名称|说明|  
|------------------|-----------------|  
|**CrashDumpConfiguration**|必需。 必需属性：<br /><br /> **processName** - 希望 Azure 诊断为其收集故障转储的进程的名称。|  
|**crashDumpType**|将 Azure 诊断配置为收集少量或完整故障转储。|  
|**directoryQuotaPercentage**|配置为 VM 上故障转储保留的 **overallQuotaInMB** 的百分比。|  

## <a name="directories-element"></a>Directories 元素  
 启用收集目录内容、IIS 失败的访问请求日志和/或 IIS 日志。 下表介绍子元素：  

|元素名称|说明|  
|------------------|-----------------|  
|**DataSources**|要监视的目录的列表。|  
|**FailedRequestLogs**|在配置中包括此元素可启用收集有关对 IIS 站点或应用程序的失败请求日志。 还必须在 **Web.config** 文件中的 **system.WebServer** 下启用跟踪选项。|  
|**IISLogs**|在配置中包括此元素可启用收集 IIS 日志：<br /><br /> **containerName** - Azure 存储帐户中用于存储 IIS 日志的 blob 容器的名称。|  

## <a name="datasources-element"></a>DataSources 元素  
 要监视的目录的列表。 下表介绍子元素：  

|元素名称|说明|  
|------------------|-----------------|  
|**DirectoryConfiguration**|必需。 必需属性：<br /><br /> **containerName** - Azure 存储帐户中用于存储日志文件的 blob 容器的名称。|  

## <a name="directoryconfiguration-element"></a>DirectoryConfiguration 元素  
 **DirectoryConfiguration** 可能包括 **Absolute** 或 **LocalResource** 元素，但不能同时包含两者。 下表介绍子元素：  

|元素名称|说明|  
|------------------|-----------------|  
|**Absolute**|要监视的目录的绝对路径。 需要以下属性：<br /><br /> -                     **Path** - 要监视的目录的绝对路径。<br /><br /> -                      **expandEnvironment** - 配置是否在路径中扩展环境变量。|  
|**LocalResource**|要监视的本地资源的相对路径。 必需属性：<br /><br /> -                     **Name** - 包含要监视的目录的本地资源<br /><br /> -                     **relativePath** - 包含要监视的目录的名称的相对路径|  

## <a name="etwproviders-element"></a>EtwProviders 元素  
 配置从基于 EventSource 和/或 ETW 清单的提供程序收集 ETW 事件。 下表介绍子元素：  

|元素名称|说明|  
|------------------|-----------------|  
|**EtwEventSourceProviderConfiguration**|配置收集从 [EventSource 类](http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx)生成的事件。 必需属性：<br /><br /> **provider** - EventSource 事件的类名称。<br /><br /> 可选属性：<br /><br /> -                     **scheduledTransferLogLevelFilter** - 要传输到存储帐户的最低严重级别。<br /><br /> -                     **scheduledTransferPeriod** - 到存储空间的计划传输之间的时间间隔，向上舍入为最接近的分钟数。 值是 [XML 持续时间数据类型](http://www.w3schools.com/schema/schema_dtypes_date.asp)。|  
|**EtwManifestProviderConfiguration**|必需属性：<br /><br /> **provider** - 事件提供程序的 GUID<br /><br /> 可选属性：<br /><br /> - **scheduledTransferLogLevelFilter** - 要传输到存储帐户的最低严重级别。<br /><br /> -                     **scheduledTransferPeriod** - 到存储空间的计划传输之间的时间间隔，向上舍入为最接近的分钟数。 值是 [XML 持续时间数据类型](http://www.w3schools.com/schema/schema_dtypes_date.asp)。|  

## <a name="etweventsourceproviderconfiguration-element"></a>EtwEventSourceProviderConfiguration 元素  
 配置收集从 [EventSource 类](http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx)生成的事件。 下表介绍子元素：  

|元素名称|说明|  
|------------------|-----------------|  
|**DefaultEvents**|可选属性：<br /><br /> **eventDestination** -存储事件的表的名称|  
|**Event**|必需属性：<br /><br /> **id** - 事件 ID。<br /><br /> 可选属性：<br /><br /> **eventDestination** -存储事件的表的名称|  

## <a name="etwmanifestproviderconfiguration-element"></a>EtwManifestProviderConfiguration 元素  
 下表介绍子元素：  

|元素名称|说明|  
|------------------|-----------------|  
|**DefaultEvents**|可选属性：<br /><br /> **eventDestination** -存储事件的表的名称|  
|**Event**|必需属性：<br /><br /> **id** - 事件 ID。<br /><br /> 可选属性：<br /><br /> **eventDestination** -存储事件的表的名称|  

## <a name="metrics-element"></a>Metrics 元素  
 可以生成针对快速查询进行优化的性能计数器表。 下表介绍子元素：  

|元素名称|说明|  
|------------------|-----------------|  
|**MetricAggregation**|必需属性：<br /><br /> **scheduledTransferPeriod** - 到存储空间的计划传输之间的时间间隔，向上舍入为最接近的分钟数。 值是 [XML 持续时间数据类型](http://www.w3schools.com/schema/schema_dtypes_date.asp)。|  

## <a name="performancecounters-element"></a>PerformanceCounters 元素  
 启用性能计数器收集。 下表介绍子元素：  

|元素名称|说明|  
|------------------|-----------------|  
|**PerformanceCounterConfiguration**|需要以下属性：<br /><br /> -                     **counterSpecifier** - 性能计数器的名称。 例如，`\Processor(_Total)\% Processor Time`。 若要获取性能计数器列表，请在主机上运行 `typeperf` 命令。<br /><br /> -                     **sampleRate** - 对计数器进行采样的频率。<br /><br /> 可选属性：<br /><br /> **unit** - 计数器的度量单位。|  

## <a name="performancecounterconfiguration-element"></a>PerformanceCounterConfiguration 元素  
 下表介绍子元素：  

|元素名称|说明|  
|------------------|-----------------|  
|**批注**|必需属性：<br /><br /> **displayName** - 计数器的显示名称<br /><br /> 可选属性：<br /><br /> **locale** - 显示计数器名称时使用的区域设置|  

## <a name="windowseventlog-element"></a>WindowsEventLog 元素  
 下表介绍子元素：  

|元素名称|说明|  
|------------------|-----------------|  
|**DataSource**|要收集的 Windows 事件日志。 必需属性：<br /><br /> **name** - 描述要收集的 Windows 事件的 XPath 查询。 例如：<br /><br /> `Application!*[System[(Level >= 3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[System[(Level >= 3]]`<br /><br /> 若要收集所有事件，请指定“*”。|

