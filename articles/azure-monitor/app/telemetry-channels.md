---
title: Azure Application Insights 中的遥测通道 | Microsoft Docs
description: 如何自定义适用于 .NET 和 .NET Core 的 Azure Application Insights SDK 中的遥测通道。
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: cijothomas
ms.author: cithomas
ms.date: 05/14/2019
ms.reviewer: mbullwin
ms.openlocfilehash: cef8a06fb7e4cfb713d6531f23df9ae9c5836b68
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173615"
---
# <a name="telemetry-channels-in-application-insights"></a>Application Insights 中的遥测通道

遥测通道是 [Azure Application Insights SDK](../../azure-monitor/app/app-insights-overview.md) 不可或缺的组成部分。 它们可以管理缓冲以及将遥测数据传输到 Application Insights 服务的过程。 SDK 的 .NET 和 .NET Core 版本包含两个内置的遥测通道：`InMemoryChannel` 和 `ServerTelemetryChannel`。 本文将详细介绍每个通道，包括如何自定义通道行为。

## <a name="what-are-telemetry-channels"></a>什么是遥测通道？

遥测通道负责缓冲遥测项并将其发送到 Application Insights 服务，存储在该服务中的项可用于查询和分析。 遥测通道是实现 [`Microsoft.ApplicationInsights.ITelemetryChannel`](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.channel.itelemetrychannel?view=azure-dotnet) 接口的任何类。

遥测信道的 `Send(ITelemetry item)` 方法在调用所有遥测初始化表达式和遥测处理器之后调用。 因此，遥测处理器删除的任何项不会进入通道。 一般情况下，`Send()` 不会立即将项发送到后端。 它通常将这些项缓冲在内存中并分批发送，以提高传输效率。

[实时指标流](live-stream.md)还包含一个自定义通道用于支持遥测数据的实时流式传输。 此通道独立于常规的遥测通道，本文档对它不适用。

## <a name="built-in-telemetry-channels"></a>内置遥测通道

Application Insights .NET 和 .NET Core SDK 随附了两个内置通道：

* `InMemoryChannel`：一个轻型通道，它在内存中缓冲项，直到它们被发送。 项在内存中缓冲，每隔 30 秒刷新一次，或者每当缓冲了 500 个项时刷新。 此通道提供最基本的可靠性保证，因为它在失败后不会重试发送遥测数据。 此外，此通道不会在磁盘上保留项，因此，在（正常或非正常）关闭应用程序后，未发送的所有项都会永久丢失。 此通道实现 `Flush()` 方法，使用此方法可以强制同步刷新内存中的所有遥测项。 此通道非常适合用于短时间运行的、最好是进行同步刷新的应用程序。

    此通道随附在较大的 Microsoft.ApplicationInsights NuGet 包中，是未配置任何其他通道时，SDK 使用的默认通道。

* `ServerTelemetryChannel`：具有重试策略的更高级通道，以及用于将数据存储在本地磁盘上的功能。 如果发生暂时性错误，此通道会重试发送遥测数据。 在网络中断或者遥测量较高时，此通道还会使用本地磁盘存储在磁盘上保留项。 由于这些重试机制和本地磁盘存储，我们认为此通道更可靠，建议在所有生产方案中使用。 此通道是根据官方文档配置的 [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) 和 [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) 应用程序的默认通道。 此通道已针对长时间运行的服务器方案进行优化。 此通道实现的 [`Flush()`](#which-channel-should-i-use) 方法不是同步的。

    此通道作为 Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel NuGet 包交付，使用 Microsoft.ApplicationInsights.Web 或 Microsoft.ApplicationInsights.AspNetCore NuGet 包时可自动获取它。

## <a name="configure-a-telemetry-channel"></a>配置遥测通道

可以通过将遥测通道设置为活动的遥测配置来配置该遥测通道。 对于 ASP.NET 应用程序，配置过程涉及到将遥测通道实例设置为 `TelemetryConfiguration.Active`，或修改 `ApplicationInsights.config`。 对于 ASP.NET Core 应用程序，配置过程涉及到将通道添加到依赖项注入容器。

以下部分演示如何在各种应用程序类型中配置通道的 `StorageFolder` 设置。 `StorageFolder` 只是可配置的设置之一。 有关配置设置的完整列表，请参阅本文稍后的[设置部分](telemetry-channels.md#configurable-settings-in-channels)。

### <a name="configuration-by-using-applicationinsightsconfig-for-aspnet-applications"></a>使用适用于 ASP.NET 应用程序的 ApplicationInsights.Config 进行配置

[ApplicationInsights.config](configuration-with-applicationinsights-config.md) 中的以下节显示了在将 `ServerTelemetryChannel` 设置为自定义位置的情况下配置的 `StorageFolder` 通道：

```xml
    <TelemetrySinks>
        <Add Name="default">
            <TelemetryProcessors>
                <!-- Telemetry processors omitted for brevity  -->
            </TelemetryProcessors>
            <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel">
                <StorageFolder>d:\temp\applicationinsights</StorageFolder>
            </TelemetryChannel>
        </Add>
    </TelemetrySinks>
```

### <a name="configuration-in-code-for-aspnet-applications"></a>ASP.NET 应用程序代码中的配置

以下代码在将 `StorageFolder` 设置为自定义位置的情况下设置“ServerTelemetryChannel”实例。 请将此代码添加到应用程序的开头，通常是添加到 Global.aspx.cs 中的 `Application_Start()` 方法内。

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

### <a name="configuration-in-code-for-aspnet-core-applications"></a>ASP.NET Core 应用程序代码中的配置

修改类 `ConfigureServices` 的 `Startup.cs` 方法，如下所示：

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

> [!IMPORTANT]
> 不建议使用 `TelemetryConfiguration.Active` 配置 ASP.NET Core 应用程序的通道。

### <a name="configuration-in-code-for-netnet-core-console-applications"></a>.NET/.NET Core 控制台应用程序代码中的配置

.NET 和 .NET Core 控制台应用的代码相同：

```csharp
var serverTelemetryChannel = new ServerTelemetryChannel();
serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
```

## <a name="operational-details-of-servertelemetrychannel"></a>ServerTelemetryChannel 的操作详细信息

`ServerTelemetryChannel` 在内存中缓冲区内存储抵达的项。 每隔 30 秒或每当缓冲了 500 个项时，这些项将序列化、压缩并存储到 `Transmission` 实例中。 单个 `Transmission` 实例最多包含 500 个项，表示通过 Application Insights 服务的单个 HTTPS 调用发送的遥测数据批。

默认情况下，最多可以并行发送 10 个 `Transmission` 实例。 如果遥测数据以更快的速度抵达，或者网络或 Application Insights 后端速度缓慢，则 `Transmission` 将存储到内存中。 此内存中 `Transmission` 缓冲区的默认容量为 5 MB。 超过内存中容量时，`Transmission` 实例将存储在本地磁盘上（不超过 50 MB）。 出现网络问题时，`Transmission` 实例也会存储在本地磁盘上。 当应用程序崩溃时，只有存储在本地磁盘中的项才能幸存。 每当应用程序再次启动时，将发送这些项。

## <a name="configurable-settings-in-channels"></a>通道中的可配置设置

有关每个通道的可配置设置完整列表，请参阅：

* [InMemoryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/Microsoft.ApplicationInsights/Channel/InMemoryChannel.cs)

* [ServerTelemetryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)

下面是 `ServerTelemetryChannel` 的最常用设置：

1. `MaxTransmissionBufferCapacity`：通道在内存中缓冲传输所使用的最大内存量（以字节为单位）。 达到此容量时，新项将直接存储到本地磁盘。 默认值为 5 MB。 设置更大的值可以减少磁盘用量，但请记住，如果应用程序崩溃，内存中的项将会丢失。

1. `MaxTransmissionSenderCapacity`：将同时发送到 Application Insights 的 `Transmission` 实例的最大数量。 默认值为 10。 可将此设置配置为更大的数字，生成巨量的遥测数据时建议这样做。 执行负载测试或关闭采样时，往往会出现大量的遥测数据。

1. `StorageFolder`：通道用于根据需要将项存储到磁盘的文件夹。 在 Windows 中，如果未显式指定其他路径，则会使用 %LOCALAPPDATA% 或 %TEMP%。 在非 Windows 环境中，必须指定有效的位置，否则遥测数据不会存储到本地磁盘。

## <a name="which-channel-should-i-use"></a>应使用哪个通道？

对于涉及到长时间运行的应用程序的大多数生产方案，建议使用 `ServerTelemetryChannel`。 `Flush()` 实现的 `ServerTelemetryChannel` 方法不是同步的，不保证发送内存或磁盘中所有等待中的项。 如果要在应用程序即将关闭的情况下使用此通道，则我们建议在调用 `Flush()` 后留出一定的延迟。 所需的确切延迟时间不可预测。 这取决于多种因素，例如，有多少个项或 `Transmission` 实例位于内存中和磁盘中、其中有多少个正在传输到后端，以及该通道是否位于指数退让方案的中间部分。

如果需要执行同步刷新，我们建议使用 `InMemoryChannel`。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost"></a>Application Insights 通道是否保证传送遥测数据？ 如果不能，在哪些情况下遥测数据可能会丢失？

简单地讲，在向后端传送遥测数据方面，没有任何内置通道能够提供事务类型保证。 在可靠传送方面，`ServerTelemetryChannel` 比 `InMemoryChannel` 更先进，但它也只能是尽最大努力来尝试发送遥测数据。 在多种情况下，遥测数据仍可能会丢失，常见情况包括：

1. 当应用程序崩溃时，内存中的项就会丢失。

1. 如果网络问题的持续时间很长，遥测数据将会丢失。 出现网络中断或者 Application Insights 后端问题时，遥测数据将存储到本地磁盘。 但是，超过 24 小时的项将被丢弃。

1. 在 Windows 中，用于存储遥测数据的默认磁盘位置是 %LOCALAPPDATA% 或 %TEMP%。 这些位置通常位于计算机本地。 如果以物理方式将应用程序从一个位置迁移到另一个位置，在原始位置存储的所有遥测数据将会丢失。

1. 在 Windows 上的 Web 应用中，默认磁盘存储位置为 D:\local\LocalAppData。 此位置不是永久性的。 在应用重启、横向扩展和执行其他此类操作的情况下将被擦除，导致这些位置存储的遥测数据丢失。 可将替代默认位置，并将存储指定为某个永久性位置（例如 D:\home）。 但是，此类永久性位置由远程存储提供服务，因此运行速度可能很缓慢。

### <a name="does-servertelemetrychannel-work-on-systems-other-than-windows"></a>ServerTelemetryChannel 是否可在非 Windows 系统中工作？

尽管该通道的包和命名空间名称包含“WindowsServer”，但非 Windows 系统也支持此通道，不过存在以下例外情况。 在非 Windows 系统中，该通道默认不会创建本地存储文件夹。 必须创建本地存储文件夹，并将通道配置为使用该文件夹。 配置本地存储后，该通道在所有系统中的工作方式相同。

### <a name="does-the-sdk-create-temporary-local-storage-is-the-data-encrypted-at-storage"></a>SDK 是否创建临时本地存储？ 存储中的数据是否会加密？

出现网络问题或限制时，SDK 会将遥测项存储在本地存储中。 此数据不会在本地加密。

对于 Windows 系统，SDK 会自动在 %TEMP% 或 %LOCALAPPDATA% 目录中创建临时本地文件夹，并仅限管理员和当前用户访问该文件夹。

对于非 Windows 系统，SDK 不会自动创建本地存储，因此默认不会在本地存储数据。 你可以自行创建存储目录，并将通道配置为使用该目录。 在这种情况下，你需负责确保该目录受到保护。
详细了解[数据保留和隐私](data-retention-privacy.md#does-the-sdk-create-temporary-local-storage)。

## <a name="open-source-sdk"></a>开源 SDK
与 Application Insights 的每个 SDK 一样，通道也是开源的。 请在[官方 GitHub 存储库](https://github.com/Microsoft/ApplicationInsights-dotnet)中阅读和贡献代码，或者报告问题。

## <a name="next-steps"></a>后续步骤

* [采样](../../azure-monitor/app/sampling.md)
* [SDK 故障排除](../../azure-monitor/app/asp-net-troubleshoot-no-data.md)
