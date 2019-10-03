---
title: Azure Application Insights 中的遥测通道 |Microsoft Docs
description: 如何自定义.NET 和.NET Core 的 Azure Application Insights Sdk 中的遥测通道。
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
ms.openlocfilehash: af00641123354831c7bf174a743ded2886343579
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561346"
---
# <a name="telemetry-channels-in-application-insights"></a>在 Application Insights 中遥测通道

遥测通道是不可或缺的一部分[Azure Application Insights Sdk](../../azure-monitor/app/app-insights-overview.md)。 他们管理缓冲和传输到 Application Insights 服务的遥测数据。 .NET 和.NET Core 版本的 Sdk 有两个内置遥测通道：`InMemoryChannel`和`ServerTelemetryChannel`。 本指南介绍了每个通道在详细信息，包括如何自定义通道行为。

## <a name="what-are-telemetry-channels"></a>遥测通道有哪些？

遥测通道负责缓冲遥测项，并将它们发送到 Application Insights 服务，它们用于查询和分析的存储位置。 遥测通道是实现任何类[ `Microsoft.ApplicationInsights.ITelemetryChannel` ](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.channel.itelemetrychannel?view=azure-dotnet)接口。

`Send(ITelemetry item)`所有遥测初始值设定项后调用的遥测通道的方法和调用遥测处理器。 因此，删除遥测处理器的任何项不会到达通道。 `Send()` 不通常项即时发送给后端。 通常情况下，它在内存中缓冲它们，并将它们发送批次，高效传输中。

[实时指标 Stream](live-stream.md)还具有自定义通道支持实时传送视频流的遥测数据。 此通道独立于常规遥测通道，并且本文档不能应用于它。

## <a name="built-in-telemetry-channels"></a>内置遥测通道

Application Insights.NET 和.NET Core Sdk 附带两个内置通道：

* `InMemoryChannel`：在内存中缓冲的项，直到它们已发送一个轻型通道。 项是在内存中缓冲，并刷新一次每隔 30 秒，或每当缓冲 500 项。 此通道提供最小的可靠性保证，因为它不会发生故障后重试发送遥测数据。 此通道也不会保留项在磁盘上，因此任何未发送的项都将应用程序关闭后永久丢失 (正常或不)。 此通道实现`Flush()`方法，可以用来强制刷新所有内存中的遥测项以同步方式。 此通道是非常适用于短时间运行的应用程序同步刷新是理想之选。

    此通道是更大的 Microsoft.ApplicationInsights NuGet 包的一部分，会进行任何其他配置时，SDK 使用的默认通道。

* `ServerTelemetryChannel`：更高级的通道的重试策略和本地磁盘上存储数据的功能。 如果发生暂时性错误，此通道重试发送遥测数据。 此通道还使用本地磁盘存储在网络中断或高遥测卷期间磁盘上保留项。 由于这些重试机制和本地磁盘存储，此通道将被视为更可靠，建议对所有生产情况。 此通道是默认值[ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)并[ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)官方文档根据配置的应用程序。 此通道适用于使用长时间运行的进程服务器方案。 [ `Flush()` ](#which-channel-should-i-use)通过此通道实现的方法不同步。

    此通道附带 Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel NuGet 包，并使用 Microsoft.ApplicationInsights.Web 或 Microsoft.ApplicationInsights.AspNetCore NuGet 时自动获取包。

## <a name="configure-a-telemetry-channel"></a>配置遥测通道

通过将其设置为活动的遥测配置配置遥测通道。 对于 ASP.NET 应用程序，配置涉及到遥测通道实例设置为`TelemetryConfiguration.Active`，或通过修改`ApplicationInsights.config`。 对于 ASP.NET Core 应用程序，配置涉及到将通道添加到依赖关系注入容器。

以下部分介绍了配置的示例`StorageFolder`设置中各种应用程序类型的通道。 `StorageFolder` 只会是可配置的设置。 有关配置设置的完整列表，请参阅[设置部分](telemetry-channels.md#configurable-settings-in-channels)这篇文章中更高版本。

### <a name="configuration-by-using-applicationinsightsconfig-for-aspnet-applications"></a>通过 ApplicationInsights.config 用于 ASP.NET 应用程序的配置

以下部分从[ApplicationInsights.config](configuration-with-applicationinsights-config.md)演示`ServerTelemetryChannel`采用配置通道`StorageFolder`将设置为自定义位置：

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

### <a name="configuration-in-code-for-aspnet-applications"></a>中的 ASP.NET 应用程序代码的配置

下面的代码设置与 ServerTelemetryChannel 实例`StorageFolder`将设置为自定义位置。 将此代码开头的应用程序，通常添加在`Application_Start()`Global.aspx.cs 中的方法。

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

修改`ConfigureServices`方法的`Startup.cs`类如下所示：

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
> 配置通道以通过使用`TelemetryConfiguration.Active`不建议用于 ASP.NET Core 应用程序。

### <a name="configuration-in-code-for-netnet-core-console-applications"></a>在代码中为.NET/.NET Core 控制台应用程序的配置

为控制台应用的代码是相同的.NET 和.NET Core:

```csharp
var serverTelemetryChannel = new ServerTelemetryChannel();
serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
```

## <a name="operational-details-of-servertelemetrychannel"></a>ServerTelemetryChannel 的操作的详细信息

`ServerTelemetryChannel` 传入到内存中缓冲区中的项的存储。 项被序列化、 压缩，并存储到`Transmission`实例一次每隔 30 秒，或当已缓冲 500 项。 单个`Transmission`实例包含最多 500 个项，并表示一批的已通过单个 HTTPS 调用到 Application Insights 服务发送的遥测数据。

默认情况下，最多 10 个`Transmission`可以并行发送实例。 遥测数据到达速度较快，或者如果网络或 Application Insights 后端为较慢，`Transmission`实例存储在内存中。 此内存中的默认容量`Transmission`缓冲区为 5 MB。 当已超过内存中容量时，`Transmission`实例存储在最多为 50 MB 的本地磁盘上。 `Transmission` 实例也都存储在本地磁盘上出现网络问题时。 只存储在本地磁盘的项后应用程序崩溃，丢失。 每次重新启动应用程序时，它们已发送。

## <a name="configurable-settings-in-channels"></a>通道中的可配置设置

每个通道的可配置设置的完整列表，请参阅：

* [InMemoryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/Channel/InMemoryChannel.cs)

* [ServerTelemetryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)

以下是最常用的设置`ServerTelemetryChannel`:

1. `MaxTransmissionBufferCapacity`：最大，以字节为单位，在内存中缓冲区传输到通道所使用的内存量。 当达到此容量时，直接向本地磁盘存储新项。 默认值为 5 MB。 设置较高的值会导致较少的磁盘使用情况，但请记住，是否应用程序崩溃，内存中的项目将会丢失。

1. `MaxTransmissionSenderCapacity`：最大数目`Transmission`将同时发送到 Application Insights 的实例。 默认值为 10。 此设置可以配置为较大的数字，生成大量遥测数据时，建议使用。 在负载测试或采样已关闭时通常会发生高容量。

1. `StorageFolder`：通道用于存储要根据需要的磁盘的项的文件夹。 在 Windows 中，%LOCALAPPDATA%或 %TEMP%是如果使用显式指定任何其他路径。 在非 Windows 环境中，必须指定有效的位置或遥测数据不会存储到本地磁盘。

## <a name="which-channel-should-i-use"></a>应使用所用的通道？

`ServerTelemetryChannel` 对于大多数涉及长时间运行的应用程序的生产方案，建议。 `Flush()`方法实现由`ServerTelemetryChannel`不同步的并且它也不保证从内存或磁盘发送所有挂起的项目。 如果使用此通道在方案中应用程序是要关闭的情况下，我们建议你之后调用引入一些延迟`Flush()`。 可能需要延迟的确切量不是可预测的。 这取决于项的数量等因素或`Transmission`已在内存、 磁盘上的具体数量是、 多少将被传输到后端，以及通道是否指数退让方案的中间实例。

如果您需要执行同步刷新，我们建议你使用`InMemoryChannel`。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost"></a>Application Insights 通道是否保证遥测传递？ 如果没有，什么是遥测数据可能丢失的方案？

简短的回答是，没有任何内置信道提供遥测数据传递到后端的事务类型保证。 `ServerTelemetryChannel` 为更高级的与相比`InMemoryChannel`的可靠传递，但它还会仅最大努力尝试发送的遥测数据。 遥测数据可能仍会丢失几种情况下，包括这些常见方案：

1. 应用程序崩溃时，内存中的项目都会丢失。

1. 在较长的网络问题，遥测会丢失。 网络中断或问题与 Application Insights 后端发生的时间期间将遥测数据存储到本地磁盘中。 但是，早于 24 小时内的项目将被丢弃。

1. 在 Windows 中存储遥测数据的默认磁盘位置是 %LOCALAPPDATA%或 %TEMP%。 这些位置是通常的本地计算机。 如果应用程序将迁移以物理方式从一个位置到另一个，在原始位置中存储任何遥测数据将丢失。

1. 在 Windows 上的 Web 应用，默认磁盘存储位置是 D:\local\LocalAppData。 此位置不永久保存。 它被擦除应用重启、 规模输出的提速和其他此类操作，从而导致丢失存储在其中任何遥测数据中。 可以重写默认值，并指定存储到一个持久位置，例如 D:\home。 但是，这种持久化的位置提供的远程存储，因此可能会很慢。

### <a name="does-servertelemetrychannel-work-on-systems-other-than-windows"></a>ServerTelemetryChannel 是否适用于除 Windows 之外的系统？

尽管其包和命名空间的名称包含"WindowsServer"，在非 Windows，但以下情况例外的系统上支持此通道。 在除 Windows 之外的系统，通道不会创建本地存储文件夹，默认情况下。 必须创建一个本地存储文件夹，并配置要使用它的通道。 配置本地存储后，通道在所有系统的工作方式相同。

### <a name="does-the-sdk-create-temporary-local-storage-is-the-data-encrypted-at-storage"></a>SDK 是否创建临时本地存储？ 数据已存储在加密？

SDK 将遥测项存储在本地存储中，在网络问题或限制期间。 此数据不会加密本地。

对于 Windows 系统，SDK 会自动创建一个临时的本地文件夹中的 %TEMP%或 %LOCALAPPDATA%目录中，并限制对管理员和当前用户的访问。

对于非 Windows 系统，由 SDK 自动创建任何本地存储，并因此没有数据默认本地存储。 您可以自己创建的存储目录和配置要使用它的通道。 在这种情况下，您负责确保保护目录。
详细了解[数据保护和隐私](data-retention-privacy.md#does-the-sdk-create-temporary-local-storage)。

## <a name="open-source-sdk"></a>开源 SDK
与 Application Insights 的每个 SDK，通道是开放源代码。 读取和贡献代码或报告问题，在[官方 GitHub 存储库](https://github.com/Microsoft/ApplicationInsights-dotnet)。

## <a name="next-steps"></a>后续步骤

* [采样](../../azure-monitor/app/sampling.md)
* [SDK 进行故障排除](../../azure-monitor/app/asp-net-troubleshoot-no-data.md)
