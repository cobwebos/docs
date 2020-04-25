---
title: Azure 存储简介 - Azure 中的云存储 | Microsoft Docs
description: 核心 Azure 存储平台是 Microsoft 的云存储解决方案。 Azure 存储为数据对象提供具有高可用性、安全、持久、可大规模缩放且具有冗余的存储。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 81ee07eb41df6d8d663510913572b829feffd995
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2020
ms.locfileid: "82133782"
---
# <a name="introduction-to-the-core-azure-storage-services"></a>核心 Azure 存储服务简介

Azure 存储平台是适用于新式数据存储方案的 Microsoft 云存储解决方案。 核心存储服务为数据对象、适用于 Azure 虚拟机（Vm）的磁盘存储、适用于云的文件系统服务、用于可靠消息传送的消息存储和 NoSQL 存储提供高度可缩放的对象存储。 这些服务包括：

- **持久且具有高可用性。** 冗余可确保数据在发生短暂的硬件故障时是安全的。 还可以选择在各个数据中心或地理区域之间复制数据，从而在发生本地灾难或自然灾害时提供额外的保护。 以此方式复制的数据在发生意外中断时将保持高可用性。
- **安全。** 写入到 Azure 存储帐户的所有数据都由该服务加密。 Azure 存储可以精细地控制谁可以访问你的数据。
- **可缩放。** Azure 存储设计为可大规模缩放以满足当今的应用程序在数据存储和性能方面的需求。
- **托管的。** Azure 为你处理硬件维护、更新和关键问题。
- **易访问。** 可以通过 HTTP 或 HTTPS 从世界上的任何位置访问 Azure 存储中的数据。 Microsoft 以各种语言（包括 NET、Java、Node.js、Python、PHP、Ruby、Go 和其他语言）提供了适用于 Azure 存储的客户端库以及成熟的 REST API。 Azure 存储支持通过 Azure PowerShell 或 Azure CLI 运行脚本。 而且，Azure 门户和 Azure 存储资源管理器提供了用于处理数据的简单可视化解决方案。  

## <a name="core-storage-services"></a>核心存储服务

Azure 存储平台包括以下数据服务：

- [Azure Blob](../blobs/storage-blobs-introduction.md)：适用于文本和二进制数据的可大规模缩放的对象存储。 还包括通过 Data Lake Storage Gen2 支持大数据分析。
- [Azure 文件](../files/storage-files-introduction.md)：适用于云或本地部署的托管文件共享。
- [Azure 队列](../queues/storage-queues-introduction.md)：用于在应用程序组件之间进行可靠的消息传送的消息传送存储。
- [Azure 表](../tables/table-storage-overview.md)：一种 NoSQL 存储，适合用作结构化数据的无模式存储。
- [Azure 磁盘](../../virtual-machines/windows/managed-disks-overview.md)： azure vm 的块级别存储卷。

对每个服务的访问都通过存储帐户进行。 若要开始使用，请参阅[创建存储帐户](storage-account-create.md)。

## <a name="example-scenarios"></a>示例方案

下表比较了文件、Blob、磁盘、队列和表，并显示了每个文件的示例方案。

| Feature | 说明 | 何时使用 |
|--------------|-------------|-------------|
| **Azure 文件** |提供完全托管的云文件共享，你可以从任何位置通过行业标准服务器消息块（SMB）协议进行访问。<br><br>可以从适用于 Windows、Linux 和 macOS 的云或本地部署装载 Azure 文件共享。 | 你需要将应用程序 "直接迁移" 到已使用本机文件系统 Api 在其与在 Azure 中运行的其他应用程序之间共享数据的云。<br/><br/>需要替换或补充本地文件服务器或 NAS 设备。<br><br> 可让你存储需要从许多虚拟机访问的开发和调试工具。 |
| **Azure Blob** | 允许在块 blob 中大规模存储和访问非结构化数据。<br/><br/>还支持 [Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md)，用于企业大数据分析解决方案。 | 使应用程序能够支持流式处理和随机访问方案。<br/><br/>可让你从任意位置访问应用程序数据。<br/><br/>想要在 Azure 上生成企业数据湖并执行大数据分析。 |
| **Azure 磁盘** | 允许从附加的虚拟硬盘持久存储和访问数据。 | 你需要 "直接迁移" 使用本机文件系统 Api 的应用程序，以便将数据读取和写入到永久性磁盘。<br/><br/>可让你存储不需要从磁盘附加到的虚拟机外部访问的数据。 |
| **Azure 队列** | 允许在应用程序组件之间进行异步消息排队。 | 你需要分离应用程序组件，并使用异步消息传递在它们之间进行通信。<br><br>有关何时使用队列存储和服务总线队列的指导，请参阅[存储队列和服务总线队列-比较与](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted)对照。 |
| **Azure 表** | 允许你将结构化的 NoSQL 数据存储在云中，并通过无架构设计提供键/属性存储。 | 你需要存储灵活的数据集，例如 web 应用程序的用户数据、通讯簿、设备信息，或者服务需要的其他类型的元数据。 <br/><br/>有关何时使用表存储与 Azure Cosmos DB 表 API 的指南，请参阅[使用 Azure Cosmos DB 表 API 和 Azure 表存储进行开发](../../cosmos-db/table-support.md)。 |

## <a name="blob-storage"></a>Blob 存储

Azure Blob 存储是 Microsoft 提供的适用于云的对象存储解决方案。 Blob 存储最适合存储巨量的非结构化数据，例如文本或二进制数据。

Blob 存储最适合用于：

- 直接向浏览器提供图像或文档。
- 存储文件以供分布式访问。
- 对视频和音频进行流式处理。
- 存储用于备份和还原、灾难恢复及存档的数据。
- 存储数据以供本地或 Azure 托管服务执行分析。

可以通过 HTTP 或 HTTPS 从世界上的任何位置访问 Blob 存储中的对象。 用户或客户端应用程序可以通过 URL、[Azure 存储 REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api)、[Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage)、[Azure CLI](https://docs.microsoft.com/cli/azure/storage) 或 Azure 存储客户端库访问 Blob。 存储客户端库以多种语言提供，包括 [.NET](/dotnet/api/overview/azure/storage?view=azure-dotnet)、[Java](https://docs.microsoft.com/java/api/overview/azure/storage)、[Node.js](https://azure.github.io/azure-storage-node)、[Python](https://azure-storage.readthedocs.io/)、[PHP](https://azure.github.io/azure-storage-php/) 和 [Ruby](https://azure.github.io/azure-storage-ruby)。

有关 Blob 存储的详细信息，请参阅 [Blob 存储简介](../blobs/storage-blobs-introduction.md)。

## <a name="azure-files"></a>Azure 文件

可以通过 [Azure 文件](../files/storage-files-introduction.md)设置可用性高的网络文件共享，以便使用标准的服务器消息块 (SMB) 协议对其进行访问。 这意味着，多个 VM 可以共享启用了读取和写入访问权限的相同文件。 也可使用 REST 接口或存储客户端库来读取文件。

Azure 文件不同于公司文件共享的一点是，可以在全球任何地方使用 URL 来访问文件，只要该 URL 指向文件且包含共享访问签名 (SAS) 令牌即可。 可以生成 SAS 令牌，用于在指定时间内对私有资产进行特定访问。

文件共享适用于许多常用方案：

- 许多本地应用程序使用文件共享。 此功能可以更方便地迁移那些将数据共享到 Azure 的应用程序。 如果将文件共享装载到本地应用程序所使用的驱动器号，则应用程序中访问文件共享的那部分应尽量少做更改（如果必须进行更改的话）。

- 配置文件可以在一个文件共享上存储，从多个 VM 进行访问。 可以将一个组中多个开发人员使用的工具和实用程序存储到文件共享中，确保每个人都能找到它们并使用同一版本。

- 资源日志、指标和故障转储只是三个数据示例，可将数据写入文件共享并在以后处理或分析。

有关 Azure 文件的详细信息，请参阅 [Azure 文件简介](../files/storage-files-introduction.md)。

某些 SMB 功能不适用于云。 有关详细信息，请参阅 [Azure 文件服务不支持的功能](/rest/api/storageservices/features-not-supported-by-the-azure-file-service)。

## <a name="queue-storage"></a>队列存储

Azure 队列服务用于存储和检索消息。 队列消息最大可以为 64 KB，一个队列可以包含数百万条消息。 队列通常用于存储需要异步处理的消息的列表。

例如，假设你需要客户能够上传图片，并且你需要创建每个图片的缩略图。 可以让客户在上传图片时等待你创建缩略图， 也可以使用队列。 当客户完成上传操作后，向队列写入一条消息。 然后通过 Azure Function 从队列检索该消息并创建缩略图。 此处理过程的每一部分都可以单独进行缩放，让你可以根据使用情况进行调整，加强控制。

有关 Azure 队列的详细信息，请参阅[队列简介](../queues/storage-queues-introduction.md)。

## <a name="table-storage"></a>表存储

Azure 表存储现在是 Azure Cosmos DB 的一部分。 若要查看 Azure 表存储文档，请参阅 [Azure 表存储概述](../tables/table-storage-overview.md)。 除了现有的 Azure 表存储服务，还有新的 Azure Cosmos DB 表 API 产品/服务，后者提供吞吐量优化表、全局分发和自动辅助索引。 若要了解详细信息并尝试新的高级体验，请参阅[Azure Cosmos DB 表 API](https://aka.ms/premiumtables)。

有关表存储的详细信息，请参阅 [Azure 表存储概述](../tables/table-storage-overview.md)。

## <a name="disk-storage"></a>磁盘存储

Azure 托管磁盘是虚拟硬盘 (VHD)。 可以将其视为本地服务器中的物理磁盘，但它是虚拟化的。 Azure 托管磁盘作为页 blob 存储，后者是 Azure 中的随机 IO 存储对象。 我们之所以将托管磁盘称为“托管”是因为，它是对页 blob、blob 容器和 Azure 存储帐户的抽象。 对于托管磁盘，你所要做的就是预配磁盘，而 Azure 负责其余的工作。

有关托管磁盘的详细信息，请参阅 [Azure 托管磁盘简介](../../virtual-machines/windows/managed-disks-overview.md)。

## <a name="types-of-storage-accounts"></a>存储帐户的类型

Azure 存储提供多种类型的存储帐户。 每个类型支持不同的功能，并具有自身的定价模型。 有关存储帐户类型的详细信息，请参阅 [Azure 存储帐户概述](storage-account-overview.md)。

## <a name="secure-access-to-storage-accounts"></a>保护对存储帐户的访问

对 Azure 存储的每个请求必须获得授权。 Azure 存储支持以下授权方法：

- **用于 Blob 和队列数据的 Azure Active Directory (Azure AD) 集成。** Azure 存储支持通过基于角色的访问控制 (RBAC) 使用 Azure AD 为 Blob 和队列服务进行身份验证和授权。 建议使用 Azure AD 进行授权请求，以确保极高的安全性和易用性。 有关详细信息，请参阅[使用 Azure Active Directory 授予对 Azure Blob 和队列的访问权限](storage-auth-aad.md)。
- **Azure AD Azure 文件的 SMB 的授权。** Azure 文件通过 SMB （服务器消息块） Azure Active Directory 域服务（Azure AD DS）或本地 Active Directory 域服务（预览版）支持基于身份的授权。 已加入域的 Windows Vm 可使用 Azure AD 凭据访问 Azure 文件共享。 有关详细信息，请参阅[Azure 文件基于标识的身份验证支持概述，了解如何使用](../files/storage-files-active-directory-overview.md) [azure 文件部署和规划 azure 文件部署](../files/storage-files-planning.md#identity)。
- **通过共享密钥进行授权。** Azure 存储 Blob、文件、队列和表服务支持通过共享密钥授权。 使用共享密钥授权的客户端会将标头传递给使用存储帐户访问密钥进行签名的每个请求。 有关详细信息，请参阅[通过共享密钥进行授权](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key)。
- **使用共享访问签名 (SAS) 进行授权。** 共享访问签名 (SAS) 是一个字符串，其中包含的安全令牌可以追加到存储资源的 URI。 安全令牌封装了各种约束，例如权限、访问时间间隔。 有关详细信息，请参阅[使用共享访问签名（SAS）](storage-sas-overview.md)。
- **对容器和 Blob 的匿名访问。** 容器及其 Blob 也许可以公开使用。 指定某个容器或 Blob 为公用的时，任何人都可以匿名读取它，不需要进行身份验证。 有关详细信息，请参阅[管理对容器和 Blob 的匿名读取访问](../blobs/storage-manage-access-to-resources.md)。

## <a name="encryption"></a>加密

核心存储服务提供两种基本类型的加密。 若要详细了解安全性和加密，请参阅 [Azure 存储安全指南](../blobs/security-recommendations.md)。

### <a name="encryption-at-rest"></a>静态加密

Azure 存储加密可保护数据，使组织能够信守在安全性与合规性方面作出的承诺。 Azure 存储先自动加密所有数据，再将数据保存到存储帐户，然后在检索之前解密数据。 加密、解密和密钥管理过程对用户是透明的。 客户也可选择使用 Azure Key Vault 管理自己的密钥。 有关详细信息，请参阅[静态数据的 Azure 存储加密](storage-service-encryption.md)。

### <a name="client-side-encryption"></a>客户端加密

Azure 存储客户端库提供的方法用于加密客户端库的数据，然后通过网络发送数据并解密响应。 通过客户端加密加密的数据也通过 Azure 存储进行静态加密。 有关客户端加密的详细信息，请参阅 [Azure 存储的使用 .NET 的客户端加密](storage-client-side-encryption.md)。

## <a name="redundancy"></a>冗余

若要确保数据持久，Azure 存储存储数据的多个副本。 设置存储帐户时，可选择冗余选项。 有关详细信息，请参阅 [Azure 存储冗余](/azure/storage/common/storage-redundancy?toc=/azure/storage/blobs/toc.json)。

## <a name="transfer-data-to-and-from-azure-storage"></a>将数据传入和传出 Azure 存储

有多个选项用于将数据移入或移出 Azure 存储。 选择哪个选项取决于数据集的大小和网络带宽。 有关详细信息，请参阅[选择 Azure 数据传输解决方案](storage-choose-data-transfer-solution.md)。

## <a name="pricing"></a>定价

决定如何存储和访问数据时，还应考虑到相关的成本。 有关详细信息，请参阅[Azure 存储定价](https://azure.microsoft.com/pricing/details/storage/)。

## <a name="storage-apis-libraries-and-tools"></a>存储 API、库和工具

可以通过可以发出 HTTP/HTTPS 请求的任何语言来访问存储帐户中的资源。 此外，核心 Azure 存储服务提供多种常用语言的编程库。 这些库通过对细节进行处理简化了使用 Azure 存储的许多方面，这些细节包括同步和异步调用、操作的批处理、异常管理、自动重试、操作行为，等等。 这些库当前可供下列语言和平台以及正在筹备的其他语言和平台使用：

### <a name="azure-storage-data-api-and-library-references"></a>Azure 存储数据 API 和库参考

- [Azure 存储 REST API](https://docs.microsoft.com/rest/api/storageservices/)
- [适用于 .NET 的 Azure 存储客户端库](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
- [适用于 Java/Android 的 Azure 存储客户端库](https://docs.microsoft.com/java/api/overview/azure/storage)
- [适用于 Node.js 的 Azure 存储客户端库](https://docs.microsoft.com/javascript/api/overview/azure/storage-overview)
- [适用于 Python 的 Azure 存储客户端库](https://github.com/Azure/azure-storage-python)
- [适用于 PHP 的 Azure 存储客户端库](https://github.com/Azure/azure-storage-php)
- [适用于 Ruby 的 Azure 存储客户端库](https://github.com/Azure/azure-storage-ruby)
- [用于 c + + 的 Azure 存储客户端库](https://github.com/Azure/azure-storage-cpp)

### <a name="azure-storage-management-api-and-library-references"></a>Azure 存储管理 API 和库参考

- [存储资源提供程序 REST API](https://docs.microsoft.com/rest/api/storagerp/)
- [适用于 .NET 的存储资源提供程序客户端库](https://docs.microsoft.com/dotnet/api/overview/azure/storage/management)
- [存储服务管理 REST API (Classic)](https://msdn.microsoft.com/library/azure/ee460790.aspx)

### <a name="azure-storage-data-movement-api-and-library-references"></a>Azure 存储数据移动 API 和库参考

- [存储导入/导出服务 REST API](https://docs.microsoft.com/rest/api/storageimportexport/)
- [适用于 .NET 的存储数据移动客户端库](/dotnet/api/microsoft.azure.storage.datamovement)

### <a name="tools-and-utilities"></a>工具和实用程序

- [适用于存储的 Azure PowerShell Cmdlet](https://docs.microsoft.com/powershell/module/az.storage)
- [适用于存储的 Azure CLI Cmdlet](https://docs.microsoft.com/cli/azure/storage)
- [AzCopy 命令行实用程序](https://aka.ms/downloadazcopy)
- [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)是 Microsoft 免费提供的独立应用，适用于在 Windows、macOS 和 Linux 上以可视方式处理 Azure 存储数据。
- [Azure 存储客户端工具](../storage-explorers.md)
- [Azure 开发人员工具](https://azure.microsoft.com/tools/)

## <a name="next-steps"></a>后续步骤

若要启动并运行核心 Azure 存储服务，请参阅[创建存储帐户](storage-account-create.md)。
