---
title: Windows 诊断扩展架构
description: Azure Monitor 中的 Windows 诊断扩展（WAD）的配置架构参考。
ms.subservice: diagnostic-extension
ms.topic: reference
author: bwren
ms.author: bwren
ms.date: 01/20/2020
ms.openlocfilehash: 3adf4b59c0605859ada75577c083094541815984
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78384119"
---
# <a name="windows-diagnostics-extension-schema"></a>Windows 诊断扩展架构
Azure 诊断扩展是 Azure Monitor 中的一个代理，它收集来宾操作系统中的监视数据和 Azure 计算资源的工作负荷。 本文详细介绍了用于在 Windows 虚拟机和其他计算资源上配置诊断扩展的架构。

> [!NOTE]
> 本文中的架构适用于版本1.3 和更高版本（Azure SDK 2.4 和更高版本）。 已对较新的配置节进行注释，以显示其添加于哪一版本。 架构的版本1.0 和1.2 已存档，不再可用。 

## <a name="public-configuration-file-schema"></a>公共配置文件架构

通过执行以下 PowerShell 命令来下载公共配置文件架构定义：  

```powershell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  


## <a name="common-attribute-types"></a>常见的属性类型  
 **scheduledTransferPeriod** 属性显示在多个元素中。 它是到存储空间的计划传输之间的间隔，向上舍入为最接近的分钟数。 值是 [XML“持续时间数据类型。”](https://www.w3schools.com/xml/schema_dtypes_date.asp)


## <a name="diagnosticsconfiguration-element"></a>DiagnosticsConfiguration 元素  
 *树：根 - DiagnosticsConfiguration*

在版本 1.3 中添加。  

诊断配置文件的顶级元素。  

**Attribute**  xmlns - 诊断配置文件的 XML 命名空间是：  
`http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration`


|子元素|说明|  
|--------------------|-----------------|  
|**PublicConfig**|必需。 在此页的其他位置查看说明。|  
|**PrivateConfig**|可选。 在此页的其他位置查看说明。|  
|**IsEnabled**|布尔值。 在此页的其他位置查看说明。|  

## <a name="publicconfig-element"></a>PublicConfig 元素  
 *树：根 - DiagnosticsConfiguration - PublicConfig*

 描述公共诊断配置。  

|子元素|说明|  
|--------------------|-----------------|  
|**WadCfg**|必需。 在此页的其他位置查看说明。|  
|**StorageAccount**|用于存储数据的 Azure 存储帐户的名称。 执行 Set-AzureServiceDiagnosticsExtension cmdlet 时，还可能将其指定为参数。|  
|**StorageType**|可以是 *Table*、*Blob* 或 *TableAndBlob*。 Table 是默认值。 当选择了 TableAndBlob 时，诊断数据将写入两次 -- 针对每种类型写入一次。|  
|**LocalResourceDirectory**|Monitoring Agent 在其中存储事件数据的虚拟机上的目录。 如果不设置，则使用默认目录：<br /><br /> 对于辅助角色/Web 角色：`C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> 对于虚拟机：`C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> 必需属性：<br /><br /> - **path** - Azure 诊断将使用的系统上的目录。<br /><br /> - **expandEnvironment** - 控制是否在路径名称中扩展环境变量。|  

## <a name="wadcfg-element"></a>WadCFG 元素  
 *树：根 - DiagnosticsConfiguration - PublicConfig - WadCFG*

 标识并配置要收集的遥测数据。  


## <a name="diagnosticmonitorconfiguration-element"></a>DiagnosticMonitorConfiguration 元素
 *树：根 - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration*

 必选

|属性|说明|  
|----------------|-----------------|  
| **overallQuotaInMB** | 由 Azure 诊断收集的各类诊断数据使用的最大本地磁盘空间量。 默认设置是 4096 MB。<br />
|**useProxyServer** | 将 Azure 诊断配置为使用在 IE 设置中设置的代理服务器设置。|
|**sinks** | 在 1.5 中添加。 可选。 指向接收器位置以同时发送支持接收器的所有子元素的诊断数据。 接收器示例是 Application Insights 或事件中心。|  


<br /> <br />

|子元素|说明|  
|--------------------|-----------------|  
|**CrashDumps**|在此页的其他位置查看说明。|  
|**DiagnosticInfrastructureLogs**|启用收集 Azure 诊断生成的日志。 诊断基础结构日志可用于排查诊断系统本身的故障。 可选属性：<br /><br /> - **scheduledTransferLogLevelFilter** - 配置收集的日志的最低严重级别。<br /><br /> - **scheduledTransferPeriod** - 到存储空间的计划传输之间的时间间隔，向上舍入为最接近的分钟数。 值是 [XML“持续时间数据类型。”](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  
|**Directories**|在此页的其他位置查看说明。|  
|**EtwProviders**|在此页的其他位置查看说明。|  
|**指标**|在此页的其他位置查看说明。|  
|**PerformanceCounters**|在此页的其他位置查看说明。|  
|**WindowsEventLog**|在此页的其他位置查看说明。|
|**DockerSources**|在此页的其他位置查看说明。 |



## <a name="crashdumps-element"></a>CrashDumps 元素  
 *树：根 - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - CrashDumps*

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
 *树：根 - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration -  Directories*

 启用收集目录内容、IIS 失败的访问请求日志和/或 IIS 日志。  

 可选的 **scheduledTransferPeriod** 属性。 请参阅前面的说明。  

|子元素|说明|  
|--------------------|-----------------|  
|**IISLogs**|在配置中包括此元素可启用收集 IIS 日志：<br /><br /> **containerName** - Azure 存储帐户中用于存储 IIS 日志的 blob 容器的名称。|   
|**FailedRequestLogs**|在配置中包括此元素可启用收集有关对 IIS 站点或应用程序的失败请求的日志。 还必须在 **Web.config** 文件中的 **system.WebServer** 下启用跟踪选项。|  
|**DataSources**|要监视的目录的列表。|




## <a name="datasources-element"></a>DataSources 元素  
 *树：根 - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Directories - DataSources*

 要监视的目录的列表。  

|子元素|说明|  
|--------------------|-----------------|  
|**DirectoryConfiguration**|必需。 必需属性：<br /><br /> **containerName** - Azure 存储帐户中用于存储日志文件的 blob 容器的名称。|  





## <a name="directoryconfiguration-element"></a>DirectoryConfiguration 元素  
 *树：根 - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Directories - DataSources - DirectoryConfiguration*

 可能包括 **Absolute** 或 **LocalResource** 元素，但不能同时包含两者。  

|子元素|说明|  
|--------------------|-----------------|  
|**Absolute**|要监视的目录的绝对路径。 需要以下属性：<br /><br /> - **Path** - 要监视的目录的绝对路径。<br /><br /> - **expandEnvironment** - 配置是否在路径中扩展环境变量。|  
|**LocalResource**|要监视的本地资源的相对路径。 必需属性：<br /><br /> - **Name** - 包含要监视的目录的本地资源<br /><br /> - **relativePath** - 包含要监视的目录的名称的相对路径|  



## <a name="etwproviders-element"></a>EtwProviders 元素  
 *树：根 - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders*

 配置从基于 EventSource 和/或 ETW 清单的提供程序收集 ETW 事件。  

|子元素|说明|  
|--------------------|-----------------|  
|**EtwEventSourceProviderConfiguration**|配置收集从 [EventSource 类](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx)生成的事件。 必需属性：<br /><br /> **provider** - EventSource 事件的类名称。<br /><br /> 可选属性：<br /><br /> - **scheduledTransferLogLevelFilter** - 要传输到存储帐户的最低严重级别。<br /><br /> - **scheduledTransferPeriod** - 到存储空间的计划传输之间的时间间隔，向上舍入为最接近的分钟数。 值是 [XML“持续时间数据类型。”](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  
|**EtwManifestProviderConfiguration**|必需属性：<br /><br /> **provider** - 事件提供程序的 GUID<br /><br /> 可选属性：<br /><br /> - **scheduledTransferLogLevelFilter** - 要传输到存储帐户的最低严重级别。<br /><br /> - **scheduledTransferPeriod** - 到存储空间的计划传输之间的时间间隔，向上舍入为最接近的分钟数。 值是 [XML“持续时间数据类型。”](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  



## <a name="etweventsourceproviderconfiguration-element"></a>EtwEventSourceProviderConfiguration 元素  
 *树：根 - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders- EtwEventSourceProviderConfiguration*

 配置收集从 [EventSource 类](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx)生成的事件。  

|子元素|说明|  
|--------------------|-----------------|  
|**DefaultEvents**|可选属性：<br/><br/> **eventDestination** -存储事件的表的名称|  
|**事件**|必需属性：<br /><br /> **id** - 事件 ID。<br /><br /> 可选属性：<br /><br /> **eventDestination** -存储事件的表的名称|  



## <a name="etwmanifestproviderconfiguration-element"></a>EtwManifestProviderConfiguration 元素  
 *树：根 - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders - EtwManifestProviderConfiguration*

|子元素|说明|  
|--------------------|-----------------|  
|**DefaultEvents**|可选属性：<br /><br /> **eventDestination** -存储事件的表的名称|  
|**事件**|必需属性：<br /><br /> **id** - 事件 ID。<br /><br /> 可选属性：<br /><br /> **eventDestination** -存储事件的表的名称|  



## <a name="metrics-element"></a>Metrics 元素  
 *树：根 - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Metrics*

 可以生成针对快速查询进行优化的性能计数器表。 在 **PerformanceCounters** 元素中定义的每个性能计数器除存储在性能计数器表内外，还存储在度量值表中。  

 必需 **resourceId** 属性。  要在其中部署 Azure 诊断的虚拟机或虚拟机规模集的资源 ID。 从 **Azure 门户**获取 [resourceID](https://portal.azure.com)。 选择“浏览” -> “资源组” -> “<名称 **”\>** 。 单击“属性”磁贴，并从“ID”字段复制值。  

|子元素|说明|  
|--------------------|-----------------|  
|**MetricAggregation**|必需属性：<br /><br /> **scheduledTransferPeriod** - 到存储空间的计划传输之间的时间间隔，向上舍入为最接近的分钟数。 值是 [XML“持续时间数据类型。”](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  



## <a name="performancecounters-element"></a>PerformanceCounters 元素  
 *树：根 - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - PerformanceCounters*

 启用性能计数器收集。  

 可选属性：  

 可选的 **scheduledTransferPeriod** 属性。 请参阅前面的说明。

|子元素|说明|  
|-------------------|-----------------|  
|**PerformanceCounterConfiguration**|需要以下属性：<br /><br /> - **counterSpecifier** - 性能计数器的名称。 例如，`\Processor(_Total)\% Processor Time` 。 若要获取性能计数器列表，请在主机上运行 `typeperf` 命令。<br /><br /> - **sampleRate** - 应对计数器进行采样的频率。<br /><br /> 可选属性：<br /><br /> **unit** - 计数器的度量单位。 [Unittype.pixel 度量类](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.sql.models.unittype?view=azure-dotnet)提供了值 |
|**sinks** | 在 1.5 中添加。 可选。 指向同时要发送诊断数据的接收器位置。 例如 Azure Monitor 或事件中心。|    




## <a name="windowseventlog-element"></a>WindowsEventLog 元素
 *树：根 - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - WindowsEventLog*

 启用收集 Windows 事件日志。  

 可选的 **scheduledTransferPeriod** 属性。 请参阅前面的说明。  

|子元素|说明|  
|-------------------|-----------------|  
|**DataSource**|要收集的 Windows 事件日志。 必需属性：<br /><br /> **name** - 描述要收集的 Windows 事件的 XPath 查询。 例如：<br /><br /> `Application!*[System[(Level <=3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[System[(Level <= 3)]`<br /><br /> 若要收集所有事件，请指定“*”|  




## <a name="logs-element"></a>Logs 元素  
 *树：根 - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Logs*

 在 1.0 和 1.1 版本中提供。 1\.2 中不提供。 在 1.3 中重新添加。  

 定义基本 Azure 日志的缓冲区配置。  

|Attribute|类型|说明|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|**unsignedInt**|可选。 指定可用于存储指定数据的文件系统存储最大容量。<br /><br /> 默认值为 0。|  
|**scheduledTransferLogLevelFilter**|**string**|可选。 指定传输的日志条目的最低严重级别。 默认值是“未定义”，这会传输所有日志。 其他可能的值是（按信息严重级别从高到低排序）“详细”、“信息”、“警告”、“错误”和“严重”。|  
|**scheduledTransferPeriod**|**duration**|可选。 指定计划的数据传输之间的时间间隔，向上舍入为最接近的分钟数。<br /><br /> 默认是 PT0S。|  
|**sinks** |**string**| 在 1.5 中添加。 可选。 指向同时要发送诊断数据的接收器位置。 例如，Application Insights 或事件中心。|  

## <a name="dockersources"></a>DockerSources
 *树：根 - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - DockerSources*

 在 1.9 中添加的。

|元素名称|说明|  
|------------------|-----------------|  
|**Stats**|告诉系统收集 Docker 容器的统计信息|  

## <a name="sinksconfig-element"></a>SinksConfig 元素  
 *树：根 - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig*

 向其中发送诊断数据的位置的列表以及与这些位置关联的配置。  

|元素名称|说明|  
|------------------|-----------------|  
|**接收器**|在此页的其他位置查看说明。|  

## <a name="sink-element"></a>Sink 元素
 *树：根 - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig - Sink*

 在版本 1.5 中添加。  

 定义向其中发送诊断数据的位置。 例如，Application Insights 服务。  

|Attribute|类型|说明|  
|---------------|----------|-----------------|  
|name|字符串|标识 sinkname 的字符串。|  

|元素|类型|说明|  
|-------------|----------|-----------------|  
|**Application Insights**|字符串|仅在将数据发送到 Application Insights 时使用。 包含有权访问的有效 Application Insights 帐户的检测密钥。|  
|**通道**|字符串|每个对应一个流处理的其他筛选|  

## <a name="channels-element"></a>Channels 元素  
 *树：根 - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig - Sink - Channels*

 在版本 1.5 中添加。  

 定义通过接收器的日志数据流的筛选器。  

|元素|类型|说明|  
|-------------|----------|-----------------|  
|**Channel**|字符串|在此页的其他位置查看说明。|  

## <a name="channel-element"></a>Channel 元素
 *树：根 - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig - Sink - Channels - Channel*

 在版本 1.5 中添加。  

 定义向其中发送诊断数据的位置。 例如，Application Insights 服务。  

|属性|类型|说明|  
|----------------|----------|-----------------|  
|**logLevel**|**string**|指定传输的日志条目的最低严重级别。 默认值是“未定义”，这会传输所有日志。 其他可能的值是（按信息严重级别从高到低排序）“详细”、“信息”、“警告”、“错误”和“严重”。|  
|name|**string**|要引用的通道的唯一名称|  


## <a name="privateconfig-element"></a>PrivateConfig 元素
 *树：根 - DiagnosticsConfiguration - PrivateConfig*

 在版本 1.3 中添加。  

 可选  

 存储存储帐户的私有详细信息（名称、密钥和终结点）。 此信息发送到虚拟机，但不能从中检索。  

|子元素|说明|  
|--------------------|-----------------|  
|**StorageAccount**|要使用的存储帐户。 需要以下属性<br /><br /> - **name** - 存储帐户的名称。<br /><br /> - **key** - 存储帐户的密钥。<br /><br /> - **endpoint** - 用于访问存储帐户的终结点。 <br /><br /> -**sasToken** （已添加1.8.1）-可以在专用配置中指定 SAS 令牌而非存储帐户密钥。如果提供，则忽略存储帐户密钥。 <br />对 SAS 令牌的要求： <br />- 仅支持帐户 SAS 令牌 <br />- *b*、*t* 服务类型是必需的。 <br /> - *a*、*c*、*u*、*w* 权限是必需的。 <br /> - *c*、*o* 资源类型是必需的。 <br /> - 仅支持 HTTPS 协议 <br /> - 起始时间和到期时间必须有效。|  


## <a name="isenabled-element"></a>IsEnabled 元素  
 *树：根 - DiagnosticsConfiguration - IsEnabled*

 布尔值。 使用 `true` 启用诊断或使用 `false` 禁用诊断。

## <a name="example-configuration"></a>配置示例
 下面是 JSON 和 XML 中所示的 Windows 诊断扩展的完整示例配置。

 
### <a name="json"></a>JSON

*PublicConfig*和*privateconfig.json*是分离的，因为在大多数 JSON 使用情况下，它们将作为不同的变量传递。 这些情况包括资源管理器模板、PowerShell 和 Visual Studio。

> [!NOTE]
> 公共配置 Azure Monitor 接收器定义具有两个属性： *resourceId*和*region*。 这些属性仅是经典 VM 和经典云服务所必需的。 这些属性不应用于其他资源。

```json
"PublicConfig" {
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 10000,
            "DiagnosticInfrastructureLogs": {
                "scheduledTransferLogLevelFilter": "Error"
            },
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
                "sinks": "AzureMonitorSink",
                "PerformanceCounterConfiguration": [
                    {
                        "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                        "sampleRate": "PT1M",
                        "unit": "percent"
                    }
                ]
            },
            "Directories": {
                "scheduledTransferPeriod": "PT5M",
                "IISLogs": {
                    "containerName": "iislogs"
                },
                "FailedRequestLogs": {
                    "containerName": "iisfailed"
                },
                "DataSources": [
                    {
                        "containerName": "mynewprocess",
                        "Absolute": {
                            "path": "C:\\MyNewProcess",
                            "expandEnvironment": false
                        }
                    },
                    {
                        "containerName": "badapp",
                        "Absolute": {
                            "path": "%SYSTEMDRIVE%\\BadApp",
                            "expandEnvironment": true
                        }
                    },
                    {
                        "containerName": "goodapp",
                        "LocalResource": {
                            "relativePath": "..\\PeanutButter",
                            "name": "Skippy"
                        }
                    }
                ]
            },
            "EtwProviders": {
                "sinks": "",
                "EtwEventSourceProviderConfiguration": [
                    {
                        "scheduledTransferPeriod": "PT5M",
                        "provider": "MyProviderClass",
                        "Event": [
                            {
                                "id": 0
                            },
                            {
                                "id": 1,
                                "eventDestination": "errorTable"
                            }
                        ],
                        "DefaultEvents": {
                        }
                    }
                ],
                "EtwManifestProviderConfiguration": [
                    {
                        "scheduledTransferPeriod": "PT2M",
                        "scheduledTransferLogLevelFilter": "Information",
                        "provider": "5974b00b-84c2-44bc-9e58-3a2451b4e3ad",
                        "Event": [
                            {
                                "id": 0
                            }
                        ],
                        "DefaultEvents": {
                        }
                    }
                ]
            },
            "WindowsEventLog": {
                "scheduledTransferPeriod": "PT5M",
                "DataSource": [
                    {
                        "name": "System!*[System[Provider[@Name='Microsoft Antimalware']]]"
                    },
                    {
                        "name": "System!*[System[Provider[@Name='NTFS'] and (EventID=55)]]"
                    },
                    {
                        "name": "System!*[System[Provider[@Name='disk'] and (EventID=7 or EventID=52 or EventID=55)]]"
                    }
                ]
            },
            "Logs": {
                "scheduledTransferPeriod": "PT1M",
                "scheduledTransferLogLevelFilter": "Verbose",
                "sinks": "ApplicationInsights.AppLogs"
            },
            "CrashDumps": {
                "directoryQuotaPercentage": 30,
                "dumpType": "Mini",
                "containerName": "wad-crashdumps",
                "CrashDumpConfiguration": [
                    {
                        "processName": "mynewprocess.exe"
                    },
                    {
                        "processName": "badapp.exe"
                    }
                ]
            }
        },
        "SinksConfig": {
            "Sink": [
                {
                    "name": "AzureMonitorSink",
                    "AzureMonitor":
                    {
                        "ResourceId": "{insert resourceId if a classic VM or cloud service, else property not needed}",
                        "Region": "{insert Azure region of resource if a classic VM or cloud service, else property not needed}"
                    }
                },
                {
                    "name": "ApplicationInsights",
                    "ApplicationInsights": "{Insert InstrumentationKey}",
                    "Channels": {
                        "Channel": [
                            {
                                "logLevel": "Error",
                                "name": "Errors"
                            },
                            {
                                "logLevel": "Verbose",
                                "name": "AppLogs"
                            }
                        ]
                    }
                },
                {
                    "name": "EventHub",
                    "EventHub": {
                        "Url": "https://myeventhub-ns.servicebus.windows.net/diageventhub",
                        "SharedAccessKeyName": "SendRule",
                        "usePublisherId": false
                    }
                },
                {
                    "name": "secondaryEventHub",
                    "EventHub": {
                        "Url": "https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub",
                        "SharedAccessKeyName": "SendRule",
                        "usePublisherId": false
                    }
                },
                {
                    "name": "secondaryStorageAccount",
                    "StorageAccount": {
                        "name": "secondarydiagstorageaccount",
                        "endpoint": "https://core.windows.net"
                    }
                }
            ]
        }
    },
    "StorageAccount": "diagstorageaccount",
    "StorageType": "TableAndBlob"
}
```

> [!NOTE]
> 公共配置 Azure Monitor 接收器定义具有两个属性： *PrincipalId*和*Secret*。 这些属性仅是经典 VM 和经典云服务所必需的。 这些属性不应用于其他资源。


```json
"PrivateConfig" {
    "storageAccountName": "diagstorageaccount",
    "storageAccountKey": "{base64 encoded key}",
    "storageAccountEndPoint": "https://core.windows.net",
    "storageAccountSasToken": "{sas token}",
    "EventHub": {
        "Url": "https://myeventhub-ns.servicebus.windows.net/diageventhub",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "{base64 encoded key}"
    },
    "AzureMonitorAccount": {
        "ServicePrincipalMeta": {
            "PrincipalId": "{Insert service principal client Id}",
            "Secret": "{Insert service principal client secret}"
        }
    },
    "SecondaryStorageAccounts": {
        "StorageAccount": [
            {
                "name": "secondarydiagstorageaccount",
                "key": "{base64 encoded key}",
                "endpoint": "https://core.windows.net",
                "sasToken": "{sas token}"
            }
        ]
    },
    "SecondaryEventHubs": {
        "EventHub": [
            {
                "Url": "https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub",
                "SharedAccessKeyName": "SendRule",
                "SharedAccessKey": "{base64 encoded key}"
            }
        ]
    }
}

```

### <a name="xml"></a>XML

```xml  
<?xml version="1.0" encoding="utf-8"?>  
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">   
  <PublicConfig>  
    <WadCfg>  
      <DiagnosticMonitorConfiguration overallQuotaInMB="10000">  

        <PerformanceCounters scheduledTransferPeriod="PT1M", sinks="AzureMonitorSink">  
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

        <DockerSources> <!-- Added in 1.9 -->
          <Stats enabled="true" sampleRate="PT1M" scheduledTransferPeriod="PT1M" />
        </DockerSources>

      </DiagnosticMonitorConfiguration>  

      <SinksConfig>   <!-- Added in 1.5 -->  
        <Sink name="AzureMonitorSink">
            <AzureMonitor> <!-- Added in 1.11 -->
                <resourceId>{insert resourceId}</ResourceId> <!-- Parameter only needed for classic VMs and Classic Cloud Services, exclude VMSS and Resource Manager VMs-->
                <Region>{insert Azure region of resource}</Region> <!-- Parameter only needed for classic VMs and Classic Cloud Services, exclude VMSS and Resource Manager VMs -->
            </AzureMonitor>
        </Sink>
        <Sink name="ApplicationInsights">   
          <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>   
          <Channels>   
            <Channel logLevel="Error" name="Errors"  />   
            <Channel logLevel="Verbose" name="AppLogs"  />   
          </Channels>   
        </Sink>   
        <Sink name="EventHub"> <!-- Added in 1.7 -->
          <EventHub Url="https://myeventhub-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" usePublisherId="false" />
        </Sink>
        <Sink name="secondaryEventHub"> <!-- Added in 1.7 -->
          <EventHub Url="https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub" SharedAccessKeyName="SendRule" usePublisherId="false" />
        </Sink>
        <Sink name="secondaryStorageAccount"> <!-- Added in 1.7 -->
          <StorageAccount name="secondarydiagstorageaccount" endpoint="https://core.windows.net" />
        </Sink>
   </SinksConfig>

  </WadCfg>  

  <StorageAccount>diagstorageaccount</StorageAccount>
  <StorageType>TableAndBlob</StorageType> <!-- Added in 1.8 -->  
  </PublicConfig>  

  <PrivateConfig>  <!-- Added in 1.3 -->  
    <StorageAccount name="" key="" endpoint="" sasToken="{sas token}"  />  <!-- sasToken in Private config added in 1.8.1 -->  
    <EventHub Url="https://myeventhub-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />

    <AzureMonitorAccount>
        <ServicePrincipalMeta> <!-- Added in 1.11; only needed for classic VMs and Classic cloud services -->
            <PrincipalId>{Insert service principal clientId}</PrincipalId>
            <Secret>{Insert service principal client secret}</Secret>
        </ServicePrincipalMeta>
    </AzureMonitorAccount>

    <SecondaryStorageAccounts>
       <StorageAccount name="secondarydiagstorageaccount" key="{base64 encoded key}" endpoint="https://core.windows.net" sasToken="{sas token}" />
    </SecondaryStorageAccounts>

    <SecondaryEventHubs>
       <EventHub Url="https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />
    </SecondaryEventHubs>

  </PrivateConfig>  
  <IsEnabled>true</IsEnabled>  
</DiagnosticsConfiguration>  

```  
> [!NOTE]
> 公共配置 Azure Monitor 接收器定义有两个属性：resourceId 和 region。 这些属性仅是经典 VM 和经典云服务所必需的。 这些属性不应该用于资源管理器虚拟机或虚拟机规模集。
> Azure Monitor 接收器还有一个额外的 Private Config 元素，它传入主体 ID 和机密。 此属性仅是经典 VM 和经典云服务所必需的。 对于资源管理器 VM 和 VMSS，可以排除 private config 元素中的 Azure Monitor 定义。
>