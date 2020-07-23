---
title: 队列存储的性能与可伸缩性查检表 - Azure 存储
description: 开发高性能应用程序时有关队列存储的经证实的做法查检表。
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 10/10/2019
ms.author: tamram
ms.subservice: queues
ms.openlocfilehash: eb1821537e6e25b05dfdca3107729eecf4c6e1bf
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "75750498"
---
# <a name="performance-and-scalability-checklist-for-queue-storage"></a>队列存储的性能与可伸缩性查检表

在开发使用队列存储的高性能应用程序方面，Microsoft 制定了许多经过证实的做法。 此查检表列出了开发人员在优化性能时可以遵循的关键做法。 在设计应用程序时以及在整个流程中，请牢记这些做法。

Azure 存储在容量、事务速率和带宽方面存在可伸缩性与性能目标。 有关 Azure 存储可伸缩性目标的详细信息，请参阅[标准存储帐户的可伸缩性和性能目标](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)和[队列存储的可伸缩性和性能目标](scalability-targets.md)。

## <a name="checklist"></a>清单

本文以查检表的形式组织了在开发队列存储应用程序时在性能方面可以遵循的经过证实的做法。

| 完成 | 类别 | 设计注意事项 |
| --- | --- | --- |
| &nbsp; |可伸缩性目标 |[是否可将应用程序设计为避免使用的存储帐户数超过最大数目？](#maximum-number-of-storage-accounts) |
| &nbsp; |可伸缩性目标 |[是否要避免接近容量和事务限制？](#capacity-and-transaction-targets) |
| &nbsp; |网络 |[客户端设备是否具有足够高的带宽和足够低的延迟，以实现所需的性能？](#throughput) |
| &nbsp; |网络 |[客户端设备是否具有优质网络链接？](#link-quality) |
| &nbsp; |网络 |[客户端应用程序是否位于存储帐户所在的同一区域？](#location) |
| &nbsp; |直接客户端访问 |[是否使用共享访问签名 (SAS) 和跨源资源共享 (CORS) 来实现对 Azure 存储的直接访问？](#sas-and-cors) |
| &nbsp; |.NET 配置 |[是否使用 .NET Core 2.1 或更高版本来实现最佳性能？](#use-net-core) |
| &nbsp; |.NET 配置 |[是否已将客户端配置为使用足够数量的并发连接？](#increase-default-connection-limit) |
| &nbsp; |.NET 配置 |[对于 .NET 应用程序，是否已将 .NET 配置为使用足够数量的线程？](#increase-minimum-number-of-threads) |
| &nbsp; |并行度 |[是否能够确保对并行度进行适当的界定，使客户端功能不会过载或接近可伸缩性目标？](#unbounded-parallelism) |
| &nbsp; |工具 |[是否使用 Microsoft 提供的最新版客户端库和工具？](#client-libraries-and-tools) |
| &nbsp; |重试 |[是否对限制错误和超时使用重试策略和指数退避？](#timeout-and-server-busy-errors) |
| &nbsp; |重试 |[对于不可重试的错误，应用程序是否会避免重试？](#non-retryable-errors) |
| &nbsp; |配置 |[是否已关闭 Nagle 算法以改进小型请求的性能？](#disable-nagle) |
| &nbsp; |消息大小 |[消息是否经过压缩，以此来改进队列的性能？](#message-size) |
| &nbsp; |批量检索 |[是否使用单个 GET 操作检索多个消息？](#batch-retrieval) |
| &nbsp; |轮询频率 |[是否会进行足够频繁的轮询，以减少感知到的应用程序的延迟？](#queue-polling-interval) |
| &nbsp; |更新消息 |[是否使用“更新消息”操作来存储消息处理进度，以便在发生错误时不必重新处理整个消息？](#use-update-message) |
| &nbsp; |体系结构 |[是否会将长时间运行的工作负载置于关键路径之外，以便使用队列来提高整个应用程序的伸缩性，然后再进行独立的伸缩？](#application-architecture) |

## <a name="scalability-targets"></a>可伸缩性目标

如果应用程序接近或超过任何可伸缩性目标，则可能会出现事务处理延迟或限制越来越严重的现象。 当 Azure 存储对应用程序进行限制时，该服务将开始返回 503（服务器繁忙）或 500（操作超时）错误代码。 保持在可伸缩性目标限制范围内，以避免这些错误，是增强应用程序性能的重要组成部分。

有关队列服务可伸缩性目标的详细信息，请参阅 [Azure 存储可伸缩性和性能目标](/azure/storage/queues/scalability-targets#scale-targets-for-queue-storage)。

### <a name="maximum-number-of-storage-accounts"></a>最大存储帐户数

如果即将达到特定订阅/区域组合允许的最大存储帐户数，你是否会使用多个存储帐户进行分片，以增加流入量、流出量、每秒 I/O 操作次数 (IOPS) 或容量？ 对于这种情况，Microsoft 建议在可能的情况下，利用存储帐户的更高限制来减少工作负荷所需的存储帐户数。 若要请求提高存储帐户的限制，请联系 [Azure 支持部门](https://azure.microsoft.com/support/options/)。 有关详细信息，请参阅[宣布推出更大容量和规模的存储帐户](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/)。

### <a name="capacity-and-transaction-targets"></a>容量和事务目标

如果应用程序正接近单个存储帐户的可伸缩性目标，可考虑采用以下方法之一：  

- 如果队列的可伸缩性目标不足以满足应用程序的需求，请使用多个队列并将消息分配到这些队列。
- 重新考虑导致应用程序接近或超过可伸缩性目标的工作负载。 能否对其进行另外的设计，以便使用较少的带宽、容量或处理事务？
- 如果应用程序肯定会超出伸缩性目标之一，请创建多个存储帐户并将应用程序数据跨多个这样的存储帐户进行分区。 如果使用这种模式，则在设计应用程序时，必须确保能够在以后添加更多的存储帐户，以便进行负载均衡。 存储帐户本身除了用于数据存储、事务处理或数据传输之外，并无其他开销。
- 如果应用程序接近带宽目标，请考虑压缩客户端的数据，以减少将数据发送到 Azure 存储所需的带宽。
    压缩数据虽然可以节省带宽并提高网络性能，但也可能会对性能带来负面影响。 评估客户端数据压缩和解压缩的额外处理要求对性能造成的影响。 请记住，存储压缩数据可能会使故障排除变得更复杂，因为使用标准工具查看这些数据可能会更困难。
- 如果应用程序接近可伸缩性目标，请确保对重试使用指数退避。 最好是尝试通过实施本文中所述的建议来避免达到可伸缩性目标。 但是，对重试使用指数退避会导致应用程序无法快速重试，从而导致限制问题恶化。 有关详细信息，请参阅标题为[超时和服务器繁忙错误](#timeout-and-server-busy-errors)的部分。

## <a name="networking"></a>网络

物理网络对应用程序的约束可能会严重影响性能。 以下部分描述了用户可能会遇到的某些限制。  

### <a name="client-network-capability"></a>客户端网络功能

如以下各部分所述，网络链接的带宽和质量在应用程序性能方面发挥着重要作用。

#### <a name="throughput"></a>吞吐量

通常情况下，对带宽来说，问题在于客户端的功能。 较大的 Azure 实例的 NIC 具有较大的容量，因此如果需要提高单个计算机的网络限制，则应考虑使用较大的实例或更多 VM。 如果从本地应用程序访问 Azure 存储，可应用相同的规则：了解客户端设备的网络功能以及与 Azure 存储位置的网络连接情况，然后根据需要对其进行改进，或者将应用程序设计为可在这种网络功能下工作。

#### <a name="link-quality"></a>链接质量

请注意，因错误和数据包丢失而导致的网络状况会降低有效吞吐量，使用任何网络都是这样。  WireShark 或 NetMon 可用于诊断此问题。  

### <a name="location"></a>位置

在任何分布式环境中，将客户端放置在服务器附近可提供最佳性能。 要以最低的延迟访问 Azure 存储，则最好是将客户端放置在同一 Azure 区域内。 例如，如果 Azure Web 应用使用 Azure 存储，请将二者放在同一个区域（例如美国西部或东南亚）。 将资源放到一起可降低延迟和成本，因为在同一个区域使用带宽是免费的。  

如果客户端应用程序要访问 Azure 存储但不是托管在 Azure 中（例如移动设备应用或本地企业服务），则将存储帐户放在靠近这些客户端的区域可降低延迟。 如果客户端广泛分布在各地（例如，有些客户端分布在北美，有些则在欧洲），请考虑在每个区域使用一个存储帐户。 如果应用程序存储的数据是特定于各个用户的，不需要在存储帐户之间复制数据，则此方法更容易实施。

## <a name="sas-and-cors"></a>SAS 和 CORS

假设你需要授权用户 Web 浏览器或手机应用中运行的代码（例如 JavaScript）访问 Azure 存储中的数据。 一种方法是构建充当代理的服务应用程序。 用户的设备将对服务进行身份验证，而后者又可授权访问 Azure 存储资源。 这样，就可以避免在不安全的设备上公开存储帐户密钥。 但是，此方法会明显增大服务应用程序的开销，因为在用户设备与 Azure 存储之间传输的所有数据必须通过服务应用程序。

使用共享访问签名 (SAS) 即可避免将服务应用程序用作 Azure 存储的代理。 使用 SAS 可让用户设备通过受限访问令牌直接对 Azure 存储发出请求。 例如，如果用户想要将照片上传到应用程序，则服务应用程序可以生成 SAS 并将其发送到用户的设备。 SAS 令牌可按指定的时间间隔授予写入 Azure 存储资源的权限，该时间过后，SAS 令牌将会过期。 有关 SAS 的详细信息，请参阅[使用共享访问签名 (SAS) 授予对 Azure 存储资源的有限访问权限](../common/storage-sas-overview.md)。  

通常，Web 浏览器不允许某个域上的网站所托管的页面中的 JavaScript 对另一个域执行某些操作（例如写入操作）。 此策略称为同源策略，可防止一个页面上的恶意脚本获取另一网页上的数据的访问权限。 但是，在云中构建解决方案时，同源策略可能会成为一种限制。 跨源资源共享 (CORS) 是一种浏览器功能，它使目标域能够与信任源自源域的请求的浏览器通信。

例如，假设 Azure 中运行的某个 Web 应用程序对 Azure 存储帐户发出了某个资源请求。 该 Web 应用程序是源域，存储帐户是目标域。 可为任何 Azure 存储服务配置 CORS，以便与从 Azure 存储信任的源域发出请求的 Web 浏览器通信。 有关 CORS 的详细信息，请参阅 [Azure 存储的跨源资源共享 (CORS) 支持](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services)。  
  
SAS 和 CORS 都有助于避免 Web 应用程序上出现不必要的负载。  

## <a name="net-configuration"></a>.NET 配置

如果使用的是 .NET Framework，则本部分列出的数种快速配置设置可以用于显著提高性能。  如果使用其他语言，则需查看类似的概念是否适用于所选择的语言。  

### <a name="use-net-core"></a>使用 .NET Core

使用 .NET Core 2.1 或更高版本开发 Azure 存储应用程序，以利用性能增强功能。 建议尽量使用 .NET Core 3.x。

有关 .NET Core 的性能改进的详细信息，请参阅以下博客文章：

- [Performance Improvements in .NET Core 3.0](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-3-0/)（.NET Core 3.0 的性能改进）
- [Performance Improvements in .NET Core 2.1](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-2-1/)（.NET Core 2.1 的性能改进）

### <a name="increase-default-connection-limit"></a>提高默认连接限制

在 .NET 中，以下代码可将默认的连接限制（通常情况下，在客户端环境中为 2，在服务器环境中为 10）提高到 100。 通常情况下，应将值大致设置为应用程序使用的线程数。  

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

在打开任何连接前设置连接限制。  

对于其他编程语言，请参阅该语言的文档以确定如何设置连接限制。  

有关详细信息，请参阅博客文章 [Web 服务：并发连接](https://blogs.msdn.microsoft.com/darrenj/2005/03/07/web-services-concurrent-connections/)。  

### <a name="increase-minimum-number-of-threads"></a>增大最小线程数

如果结合异步任务使用同步调用，可能需要增大线程池中的线程数：

```csharp
ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  
```

有关详细信息，请参阅 [ThreadPool.SetMinThreads](/dotnet/api/system.threading.threadpool.setminthreads) 方法。  

## <a name="unbounded-parallelism"></a>不受限制的并行度

虽然并行度有助于提高性能，但在使用不受限制的并行度时应保持谨慎，因为这意味着对线程数或并行请求数没有限制。 请务必限制上传或下载数据、访问同一存储帐户中的多个分区以及访问同一分区中的多个项的并行请求。 如果并行度不受限制，应用程序则可能会超出客户端设备的承受程度或超出存储帐户的可伸缩性目标，导致延迟和限制时间增长。  

## <a name="client-libraries-and-tools"></a>客户端库和工具

为获得最佳性能，请始终使用 Microsoft 提供的最新客户端库和工具。 Azure 存储客户端库适用于各种语言。 Azure 存储还支持 PowerShell 和 Azure CLI。 Microsoft 正在积极开发这些客户端库和工具，并注重其性能，使用最新服务版本对其进行更新，确保这些工具可以在内部协调好许多经过证实的做法。 有关详细信息，请参阅 [Azure 存储参考文档](/azure/storage/#reference)。

## <a name="handle-service-errors"></a>处理服务错误

当服务无法处理请求时，Azure 存储会返回错误。 了解 Azure 存储在特定情况下可能返回的错误将有助于优化性能。

### <a name="timeout-and-server-busy-errors"></a>超时和服务器繁忙错误

如果应用程序即将达到可伸缩性限制，Azure 存储可能会对其进行限制。 在某些情况下，Azure 存储可能会出于某种暂时性的状况而无法处理请求。 对于这两种情况，服务可能返回 503（服务器繁忙）或 500（超时）错误。 如果服务正在对数据分区进行重新均衡以提高吞吐量，则也可能会发生这些错误。 通常，客户端应用程序应重试导致上述某种错误的操作。 但是，如果 Azure 存储因为应用程序即将超出可伸缩性目标而限制应用程序，或者其他某种原因导致服务无法为请求提供服务，则过于频繁的重试可能会使问题变得更糟。 建议使用指数退避重试策略，客户端库默认采用此行为。 例如，应用程序可能会在 2 秒后、4 秒后、10 秒后，以及 30 秒后进行重试，最后彻底放弃重试。 这样，应用程序可明显减少其在服务中施加的负载，而不会使得导致出现限制的行为恶化。  

连接错误可以立即重试，因为它不是限制造成的，而且应该是暂时性的。  

### <a name="non-retryable-errors"></a>不可重试的错误

客户端库将处理重试，同时能够识别哪些错误可重试，哪些不可重试。 但是，如果直接调用 Azure 存储 REST API，则不应重试某些错误。 例如，400（错误的请求）错误表示客户端应用程序发送了一个无法处理的请求（因为该请求未采用预期的格式）。 每次重新发送此请求都会导致相同的响应，因此没有必要重试。 如果直接调用 Azure 存储 REST API，请注意潜在错误以及是否应重试这些错误。

有关 Azure 存储错误代码的详细信息，请参阅[状态和错误代码](/rest/api/storageservices/status-and-error-codes2)。

## <a name="disable-nagle"></a>禁用 Nagle

Nagle 的算法已跨 TCP/IP 网络进行了广泛的实施，是一种改进网络性能的方法。 不过，该方法并非适用于所有情况（例如高度交互式的环境）。 Nagle 的算法会对 Azure 表服务请求的性能造成负面影响，因此应尽量将其禁用。

## <a name="message-size"></a>消息大小

队列性能和可伸缩性随消息增大而下降。 只将接收方所需的信息放在消息中。  

## <a name="batch-retrieval"></a>批量检索

可以在单个操作中从队列检索多达 32 条消息。 批量检索可以减少客户端应用程序的往返次数，尤其适合高延迟环境，例如移动设备。  

## <a name="queue-polling-interval"></a>队列轮询间隔

大多数应用程序会轮询获取队列中的消息，队列可能是适合于该应用程序的事务的最大源之一。 理智选择轮询间隔：轮询过于频繁可能导致应用程序接近队列的可伸缩性目标。 不过，200,000 次事务仅收费 0.01 美元（在撰写本文时），一个处理器每秒轮询一次且持续一个月的成本不到 15 美分，因此成本通常不是影响选择轮询间隔的一项因素。  

如需最新成本信息，请参阅 [Azure 存储定价](https://azure.microsoft.com/pricing/details/storage/)。  

## <a name="use-update-message"></a>使用“更新消息”

可以使用“更新消息”操作来增大不可见性超时或更新消息的状态信息。  与作业每完成一步就将其从一个队列传到下一个队列的工作流相比，使用“更新消息”可能更高效。  应用程序可将作业状态保存到消息，然后可以继续工作，而不必在作业的每一步完成时，为了执行作业的下一步而将消息重新排队。 请注意，每个“更新消息”操作将计入到可伸缩性目标。 

## <a name="application-architecture"></a>应用程序体系结构

使用队列来使应用程序体系结构保持可伸缩性。 下面列出了一些方法，可以通过这些方法，使用队列来提高应用程序的可伸缩性：  

- 可以使用队列来创建需要处理的积压工作，并消除应用程序中的工作负载。 例如，可以将用户发出的执行处理器密集型工作（例如，调整已上传图像的大小）的请求排队。
- 可以使用队列来分离应用程序的某些部分，以便对其进行独立的缩放操作。 例如，Web 前端可以将用户提交的调查结果置于某个队列中，留待以后分析和存储。 可以添加更多的辅助角色实例，使之按要求处理队列数据。  

## <a name="next-steps"></a>后续步骤

- [队列存储的可伸缩性和性能目标](scalability-targets.md)
- [标准存储帐户的可伸缩性和性能目标](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
- [状态和错误代码](/rest/api/storageservices/Status-and-Error-Codes2)
