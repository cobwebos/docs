---
title: Azure 存储简介 - Azure 中的云存储 | Microsoft Docs
description: Azure 存储是 Microsoft 提供的云存储解决方案。 Azure 存储为数据对象提供具有高可用性、安全、持久、可大规模缩放且具有冗余的存储。
services: storage
author: tamram
ms.service: storage
ms.topic: get-started-article
ms.date: 07/11/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: 5e9d105e0824eac6052f4ea3f99a09c19a668294
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2018
ms.locfileid: "39525563"
---
# <a name="introduction-to-azure-storage"></a>Azure 存储简介

Azure 存储是 Microsoft 提供的适用于现代数据存储场景的云存储解决方案。 Azure 存储为数据对象提供可大规模缩放的对象存储，为云提供文件系统服务，并且提供用于可靠消息传送的消息传送存储以及 NoSQL 存储。 Azure 存储：

- **持久且具有高可用性。** 冗余可确保数据在发生短暂的硬件故障时是安全的。 还可以选择在各个数据中心或地理区域之间复制数据，从而在发生本地灾难或自然灾害时提供额外的保护。 以此方式复制的数据在发生意外中断时将保持高可用性。 
- **安全。** 该服务将对写入到 Azure 存储的所有数据进行加密。 Azure 存储可以精细地控制谁可以访问你的数据。
- **可缩放。** Azure 存储设计为可大规模缩放以满足当今的应用程序在数据存储和性能方面的需求。 
- **托管的。** Microsoft Azure 会替你处理维护和任何严重问题。
- **易访问。** 可以通过 HTTP 或 HTTPS 从世界上的任何位置访问 Azure 存储中的数据。 Microsoft 以各种语言（NET、Java、Node.js、Python、PHP、Ruby、Go 和其他语言）提供了适用于 Azure 存储的 SDK 以及成熟的 REST API。 Azure 存储支持通过 Azure PowerShell 或 Azure CLI 运行脚本。 而且，Azure 门户和 Azure 存储资源管理器提供了用于处理数据的简单可视化解决方案。  

## <a name="azure-storage-services"></a>Azure 存储服务

Azure 存储包括以下数据服务： 

- [Azure Blob](../blobs/storage-blobs-introduction.md)：适用于文本和二进制数据的可大规模缩放的对象存储。
- [Azure 文件](../files/storage-files-introduction.md)：适用于云或本地部署的托管文件共享。
- [Azure 队列](../queues/storage-queues-introduction.md)：用于在应用程序组件之间进行可靠的消息传送的消息传送存储。 
- [Azure 表](../tables/table-storage-overview.md)：一种 NoSQL 存储，适合用作结构化数据的无模式存储。

对每个服务的访问都通过存储帐户进行。 若要开始使用，请参阅[创建存储帐户](storage-quickstart-create-account.md)。

## <a name="blob-storage"></a>Blob 存储

Azure Blob 存储是 Microsoft 提供的适用于云的对象存储解决方案。 Blob 存储最适合存储巨量的非结构化数据，例如文本或二进制数据。 

Blob 存储最适合用于：

* 直接向浏览器提供图像或文档。
* 存储文件以供分布式访问。
* 对视频和音频进行流式处理。
* 存储用于备份和还原、灾难恢复及存档的数据。
* 存储数据以供本地或 Azure 托管服务执行分析。

可以通过 HTTP 或 HTTPS 从世界上的任何位置访问 Blob 存储中的对象。 用户或客户端应用程序可以通过 URL、[Azure 存储 REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api)、[Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage)、[Azure CLI](https://docs.microsoft.com/cli/azure/storage) 或 Azure 存储客户端库访问 Blob。 存储客户端库以多种语言提供，包括 [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/client)、[Java](https://docs.microsoft.com/java/api/overview/azure/storage/client)、[Node.js](http://azure.github.io/azure-storage-node)、[Python](https://azure-storage.readthedocs.io/en/latest/index.html)、[PHP](http://azure.github.io/azure-storage-php/) 和 [Ruby](http://azure.github.io/azure-storage-ruby)。

有关 Blob 存储的详细信息，请参阅 [Azure 中的对象存储简介](../blobs/storage-blobs-introduction.md)。

## <a name="azure-files"></a>Azure 文件
可以通过 [Azure 文件](../files/storage-files-introduction.md)设置可用性高的网络文件共享，以便使用标准的服务器消息块 (SMB) 协议对其进行访问。 这意味着，多个 VM 可以共享启用了读取和写入访问权限的相同文件。 也可使用 REST 接口或存储客户端库来读取文件。

Azure 文件不同于公司文件共享的一点是，可以在全球任何地方使用 URL 来访问文件，只要该 URL 指向文件且包含共享访问签名 (SAS) 令牌即可。 可以生成 SAS 令牌，用于在指定时间内对私有资产进行特定访问。

文件共享适用于许多常用方案：

* 许多本地应用程序使用文件共享。 此功能可以更方便地迁移那些将数据共享到 Azure 的应用程序。 如果将文件共享装载到本地应用程序所使用的驱动器号，则应用程序中访问文件共享的那部分应尽量少做更改（如果必须进行更改的话）。

* 配置文件可以在一个文件共享上存储，从多个 VM 进行访问。 可以将一个组中多个开发人员使用的工具和实用程序存储到文件共享中，确保每个人都能找到它们并使用同一版本。

* 例如，诊断日志、指标和故障转储是三种可以写入到文件共享中供以后处理或分析的数据。

目前不支持基于 Active Directory 的身份验证和访问控制列表 (ACL)，但将来的某个时候会提供此方面的支持。 存储帐户凭据用于提供访问文件共享所需的身份验证。 这意味着，任何人只要装载了共享都具有该共享的完整读/写访问权限。

有关 Azure 文件的详细信息，请参阅 [Azure 文件简介](../files/storage-files-introduction.md)。

## <a name="queue-storage"></a>队列存储

Azure 队列服务用于存储和检索消息。 队列消息最大可以为 64 KB，一个队列可以包含数百万条消息。 队列通常用于存储需要异步处理的消息的列表。

例如，假设你需要客户能够上传图片，并且你需要创建每个图片的缩略图。 可以让客户在上传图片时等待你创建缩略图， 也可以使用队列。 当客户完成上传操作后，向队列写入一条消息。 然后通过 Azure Function 从队列检索该消息并创建缩略图。 此处理过程的每一部分都可以单独进行缩放，让你可以根据使用情况进行调整，加强控制。

有关 Azure 队列的详细信息，请参阅[队列简介](../queues/storage-queues-introduction.md)。

## <a name="table-storage"></a>表存储

Azure 表存储现在是 Azure Cosmos DB 的一部分。 若要查看 Azure 表存储文档，请参阅 [Azure 表存储概述](../tables/table-storage-overview.md)。 除了现有的 Azure 表存储服务，还有新的 Azure Cosmos DB 表 API 产品/服务，后者提供吞吐量优化表、全局分发和自动辅助索引。 若要详细了解和尝试新的高级体验，请查看 [Azure Cosmos DB 表 API](https://aka.ms/premiumtables)。

有关表存储的详细信息，请参阅 [Azure 表存储概述](../tables/table-storage-overview.md)。

## <a name="disk-storage"></a>磁盘存储

Azure 存储还包括虚拟机使用的托管和非托管磁盘功能。 有关这些功能的详细信息，请参阅[计算服务文档](https://docs.microsoft.com/en-gb/azure/#pivot=products&panel=Compute)。

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

有两种适用于存储服务的基本加密类型。 若要详细了解安全性和加密，请参阅 [Azure 存储安全指南](storage-security-guide.md)。

### <a name="encryption-at-rest"></a>静态加密

静态 Azure 存储服务加密 (SSE) 可帮助保护数据，使组织能够信守在安全性与符合性方面所做的承诺。 使用此功能，Azure 存储可以先自动加密数据，再将数据保存到存储，并在检索之前解密数据。 加密、解密和密钥管理对于用户而言是完全透明的。


SSE 自动加密所有性能层（标准和高级）、所有部署模型（Azure 资源管理器和经典）、所有 Azure 存储服务（Blob、队列、表和文件）中的数据。 SSE 不影响 Azure 存储性能。

有关 SSE 静态加密的详细信息，请参阅[静态数据的 Azure 存储服务加密](storage-service-encryption.md)。

### <a name="client-side-encryption"></a>客户端加密

可以调用存储客户端库的方法以编程方式加密数据，然后再将数据通过网络从客户端发送到 Azure。 数据以加密方式存储，这意味着数据也是静态加密的。 读回数据时，会在收到数据信息后再将其解密。

有关客户端加密的详细信息，请参阅 [Microsoft Azure 存储的使用 .NET 的客户端加密](storage-client-side-encryption.md)。

## <a name="replication"></a>复制

为了确保数据的持久性，Azure 存储会复制多个数据副本。 设置存储帐户时，可选择复制类型。 大多数情况下，可以在创建存储帐户后修改此设置。 

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

有关灾难恢复信息，请参阅[在 Azure 存储中断时该怎么办](storage-disaster-recovery-guidance.md)。

## <a name="transferring-data-to-and-from-azure-storage"></a>将数据传输到和移出 Azure 存储

可以使用 AzCopy 命令行实用程序在存储帐户内或跨存储帐户复制 Blob 和文件数据。 如需帮助，请参阅以下文章之一：

* [使用 Windows 版 AzCopy 传输数据](storage-use-azcopy.md)
* [使用 Linux 版 AzCopy 传输数据](storage-use-azcopy-linux.md)

AzCopy 在 [Azure 数据移动库](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/)的基础上构建，当前以预览版提供。

可以使用 Azure 导入/导出服务将大量 Blob 数据导入或导出存储帐户。 你需要准备多个硬盘驱动器并将其邮寄到 Azure 数据中心，由该中心的人员将数据传入/传出硬盘驱动器，再将硬盘驱动器寄回给你。 有关导入/导出服务的详细信息，请参阅[使用 Microsoft Azure 导入/导出服务将数据传输到 Blob 存储中](../storage-import-export-service.md)。

若要快速以低成本且可靠的方式将大量 Blob 数据导入到存储帐户中，还可以使用 Azure Data Box 磁盘。 Microsoft 通过区域运营商向你的数据中心发运最多 5 个容量为 40 TB 的加密固态磁盘 (SSD)。 你快速配置磁盘，通过 USB 连接将数据复制到磁盘，然后将磁盘发运回 Azure。 在 Azure 数据中心内，你的数据自动从驱动器上传到云。 有关此解决方案的详细信息，请转到 [Azure Data Box 磁盘概述](https://docs.microsoft.com/azure/databox/data-box-disk-overview)。

## <a name="pricing"></a>定价

有关 Azure 存储定价的详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/storage/blobs/)。

## <a name="storage-apis-libraries-and-tools"></a>存储 API、库和工具
Azure 存储资源可以通过任何发出 HTTP/HTTPS 请求的语言来进行访问。 另外，Azure 存储还为多种主流语言提供了编程库。 这些库通过对细节进行处理简化了使用 Azure 存储的许多方面，这些细节包括同步和异步调用、操作的批处理、异常管理、自动重试、操作行为，等等。 这些库当前可供下列语言和平台以及正在筹备的其他语言和平台使用：

### <a name="azure-storage-data-api-and-library-references"></a>Azure 存储数据 API 和库参考
* [存储服务 REST API](https://docs.microsoft.com/rest/api/storageservices/)
* [适用于 .NET 的存储客户端库](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
* [适用于 Java/Android 的存储空间客户端库](https://docs.microsoft.com/java/api/overview/azure/storage)
* [适用于 Node.js 的存储空间客户端库](https://docs.microsoft.com/javascript/api/azure-storage)
* [适用于 Python 的存储空间客户端库](https://github.com/Azure/azure-storage-python)
* [适用于 PHP 的存储空间客户端库](https://github.com/Azure/azure-storage-php)
* [适用于 Ruby 的存储空间客户端库](https://github.com/Azure/azure-storage-ruby)
* [适用于 C++ 的存储客户端库](https://github.com/Azure/azure-storage-cpp)

### <a name="azure-storage-management-api-and-library-references"></a>Azure 存储管理 API 和库参考
* [存储资源提供程序 REST API](https://docs.microsoft.com/rest/api/storagerp/)
* [适用于 .NET 的存储资源提供程序客户端库](https://docs.microsoft.com/dotnet/api/overview/azure/storage/management)
* [存储服务管理 REST API (Classic)](https://msdn.microsoft.com/library/azure/ee460790.aspx)

### <a name="azure-storage-data-movement-api-and-library-references"></a>Azure 存储数据移动 API 和库参考
* [存储导入/导出服务 REST API](https://docs.microsoft.com/rest/api/storageimportexport/)
* [适用于 .NET 的存储数据移动客户端库](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.datamovement)

### <a name="tools-and-utilities"></a>工具和实用程序
* [适用于存储的 Azure PowerShell Cmdlet](https://docs.microsoft.com/powershell/module/azure.storage)
* [适用于存储的 Azure CLI Cmdlet](https://docs.microsoft.com/cli/azure/storage)
* [AzCopy 命令行实用程序](http://aka.ms/downloadazcopy)
* [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)是 Microsoft 免费提供的独立应用，适用于在 Windows、macOS 和 Linux 上以可视方式处理 Azure 存储数据。
* [Azure 存储客户端工具](../storage-explorers.md)
* [Azure 开发人员工具](https://azure.microsoft.com/tools/)

## <a name="next-steps"></a>后续步骤

若要启动并运行 Azure 存储，请参阅[创建存储帐户](storage-quickstart-create-account.md)。
