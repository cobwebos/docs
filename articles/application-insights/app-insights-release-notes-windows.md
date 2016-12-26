---
title: "适用于 Windows 的 Application Insights 发行说明"
description: "（已弃用）SDK 发行说明。"
services: application-insights
documentationcenter: 
author: alancameronwills
manager: douge
ms.assetid: 62115aba-f806-447b-9cca-76d0e11cfbd5
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/12/2016
ms.author: joshweb
ROBOTS: NOINDEX,NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: 8c5324742e42a1f82bb3031af4380fc5f0241d7f
ms.openlocfilehash: 6868024441e3e0e0d2fdc44499a9b1e00364b31e

---
# <a name="release-notes-for-application-insights-sdk-for-windows-phone-and-store"></a>用于 Windows Phone 和应用商店的 Application Insights SDK 发行说明

> [!WARNING]
> 不再支持 Windows Phone 和应用商店 SDK。 建议通过 [HockeyApp](https://www.hockeyapp.net/) 管理分发以及监视移动和桌面应用。
>  

Application Insights SDK 将关于动态应用的遥测发送到 [Application Insights](https://azure.microsoft.com/services/application-insights/)，可从中分析它的使用情况和性能。

## <a name="version-111"></a>版本 1.1.1
### <a name="windows-sdk"></a>Windows SDK
* 修复了在使用 Windows Phone Silverlight SDK 时出现故障期间的暂停。 在此更改之后，对于在对 WindowsAppInitialier.InitializeAsync(...) 的调用后大约晚于 2 秒发生的任何故障，都将保存到磁盘，并在下次启动应用时发送。 如果故障发生在调用之后的大约 2 秒内，将忽略它。  
* 将 NuGet 的依赖项设置为特定版本的核心和 Microsoft.ApplicationInsights.PersistenceChannel (v1.2.3)。   

### <a name="core-sdk"></a>核心 SDK
* 核心托管在 github 中。 可以[在 github 中](http://github.com/Microsoft/ApplicationInsights-dotnet/releases)找到核心 SDK 的发行说明。

## <a name="version-11"></a>版本 1.1
### <a name="core-sdk"></a>核心 SDK
* SDK 现在引入了新的遥测类型 ```DependencyTelemetry```，其中包含有关应用程序中依赖项调用的信息
* 新方法 ```TelemetryClient.TrackDependency``` 允许从应用程序发送有关依赖项调用的信息

## <a name="version-100"></a>版本 1.0.0
### <a name="windows-app-sdk"></a>Windows 应用 SDK
* Windows 应用的新初始化。 具有 `InitializeAsync()` 方法的新 `WindowsAppInitializer` 类允许启动 SDK 集合的初始化。 与先前的 ApplicationInsights.config 技术相比，此更改实现更精确的控制和显著的应用初始化性能改进。
* DeveloperMode 不再自动设定。 若要更改 DeveloperMode 行为，必须在代码中指定。
* NuGet 包不再注入 ApplicationInsights.config。 建议在手动添加 NuGet 包时，使用新的 WindowsAppInitializer。
* ApplicationInsights.config 只读取 `<InstrumentationKey>`，所有其他设置将在 WindowsAppInitializer 设置的首选项中忽略。
* 应用商店市场将由 SDK 自动收集。
* 大量 Bug 修复、稳定性改进以及性能增强。

### <a name="core-sdk"></a>核心 SDK
* 不再需要 ApplicationInsights.config 文件。 并且不通过 NuGet 包添加。 可以在代码中完全指定配置。
* NuGet 包将不再向解决方案添加目标文件。 这将在调试版本中删除 DeveloperMode 的自动设置。 DeveloperMode 应在代码中手动设置。

## <a name="version-017"></a>版本 0.17
### <a name="windows-app-sdk"></a>Windows 应用 SDK
* Windows 应用程序 SDK 现在支持针对 Windows 10 Technical Preview 并使用 VS 2015 RC 创建的通用 Windows 应用。

### <a name="core-sdk"></a>核心 SDK
* TelemetryClient 默认设为使用 InMemoryChannel 初始化。
* 已添加新 API `TelemetryClient.Flush()`。 此 Flush 方法将触发以下即时操作：阻止所有已登录到该客户端的遥测上载。 这使得可以在进程关闭前手动触发上载。
* NuGet 包已添加 .Net 4.5 目标。 此目标不具有任何外部依赖项（已删除 BCL 和 EventSource 依赖项）。

## <a name="version-016"></a>版本 0.16
2015-04-28 预览版

* SDK 现在支持 DNX 目标平台，以启用 [.NET Core Framework](http://www.dotnetfoundation.org/NETCore5) 应用程序的监视。
* ```TelemetryClient``` 的实例不再缓存检测密钥。 现在如果未在 ```TelemetryClient``` 中显式设置检测密钥，```InstrumentationKey``` 将返回 null。 它修复了在某些遥测已收集后设置 ```TelemetryConfiguration.Active.InstrumentationKey``` 时出现的问题，遥测模块（如依赖项收集器、Web 请求数据收集和性能计数器收集器）将使用新的检测密钥。

## <a name="version-015"></a>版本 0.15
* 新属性 ```Operation.SyntheticSource``` 现在可用于 ```TelemetryContext```。 现在，可以将遥测项标记为“非真实用户流量”并指定如何生成此流量。 例如，通过设置此属性，可以将测试自动化的流量与加载测试流量区分开来。
* 通道逻辑已移动到名为 Microsoft.ApplicationInsights.PersistenceChannel 的单个 NuGet。 默认通道现在称为 InMemoryChannel
* 新方法 ```TelemetryClient.Flush``` 允许以同步方式从缓冲区刷新遥测项

## <a name="version-013"></a>版本 0.13
没有可用的旧版本发行说明。 




<!--HONumber=Nov16_HO4-->


