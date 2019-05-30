---
title: Azure Application Insights 中的遥测通道 |Microsoft Docs
description: 如何为.NET/.NET 核心自定义遥测通道在 Azure Application Insights Sdk。
services: application-insights
documentationcenter: .net
author: cijothomas
manager: carmonm
ms.assetid: 015ab744-d514-42c0-8553-8410eef00368
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/14/2019
ms.reviewer: mbullwin
ms.author: cithomas
ms.openlocfilehash: fa99e29e9ec6c2bef7296a50dd381ee5fc60a1cb
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2019
ms.locfileid: "66255809"
---
# <a name="telemetrychannel-in-application-insights"></a>在 Application Insights 中的 TelemetryChannel

TelemetryChannel 是不可或缺的一部分[Azure Application Insights Sdk](../../azure-monitor/app/app-insights-overview.md)。 它管理缓冲和传输到 Application Insights 服务的遥测数据。 Sdk 的.NET 和.NET Core 版本包含两个内置 TelemetryChannels-`InMemoryChannel`和`ServerTelemetryChannel`。 本指南介绍了每个通道在详细信息，包括如何用户可以自定义通道行为。

## <a name="what-is-a-telemetrychannel"></a>什么是 TelemetryChannel？

`TelemetryChannel` 负责进行缓冲，并将遥测项发送到 Application Insights 服务，用于查询和分析存储位置。 它是实现接口的任何类 [`Microsoft.ApplicationInsights.ITelemetryChannel`](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.channel.itelemetrychannel?view=azure-dotnet)

`Send(ITelemetry item)` TelemetryChannel 方法称为毕竟`TelemetryInitializer`s 和`TelemetryProcessor`称为 s。 这意味着，删除任何项`TelemetryProcessor`不会到达通道。 `Send()` 不会不通常项即时发送到后端。 它们通常缓冲在内存中，并发送批次，高效传输中。

[LiveMetrics](live-stream.md)还有提供支持的遥测数据实时流式传输的自定义通道。 此通道独立于常规遥测通道，并且本文档不适用于使用的通道`LiveMetrics`。

## <a name="built-in-telemetrychannels"></a>内置 TelemetryChannels

Application Insights.NET/.NET Core SDK 附带了两个内置通道：

* **InMemoryChannel** 
 `InMemoryChannel`是在内存中缓冲的项，直到已发送的细线通道。 项是在内存中缓冲，并刷新一次每隔 30 秒或每当已缓冲 500 项。 此通道提供了最小的可靠性保证它不会重试在失败时发送遥测数据。 此通道不会在磁盘上，保留项，因此任何未发送的项在应用程序关闭后永久丢失 (正常)。 没有`Flush()`实现此通道，可用来强制刷新所有内存中的遥测项同步的方法。 这是非常适用于短时间运行的应用程序同步刷新是理想之选。

    此通道附带部分`Microsoft.ApplicationInsights`nuget 包本身，和是没有其他配置时，SDK 使用的默认通道。

* **ServerTelemetryChannel** 
 `ServerTelemetryChannel`是更高级的通道，通道具有重试策略和本地磁盘上存储数据的功能。 如果发生暂时性错误，此通道重试发送遥测数据。 此通道还使用本地磁盘存储在网络中断或高遥测卷期间磁盘上保留项。 由于这些重试机制和本地磁盘存储，此通道将被视为更可靠，建议对所有生产情况。 此通道是默认值[ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)并[ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)根据链接的官方文档配置的应用程序。此通道适用于长时间运行进程的服务器方案。 [ `Flush()` ](#which-channel-should-i-use)通过此通道实现的方法是不同步。

    此通道都作为 NuGet 包`Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel`，并使用 NuGet 包时自动变为`Microsoft.ApplicationInsights.Web`或`Microsoft.ApplicationInsights.AspNetCore`。

## <a name="configuring-telemetrychannel"></a>配置 TelemetryChannel

可以通过设置所需配置遥测通道`TelemetryChannel`在活动`TelemetryConfiguration`。 对于 Asp.Net 应用程序，配置涉及设置`TelemetryChannel`上`TelemetryConfiguration.Active`，或修改`ApplicationInsights.config`。 对于 ASP.NET Core 应用程序，配置涉及到依赖关系注入容器中添加所需的通道。

下面显示了示例用户正在配置`StorageFolder`通道。 `StorageFolder` 只会是可配置的设置。 配置设置的完整列表所述[设置部分中](telemetry-channels.md#configurable-settings-in-channel)。

## <a name="configuration-using-applicationinsightsconfig-for-aspnet-applications"></a>配置 ASP.NET 应用程序使用 ApplicationInsights.Config

以下部分从[ApplicationInsights.config](configuration-with-applicationinsights-config.md)显示了使用配置的 ServerTelemetryChannel`StorageFolder`将设置为自定义位置。

```xml
    <TelemetrySinks>
        <Add Name="default">
            <TelemetryProcessors>
                <!--TelemetryProcessors omitted for brevity  -->
            </TelemetryProcessors>
            <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel">
                <StorageFolder>d:\temp\applicationinsights</StorageFolder>
            </TelemetryChannel>
        </Add>
    </TelemetrySinks>
```

## <a name="configuration-in-code-for-aspnet-applications"></a>在代码中的 ASP.NET 应用程序的配置

以下代码中设置了与 ServerTelemetryChannel`StorageFolder`将设置为自定义位置。 应将此代码添加该应用程序，通常在中的 application_start （） 方法的开头 `Global.aspx.cs`

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
protected void Application_Start()
{
    var serverTelemetryChannel = new ServerTelemetryChannel();
    serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
    serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
    TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
}
```

## <a name="configuration-in-code-for-aspnet-core-applications"></a>在代码中的 ASP.NET Core 应用程序的配置

修改`ConfigureServices`方法的`Startup.cs`类，如下所示。

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

public void ConfigureServices(IServiceCollection services)
{
    // This sets up ServerTelemetryChannel with StorageFolder set to a custom location.
    services.AddSingleton(typeof(ITelemetryChannel), new ServerTelemetryChannel() {StorageFolder = @"d:\temp\applicationinsights" });

    services.AddApplicationInsightsTelemetry();
}

```

> [!NOTE]
> 务必要注意，配置的通道使用`TelemetryConfiguration.Active`不建议用于 ASP.NET Core 应用程序。

## <a name="configuring-telemetrychannel-in-code-for-netnet-core-console-applications"></a>为.NET/.NET Core 控制台应用程序在代码中配置 TelemetryChannel

对于控制台应用，该代码是相同的.NET 和.NET Core，如下所示。

```csharp
var serverTelemetryChannel = new ServerTelemetryChannel();
serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
```

## <a name="operational-details-of-servertelemetrychannel"></a>ServerTelemetryChannel 的操作的详细信息

`ServerTelemetryChannel`缓冲在内存中缓冲区中到达的项。 序列化、 压缩，并且存储到`Transmission`实例后的每 30 秒或当 500 个项进行缓冲。 单个`Transmission`实例包含最多 500 个项，并表示一批在单个 https 调用 Application Insights 服务发送的遥测数据。 默认情况下，可以有最多 10 个`Transmission`s 发送并行。 如果遥测数据到达速度较快，或如果网络/Application Insights 后端速度较慢，然后`Transmission`s 获取存储到内存中。 此内存中的传输缓冲区的默认容量为 5 MB。 一旦超出了内存中的容量， `Transmission`s 本地磁盘上存储的最多为 50 MB。 `Transmission`当网络出问题时，将本地磁盘上存储 s。 仅在本地磁盘中存储的项后丢失应用程序崩溃，每当重新启动该应用程序时将要发送。

## <a name="configurable-settings-in-channel"></a>在通道中的可配置设置

下面是每个通道的可配置设置的完整列表：

[InMemoryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/Channel/InMemoryChannel.cs)

[ServerTelemetryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)

最常用的设置`ServerTelemetryChannel`如下所示：

1. `MaxTransmissionBufferCapacity` -最大内存，以字节为单位，在内存中缓冲区传输到通道所使用的量。 一旦达到此容量，将直接向本地磁盘存储新项。 默认值为 5 MB。 潜在客户的更高版本的值设置为较小的磁盘使用情况，但务必要注意，是否应用程序崩溃，内存中的项目将会丢失。

2. `MaxTransmissionSenderCapacity` -最长`Transmission`将同时发送到 Application Insights 的 s。 默认值为 10，但可以将它配置为较大的数字。 当生成大量遥测数据时，通常当执行负载测试和/或采样已关闭时，建议此操作。

3. `StorageFolder` 的通道用于存储要根据需要的磁盘的项文件夹。 在 Windows 中，%localappdata%或 %temp%是，如果使用显式配置执行任何操作。 在非 Windows 环境中，用户**必须**配置有效的位置，而无需其遥测数据不会存储到本地磁盘。

## <a name="which-channel-should-i-use"></a>应使用所用的通道？

`ServerTelemetryChannel` 对于长时间运行的应用程序的大多数生产方案，建议。 `Flush()`的方法实现`ServerTelemetryChannel`不是同步的并且`Flush()`不保证发送所有挂起的项目从内存/磁盘。 如果在其中应用程序即将关闭的情况下，则我们建议为一些延迟之后调用的方案中使用此通道`Flush()`此通道上。 延迟所需的确切量不是可预测的因为它依赖项的数量等因素或`Transmissions`是在内存中，具体数量是在磁盘中，多少正在传输到备份，并且如果通道正在指数退让方案。 如果没有要执行同步刷新，然后需要`InMemoryChannel`建议。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="does-applicationinsights-channel-offer-guaranteed-telemetry-delivery-or-what-are-the-scenarios-where-telemetry-can-be-lost"></a>*ApplicationInsights 通道是否提供有保证的遥测数据传递扩展插件或遥测数据可能丢失的情况是什么？*

* 简短的答案是没有任何内置信道提供有关遥测数据传递到后端的事务类型保证。 虽然`ServerTelemetryChannel`更高级的相对于`InMemoryChannel`来进行可靠的遥测数据传递，还可以最大努力尝试发送遥测数据和遥测数据可能仍会丢失几个方案中。 遥测是丢失的常见方案包括：

1. 每当应用程序崩溃，内存中的项目都会丢失。
1. 在网络中断或 Application Insights 后端的问题，遥测数据获取存储到本地磁盘。 但是，早于 24 小时内的项目将被丢弃。 因此在长时间内网络问题过程遥测数据会丢失。
1. 在 Windows 中存储遥测数据的默认磁盘位置是 %localappdata%或 %temp%。 这些位置是通常的本地计算机。 如果应用程序将迁移以物理方式从一个位置到另一个，在此位置中存储任何遥测数据将丢失。
1. 在 Azure Web 应用 (Windows) 中，默认磁盘位置为"D:\local\LocalAppData"。 此位置不保持不变，并在应用重启擦除，规模输出中，依此类推，从而导致失去了遥测功能存储在这些位置中。 用户可以重写存储到一个持久位置，例如"D:\home"，但这些持久化的位置下面提供的远程存储，并可能会很慢。

### <a name="does-servertelemetrychannel-work-in-non-windows-systems"></a>*ServerTelemetryChannel 是否在非 Windows 系统中工作？*

* 尽管在 windows Server/包命名空间的名称，此通道支持在非 Windows 系统中出现以下异常。 在非 Windows，通道不会创建本地存储文件夹，默认情况下。 用户必须创建一个本地存储文件夹，并配置要使用它的通道。 配置本地存储后，通道在 Windows 和非 Windows 系统中运行相同。

### <a name="does-the-sdk-create-temporary-local-storage-is-the-data-encrypted-at-storage"></a>*SDK 不会创建临时本地存储？数据已存储在加密？*

* SDK 将遥测项存储在本地存储中，在网络问题或限制期间。 此数据不会加密本地。
对于 Windows 系统，SDK 会自动在 TEMP 或 APPDATA 目录中，创建一个临时的本地文件夹，并限制对管理员和当前用户的访问。
对于非 Windows，sdk，自动创建任何本地存储并因此没有数据默认本地存储。 用户可以创建存储目录本身，并配置要使用它的通道。 在这种情况下，用户负责确保该目录得到保护。
详细了解[数据保护和隐私](data-retention-privacy.md#does-the-sdk-create-temporary-local-storage)。

## <a name="open-source-sdk"></a>开源 SDK
每个 Application Insights Sdk，如通道也是开放源代码。 阅读和参与代码，或报告问题[官方 GitHub 存储库](https://github.com/Microsoft/ApplicationInsights-dotnet)。

## <a name="next-steps"></a>后续步骤

* [采样](../../azure-monitor/app/sampling.md)
* [SDK 进行故障排除](../../azure-monitor/app/asp-net-troubleshoot-no-data.md)
