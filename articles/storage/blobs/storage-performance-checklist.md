---
title: Blob 存储的性能和可伸缩性清单-Azure 存储
description: 用于在开发高性能应用程序时与 Blob 存储配合使用的已验证做法清单。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 56bb5a1ac3c4003eca6ebe8392fc5b97f36a3317
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2019
ms.locfileid: "72311131"
---
# <a name="performance-and-scalability-checklist-for-blob-storage"></a>Blob 存储的性能和可伸缩性清单

Microsoft 已开发了许多经验证的做法，用于使用 Blob 存储开发高性能的应用程序。 此清单列出了开发人员可以遵循的关键做法来优化性能。 在设计应用程序时以及整个过程中，请牢记这些做法。

Azure 存储空间的可伸缩性和性能目标为容量、事务速率和带宽。 有关 Azure 存储可伸缩性目标的详细信息，请参阅[存储帐户的 Azure 存储可伸缩性和性能目标](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

## <a name="checklist"></a>清单

本文针对在开发 Blob 存储应用程序时可以遵循的清单，组织了经验证的性能做法。

| 完成 | 类别 | 设计注意事项 |
| --- | --- | --- |
| &nbsp; |可伸缩性目标 |[能否将应用程序设计为使用的存储帐户不超过最大数目？](#maximum-number-of-storage-accounts) |
| &nbsp; |可伸缩性目标 |[是否要避免接近容量和事务限制？](#capacity-and-transaction-targets) |
| &nbsp; |可伸缩性目标 |[是否有大量客户端同时访问单个 blob？](#multiple-clients-accessing-a-single-blob-concurrently) |
| &nbsp; |可伸缩性目标 |[应用程序是否会保持在单个 blob 的可伸缩性目标内？](#bandwidth-and-operations-per-blob) |
| &nbsp; |分区 |[命名约定是否旨在实现更好的负载均衡？](#partitioning) |
| &nbsp; |网络 |[客户端设备是否具有足够高的带宽和低延迟，以实现所需的性能？](#throughput) |
| &nbsp; |网络 |[客户端设备是否具有高质量的网络链接？](#link-quality) |
| &nbsp; |网络 |[客户端应用程序是否位于与存储帐户相同的区域中？](#location) |
| &nbsp; |直接客户端访问 |[是否使用共享访问签名（SAS）和跨域资源共享（CORS）来启用对 Azure 存储的直接访问？](#sas-and-cors) |
| &nbsp; |缓存 |[你的应用程序是否会缓存经常访问和很少更改的数据？](#reading-data) |
| &nbsp; |缓存 |[应用程序是否通过在客户端上缓存更新来对更新进行批处理，然后将其上传到更大的集中？](#uploading-data-in-batches) |
| &nbsp; |.NET 配置 |[你是否正在使用 .NET Core 2.1 或更高版本来获得最佳性能？](#use-net-core) |
| &nbsp; |.NET 配置 |[是否已将客户端配置为使用足够数量的并发连接？](#increase-default-connection-limit) |
| &nbsp; |.NET 配置 |[对于 .NET 应用程序，是否已将 .NET 配置为使用足够数量的线程？](#increase-minimum-number-of-threads) |
| &nbsp; |并行度 |[您是否确保了并行度已正确界定，以便不会重载您的客户端功能或可伸缩性目标？](#unbounded-parallelism) |
| &nbsp; |工具 |[你是否正在使用最新版本的 Microsoft 提供的客户端库和工具？](#client-libraries-and-tools) |
| &nbsp; |重试 |[是否对限制错误和超时使用具有指数回退的重试策略？](#timeout-and-server-busy-errors) |
| &nbsp; |重试 |[对于不可重试的错误，应用程序是否会避免重试？](#non-retryable-errors) |
| &nbsp; |复制 blob |[是否以最有效的方式复制 blob？](#blob-copy-apis) |
| &nbsp; |复制 blob |[是否对大容量复制操作使用最新版本的 AzCopy？](#use-azcopy) |
| &nbsp; |复制 blob |[是否在使用 Azure Data Box 系列来导入大量数据？](#use-azure-data-box) |
| &nbsp; |内容分发 |[是否要使用 CDN 进行内容分发？](#content-distribution) |
| &nbsp; |使用元数据 |[是否会将频繁使用的有关 Blob 的元数据存储在其元数据中？](#use-metadata) |
| &nbsp; |快速上传 |[尝试快速上传一个 Blob 时，是否会以并行方式上传块？](#upload-one-large-blob-quickly) |
| &nbsp; |快速上传 |[尝试快速上传许多 Blob 时，是否会以并行方式上传 Blob？](#upload-many-blobs-quickly) |
| &nbsp; |Blob 类型 |[是否会根据需要使用页 Blob 或块 Blob？](#choose-the-correct-type-of-blob) |

## <a name="scalability-targets"></a>可伸缩性目标

如果应用程序接近或超过任何可伸缩性目标，则可能会出现事务处理延迟或限制越来越严重的现象。 当 Azure 存储空间限制应用程序时，服务将开始返回503（服务器繁忙）或500（操作超时）错误代码。 若要避免这些错误，请在可伸缩性目标的限制范围内实现，这是提高应用程序性能的重要组成部分。

有关队列服务的可伸缩性目标的详细信息，请参阅[Azure 存储可伸缩性和性能目标](/azure/storage/common/storage-scalability-targets?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-blob-storage-scale-targets)。

### <a name="maximum-number-of-storage-accounts"></a>最大存储帐户数

如果接近特定订阅/区域组合所允许的存储帐户的最大数量，请评估方案，并确定是否适用以下任何情况：

- 使用存储帐户来存储非托管磁盘，并将这些磁盘添加到虚拟机（Vm）吗？ 对于此方案，Microsoft 建议使用托管磁盘。 托管磁盘会自动缩放，无需创建和管理单独的存储帐户。 有关详细信息，请参阅[Azure 托管磁盘简介](../../virtual-machines/windows/managed-disks-overview.md)
- 是否为每个客户使用一个存储帐户，以便进行数据隔离？ 对于此方案，Microsoft 建议为每个客户使用 blob 容器，而不是使用整个存储帐户。 Azure 存储现在允许基于每个容器分配基于角色的访问控制（RBAC）角色。 有关详细信息，请参阅[在 Azure 门户中使用 RBAC 授予对 Azure blob 和队列数据的访问权限](../common/storage-auth-aad-rbac-portal.md)。
- 是否在使用多个存储帐户来分片增加入口、出口、每秒 i/o 操作数（IOPS）或容量？ 在这种情况下，Microsoft 建议你充分利用标准存储帐户的限制，以减少工作负载所需的存储帐户数（如果可能）。 联系[Azure 支持](https://azure.microsoft.com/support/options/)人员，请求增加存储帐户的限制。 有关详细信息，请参阅[宣布更大、更大规模的存储帐户](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/)。

### <a name="capacity-and-transaction-targets"></a>容量和事务目标

如果应用程序正接近单个存储帐户的可伸缩性目标，可考虑采用以下方法之一：  

- 如果你的应用程序达到了事务目标，则请考虑使用块 blob 存储帐户，这些帐户经过优化，可实现高事务速率和低和一致性延迟。 有关详细信息，请参阅 [Azure 存储帐户概述](../common/storage-account-overview.md)。
- 重新考虑导致应用程序接近或超过可伸缩性目标的工作负载。 能否对其进行另外的设计，以便使用较少的带宽、容量或处理事务？
- 如果你的应用程序必须超过可伸缩性目标之一，则创建多个存储帐户，并在这些多个存储帐户之间对应用程序数据进行分区。 如果使用这种模式，则在设计应用程序时，必须确保能够在以后添加更多的存储帐户，以便进行负载均衡。 存储帐户本身在存储、事务或传输的数据方面不会有任何费用。
- 如果你的应用程序达到了带宽目标，则考虑在客户端压缩数据，以减少将数据发送到 Azure 存储所需的带宽。
    压缩数据可以节省带宽和提高网络性能，还可以对性能产生负面影响。 评估客户端的数据压缩和解压缩的额外处理要求对性能的影响。 请记住，存储压缩数据会使故障排除更加困难，因为使用标准工具查看数据可能会更困难。
- 如果你的应用程序接近可伸缩性目标，则请确保对重试使用指数回退。 最好尝试通过实现本文中所述的建议避免达到可伸缩性目标。 但是，对重试使用指数回退会使应用程序无法快速重试，这可能导致限制更糟。 有关详细信息，请参阅标题为[超时和服务器繁忙错误](#timeout-and-server-busy-errors)的部分。

### <a name="multiple-clients-accessing-a-single-blob-concurrently"></a>多个客户端同时访问单个 blob

如果有大量客户端同时访问单个 blob，则需要考虑每个 blob 和每个存储帐户的可伸缩性目标。 可以访问单个 blob 的客户端确切数量因多种因素而异，例如请求 blob 的客户端数量、blob 的大小和网络条件。

如果可以通过 CDN （如来自网站的图像或视频）分发该 blob，则可以使用 CDN。 有关详细信息，请参阅标题为[内容分发](#content-distribution)的部分。

在其他情况下，如数据机密的科学模拟，你有两种选择。 第一种是错开工作负荷的访问，以便在一段时间内访问 blob，而不是同时进行访问。 或者，可以临时将 blob 复制到多个存储帐户，以增加每个 blob 和存储帐户的总 IOPS。 结果根据应用程序的行为而有所不同，因此请确保在设计期间测试并发模式。

### <a name="bandwidth-and-operations-per-blob"></a>每个 blob 的带宽和操作

单个 blob 每秒最多支持500请求。 如果有多个客户端需要读取同一 blob 并且可能超出此限制，请考虑使用块 blob 存储帐户。 块 blob 存储帐户提供更高的请求速率或每秒 i/o 操作数（IOPS）。

你还可以使用内容交付网络（CDN）（如 Azure CDN）在 blob 上分发操作。 有关 Azure CDN 的详细信息，请参阅[Azure CDN 概述](../../cdn/cdn-overview.md)。  

## <a name="partitioning"></a>分区

了解 Azure 存储分区 blob 数据的方式对于提高性能十分有用。 与跨多个分区的数据相比，Azure 存储空间可以更快地在单个分区中提供数据。 通过适当地命名 blob，可以提高读取请求的效率。

Blob 存储使用基于范围的分区方案进行缩放和负载均衡。 每个 blob 都有一个分区键，其中包含完整的 blob 名称（account + container + blob）。 分区键用于将 blob 数据分区为范围。 然后，在 Blob 存储中对这些范围进行负载均衡。

基于范围的分区意味着使用词汇排序的命名约定（例如， *mypayroll*、 *myperformance*、 *myemployees*等）或时间戳（*log20160101*、 *log20160102*、 *log20160102*等）更有可能导致分区位于同一个分区服务器上。 直到增加的负载要求它们拆分为较小的范围。 在同一分区服务器上归置 blob 可以提高性能，因此，性能增强的一个重要部分就是以最有效地组织它们的方式命名 blob。

例如，容器中的所有 Bob 可接受单个服务器的服务，直到这些 Blob 的负载需要进一步进行重新平衡分区范围。 同样，一组按词法顺序排列的一组非常简单的帐户可能由单个服务器提供服务，直到其中一个或所有帐户的负载要求它们拆分到多个分区服务器。

每个负载均衡操作可能在操作期间会影响存储调用的延迟。 该服务处理到分区的突发流量的能力受到单个分区服务器的可伸缩性的限制，直到负载均衡操作启动并间重新平衡分区键范围。

可以遵循一些最佳实践来降低此类操作的频率。  

- 如果可能，请为标准存储帐户使用大于 4 MiB 的 blob 或块大小，为高级存储帐户使用大于256的 KiB。 较大的 blob 或块大小会自动激活高吞吐量块 blob。 高吞吐量块 blob 提供不受分区命名影响的高性能引入。
- 检查用于帐户、容器、blob、表和队列的命名约定。 请考虑使用最符合需求的哈希函数作为帐户、容器或 blob 名称的前缀，并使用三位数哈希。
- 如果使用时间戳或数字标识符来组织数据，请确保使用的不是仅限追加（或仅预置）的流量模式。 这些模式不适用于基于范围的分区系统。 这些模式可能导致所有流量进入单个分区并限制系统有效负载平衡。

    例如，如果你的日常操作使用带有时间戳的 blob，如*yyyymmdd*，则该日常操作的所有流量将定向到单个分区服务器提供的单个 blob。 考虑每个 blob 的限制和每个分区的限制是否符合您的需要，如果需要，请考虑将此操作分解为多个 blob。 同样，如果在表中存储时序数据，则所有流量都可能定向到键命名空间的最后一部分。 如果使用的是数字 Id，请在 ID 前面加上三位数哈希。 如果使用时间戳，请在时间戳前面加上秒的值，例如*ssyyyymmdd*。 如果你的应用程序定期执行列出和查询操作，请选择将限制查询数量的哈希函数。 在某些情况下，随机前缀可能就已足够。
  
- 有关 Azure 存储中使用的分区方案的详细信息，@no__t 请参阅 0Azure Storage：具有非常一致性的高可用云存储服务](https://sigops.org/sosp/sosp11/current/2011-Cascais/printable/11-calder.pdf)。

## <a name="networking"></a>网络

应用程序的物理网络约束可能会对性能产生重大影响。 以下部分描述了用户可能会遇到的一些限制。  

### <a name="client-network-capability"></a>客户端网络功能

网络链接的带宽和质量在应用程序性能方面扮演着重要角色，如以下各节所述。

#### <a name="throughput"></a>吞吐量

通常情况下，对带宽来说，问题在于客户端的功能。 较大的 Azure 实例的 NIC 具有较大的容量，因此如果需要提高单个计算机的网络限制，则应考虑使用较大的实例或更多 VM。 如果要从本地应用程序访问 Azure 存储，则会应用相同的规则：了解客户端设备的网络功能以及与 Azure 存储位置的网络连接性，并根据需要对其进行改进或设计应用程序在其功能中工作。

#### <a name="link-quality"></a>链接质量

与任何网络使用情况一样，请记住导致错误和数据包丢失的网络状况会降低有效吞吐量。  WireShark 或 NetMon 可用于诊断此问题。  

### <a name="location"></a>Location

在任何分布式环境中，将客户端放置在服务器附近可提供最佳性能。 要以最低的延迟访问 Azure 存储，则最好是将客户端放置在同一 Azure 区域内。 例如，如果你有使用 Azure 存储的 Azure web 应用，请在单个区域（例如美国西部或亚洲东南部）中找到它们。 归置资源会降低延迟和成本，因为单个区域内的带宽使用是免费的。  

如果客户端应用程序将访问 Azure 存储，而不是托管在 Azure 中（例如移动设备应用或本地企业服务），那么，在附近的区域中查找存储帐户可能会降低延迟。 如果客户端分布广泛（例如，某些客户端在北美中，一些在欧洲），请考虑对每个区域使用一个存储帐户。 如果应用程序存储的数据是特定于各个用户的，不需要在存储帐户之间复制数据，则此方法更容易实施。

若要广泛地分发 blob 内容，请使用内容交付网络，如 Azure CDN。 关于 Azure CDN 的详细信息，请参阅 [Azure CDN](../../cdn/cdn-overview.md)。  

## <a name="sas-and-cors"></a>SAS 和 CORS

假设你需要授权代码（如 JavaScript），这些代码在用户的 web 浏览器或移动电话应用程序中运行，用于访问 Azure 存储中的数据。 一种方法是构建充当代理的服务应用程序。 用户的设备使用服务进行身份验证，而后者又授权访问 Azure 存储资源。 这样，就可以避免在不安全的设备上公开存储帐户密钥。 但是，这种方法会给服务应用程序带来很大的开销，因为在用户设备和 Azure 存储之间传输的所有数据都必须通过服务应用程序。

可以通过使用共享访问签名（SAS）避免将服务应用程序用作 Azure 存储的代理。 使用 SAS，可以让用户的设备通过受限访问令牌将请求直接发送到 Azure 存储。 例如，如果用户想要将照片上传到应用程序，则服务应用程序可以生成 SAS 并将其发送到用户的设备。 SAS 令牌可以在指定的时间间隔内授予写入 Azure 存储资源的权限，超过该时间间隔后，SAS 令牌将过期。 有关 SAS 的详细信息，请参阅[使用共享访问签名 (SAS) 授予对 Azure 存储资源的有限访问权限](../common/storage-sas-overview.md)。  

通常，web 浏览器不会允许某个域上的网站所托管的页面中的 JavaScript 执行某些操作（如写入操作）到另一个域。 此策略称为相同源策略，可防止一页上的恶意脚本获取对其他网页上的数据的访问权限。 但是，在云中生成解决方案时，相同的源策略可能会受到限制。 跨域资源共享（CORS）是一种浏览器功能，使目标域能够与它信任源域中的请求的浏览器进行通信。

例如，假设在 Azure 中运行的 web 应用程序向 Azure 存储帐户请求资源。 Web 应用程序是源域，存储帐户是目标域。 可以为任何 Azure 存储服务配置 CORS，使其与 web 浏览器进行通信，以便从源域请求受 Azure 存储的信任。 有关 CORS 的详细信息，请参阅[Azure 存储的跨域资源共享（CORS）支持](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services)。  
  
SAS 和 CORS 都有助于避免 web 应用程序上出现不必要的负载。  

## <a name="caching"></a>缓存

缓存在性能方面扮演着重要的角色。 以下各节讨论缓存的最佳实践。

### <a name="reading-data"></a>读取数据

通常，读取数据一次比读取数据两次更为可取。 假设有一个 web 应用程序的示例，该应用程序已从 Azure 存储中检索 50 MiB blob 以作为用户的内容。 理想情况下，应用程序在本地将 blob 缓存到磁盘，然后检索缓存的版本以获取后续的用户请求。

避免检索 blob 的一种方法是，如果未对其进行修改，因为它已被缓存，因此需要使用条件标头来修改时间。 如果上次修改时间在缓存 blob 的时间之后，则将检索并重新缓存 blob。 否则，将检索缓存的 blob 以获得最佳性能。

您还可以决定将应用程序设计为假定 blob 在检索后短时间内保持不变。 在这种情况下，应用程序不需要检查是否在该时间间隔内修改了 blob。

配置数据、查找数据和应用程序经常使用的其他数据都适合进行缓存。  

有关使用条件标头的详细信息，请参阅为[Blob 服务操作指定条件标头](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations)。  

### <a name="uploading-data-in-batches"></a>成批上载数据

在某些情况下，你可以在本地聚合数据，然后以批处理方式定期上传数据，而不是立即上传每个数据片段。 例如，假设 web 应用程序保留活动的日志文件。 应用程序可以在表中上传发生的每个活动的详细信息（这需要许多存储操作），也可以将活动详细信息保存到本地日志文件中，然后定期将所有活动详细信息作为分隔文件上传到 blob。 如果每个日志条目的大小为 1 KB，则可以在单个事务中上载上千个条目。 单个事务支持上传大小最多为 64 MiB 的 blob。 应用程序开发人员必须针对可能发生的客户端设备或上传故障进行设计。 如果需要下载活动数据的时间间隔而不是单个活动，则建议使用 Blob 存储而不是单个活动。

## <a name="net-configuration"></a>.NET 配置

如果使用的是 .NET Framework，则本部分列出的数种快速配置设置可以用于显著提高性能。  如果使用其他语言，则需查看类似的概念是否适用于所选择的语言。  

### <a name="use-net-core"></a>使用 .NET Core

开发使用 .NET Core 2.1 或更高版本的 Azure 存储应用程序以利用性能增强功能。 如果可能，建议使用 .NET Core 2.x。

有关 .NET Core 中的性能改进的详细信息，请参阅以下博客文章：

- [.NET Core 3.0 的性能改进](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-3-0/)
- [.NET Core 2.1 的性能改进](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-2-1/)

### <a name="increase-default-connection-limit"></a>提高默认连接限制

在 .NET 中，以下代码将默认连接限制（通常在客户端环境中为2，在服务器环境中为10）增加到100。 通常情况下，应将值大致设置为应用程序使用的线程数。 在打开任何连接前设置连接限制。

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

对于其他编程语言，请参阅文档以确定如何设置连接限制。  

有关详细信息，请参阅博客文章 [Web 服务：并发连接](https://blogs.msdn.microsoft.com/darrenj/2005/03/07/web-services-concurrent-connections/)。  

### <a name="increase-minimum-number-of-threads"></a>增加最小线程数

如果同时使用同步调用和异步任务，您可能需要增加线程池中的线程数：

```csharp
ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  
```

有关详细信息，请参阅[SetMinThreads](/dotnet/api/system.threading.threadpool.setminthreads)方法。  

## <a name="unbounded-parallelism"></a>无限并行度

尽管并行性可以很好地提高性能，但使用无限并行度时要小心，这意味着不会对线程数或并行请求施加限制。 请确保限制并行请求以上载或下载数据、访问同一存储帐户中的多个分区，或访问同一分区中的多个项。 如果并行度不受限制，你的应用程序可能会超出客户端设备的功能或存储帐户的可伸缩性目标，从而导致更长的延迟和限制。  

## <a name="client-libraries-and-tools"></a>客户端库和工具

为了获得最佳性能，请始终使用 Microsoft 提供的最新客户端库和工具。 Azure 存储客户端库适用于多种语言。 Azure 存储还支持 PowerShell 和 Azure CLI。 Microsoft 积极地开发这些客户端库和工具的性能，使其与最新的服务版本保持最新状态，并确保它们能在内部处理许多经验证的性能做法。 有关详细信息，请参阅[Azure 存储参考文档](/azure/storage/#reference)。

## <a name="handle-service-errors"></a>处理服务错误

当服务无法处理请求时，Azure 存储将返回错误。 了解在给定方案中可能由 Azure 存储返回的错误有助于优化性能。

### <a name="timeout-and-server-busy-errors"></a>超时和服务器繁忙错误

如果你的应用程序接近可伸缩性限制，Azure 存储可能会限制你的应用程序。 在某些情况下，由于某些暂时性情况，Azure 存储可能无法处理请求。 在这两种情况下，服务可能返回503（服务器繁忙）或500（超时）错误。 如果服务正在重新平衡数据分区以获得更高的吞吐量，则也会发生这些错误。 通常，客户端应用程序应重试导致其中一个错误的操作。 但是，如果由于 Azure 存储空间超过可伸缩性目标而限制应用程序，或者服务由于其他原因而无法处理请求，则重新尝试会使问题更糟。 建议使用指数回退重试策略，并且客户端库默认为此行为。 例如，应用程序可能会在 2 秒后、4 秒后、10 秒后，以及 30 秒后进行重试，最后彻底放弃重试。 通过这种方式，你的应用程序将大大降低其对服务的负载，而不是可能导致限制的更糟行为。  

连接错误可以立即重试，因为它不是限制造成的，而且应该是暂时性的。  

### <a name="non-retryable-errors"></a>不可重试的错误

客户端库处理重试，并意识到哪些错误可以重试，哪些不能重试。 但是，如果直接调用 Azure 存储 REST API，则会出现一些错误，不应重试。 例如，400（错误请求）错误表示客户端应用程序发送了无法处理的请求，因为它不是预期的形式。 重新发送此请求每次都会导致相同的响应，因此没有任何重试点。 如果直接 REST API 调用 Azure 存储，请注意潜在错误以及是否应重试这些错误。

有关 Azure 存储错误代码的详细信息，请参阅[状态和错误代码](/rest/api/storageservices/status-and-error-codes2)。

## <a name="copying-and-moving-blobs"></a>复制和移动 Blob

Azure 存储提供多种解决方案，用于在存储帐户中、在存储帐户之间以及在本地系统和云之间复制和移动 blob。 本部分介绍这些选项对性能的影响。 有关有效地将数据传输到 Blob 存储或从 Blob 存储传输数据的信息，请参阅[选择用于数据传输的 Azure 解决方案](../common/storage-choose-data-transfer-solution.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

### <a name="blob-copy-apis"></a>Blob 复制 Api

若要跨存储帐户复制 blob，请使用 URL 操作的[Put 块](/rest/api/storageservices/put-block-from-url)。 此操作将数据从任何 URL 源同步复制到块 blob。 如果要跨存储帐户迁移数据，使用 @no__t 操作可以显著减少所需的带宽。 由于复制操作发生在服务端，因此无需下载并重新上传数据。

若要复制同一存储帐户中的数据，请使用[复制 Blob](/rest/api/storageservices/Copy-Blob)操作。 复制同一存储帐户中的数据通常会快速完成。  

### <a name="use-azcopy"></a>使用 AzCopy

AzCopy 命令行实用程序是一种简单且高效的选项，可用于大容量地将 blob 从存储帐户复制到、从存储帐户和跨存储帐户。 AzCopy 针对此方案进行了优化，并可实现高传输速率。 AzCopy 版本10使用 `Put Block From URL` 操作来跨存储帐户复制 blob 数据。 有关详细信息，请参阅[使用 AzCopy V10 将数据复制或移动到 Azure 存储](/azure/storage/common/storage-use-azcopy-v10)。  

### <a name="use-azure-data-box"></a>使用 Azure Data Box

若要在 Blob 存储中导入大量数据，请考虑使用 Azure Data Box 系列进行脱机传输。 当你按时间、网络可用性或成本进行限制时，Microsoft 提供的 Data Box 设备可用于将大量数据移到 Azure。 有关详细信息，请参阅[Azure DataBox 文档](/azure/databox/)。

## <a name="content-distribution"></a>内容分发

有时，应用程序需要向位于同一区域或多个区域的许多用户提供相同的内容（例如网站主页中使用的产品演示视频）。 在这种情况下，请使用内容交付网络（CDN）（如 Azure CDN）在地理上分发 blob 内容。 与存在于一个区域且无法以低延迟向其他区域交付内容的 Azure 存储帐户不同，Azure CDN 使用位于全世界多个数据中心的服务器。 此外，与单个存储帐户相比，CDN 通常可以支持更高的出口限制。  

关于 Azure CDN 的详细信息，请参阅 [Azure CDN](../../cdn/cdn-overview.md)。

## <a name="use-metadata"></a>使用元数据

Blob 服务支持 HEAD 请求，这些请求可以包含 Blob 属性或元数据。 例如，如果应用程序需要照片中的 Exif （exchangable 图像格式）数据，则可以检索照片并将其解压缩。 为了节省带宽并改进性能，应用程序可以在应用程序上传照片时将 Exif 数据存储在 blob 的元数据中。 然后，只使用 HEAD 请求便可检索元数据中的 Exif 数据。 仅检索元数据，而不检索 blob 的全部内容可节省大量带宽，并减少提取 Exif 数据所需的处理时间。 请记住，每个 blob 可以存储 8 KiB 元数据。  

## <a name="upload-blobs-quickly"></a>快速上传 blob

若要快速上传 blob，请首先确定是要上传一个还是多个 blob。 请参阅以下指南，根据具体情况确定要使用的正确方法。  

### <a name="upload-one-large-blob-quickly"></a>快速上传一个大型 blob

若要快速上传一个大型 blob，客户端应用程序可以并行上传其块或页，同时注意各个 blob 的可伸缩性目标和整个存储帐户的可伸缩性目标。 Azure 存储客户端库支持并行上传。 例如，你可以使用以下属性来指定 .NET 或 Java 中允许的并发请求数。 其他支持的语言的客户端库提供了类似的选项。

- 对于 .NET，请设置[BlobRequestOptions. ParallelOperationThreadCount](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.paralleloperationthreadcount)属性。
- 对于 Java/Android，请调用[BlobRequestOptions. blobrequestoptions.setconcurrentrequestcount （Final Integer concurrentRequestCount）](/java/api/com.microsoft.azure.storage.blob._blob_request_options.setconcurrentrequestcount)方法。

### <a name="upload-many-blobs-quickly"></a>快速上传多个 blob

若要快速上传多个 Blob，请以并行方式上传。 并行上传比上传单个 blob 的速度更快，因为它将上传到存储服务的多个分区中。 默认情况下，AzCopy 以并行方式执行上载，建议用于此方案。 有关详细信息，请参阅[AzCopy 入门](../common/storage-use-azcopy-v10.md)。  

## <a name="choose-the-correct-type-of-blob"></a>选择正确的 blob 类型

Azure 存储支持块 blob、追加 blob 和页 blob。 在给定的使用方案中，对 Blob 类型的选择会影响到解决方案的执行情况和可伸缩性。

当你希望有效地上传大量数据时，块 blob 适用。 例如，将照片或视频上传到 Blob 存储的客户端应用程序将目标为块 blob。

追加 blob 类似于块 blob，因为它们是由块组成的。 修改追加 blob 时，块仅添加到 blob 的末尾。 当应用程序需要将数据添加到现有 blob 时，附加 blob 适用于日志记录等方案。

如果应用程序需要对数据执行随机写入，则适用于页 blob。 例如，Azure 虚拟机磁盘存储为页 blob。 有关详细信息，请参阅[了解块 blob、追加 blob 和页 blob](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)。  

## <a name="next-steps"></a>后续步骤

- [存储帐户的 Azure 存储可伸缩性和性能目标](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [队列存储的性能和可伸缩性清单](../queues/storage-performance-checklist.md)
- [表存储的性能和可伸缩性清单](../tables/storage-performance-checklist.md)
- [状态和错误代码](/rest/api/storageservices/Status-and-Error-Codes2)
