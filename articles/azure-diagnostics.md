<properties
	pageTitle="Azure 诊断概述"
	description="使用 Azure 诊断在云服务、虚拟机和 Service Fabric 中进行调试、性能度量、监视和流量分析"
	services="multiple"
	documentationCenter=".net"
	authors="rboucher"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="multiple"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="02/20/2016"
	ms.author="robb"/>


# 什么是 Microsoft Azure 诊断


Azure 诊断是 Azure 中可对部署的应用程序启用诊断数据收集的功能。可以使用于自许多不同源的诊断扩展。目前支持的有 Azure 云服务 Web 和辅助角色、运行 Microsoft Windows 的 Azure 虚拟机，以及 Service Fabric。其他 Azure 服务都有自身不同的诊断扩展。

## 可以收集的数据

Azure 诊断可以收集以下类型的数据：

数据源|说明
---|---
性能计数器 | 操作系统和自定义性能计数器
应用程序日志 | 应用程序写入的跟踪消息
Windows 事件日志 | 发送到 Windows 事件日志记录系统的信息
.NET 事件源 | 使用 .NET [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) 类的代码编写事件
IIS Logs | 有关 IIS 网站的信息
基于清单的 ETW | 由任何进程生成的 Windows 事件的事件跟踪
故障转储 | 有关应用程序崩溃时进程状态的信息
自定义错误日志 | 应用程序或服务创建的日志
Azure Diagnostics基础结构日志|有关诊断自身的信息

Azure 诊断扩展可将此数据发送到 Azure 存储帐户，或者发送到 [Application Insights](./application-insights/app-insights-cloudservices.md) 等服务。可以将这些数据用于调试和故障排除、度量性能、监视资源使用状况、进行流量分析和容量规划以及进行审核。


## 版本控制
请参阅 [Azure 诊断版本控制历史记录](azure-diagnostics-versioning-history.md)。

## 后续步骤
请选择要尝试在哪个服务上收集诊断数据，并使用以下文章来入门。有关具体任务的参考，请使用一般的 Azure 诊断链接。

## Web Apps
请注意，Web Apps 不使用 Azure 诊断。请在 [Web Apps](./app-service-web/web-sites-enable-diagnostic-log.md) 中查找相应的信息。

## 使用 Azure 诊断的云服务
- 如果使用 Visual Studio，请参阅[使用 Visual Studio 跟踪云服务应用程序](./vs-azure-tools-debug-cloud-services-virtual-machines.md)来帮助入门。否则，请参阅
- [如何使用 Azure 诊断监视云服务](./cloud-services/cloud-services-how-to-monitor.md)
- [在云服务应用程序中设置 Azure 诊断](./cloud-services/cloud-services-dotnet-diagnostics.md)

有关更高级的主题，请参阅
- [将 Azure 诊断与适用于云服务的 Application Insights 配合使用](./application-insights/app-insights-cloudservices.md)
- [使用 Azure 诊断跟踪云服务应用程序的流](./cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)
- [使用 PowerShell 在云服务上设置诊断](./virtual-machines/virtual-machines-extensions-diagnostics-windows-powershell.md)


## 使用 Azure 诊断的虚拟机
- 如果使用 Visual Studio，请参阅[使用 Visual Studio 跟踪 Microsoft Azure 虚拟机](./vs-azure-tools-debug-cloud-services-virtual-machines.md)来帮助入门。否则，请参阅
- [在 Azure 虚拟机上设置 Azure 诊断](./virtual-machines-dotnet-diagnostics.md)

有关更高级的主题，请参阅
- [使用 PowerShell 在 Azure 虚拟机上设置诊断](./virtual-machines/virtual-machines-extensions-diagnostics-windows-powershell.md)
- [使用 Azure 资源管理器模板创建具有监视和诊断功能的 Windows 虚拟机](./virtual-machines/virtual-machines-extensions-diagnostics-windows-template.md)

## 使用 Azure 诊断的 Service Fabric
请参阅[监视 Service Fabric 应用程序](./service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)来帮助入门。打开此文章后，可以使用左侧的导航树来查看其他许多 Service Fabric 诊断文章。

## 一般的 Azure 诊断文章
- [Azure 诊断架构配置](https://msdn.microsoft.com/library/azure/mt634524.aspx) - 了解如何更改架构文件以收集和路由诊断数据。请注意，也可以使用 Visual Studio 来更改架构文件。
- [Azure 诊断数据在 Azure 存储空间中的存储方式](./cloud-services/cloud-services-dotnet-diagnostics-storage.md) - 了解诊断数据写入到的表和 Blob 的名称。
- 了解如何[在 Azure 诊断中使用性能计数器](./cloud-services/cloud-services-dotnet-diagnostics-performance-counters.md)。
- 了解如何[将 Azure 诊断信息路由到 Application Insights](./azure-diagnostics-configure-applicationinsights.md)。
- 如果你在开始诊断时或者在 Azure 存储空间表中查找数据时遇到问题，请参阅 [Azure 诊断故障排除](./azure-diagnostics-troubleshooting.md)。

<!---HONumber=Mooncake_0328_2016-->