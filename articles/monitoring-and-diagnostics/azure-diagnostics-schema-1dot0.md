---
title: "Azure 诊断 1.0 配置架构 | Microsoft Docs"
description: "只有在用户将 Azure SDK 2.4 及更低版本 与 Azure 虚拟机、虚拟机规模集、Service Fabric 或云服务配合使用时，才相关。"
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
ms.openlocfilehash: 92bb7ca3414962f18da8505b87ca23f0b704e811


---
# <a name="azure-diagnostics-10-configuration-schema"></a>Azure 诊断 1.0 配置架构
> [!NOTE]
> Azure 诊断是一个组件，用于从 Azure 虚拟机、虚拟机规模集、Service Fabric 和云服务收集性能计数器和其他统计信息。  在使用以下某个服务时，才需要参阅此页。
>

Azure 诊断可以与其他 Microsoft 诊断产品（例如 Azure Monitor、Application Insights 和 Log Analytics）结合使用。

Azure 诊断配置文件定义用于初始化诊断监视器的值。 此文件用于在诊断监视器启动时初始化诊断配置设置。  

 默认情况下，Azure 诊断配置架构文件将安装到 `C:\Program Files\Microsoft SDKs\Azure\.NET SDK\<version>\schemas` 目录。 将 `<version>` 替换为 [Azure SDK](http://www.windowsazure.com/develop/downloads/) 的已安装版本。  

> [!NOTE]
>  诊断配置文件通常用于启动任务，此类任务需要在启动进程之前收集诊断数据。 有关使用 Azure 诊断的详细信息，请参阅[使用 Azure 诊断收集日志记录数据](assetId:///83a91c23-5ca2-4fc9-8df3-62036c37a3d7)。  

## <a name="example-of-the-diagnostics-configuration-file"></a>诊断配置文件示例  
 以下示例演示典型的诊断配置文件：  

```xml  
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticMonitorConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"  
      configurationChangePollInterval="PT1M"  
      overallQuotaInMB="4096">  
   <DiagnosticInfrastructureLogs bufferQuotaInMB="1024"  
      scheduledTransferLogLevelFilter="Verbose"  
      scheduledTransferPeriod="PT1M" />  
   <Logs bufferQuotaInMB="1024"  
      scheduledTransferLogLevelFilter="Verbose"  
      scheduledTransferPeriod="PT1M" />  

   <Directories bufferQuotaInMB="1024"   
      scheduledTransferPeriod="PT1M">  

      <!-- These three elements specify the special directories   
           that are set up for the log types -->  
      <CrashDumps container="wad-crash-dumps" directoryQuotaInMB="256" />  
      <FailedRequestLogs container="wad-frq" directoryQuotaInMB="256" />  
      <IISLogs container="wad-iis" directoryQuotaInMB="256" />  

      <!-- For regular directories the DataSources element is used -->  
      <DataSources>  
         <DirectoryConfiguration container="wad-panther" directoryQuotaInMB="128">  
            <!-- Absolute specifies an absolute path with optional environment expansion -->  
            <Absolute expandEnvironment="true" path="%SystemRoot%\system32\sysprep\Panther" />  
         </DirectoryConfiguration>  
         <DirectoryConfiguration container="wad-custom" directoryQuotaInMB="128">  
            <!-- LocalResource specifies a path relative to a local   
                 resource defined in the service definition -->  
            <LocalResource name="MyLoggingLocalResource" relativePath="logs" />  
         </DirectoryConfiguration>  
      </DataSources>  
   </Directories>  

   <PerformanceCounters bufferQuotaInMB="512" scheduledTransferPeriod="PT1M">  
      <!-- The counter specifier is in the same format as the imperative   
           diagnostics configuration API -->  
      <PerformanceCounterConfiguration   
         counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT5S" />  
   </PerformanceCounters>  

   <WindowsEventLog bufferQuotaInMB="512"  
      scheduledTransferLogLevelFilter="Verbose"  
      scheduledTransferPeriod="PT1M">  
      <!-- The event log name is in the same format as the imperative   
           diagnostics configuration API -->  
      <DataSource name="System!*" />  
   </WindowsEventLog>  
</DiagnosticMonitorConfiguration>  
```  

## <a name="diagnosticsconfiguration-namespace"></a>DiagnosticsConfiguration 命名空间  
 诊断配置文件的 XML 命名空间是：  

```  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  
```  

## <a name="schema-elements"></a>架构元素  
 诊断配置文件包括以下元素。


## <a name="diagnosticmonitorconfiguration-element"></a>DiagnosticMonitorConfiguration 元素  
诊断配置文件的顶级元素。  

属性：

|属性  |类型   |必选| 默认 | 说明|  
|-----------|-------|--------|---------|------------|  
|**configurationChangePollInterval**|duration|可选 | PT1M| 指定诊断监视器轮询诊断配置更改的时间间隔。|  
|**overallQuotaInMB**|unsignedInt|可选| 4000 MB。 提供的值不得超过此值 |为所有日志记录缓冲区分配的文件系统存储的总量。|  

## <a name="diagnosticinfrastructurelogs-element"></a>DiagnosticInfrastructureLogs 元素  
定义由基础诊断基础结构生成的日志的缓冲区配置。

父元素：[DiagnosticMonitorConfiguration 元素](#DiagnosticMonitorConfiguration)。  

属性：

|属性|类型|说明|  
|---------|----|-----------------|  
|**bufferQuotaInMB**|unsignedInt|可选。 指定可用于存储指定数据的文件系统存储最大容量。<br /><br /> 默认值为 0。|  
|**scheduledTransferLogLevelFilter**|字符串|可选。 指定传输的日志条目的最低严重级别。 默认值为“未定义”。 其他可能的值为“详细”、“信息”、“警告”、“错误”和“严重”。|  
|**scheduledTransferPeriod**|duration|可选。 指定计划的数据传输之间的时间间隔，向上舍入为最接近的分钟数。<br /><br /> 默认是 PT0S。|  

## <a name="logs-element"></a>Logs 元素  
 定义基本 Azure 日志的缓冲区配置。

 父元素：[DiagnosticMonitorConfiguration 元素](#DiagnosticMonitorConfiguration)。  

属性：  

|属性|类型|说明|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|可选。 指定可用于存储指定数据的文件系统存储最大容量。<br /><br /> 默认值为 0。|  
|**scheduledTransferLogLevelFilter**|字符串|可选。 指定传输的日志条目的最低严重级别。 默认值为“未定义”。 其他可能的值为“详细”、“信息”、“警告”、“错误”和“严重”。|  
|**scheduledTransferPeriod**|duration|可选。 指定计划的数据传输之间的时间间隔，向上舍入为最接近的分钟数。<br /><br /> 默认是 PT0S。|  

## <a name="directories-element"></a>Directories 元素  
为你可以定义的基于文件的日志定义缓冲区配置。

父元素：[DiagnosticMonitorConfiguration 元素](#DiagnosticMonitorConfiguration)。  


属性：  

|属性|类型|说明|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|可选。 指定可用于存储指定数据的文件系统存储最大容量。<br /><br /> 默认值为 0。|  
|**scheduledTransferPeriod**|duration|可选。 指定计划的数据传输之间的时间间隔，向上舍入为最接近的分钟数。<br /><br /> 默认是 PT0S。|  

## <a name="crashdumps-element"></a>CrashDumps 元素  
 定义故障转储目录。

 父元素：[Directories 元素](#Directories)。  

属性：  

|属性|类型|说明|  
|---------------|----------|-----------------|  
|**容器**|字符串|容器的名称，将要传输容器中的目录内容。|  
|**directoryQuotaInMB**|unsignedInt|可选。 指定目录的大小上限（以兆字节为单位）。<br /><br /> 默认值为 0。|  

## <a name="failedrequestlogs-element"></a>FailedRequestLogs 元素  
 定义失败的请求日志目录。

 父元素：[Directories 元素](#Directories)。  

属性：  

|属性|类型|说明|  
|---------------|----------|-----------------|  
|**容器**|字符串|容器的名称，将要传输容器中的目录内容。|  
|**directoryQuotaInMB**|unsignedInt|可选。 指定目录的大小上限（以兆字节为单位）。<br /><br /> 默认值为 0。|  

##  <a name="iislogs-element"></a>IISLogs 元素  
 定义 IIS 日志目录。

 父元素：[Directories 元素](#Directories)。  

属性：  

|属性|类型|说明|  
|---------------|----------|-----------------|  
|**容器**|字符串|容器的名称，将要传输容器中的目录内容。|  
|**directoryQuotaInMB**|unsignedInt|可选。 指定目录的大小上限（以兆字节为单位）。<br /><br /> 默认值为 0。|  

## <a name="datasources-element"></a>DataSources 元素  
 定义零个或多个其他日志目录。

 父元素：[Directories 元素](#Directories)。

## <a name="directoryconfiguration-element"></a>DirectoryConfiguration 元素  
 定义要监视的日志文件的目录。

 父元素：[DataSources 元素](#DataSources)。

属性：

|属性|类型|说明|  
|---------------|----------|-----------------|  
|**容器**|字符串|容器的名称，将要传输容器中的目录内容。|  
|**directoryQuotaInMB**|unsignedInt|可选。 指定目录的大小上限（以兆字节为单位）。<br /><br /> 默认值为 0。|  

## <a name="absolute-element"></a>Absolute 元素  
 定义要使用可选环境扩展监视的目录的绝对路径。

 父元素：[DirectoryConfiguration 元素](#DirectoryConfiguration)。  

属性：  

|属性|类型|说明|  
|---------------|----------|-----------------|  
|**路径**|字符串|必需。 要监视的目录的绝对路径。|  
|**expandEnvironment**|布尔值|必需。 如果设置为 **true**，则扩展路径中的环境变量。|  

## <a name="localresource-element"></a>LocalResource 元素  
 定义服务定义中定义的本地资源的相对路径。

 父元素：[DirectoryConfiguration 元素](#DirectoryConfiguration)。  

属性：  

|属性|类型|说明|  
|---------------|----------|-----------------|  
|**name**|字符串|必需。 包含要监视的目录的本地资源的名称。|  
|**relativePath**|字符串|必需。 相对于要监视的本地资源的路径。|  

## <a name="performancecounters-element"></a>PerformanceCounters 元素  
 定义要收集的性能计数器的路径。

 父元素：[DiagnosticMonitorConfiguration 元素](#DiagnosticMonitorConfiguration)。


 属性：  

|属性|类型|说明|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|可选。 指定可用于存储指定数据的文件系统存储最大容量。<br /><br /> 默认值为 0。|  
|**scheduledTransferPeriod**|duration|可选。 指定计划的数据传输之间的时间间隔，向上舍入为最接近的分钟数。<br /><br /> 默认是 PT0S。|  

## <a name="performancecounterconfiguration-element"></a>PerformanceCounterConfiguration 元素  
 定义要收集的性能计数器。

 父元素：[PerformanceCounters 元素](#PerformanceCounters)。  

 属性：  

|属性|类型|说明|  
|---------------|----------|-----------------|  
|**counterSpecifier**|字符串|必需。 要收集的性能计数器的路径。|  
|**sampleRate**|duration|必需。 应收集性能计数器的速率。|  

## <a name="windowseventlog-element"></a>WindowsEventLog 元素  
 定义要监视的事件日志。

 父元素：[DiagnosticMonitorConfiguration 元素](#DiagnosticMonitorConfiguration)。

  属性：

|属性|类型|说明|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|可选。 指定可用于存储指定数据的文件系统存储最大容量。<br /><br /> 默认值为 0。|  
|**scheduledTransferLogLevelFilter**|字符串|可选。 指定传输的日志条目的最低严重级别。 默认值为“未定义”。 其他可能的值为“详细”、“信息”、“警告”、“错误”和“严重”。|  
|**scheduledTransferPeriod**|duration|可选。 指定计划的数据传输之间的时间间隔，向上舍入为最接近的分钟数。<br /><br /> 默认是 PT0S。|  

## <a name="datasource-element"></a>DataSource 元素  
 定义要监视的事件日志。

 父元素：[WindowsEventLog 元素](#windowsEventLog)。  

 属性：

|属性|类型|说明|  
|---------------|----------|-----------------|  
|**name**|字符串|必需。 指定要收集的日志的 XPath 表达式。|  



<!--HONumber=Jan17_HO5-->


