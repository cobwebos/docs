---
title: Blob 存储的性能与可伸缩性查检表 - Azure 存储
description: 开发高性能应用程序时有关 Blob 存储的经证实的做法查检表。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 24d601dc2116b7daf315bb3c6f20c4dc0b6f6ce5
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72382038"
---
# <a name="performance-and-scalability-checklist-for-blob-storage"></a>Blob 存储的性能与可伸缩性查检表

Microsoft 已开发了许多经验证的做法，用于使用 Blob 存储开发高性能的应用程序。 此查检表列出了开发人员在优化性能时可以遵循的关键做法。 在设计应用程序时以及在整个流程中，请牢记这些做法。

Azure 存储在容量、事务速率和带宽方面存在可伸缩性与性能目标。 有关 Azure 存储可伸缩性目标的详细信息，请参阅 [Azure 存储的存储帐户可伸缩性和性能目标](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

## <a name="checklist"></a>清单

本文以查检表的形式组织了在开发 Blob 存储应用程序时在性能方面可以遵循的经过证实的做法。

| 完成 | 类别 | 设计注意事项 |
| --- | --- | --- |
| &nbsp; |可伸缩性目标 |[是否可将应用程序设计为避免使用的存储帐户数超过最大数目？](#maximum-number-of-storage-accounts) |
| &nbsp; |可伸缩性目标 |[是否要避免接近容量和事务限制？](#capacity-and-transaction-targets) |
| &nbsp; |可伸缩性目标 |[是否有大量的客户端并发访问单个 Blob？](#multiple-clients-accessing-a-single-blob-concurrently) |
| &nbsp; |可伸缩性目标 |[应用程序是否保留在单个 Blob 的可伸缩性目标范围内？](#bandwidth-and-operations-per-blob) |
| &nbsp; |分区 |[命名约定是否旨在实现更好的负载均衡？](#partitioning) |
| &nbsp; |联网 |[客户端设备是否具有足够高的带宽和足够低的延迟，以实现所需的性能？](#throughput) |
| &nbsp; |联网 |[客户端设备是否具有优质网络链接？](#link-quality) |
| &nbsp; |联网 |[客户端应用程序是否位于存储帐户所在的同一区域？](#location) |
| &nbsp; |直接客户端访问 |[是否使用共享访问签名 (SAS) 和跨源资源共享 (CORS) 来实现对 Azure 存储的直接访问？](#sas-and-cors) |
| &nbsp; |缓存 |[应用程序是否缓存经常访问且极少更改的数据？](#reading-data) |
| &nbsp; |缓存 |[应用程序是否会对更新进行批处理：将更新缓存在客户端，然后以较大集的形式上传更新？](#uploading-data-in-batches) |
| &nbsp; |.NET 配置 |[是否使用 .NET Core 2.1 或更高版本来实现最佳性能？](#use-net-core) |
| &nbsp; |.NET 配置 |[是否已将客户端配置为使用足够数量的并发连接？](#increase-default-connection-limit) |
| &nbsp; |.NET 配置 |[对于 .NET 应用程序，是否已将 .NET 配置为使用足够数量的线程？](#increase-minimum-number-of-threads) |
| &nbsp; |并行度 |[是否能够确保对并行度进行适当的界定，使客户端功能不会过载或接近可伸缩性目标？](#unbounded-parallelism) |
| &nbsp; |工具 |[是否使用 Microsoft 提供的最新版客户端库和工具？](#client-libraries-and-tools) |
| &nbsp; |重试 |[是否对限制错误和超时使用重试策略和指数退避？](#timeout-and-server-busy-errors) |
| &nbsp; |重试 |[对于不可重试的错误，应用程序是否会避免重试？](#non-retryable-errors) |
| &nbsp; |复制 Blob |[是否以最高效的方式复制 Blob？](#blob-copy-apis) |
| &nbsp; |复制 Blob |[是否使用最新版本的 AzCopy 执行批量复制操作？](#use-azcopy) |
| &nbsp; |复制 Blob |[是否在使用 Azure Data Box 系列来导入大量数据？](#use-azure-data-box) |
| &nbsp; |内容分发 |[是否要使用 CDN 进行内容分发？](#content-distribution) |
| &nbsp; |使用元数据 |[是否会将频繁使用的有关 Blob 的元数据存储在其元数据中？](#use-metadata) |
| &nbsp; |快速上传 |[尝试快速上传一个 Blob 时，是否会以并行方式上传块？](#upload-one-large-blob-quickly) |
| &nbsp; |快速上传 |[尝试快速上传许多 Blob 时，是否会以并行方式上传 Blob？](#upload-many-blobs-quickly) |
| &nbsp; |Blob 类型 |[是否会根据需要使用页 Blob 或块 Blob？](#choose-the-correct-type-of-blob) |

## <a name="scalability-targets"></a>可伸缩性目标

如果应用程序接近或超过任何可伸缩性目标，则可能会出现事务处理延迟或限制越来越严重的现象。 当 Azure 存储对应用程序进行限制时，该服务将开始返回 503（服务器繁忙）或 500（操作超时）错误代码。 保持在可伸缩性目标限制范围内，以避免这些错误，是增强应用程序性能的重要组成部分。

有关队列服务可伸缩性目标的详细信息，请参阅 [Azure 存储可伸缩性和性能目标](/azure/storage/common/storage-scalability-targets?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-blob-storage-scale-targets)。

### <a name="maximum-number-of-storage-accounts"></a>最大存储帐户数

如果即将达到特定订阅/区域组合允许的最大存储帐户数，请评估你的方案并确定是否符合以下任何条件：

- 是否使用存储帐户作为非托管磁盘，并将这些磁盘添加到虚拟机 (VM)？ 对于此方案，Microsoft 建议使用托管磁盘。 托管磁盘可自动缩放，你无需创建和管理单个存储帐户。 有关详细信息，请参阅 [Azure 托管磁盘简介](../../virtual-machines/windows/managed-disks-overview.md)
- 是否对每个客户使用一个存储帐户，以实现数据隔离？ 对于此方案，Microsoft 建议为每个客户使用 blob 容器，而不是使用整个存储帐户。 Azure 存储现在允许按容器分配基于角色的访问控制 (RBAC)。 有关详细信息，请参阅[在 Azure 门户中使用 RBAC 授予对 Azure Blob 和队列数据的访问权限](../common/storage-auth-aad-rbac-portal.md)。
- 是否使用多个存储帐户进行分片，以增加流入量、流出量、每秒 I/O 操作次数 (IOPS) 或容量？ 对于这种情况，Microsoft 建议在可能的情况下，利用存储帐户的更高限制来减少工作负荷所需的存储帐户数。 若要请求提高存储帐户的限制，请联系 [Azure 支持部门](https://azure.microsoft.com/support/options/)。 有关详细信息，请参阅[宣布推出更大容量和规模的存储帐户](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/)。

### <a name="capacity-and-transaction-targets"></a>容量和事务目标

如果应用程序正接近单个存储帐户的可伸缩性目标，可考虑采用以下方法之一：  

- 如果应用程序达到了事务目标，请考虑使用块 Blob 存储帐户。这些帐户经过优化，可实现较高的事务速率和持续较低的延迟。 有关详细信息，请参阅 [Azure 存储帐户概述](../common/storage-account-overview.md)。
- 重新考虑导致应用程序接近或超过可伸缩性目标的工作负载。 能否对其进行另外的设计，以便使用较少的带宽、容量或处理事务？
- 如果应用程序肯定会超出伸缩性目标之一，请创建多个存储帐户并将应用程序数据跨多个这样的存储帐户进行分区。 如果使用这种模式，则在设计应用程序时，必须确保能够在以后添加更多的存储帐户，以便进行负载均衡。 存储帐户本身除了用于数据存储、事务处理或数据传输之外，并无其他开销。
- 如果应用程序接近带宽目标，请考虑压缩客户端的数据，以减少将数据发送到 Azure 存储所需的带宽。
    压缩数据虽然可以节省带宽并提高网络性能，但也可能会对性能带来负面影响。 评估客户端数据压缩和解压缩的额外处理要求对性能造成的影响。 请记住，存储压缩数据可能会使故障排除变得更复杂，因为使用标准工具查看这些数据可能会更困难。
- 如果应用程序接近可伸缩性目标，请确保对重试使用指数退避。 最好是尝试通过实施本文中所述的建议来避免达到可伸缩性目标。 但是，对重试使用指数退避会导致应用程序无法快速重试，从而导致限制问题恶化。 有关详细信息，请参阅标题为[超时和服务器繁忙错误](#timeout-and-server-busy-errors)的部分。

### <a name="multiple-clients-accessing-a-single-blob-concurrently"></a>并发访问单个 Blob 的多个客户端

如果有大量的客户端并发访问单个 Blob，则需要要考虑每个 Blob 和每个存储帐户的可伸缩性目标。 可以访问单个 Blob 的客户端的具体数量根据各种因素（例如，同时请求 Blob 的客户端数、Blob 的大小和网络状况）而有所不同。

如果可以通过 CDN （如来自网站的图像或视频）分发该 blob，则可以使用 CDN。 有关详细信息，请参阅标题为[内容分发](#content-distribution)的部分。

在其他情况下，例如在数据保密的科学模拟下，有两个选项。 第一个选项是错开工作负荷的访问，以确保在某个时间段内访问 Blob，而不是同时访问 Blob。 另一个选项是暂时将 Blob 复制到多个存储帐户，以增加每个 Blob 和存储帐户内的 IOPS 总数。 结果将根据应用程序的行为而异，因此，请务必在设计期间测试并发模式。

### <a name="bandwidth-and-operations-per-blob"></a>每个 Blob 的带宽和操作

单个 Blob 每秒最多可支持 500 个请求。 如果多个客户端需要读取同一 Blob，而你可能会超过此限制，请考虑使用块 Blob 存储帐户。 块 Blob 存储帐户提供更高的请求速率或每秒 I/O 操作次数 (IOPS)。

你还可以使用内容交付网络（CDN）（如 Azure CDN）在 blob 上分发操作。 有关 Azure CDN 的详细信息，请参阅[Azure CDN 概述](../../cdn/cdn-overview.md)。  

## <a name="partitioning"></a>分区

了解 Azure 存储对 Blob 数据的分区方式有助于增强性能。 与数据分散在多个分区相比，在单个分区中，Azure 存储可以更快地提供数据。 适当地为 Blob 命名可以提高读取请求的效率。

Blob 存储使用基于范围的分区方案来进行缩放和负载均衡。 每个 Blob 有一个分区键，该键由完整的 Blob 名称（帐户+容器+Blob）构成。 分区键用于将 Blob 数据分区成范围。 然后，范围将在整个 Blob 存储中进行负载均衡。

基于范围的分区意味着，使用词汇顺序（例如 *msftpayroll*、*msftperformance*、*msftemployees* 等）或时间戳（*log20160101*、*log20160102*、*log20160102* 等）的命名约束更有可能会导致将分区共置在同一个分区服务器中， 直到更大的负载需要将它们拆分成较小的范围。 将 Blob 共置在同一分区服务器中可以提高性能，因此，性能增强的一个重要部分涉及到以最有效的组织方式命名 Blob。

例如，容器中的所有 Bob 可接受单个服务器的服务，直到这些 Blob 的负载需要进一步进行重新平衡分区范围。 同样，一组名称按词汇顺序排列的少量负载帐户可以接受单个服务器的服务，直到其中一个或所有帐户的负载请求它们拆分到多个分区服务器。

每个负载均衡操作可能在操作期间会影响存储调用的延迟。 服务处理某个分区流量激增的能力受到单个分区服务器可伸缩性的限制，直到负载均衡操作着手重新平衡分区键范围。

可以遵循一些最佳实践来降低此类操作的频率。  

- 如果可能，请对标准存储帐户使用大于 4 MiB 的 Blob 或块大小，对高级存储帐户使用大于 256 KiB 的 Blob 或块大小。 较大的 Blob 或块大小会自动激活高吞吐量块 Blob。 高吞吐量块 Blob 提供不受分区命名影响的高性能引入。
- 检查帐户、容器、Blob、表和队列使用的命名约定。 考虑使用最符合需求的哈希函数，在帐户、容器或 Blob 名前加上三位数哈希。
- 如果使用时间戳或数字标识符组织数据，请确保使用的不是仅附加在后（或仅在前面加上）的流量模式。 这些模式不适用于基于范围的分区系统。 这些模式可能导致所有流量进入单个分区并限制系统进行有效的负载均衡。

    例如，如果日常操作使用有时间戳的 Blob，如 *yyyymmdd*，则该日常操作的所有流量都定向到由单个分区服务器服务的单个 Blob。 考虑每个 Blob 的限制和每个分区的限制是否符合需求，并考虑是否需要将此操作拆分成多个 Blob。 同样，如果在表中存储时序数据，则所有流量都可能定向到键命名空间的最后一个部分。 如果使用数字 ID，请使用三位数哈希作为 ID 的前缀。 如果使用时间戳，请使用秒值作为时间戳的前缀，例如 *ssyyyymmdd*。 如果应用程序定期执行列出和查询操作，请选择限制查询次数的哈希函数。 在某些情况下，使用随机前缀便已足够。
  
- 有关 Azure 存储中使用的分区方案的详细信息，请参阅[Azure 存储：具有高度一致性的高可用性云存储服务](https://sigops.org/sosp/sosp11/current/2011-Cascais/printable/11-calder.pdf)。

## <a name="networking"></a>联网

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

若要广泛地分发 blob 内容，请使用内容交付网络，如 Azure CDN。 关于 Azure CDN 的详细信息，请参阅 [Azure CDN](../../cdn/cdn-overview.md)。  

## <a name="sas-and-cors"></a>SAS 和 CORS

假设你需要授权用户 Web 浏览器或手机应用中运行的代码（例如 JavaScript）访问 Azure 存储中的数据。 一种方法是构建充当代理的服务应用程序。 用户的设备将对服务进行身份验证，而后者又可授权访问 Azure 存储资源。 这样，就可以避免在不安全的设备上公开存储帐户密钥。 但是，此方法会明显增大服务应用程序的开销，因为在用户设备与 Azure 存储之间传输的所有数据必须通过服务应用程序。

使用共享访问签名 (SAS) 即可避免将服务应用程序用作 Azure 存储的代理。 使用 SAS 可让用户设备通过受限访问令牌直接对 Azure 存储发出请求。 例如，如果用户想要将照片上传到应用程序，则服务应用程序可以生成 SAS 并将其发送到用户的设备。 SAS 令牌可按指定的时间间隔授予写入 Azure 存储资源的权限，该时间过后，SAS 令牌将会过期。 有关 SAS 的详细信息，请参阅[使用共享访问签名 (SAS) 授予对 Azure 存储资源的有限访问权限](../common/storage-sas-overview.md)。  

通常，Web 浏览器不允许某个域上的网站所托管的页面中的 JavaScript 对另一个域执行某些操作（例如写入操作）。 此策略称为同源策略，可防止一个页面上的恶意脚本获取另一网页上的数据的访问权限。 但是，在云中构建解决方案时，同源策略可能会成为一种限制。 跨源资源共享 (CORS) 是一种浏览器功能，它使目标域能够与信任源自源域的请求的浏览器通信。

例如，假设 Azure 中运行的某个 Web 应用程序对 Azure 存储帐户发出了某个资源请求。 该 Web 应用程序是源域，存储帐户是目标域。 可为任何 Azure 存储服务配置 CORS，以便与从 Azure 存储信任的源域发出请求的 Web 浏览器通信。 有关 CORS 的详细信息，请参阅 [Azure 存储的跨源资源共享 (CORS) 支持](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services)。  
  
SAS 和 CORS 都有助于避免 Web 应用程序上出现不必要的负载。  

## <a name="caching"></a>缓存

缓存在性能方面发挥着重要的作用。 以下部分将讨论有关缓存的最佳做法。

### <a name="reading-data"></a>读取数据

通常，读取数据一次比读取数据两次更为有利。 假设某个 Web 应用程序已从 Azure 存储中检索 50 MiB 的 Blob，并将其作为内容提供给某个用户。 理想情况下，应用程序会将 Blob 缓存到磁盘本地，并在处理后续的用户请求时检索缓存的版本。

如果 Blob 自缓存以来未经过修改，避免检索 Blob 的方法之一是使用修改时间的条件标头来限定 GET 操作。 如果上次修改时间处于 Blob 缓存时间之后，则会检索并重新缓存 Blob。 否则，将检索缓存的 Blob 以获得最佳性能。

你还可以将应用程序设计为假定 Blob 在检索后的短时间内未有变化。 在这种情况下，应用程序不需要检查 Blob 在该时间间隔内是否被修改。

配置数据、查找数据以及由应用程序频繁使用的其他数据非常适合进行缓存。  

有关使用条件标头的详细信息，请参阅[为 Blob 服务操作指定条件标头](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations)。  

### <a name="uploading-data-in-batches"></a>批量上传数据

在某些方案中，可将数据聚合在本地，然后定期批量进行上传，而不必立即上传每个数据片段。 例如，假设某个 Web 应用程序保留活动的日志文件。 该应用程序可以在表中发生每个活动时上传该活动的详细信息（这需要许多存储操作），或者，可将活动详细信息保存到本地日志文件，然后定期将所有活动详细信息以带分隔符的文件的形式上传到 Blob。 如果每个日志条目的大小为 1 KB，则你可以在单个事务中上传数千个条目。 单个事务支持上传一个最大大小为 64 MiB 的 Blob。 应用程序开发人员必须针对可能发生的客户端设备故障或上传失败情况进行相应的设计。 如果需要根据某个时间间隔下载活动数据，而不是下载单个活动的数据，则我们建议使用 Blob 存储，而不要使用表存储。

## <a name="net-configuration"></a>.NET 配置

如果使用的是 .NET Framework，则本部分列出的数种快速配置设置可以用于显著提高性能。  如果使用其他语言，则需查看类似的概念是否适用于所选择的语言。  

### <a name="use-net-core"></a>使用 .NET Core

使用 .NET Core 2.1 或更高版本开发 Azure 存储应用程序，以利用性能增强功能。 建议尽量使用 .NET Core 3.x。

有关 .NET Core 的性能改进的详细信息，请参阅以下博客文章：

- [Performance Improvements in .NET Core 3.0](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-3-0/)（.NET Core 3.0 的性能改进）
- [Performance Improvements in .NET Core 2.1](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-2-1/)（.NET Core 2.1 的性能改进）

### <a name="increase-default-connection-limit"></a>提高默认连接限制

在 .NET 中，以下代码可将默认的连接限制（通常在客户端环境中为 2，在服务器环境中为 10）提高到 100。 通常情况下，应将值大致设置为应用程序使用的线程数。 在打开任何连接前设置连接限制。

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

对于其他编程语言，请参阅文档以确定如何设置连接限制。  

有关详细信息，请参阅博客文章[Web 服务：并发连接](https://blogs.msdn.microsoft.com/darrenj/2005/03/07/web-services-concurrent-connections/)。  

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

## <a name="copying-and-moving-blobs"></a>复制和移动 Blob

Azure 存储提供多种解决方案用于在存储帐户内部、在存储帐户之间，以及在本地系统与云之间复制和移动 Blob。 本部分将介绍其中的某些选项及其对性能的影响。 有关有效地与 Blob 存储相互传输数据的信息，请参阅[选择 Azure 数据传输解决方案](../common/storage-choose-data-transfer-solution.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

### <a name="blob-copy-apis"></a>Blob 复制 API

若要跨存储帐户复制 Blob，请使用[从 URL 放置块](/rest/api/storageservices/put-block-from-url)操作。 此操作以同步方式将任何 URL 源中的数据复制到块 Blob。 使用 `Put Block from URL` 操作可以大幅减少跨存储帐户迁移数据时所需的带宽。 由于复制操作在服务端中进行，因此无需下载并重新上传数据。

若要复制同一存储帐户中的数据，请使用[复制 Blob](/rest/api/storageservices/Copy-Blob) 操作。 复制同一存储帐户中的数据通常很快就能完成。  

### <a name="use-azcopy"></a>使用 AzCopy

AzCopy 命令行实用工具是向/从以及跨存储帐户批量传输 Blob 的简单高效选项。 AzCopy 已针对此方案进行优化，可以实现较高的传输速率。 AzCopy 版本 10 使用 `Put Block From URL` 操作跨存储帐户复制 Blob 数据。 有关详细信息，请参阅[使用 AzCopy v10 将数据复制或移到 Azure 存储](/azure/storage/common/storage-use-azcopy-v10)。  

### <a name="use-azure-data-box"></a>使用 Azure Data Box

若要在 Blob 存储中导入大量数据，请考虑使用 Azure Data Box 系列进行脱机传输。 当你按时间、网络可用性或成本进行限制时，Microsoft 提供的 Data Box 设备可用于将大量数据移到 Azure。 有关详细信息，请参阅[Azure DataBox 文档](/azure/databox/)。

## <a name="content-distribution"></a>内容分发

有时，应用程序需要向位于同一区域或多个区域的许多用户提供相同的内容（例如网站主页中使用的产品演示视频）。 在这种情况下，请使用内容交付网络（CDN）（如 Azure CDN）在地理上分发 blob 内容。 与存在于一个区域且无法以低延迟向其他区域交付内容的 Azure 存储帐户不同，Azure CDN 使用位于全世界多个数据中心的服务器。 此外，与单个存储帐户相比，CDN 通常可以支持更高的出口限制。  

关于 Azure CDN 的详细信息，请参阅 [Azure CDN](../../cdn/cdn-overview.md)。

## <a name="use-metadata"></a>使用元数据

Blob 服务支持 HEAD 请求，这些请求可以包含 Blob 属性或元数据。 例如，如果应用程序需要某张照片中的 Exif（可交换图像格式）数据，则它可以检索该照片并从中提取数据。 为了节省带宽并改进性能，应用程序可以在上传照片时将 Exif 数据存储到 Blob 的元数据中。 随后，你只需使用 HEAD 请求即可检索元数据中的 Exif 数据。 仅检索元数据而不检索 Blob 的全部内容可以大幅节省带宽，并减少提取 Exif 数据所需的处理时间。 请记住，每个 Blob 可以存储 8 KiB 元数据。  

## <a name="upload-blobs-quickly"></a>快速上传 Blob

若要快速上传 Blob，请先确定是要上传一个还是多个 Blob。 请参阅以下指南，根据具体情况确定要使用的正确方法。  

### <a name="upload-one-large-blob-quickly"></a>快速上传一个大型 Blob

若要快速上传单个大型 Blob，客户端应用程序可以并行上传其块或页，但需要考虑各个 Blob 的可伸缩性目标并综合考虑存储帐户的情况。 Azure 存储客户端库支持并行上传。 例如，可使用以下属性来指定 .NET 或 Java 中允许的并发请求数。 其他受支持语言的客户端库提供类似的选项。

- 对于 .NET，请设置 [BlobRequestOptions.ParallelOperationThreadCount](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.paralleloperationthreadcount) 属性。
- 对于 Java/Android，请调用 [BlobRequestOptions.setConcurrentRequestCount(final Integer concurrentRequestCount)](/java/api/com.microsoft.azure.storage.blob._blob_request_options.setconcurrentrequestcount) 方法。

### <a name="upload-many-blobs-quickly"></a>快速上传多个 Blob

若要快速上传多个 Blob，请以并行方式上传。 并行上传要快于通过并行块上传方式一次上传一个 Blob，因为这种情况下上传会分布到存储服务的多个分区中。 AzCopy 以并行方式执行上传操作，建议用于此方案。 有关详细信息，请参阅 [AzCopy 入门](../common/storage-use-azcopy-v10.md)。  

## <a name="choose-the-correct-type-of-blob"></a>选择正确的 Blob 类型

Azure 存储支持块 Blob、追加 Blob 和页 Blob。 在给定的使用方案中，对 Blob 类型的选择会影响到解决方案的执行情况和可伸缩性。

需要以高效方式上传大量数据时，适合使用块 Blob。 例如，需要将照片或视频上传到 Blob 存储的客户端应用程序适合使用块 Blob。

追加 Blob 与块 Blob 的类似之处在于，它们都由块构成。 修改追加 Blob 时，块只会添加到 Blob 的末尾。 在日志记录等方案中，如果应用程序需要将数据添加到现有的 Blob 时，则追加 Blob 会很有用。

如果应用程序需要对数据执行随机写入，则适合使用页 Blob。 例如，Azure 虚拟机磁盘将存储为页 Blob。 有关详细信息，请参阅[了解块 Blob、追加 Blob 和页 Blob](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)。  

## <a name="next-steps"></a>后续步骤

- [存储帐户的 Azure 存储可伸缩性和性能目标](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [状态和错误代码](/rest/api/storageservices/Status-and-Error-Codes2)
