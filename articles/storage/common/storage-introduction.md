---
title: "Azure 存储简介 | Microsoft 文档"
description: "Microsoft 的云中数据存储 - Azure 存储的简介。"
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: a4a1bc58-ea14-4bf5-b040-f85114edc1f1
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/10/2017
ms.author: tamram
ms.openlocfilehash: ff0f6446b51c4549e5a367b5b767d4777a1d946d
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="introduction-to-microsoft-azure-storage"></a>Microsoft Azure 存储简介

Microsoft Azure 存储是 Microsoft 托管的云服务，其提供的存储的可用性、安全性、持久性、可伸缩性和冗余性都很高。 Microsoft 为你负责维护并处理关键问题。

Azure 存储包含三项数据服务：Blob 存储、文件存储和队列存储。 Blob 存储支持标准存储和高级存储，高级存储只使用 SSD 以尽可能提高性能。 另一功能是冷存储，用于存储大量极少访问的数据，可降低费用。

本文介绍以下内容：
* Azure 存储服务
* 存储帐户的类型
* 访问 Blob、队列和文件
* 加密
* 复制
* 将数据传入或传出存储
* 许多可用的存储客户端库。

若要快速启动并运行 Azure 存储，请查看以下快速入门教程：
* [使用 PowerShell 创建存储帐户](storage-quickstart-create-storage-account-powershell.md)
* [使用 CLI 创建存储帐户](storage-quickstart-create-storage-account-cli.md)

## <a name="introducing-the-azure-storage-services"></a>Azure 存储服务简介

若要使用 Azure 存储提供的任何服务（Blob 存储、文件存储、队列存储），请先创建一个存储帐户，然后即可将数据传入/传出该存储帐户中的特定服务。

## <a name="blob-storage"></a>Blob 存储

Blob 本质上是文件，例如存储在计算机、平板电脑、移动设备等上的文件。 这些文件可以是图片、Microsoft Excel 文件、HTML 文件、虚拟硬盘 (VHD)、大数据（例如日志）、数据库备份，几乎包括任何内容。 Blob 存储在类似于文件夹的容器中。

将文件存储在 Blob 存储中以后，即可使用 URL、REST 接口或 Azure SDK 存储客户端库之一从世界各地访问它们。 存储客户端库提供多个语言版本，包括 Node.js、Java、PHP、Ruby、Python 和 .NET。

有三种类型的 Blob -- 块 Blob、页 Blob（用于 VHD 文件）和追加 Blob。

* 块 Blob 用于保存最大约 4.7 TB 的普通文件。
* 页 Blob 用于保存最大 8 TB 的随机访问文件。 此类 Blob 用于可备份 VM 的 VHD 文件。
* 追加 Blob 由块 Blob 等块组成，但针对追加操作进行了优化。 此类 Blob 用于将信息从多个 VM 记录到同一 Blob，等等。

如果使用的是极大型数据集，因网络限制而无法通过网络向 Blob 存储上传数据或从其下载数据，则可将一组硬盘驱动器寄送到 Microsoft，以便直接通过数据中心导入或导出数据。 请参阅[使用 Microsoft Azure 导入/导出服务将数据传输到 Blob 存储中](../storage-import-export-service.md)。

## <a name="azure-files"></a>Azure 文件
可以通过 [Azure 文件](../files/storage-files-introduction.md)设置可用性高的网络文件共享，以便使用标准的服务器消息块 (SMB) 协议对其进行访问。 这意味着，多个 VM 可以共享启用了读取和写入访问权限的相同文件。 也可使用 REST 接口或存储客户端库来读取文件。

Azure 文件不同于公司文件共享的一点是，可以在全球任何地方使用 URL 来访问文件，只要该 URL 指向文件且包含共享访问签名 (SAS) 令牌即可。 可以生成 SAS 令牌，用于在指定时间内对私有资产进行特定访问。

文件共享适用于许多常用方案：

* 许多本地应用程序使用文件共享。 此功能可以更方便地迁移那些将数据共享到 Azure 的应用程序。 如果将文件共享装载到本地应用程序所使用的驱动器号，则应用程序中访问文件共享的那部分应尽量少做更改（如果必须进行更改的话）。

* 配置文件可以在一个文件共享上存储，从多个 VM 进行访问。 可以将一个组中多个开发人员使用的工具和实用程序存储到文件共享中，确保每个人都能找到它们并使用同一版本。

* 例如，诊断日志、指标和故障转储是三种可以写入到文件共享中供以后处理或分析的数据。

目前不支持基于 Active Directory 的身份验证和访问控制列表 (ACL)，但将来的某个时候会提供此方面的支持。 存储帐户凭据用于提供访问文件共享所需的身份验证。 这意味着，任何人只要装载了共享都具有该共享的完整读/写访问权限。

## <a name="queue-storage"></a>队列存储

Azure 队列服务用于存储和检索消息。 队列消息最大可以为 64 KB，一个队列可以包含数百万条消息。 队列通常用于存储需要异步处理的消息的列表。

例如，假设你需要客户能够上传图片，并且你需要创建每个图片的缩略图。 可以让客户在上传图片时等待你创建缩略图， 也可以使用队列。 当客户完成上传操作后，向队列写入一条消息。 然后通过 Azure Function 从队列检索该消息并创建缩略图。 此处理过程的每一部分都可以单独进行缩放，让你可以根据使用情况进行调整，加强控制。

## <a name="table-storage"></a>表存储

Azure 表存储现在是 Azure Cosmos DB 的一部分。 若要查看 Azure 表存储文档，请参阅 [Azure 表存储概述](../../cosmos-db/table-storage-overview.md)。 除了现有的 Azure 表存储服务，还有新的 Azure Cosmos DB 表 API 产品/服务，后者提供吞吐量优化表、全局分发和自动辅助索引。 若要详细了解和尝试新的高级体验，请查看 [Azure Cosmos DB 表 API](https://aka.ms/premiumtables)。

## <a name="disk-storage"></a>磁盘存储

Azure 存储还包括虚拟机使用的托管和非托管磁盘功能。 有关这些功能的详细信息，请参阅[计算服务文档](https://docs.microsoft.com/azure/#pivot=services&panel=Compute)。

## <a name="types-of-storage-accounts"></a>存储帐户的类型

下表显示了各种存储帐户，以及可以将哪些对象与每种帐户配合使用。

|存储帐户的类型|通用标准|通用高级|Blob 存储（热访问层和冷访问层）|
|-----|-----|-----|-----|
|支持的服务| Blob 服务、文件服务、队列服务 | Blob 服务 | Blob 服务|
|支持的 Blob 类型|块 Blob、页 Blob 和追加 Blob | 页 Blob | 块 Blob 和追加 Blob|

### <a name="general-purpose-storage-accounts"></a>通用存储帐户

有两类通用存储帐户。

#### <a name="standard-storage"></a>标准存储

使用最广泛的存储帐户是标准存储帐户，适用于所有类型的数据。 标准存储帐户使用磁介质来存储数据。

#### <a name="premium-storage"></a>高级存储

高级存储为主要用于 VHD 文件的页 Blob 提供高性能存储。 高级存储帐户使用 SSD 来存储数据。 Microsoft 建议对所有 VM 使用高级存储。

### <a name="blob-storage-accounts"></a>Blob 存储帐户

Blob 存储帐户是专用于存储块 Blob 和追加 Blob 的存储帐户。 不能在这些帐户中存储页 Blob，因此不能存储 VHD 文件。 这些帐户允许将访问层设置为“热”或“冷”；该层可以随时进行更改。

热访问层用于频繁访问的文件 -- 所支付的存储费用较高，但 Blob 访问费用则要低得多。 对于存储在冷访问层中的 Blob，所支付的 Blob 访问费用较高，但存储费用则要低得多。

## <a name="accessing-your-blobs-files-and-queues"></a>访问 Blob、文件和队列

每个存储帐户都有两个身份验证密钥，任何一个都可以用于任何操作。 由于有两个密钥，因此可以不定期地对其滚动更新以增强安全性。 确保密钥安全至关重要，因为使用密钥和帐户名即可对存储帐户中的所有数据进行无限制的访问。

本部分探讨如何通过两种方式来确保存储帐户及其数据的安全性。 若要详细了解如何确保存储帐户及其数据的安全性，请参阅 [Azure 存储安全指南](storage-security-guide.md)。

### <a name="securing-access-to-storage-accounts-using-azure-ad"></a>使用 Azure AD 确保对存储帐户进行安全访问

若要确保对存储数据进行安全访问，一种方式是控制对存储帐户密钥的访问。 使用资源管理器基于角色的访问控制 (RBAC) 时，可以向用户、组或应用程序分配角色。 这些角色与特定的一组允许的或禁止的操作绑定。 通过 RBAC 授予对存储帐户的访问权限只是对该存储帐户的管理操作（例如更改访问层）进行的处理。 不能使用 RBAC 授予对数据对象（例如特定容器或文件共享）的访问权限。 但是，可以使用 RBAC 授予对存储帐户密钥的访问权限，然后即可使用这些密钥读取数据对象。

### <a name="securing-access-using-shared-access-signatures"></a>使用共享访问签名来确保访问安全性

可以使用共享访问签名和存储的访问策略来确保数据对象的安全性。 共享访问签名 (SAS) 是一个字符串，包含可附加到资产的 URI 的安全令牌，用于委派特定存储对象的访问权限，以及指定访问的权限和日期/时间范围等限制。 此功能用途很广。 有关详细信息，请参阅[使用共享访问签名 (SAS)](storage-dotnet-shared-access-signature-part-1.md)。

### <a name="public-access-to-blobs"></a>公开访问 Blob

可以通过 Blob 服务提供对容器及其 Blob 或特定 Blob 的公开访问权限。 指定某个容器或 Blob 为公用的时，任何人都可以匿名读取它，不需要进行身份验证。 举例来说，如果你的网站使用 Blob 存储中的图像、视频或文档，则需这样做。 有关详细信息，请参阅[管理对容器和 Blob 的匿名读取访问](../blobs/storage-manage-access-to-resources.md)

## <a name="encryption"></a>加密

有多种适用于存储服务的基本加密类型。

### <a name="encryption-at-rest"></a>静态加密

可以在适用于 Azure 存储帐户的文件服务（预览版）或 Blob 服务上启用存储服务加密 (SSE)。 如果启用此功能，所有写入到特定服务的数据都会在写入前加密。 读取数据时，数据会在返回前解密。

### <a name="client-side-encryption"></a>客户端加密

可以调用存储客户端库的方法以编程方式加密数据，然后再将数据通过网络从客户端发送到 Azure。 数据以加密方式存储，这意味着数据也是静态加密的。 读回数据时，会在收到数据信息后再将其解密。

### <a name="encryption-in-transit-with-azure-file-shares"></a>通过 Azure 文件共享在传输过程中进行加密

有关共享访问签名的详细信息，请参阅 [使用共享访问签名 (SAS)](../storage-dotnet-shared-access-signature-part-1.md) 。 有关安全访问存储帐户的详细信息，请参阅[管理对容器和 Blob 的匿名读取访问](../blobs/storage-manage-access-to-resources.md)和 [Authentication for the Azure Storage Services](https://msdn.microsoft.com/library/azure/dd179428.aspx)（Azure 存储服务身份验证）。

若要详细了解如何确保存储帐户和加密的安全性，请参阅 [Azure 存储安全指南](storage-security-guide.md)。

## <a name="replication"></a>复制

为了确保数据的持久性，Azure 存储提供了保留（和管理）多个数据副本的功能。 这称为复制，有时也称为冗余。 设置存储帐户时，可选择复制类型。 大多数情况下，可以在设置存储帐户后修改此设置。

所有存储帐户都有**本地冗余存储 (LRS)**，旨在一年中提供至少 99.999999999%（11 个 9）的对象持续性。 这意味着，将由 Azure 存储在设置存储帐户时指定的数据中心管理多个数据副本。 提交更改时，会先对所有副本进行更新，然后返回成功的消息。 这意味着这些副本始终处于同步状态。另外，这些副本驻留在单独的容错域和升级域中。这意味着，即使某个存储数据的存储节点发生故障，或者需要进行脱机更新，数据也是可用的。

**本地冗余存储 (LRS)**

如上所述，启用 LRS 时，单个数据中心有多个数据副本。 这样可以解决在某个存储节点发生故障或进行脱机更新时数据变得不可用的问题，但不适用于整个数据中心变得不可用的情况。

区域冗余存储 (ZRS)

区域冗余存储 (ZRS) 旨在一年中提供至少 99.9999999999%（12 个 9）的对象持续性，方法是在保留数据的本地副本的同时，还保留数据的另一组副本。 第二组副本在一个或两个区域内跨数据中心进行异步复制。 请注意，在通用存储帐户中，ZRS 仅适用于块 Blob。 另外，在创建存储帐户并选择 ZRS 后，无法将其转换为使用任何其他类型的复制，反之亦然。

ZRS 帐户提供的持久性高于 LRS，但 ZRS 帐户没有指标或日志记录功能。

**异地冗余存储 (GRS)**

异地冗余存储 (GRS) 旨在一年中提供 99.99999999999999%（16 个 9）的对象持续性，方法是：在主区域保留本地数据副本，并在距离主区域数百英里的次要区域保留另一组数据副本。 当主区域发生故障时，Azure 存储会故障转移到辅助区域。

**读取访问异地冗余存储 (RA-GRS)**

除了你能够以读取方式访问辅助位置中的数据，读取访问异地冗余存储与 GRS 并无二致。 如果主数据中心变得临时不可用，则可继续读取辅助位置中的数据。 此功能在某些情况下很有用。 例如，可以将 Web 应用程序更改成只读模式，并让其指向辅助副本，这样一来，即使更新不可用，也可以进行某些访问。

> [!IMPORTANT]
> 创建存储帐户后，可以更改复制数据的方式，除非在创建该帐户时指定了 ZRS。 但请注意，如果从 LRS 切换到 GRS 或 RA-GRS，可能会产生额外的一次性数据传输费用。
>

有关复制的详细信息，请参阅 [Azure 存储复制](storage-redundancy.md)。

有关灾难恢复信息，请参阅[在 Azure 存储中断时该怎么办](storage-disaster-recovery-guidance.md)。

有关如何利用 RA-GRS 存储来确保高可用性的示例，请参阅[使用 RA-GRS 设计高度可用的应用程序](storage-designing-ha-apps-with-ragrs.md)。

## <a name="transferring-data-to-and-from-azure-storage"></a>将数据传输到和移出 Azure 存储

可以使用 AzCopy 命令行实用程序在存储帐户内或跨存储帐户复制 Blob 和文件数据。 如需帮助，请参阅以下文章之一：

* [使用 Windows 版 AzCopy 传输数据](storage-use-azcopy.md)
* [使用 Linux 版 AzCopy 传输数据](storage-use-azcopy-linux.md)

AzCopy 在 [Azure 数据移动库](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/)的基础上构建，当前以预览版提供。

可以使用 Azure 导入/导出服务将大量 Blob 数据导入或导出存储帐户。 你需要准备多个硬盘驱动器并将其邮寄到 Azure 数据中心，由该中心的人员将数据传入/传出硬盘驱动器，再将硬盘驱动器寄回给你。 有关导入/导出服务的详细信息，请参阅[使用 Microsoft Azure 导入/导出服务将数据传输到 Blob 存储中](../storage-import-export-service.md)。

## <a name="pricing"></a>定价

有关 Azure 存储定价的详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/storage/blobs/)。

## <a name="storage-apis-libraries-and-tools"></a>存储 API、库和工具
Azure 存储资源可以通过任何发出 HTTP/HTTPS 请求的语言来进行访问。 另外，Azure 存储还为多种主流语言提供了编程库。 这些库通过对细节进行处理简化了使用 Azure 存储的许多方面，这些细节包括同步和异步调用、操作的批处理、异常管理、自动重试、操作行为，等等。 这些库当前可供下列语言和平台以及正在筹备的其他语言和平台使用：

### <a name="azure-storage-data-services"></a>Azure 存储数据服务
* [存储服务 REST API](/rest/api/storageservices/)
* [适用于 .NET 的存储客户端库](https://docs.microsoft.com/dotnet/api/?view=azurestorage-8.1.1)
* [适用于 C++ 的存储客户端库](https://github.com/Azure/azure-storage-cpp)
* [适用于 Java/Android 的存储空间客户端库](https://azure.microsoft.com/develop/java/)
* [适用于 Node.js 的存储空间客户端库](http://dl.windowsazure.com/nodestoragedocs/index.html)
* [适用于 PHP 的存储空间客户端库](https://azure.microsoft.com/develop/php/)
* [适用于 Python 的存储空间客户端库](https://azure.microsoft.com/develop/python/)
* [适用于 Ruby 的存储空间客户端库](https://azure.microsoft.com/develop/ruby/)
* [适用于 PowerShell 的存储 Cmdlet](/powershell/module/azure.storage/?view=azurermps-4.1.0&viewFallbackFrom=azurermps-4.0.0)
* [适用于 CLI 2.0 的存储命令](/cli/azure/storage)

## <a name="next-steps"></a>后续步骤

* [详细了解 Blob 存储](../blobs/storage-blobs-introduction.md)
* [详细了解文件存储](../storage-files-introduction.md)
* [详细了解队列存储](../queues/storage-queues-introduction.md)

若要快速启动并运行 Azure 存储，请查看以下快速入门教程：
* [使用 PowerShell 创建存储帐户](storage-quickstart-create-storage-account-powershell.md)
* [使用 CLI 创建存储帐户](storage-quickstart-create-storage-account-cli.md)

<!-- FIGURE OUT WHAT TO DO WITH ALL THESE LINKS.

Azure Storage resources can be accessed by any language that can make HTTP/HTTPS requests. Additionally, Azure Storage offers programming libraries for several popular languages. These libraries simplify many aspects of working with Azure Storage by handling details such as synchronous and asynchronous invocation, batching of operations, exception management, automatic retries, operational behavior and so forth. Libraries are currently available for the following languages and platforms, with others in the pipeline:

### Azure Storage data services
* [Storage Services REST API](https://docs.microsoft.com/rest/api/storageservices/)
* [Storage Client Library for .NET](https://docs.microsoft.com/dotnet/api/?view=azurestorage-8.1.1)
* [Storage Client Library for C++](https://github.com/Azure/azure-storage-cpp)
* [Storage Client Library for Java/Android](https://azure.microsoft.com/develop/java/)
* [Storage Client Library for Node.js](http://dl.windowsazure.com/nodestoragedocs/index.html)
* [Storage Client Library for PHP](https://azure.microsoft.com/develop/php/)
* [Storage Client Library for Python](https://azure.microsoft.com/develop/python/)
* [Storage Client Library for Ruby](https://azure.microsoft.com/develop/ruby/)
* [Storage Cmdlets for PowerShell](/powershell/module/azure.storage/?view=azurermps-4.1.0&viewFallbackFrom=azurermps-4.0.0)

### Azure Storage management services
* [Storage Resource Provider REST API Reference](/rest/api/storagerp/)
* [Storage Resource Provider Client Library for .NET](/dotnet/api/microsoft.azure.management.storage)
* [Storage Resource Provider Cmdlets for PowerShell 1.0](/powershell/module/azure.storage)
* [Storage Service Management REST API (Classic)](https://msdn.microsoft.com/library/azure/ee460790.aspx)

### Azure Storage data movement services
* [Storage Import/Export Service REST API](../storage-import-export-service.md)
* [Storage Data Movement Client Library for .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/)

### Tools and utilities
* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) is a free, standalone app from Microsoft that enables you to work visually with Azure Storage data on Windows, macOS, and Linux.
* [Azure Storage Client Tools](../storage-explorers.md)
* [Azure SDKs and Tools](https://azure.microsoft.com/tools/)
* [Azure Storage Emulator](http://www.microsoft.com/download/details.aspx?id=43709)
* [Azure PowerShell](/powershell/azure/overview)
* [AzCopy Command-Line Utility](http://aka.ms/downloadazcopy)

## Next steps
To learn more about Azure Storage, explore these resources:

### Documentation
* [Azure Storage Documentation](https://azure.microsoft.com/documentation/services/storage/)
* [Create a storage account](../storage-create-storage-account.md)

-->

### <a name="for-administrators"></a>面向管理员
* [对 Azure 存储空间使用 Azure PowerShell](storage-powershell-guide-full.md)
* [将 Azure CLI 用于 Azure 存储](../storage-azure-cli.md)

### <a name="for-net-developers"></a>面向 .NET 开发人员
* [通过 .NET 开始使用 Azure Blob 存储](../blobs/storage-dotnet-how-to-use-blobs.md)
* [使用 .NET 针对 Azure 文件进行开发](../files/storage-dotnet-how-to-use-files.md)
* [通过 .NET 开始使用 Azure 表存储](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [通过 .NET 开始使用 Azure 队列存储](../storage-dotnet-how-to-use-queues.md)

### <a name="for-javaandroid-developers"></a>面向 Java/Android 开发人员
* [如何通过 Java 使用 Blob 存储](../blobs/storage-java-how-to-use-blob-storage.md)
* [使用 Java 针对 Azure 文件进行开发](../files/storage-java-how-to-use-file-storage.md)
* [如何通过 Java 使用表存储](../../cosmos-db/table-storage-how-to-use-java.md)
* [如何通过 Java 使用队列存储](../storage-java-how-to-use-queue-storage.md)

### <a name="for-nodejs-developers"></a>面向 Node.js 开发人员
* [如何通过 Node.js 使用 Blob 存储](../blobs/storage-nodejs-how-to-use-blob-storage.md)
* [如何通过 Node.js 使用表存储](../../cosmos-db/table-storage-how-to-use-nodejs.md)
* [如何通过 Node.js 使用队列存储](../storage-nodejs-how-to-use-queues.md)

### <a name="for-php-developers"></a>面向 PHP 开发人员
* [如何通过 PHP 使用 Blob 存储](../blobs/storage-php-how-to-use-blobs.md)
* [如何通过 PHP 使用表存储](../../cosmos-db/table-storage-how-to-use-php.md)
* [如何通过 PHP 使用队列存储](../storage-php-how-to-use-queues.md)

### <a name="for-ruby-developers"></a>面向 Ruby 开发人员
* [如何通过 Ruby 使用 Blob 存储](../blobs/storage-ruby-how-to-use-blob-storage.md)
* [如何通过 Ruby 使用表存储](../../cosmos-db/table-storage-how-to-use-ruby.md)
* [如何通过 Ruby 使用队列存储](../storage-ruby-how-to-use-queue-storage.md)

### <a name="for-python-developers"></a>面向 Python 开发人员
* [如何通过 Python 使用 Blob 存储](../blobs/storage-python-how-to-use-blob-storage.md)
* [使用 Python 针对 Azure 文件进行开发](../files/storage-python-how-to-use-file-storage.md)
* [如何通过 Python 使用表存储](../../cosmos-db/table-storage-how-to-use-python.md)
* [如何通过 Python 使用队列存储](../storage-python-how-to-use-queue-storage.md)
