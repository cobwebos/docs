---
title: Azure 应用程序 Insights 中的遥测通道 |Microsoft Docs
description: 如何在 Azure 应用程序 Insights Sdk for .NET 和 .NET Core 中自定义遥测通道。
ms.topic: conceptual
ms.date: 05/14/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 9c292246f947e4d3a364f79b31fe7a1deebd33d9
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78368288"
---
# <a name="telemetry-channels-in-application-insights"></a>Application Insights 中的遥测通道

遥测通道是[Azure 应用程序 Insights sdk](../../azure-monitor/app/app-insights-overview.md)必不可少的部分。 它们管理遥测并将遥测数据传输到 Application Insights 服务。 .NET Core 和 .NET Core 版本的 Sdk 有两个内置遥测通道： `InMemoryChannel` 和 `ServerTelemetryChannel`。 本文详细介绍了每个频道，包括如何自定义通道行为。

## <a name="what-are-telemetry-channels"></a>什么是遥测通道？

遥测通道负责缓冲遥测项，并将其发送到 Application Insights 服务，在该服务中存储这些项以便进行查询和分析。 遥测通道是实现[`Microsoft.ApplicationInsights.ITelemetryChannel`](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.channel.itelemetrychannel?view=azure-dotnet)接口的任何类。

遥测信道的 `Send(ITelemetry item)` 方法在调用所有遥测初始值设定项和遥测处理器后调用。 因此，遥测处理器丢弃的任何项都不会到达该通道。 `Send()` 通常不会立即将这些项发送到后端。 通常情况下，它会将它们缓冲到内存中，并分批发送它们，以便高效传输。

[实时指标流](live-stream.md)还有一个自定义通道，可为遥测的实时流提供支持。 此通道独立于常规遥测通道，此文档不适用于它。

## <a name="built-in-telemetry-channels"></a>内置遥测通道

Application Insights .NET 和 .NET Core Sdk 随附了两个内置通道：

* `InMemoryChannel`：一个轻型通道，它在内存中缓冲项，直到它们被发送。 项在内存中进行缓冲，每隔30秒刷新一次，或在缓冲500项时刷新。 此通道提供的可靠性保证最小，因为它不会在发生故障后重试发送遥测数据。 此通道也不会保留磁盘上的项目，因此任何未发送的项目会在应用程序关闭（正常或不正常）时永久丢失。 此通道实现 `Flush()` 方法，该方法可用于以同步方式强制刷新任何内存中的遥测项。 此通道非常适合用于在同步刷新理想的情况下运行的短时间运行的应用程序。

    此通道是更大的 Applicationinsights.config NuGet 包的一部分，并且是 SDK 在未配置其他内容时使用的默认通道。

* `ServerTelemetryChannel`：具有重试策略的更高级通道，以及用于将数据存储在本地磁盘上的功能。 如果发生暂时性错误，此通道将重试发送遥测。 此通道还使用本地磁盘存储在网络中断或高遥测卷期间将项目保留在磁盘上。 由于这些重试机制和本地磁盘存储，此通道被认为更可靠，建议用于所有生产方案。 此频道是根据官方文档配置的[ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)和[ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)应用程序的默认值。 此通道针对具有长时间运行的进程的服务器方案进行了优化。 此通道实现的[`Flush()`](#which-channel-should-i-use)方法不是同步方法。

    此通道作为 Applicationinsights.config WindowsServer TelemetryChannel NuGet 包发货，并在你使用或 applicationinsights.config NuGet 时自动获取它。 AspNetCore NuGet软件包.

## <a name="configure-a-telemetry-channel"></a>配置遥测通道

可以通过将遥测通道设置为活动遥测配置来配置它。 对于 ASP.NET 应用程序，配置涉及将遥测通道实例设置为 `TelemetryConfiguration.Active`或修改 `ApplicationInsights.config`。 对于 ASP.NET Core 应用程序，配置涉及到将通道添加到依赖关系注入容器。

以下部分演示了如何在各种应用程序类型中配置通道的 `StorageFolder` 设置。 `StorageFolder` 只是一个可配置的设置。 有关配置设置的完整列表，请参阅本文后面[的 "设置" 部分](telemetry-channels.md#configurable-settings-in-channels)。

### <a name="configuration-by-using-applicationinsightsconfig-for-aspnet-applications"></a>使用 Applicationinsights.config for ASP.NET 应用程序进行配置

以下[applicationinsights.config](configuration-with-applicationinsights-config.md)中的部分显示了配置为自定义位置 `StorageFolder` 的 `ServerTelemetryChannel` 通道：

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

下面的代码设置 "ServerTelemetryChannel" 实例，`StorageFolder` 设置为自定义位置。 在应用程序的开头添加此代码，通常是在 Global.aspx.cs 的 `Application_Start()` 方法中。

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

### <a name="configuration-in-code-for-aspnet-core-applications"></a>ASP.NET Core 应用程序的代码配置

修改 `Startup.cs` 类的 `ConfigureServices` 方法，如下所示：

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
> 不建议对 ASP.NET Core 应用程序使用 `TelemetryConfiguration.Active` 配置通道。

### <a name="configuration-in-code-for-netnet-core-console-applications"></a>.NET/.NET Core 控制台应用程序代码中的配置

对于控制台应用程序，.NET 和 .NET Core 的代码是相同的：

```csharp
var serverTelemetryChannel = new ServerTelemetryChannel();
serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
```

## <a name="operational-details-of-servertelemetrychannel"></a>ServerTelemetryChannel 的操作详细信息

`ServerTelemetryChannel` 将到达内存中缓冲区的项存储在内存中。 每隔30秒对项进行序列化、压缩，并将其存储到 `Transmission` 实例一次，或在缓冲500项后存储。 单个 `Transmission` 实例最多包含500个项，表示一批通过单个 HTTPS 调用发送到 Application Insights 服务的遥测数据。

默认情况下，最多可以并行发送10个 `Transmission` 实例。 如果遥测获得速度更快，或者网络或 Application Insights 后端速度缓慢，`Transmission` 实例将存储在内存中。 此内存中 `Transmission` 缓冲区的默认容量是 5 MB。 超过内存中容量时，`Transmission` 实例将存储在本地磁盘上，最大限制为 50 MB。 当存在网络问题时，`Transmission` 实例将同时存储在本地磁盘上。 只有存储在本地磁盘上的项目在应用程序崩溃时才会发生。 当应用程序重新启动时，将发送这些用户。

## <a name="configurable-settings-in-channels"></a>通道中的可配置设置

有关每个通道的可配置设置的完整列表，请参阅：

* [InMemoryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/Microsoft.ApplicationInsights/Channel/InMemoryChannel.cs)

* [ServerTelemetryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)

下面是 `ServerTelemetryChannel`最常使用的设置：

1. `MaxTransmissionBufferCapacity`：通道在内存中缓冲传输所使用的最大内存量（以字节为单位）。 达到此容量时，新项将直接存储到本地磁盘。 默认值为 5 MB。 设置较高的值将导致磁盘使用较少，但请记住，如果应用程序崩溃，内存中的项将丢失。

1. `MaxTransmissionSenderCapacity`：将同时发送到 Application Insights 的 `Transmission` 实例的最大数量。 默认值为“10”。 此设置可配置为更大的数字，如果生成的遥测数据量较大，则建议使用此设置。 大容量通常在负载测试过程中或在关闭采样时出现。

1. `StorageFolder`：通道用于根据需要将项存储到磁盘的文件夹。 在 Windows 中，如果未显式指定其他路径，则使用% LOCALAPPDATA% 或% TEMP%。 在 Windows 以外的环境中，你必须指定有效的位置，否则遥测将不会存储到本地磁盘。

## <a name="which-channel-should-i-use"></a>应该使用哪个通道？

对于涉及长时间运行的应用程序的大多数生产方案，建议使用 `ServerTelemetryChannel`。 `ServerTelemetryChannel` 实现的 `Flush()` 方法不同步，并且它也不保证从内存或磁盘发送所有挂起项。 如果在应用程序即将关闭的情况下使用此通道，则建议在调用 `Flush()`后引入一些延迟。 你可能需要的精确延迟量不是可预测的。 它依赖于多种因素，例如内存中的项目数或 `Transmission` 的实例数、磁盘上的数量、传输到后端的数量，以及通道是否处于指数回退方案的中间。

如果需要执行同步刷新，建议使用 `InMemoryChannel`。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost"></a>Application Insights 通道是否保证遥测送达？ 否则，遥测会丢失哪些情况？

简短的答案是，没有任何内置通道提供事务类型来保证遥测传递到后端。 与用于可靠传递的 `InMemoryChannel` 相比，`ServerTelemetryChannel` 更高级，但它也只会尽力发送遥测数据。 在多种情况下，遥测仍可能会丢失，包括以下常见方案：

1. 当应用程序崩溃时，内存中的项将丢失。

1. 遥测在网络问题的长时间内丢失。 在网络中断期间或在 Application Insights 后端出现问题时，遥测数据将存储到本地磁盘。 但是，超过48小时的项目将被丢弃。

1. 在 Windows 中存储遥测数据的默认磁盘位置为% LOCALAPPDATA% 或% TEMP%。 这些位置通常是计算机的本地位置。 如果应用程序在物理上从一个位置迁移到另一个位置，则原始位置中存储的任何遥测数据都将丢失。

1. 在 Windows 上的 Web 应用中，默认磁盘存储位置为 D:\local\LocalAppData。 此位置不是持久的。 它在应用程序重启、缩小以及其他此类操作中被擦除，从而导致丢失存储在此处的任何遥测数据。 可以重写默认值，并指定存储到永久性位置，如 D:\home。 但是，这种持久的位置由远程存储提供服务，因此可能会很慢。

### <a name="does-servertelemetrychannel-work-on-systems-other-than-windows"></a>ServerTelemetryChannel 是否在 Windows 以外的系统上工作？

尽管其包和命名空间的名称包含 "WindowsServer"，但 Windows 以外的系统支持此通道，但以下情况例外。 在非 Windows 系统上，默认情况下，通道不创建本地存储文件夹。 必须创建本地存储文件夹，并配置通道以使用该文件夹。 配置本地存储后，通道在所有系统上的工作方式相同。

### <a name="does-the-sdk-create-temporary-local-storage-is-the-data-encrypted-at-storage"></a>SDK 是否创建临时本地存储？ 数据是否已加密？

SDK 在网络问题期间或限制期间将遥测项存储在本地存储中。 不会在本地加密此数据。

对于 Windows 系统，SDK 会在% TEMP% 或% LOCALAPPDATA% 目录中自动创建临时本地文件夹，并仅限制管理员和当前用户的访问权限。

对于 Windows 以外的系统，SDK 不会自动创建本地存储，因此默认情况下不会将任何数据存储在本地。 你可以自己创建存储目录，并配置通道以使用它。 在这种情况下，你需要负责确保该目录是安全的。
阅读有关[数据保护和隐私](data-retention-privacy.md#does-the-sdk-create-temporary-local-storage)的详细信息。

## <a name="open-source-sdk"></a>开源 SDK
与 Application Insights 的每个 SDK 一样，通道是开源的。 阅读并参与[官方 GitHub](https://github.com/Microsoft/ApplicationInsights-dotnet)存储库中的代码或报告问题。

## <a name="next-steps"></a>后续步骤

* [采样](../../azure-monitor/app/sampling.md)
* [SDK 疑难解答](../../azure-monitor/app/asp-net-troubleshoot-no-data.md)
