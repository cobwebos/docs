---
title: 云存储，使用 Azure 存储构建高度安全、耐用、可扩展的应用
description: 了解在云中存储大型结构化和非结构化移动应用程序数据的服务。
author: codemillmatt
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 682f06936480d9018d759b4df3bed80be1206faf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240970"
---
# <a name="cloud-storage-for-highly-secure-durable-scalable-apps-with-azure-storage"></a>云存储，使用 Azure 存储提供高度安全、耐用、可扩展的应用
[Azure 存储](https://azure.microsoft.com/services/storage/)是 Microsoft 面向现代应用程序的云存储解决方案，它为数据对象提供可大规模扩展的对象存储、云的文件系统服务、用于可靠消息传送的消息存储和 NoSQL 存储。 Azure 存储：
- **耐用且高度可用：** 冗余可确保在发生瞬时硬件故障时数据是安全的。 还可以选择在各个数据中心或地理区域之间复制数据，从而在发生本地灾难或自然灾害时提供额外的保护。 以此方式复制的数据在发生意外中断时将保持高可用性。
- **安全：** 写入 Azure 存储的所有数据都由服务加密。 Azure 存储可以精细地控制谁可以访问你的数据。
- **可扩展：** 服务设计为可大规模扩展，以满足当今应用程序的数据存储和性能需求。
- **托管：** Azure 可为您处理硬件维护、更新和关键问题。
- **可访问：** 可通过 HTTP 或 HTTPS 从世界任何地方访问数据。 Microsoft 以多种语言提供客户端库，如 .NET、Java、Node.js、Python、PHP、Ruby 和 Go，以及成熟的 REST API。 Azure PowerShell 或 Azure CLI 中支持脚本编写。 Azure 门户和 Azure 存储资源管理器提供了用于处理数据的简便可视化解决方案。

使用以下服务在移动应用中启用云存储。

## <a name="azure-blob-storage"></a>Azure Blob 存储
[Azure Blob 存储](https://azure.microsoft.com/services/storage/blobs/)为云提供了对象存储解决方案。 Blob 存储经过优化，用于存储大量不符合特定数据模型或定义（如文本或二进制）的非结构化数据。 它支持客户端库使用的各种语言。 Blob 存储旨在：
- 将图像或文档直接送达浏览器。
- 存储文件以进行分布式访问。
- 流式传输视频和音频。
- 写入日志文件。
- 存储用于备份和还原、灾难恢复和存档的数据。
- 存储数据，以便按本地或 Azure 托管的服务进行分析。

**引用**
- [Azure 门户](https://portal.azure.com)
- [Azure Blob 存储文档](/azure/storage/blobs/storage-blobs-introduction)
- [快速入门](/azure/storage/blobs/storage-quickstart-blobs-portal)
- [示例](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

## <a name="azure-table-storage"></a>Azure 表存储
[Azure 表存储](https://azure.microsoft.com/services/storage/tables/)是一种服务，用于在云中存储结构化的 NoSQL 数据，并提供具有无架构设计的密钥或属性存储。 Azure 表存储可存储大量结构化数据。 该服务是 NoSQL 数据存储，它接受 Azure 云内外的经过身份验证的调用。 Azure 表是存储结构化非关系数据的理想选择。 表存储通常用于：
- 存储能够为 Web 规模应用程序提供服务的 TB 结构化数据。
- 存储不需要复杂联接、外键或存储过程的数据集，并且可以非规范化以快速访问。
- 使用群集索引快速查询数据。
- 使用 OData 协议和 LINQ 查询访问数据，这些查询与 Windows 通信基础 （WCF） 数据服务 .NET 库一起。

您可以使用表存储来存储和查询大量结构化的非关系数据。 您的表会随着需求增加而缩放。

**引用**
- [Azure 门户](https://portal.azure.com)
- [Azure 表存储文档](/azure/storage/tables/table-storage-overview)
- [示例](/azure/cosmos-db/tutorial-develop-table-dotnet?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fstorage%2Ftables%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
- [快速入门](/azure/storage/tables/table-storage-quickstart-portal)

## <a name="azure-files"></a>Azure 文件
使用[Azure 文件](https://azure.microsoft.com/services/storage/files/)，可以设置可以使用标准服务器消息块 （SMB） 协议访问的高可用性网络文件共享。 多个 VM 可以同时共享具有读取和写入访问权限的相同文件。 您还可以使用 REST 接口或存储客户端库读取文件。 您可以使用指向该文件的 URL 并包含共享访问签名 （SAS） 令牌，从世界任何地方访问文件。 您可以生成 SAS 令牌。 它们允许在特定时间内特定地访问私有资产。

Azure 文件共享可用于：
- **替换或补充本地文件服务器：** 流行的操作系统（如 Windows、macOS 和 Linux）可以直接将 Azure 文件共享装载到世界上任何位置。 此外，可以使用 Azure 文件同步将 Azure 文件共享复制到本地或云中的 Windows Server，以便在使用位置对数据进行高性能的分布式缓存。
- **提升和换档应用：** 将应用程序迁移到希望文件共享以存储文件应用程序或用户数据的云。
- **简化云开发：** Azure 文件还可通过多种方式用于简化新的云开发项目。 例如：
    - **共享应用程序设置：** 分布式应用程序的一个常见模式是，在集中位置将配置文件从许多应用程序实例访问它们。 应用程序实例可以通过文件 REST API 加载其配置。 用户可以根据需要通过在本地安装 SMB 共享来访问它们。
    - **诊断共享：** Azure 文件共享是云应用程序编写日志、指标和崩溃转储的方便场所。 日志可以由应用程序实例通过文件 REST API 写入。 开发人员可以通过在其本地计算机上安装文件共享来访问它们。 此功能可实现极大的灵活性。 开发人员可以采用云开发，而无需放弃他们所知道的现有工具。

**引用**
- [Azure 门户](https://portal.azure.com)
- [Azure 文件文档](/azure/storage/files/storage-files-introduction)
- [快速入门](/azure/storage/files/storage-files-quick-create-use-windows)

## <a name="azure-queue-storage"></a>Azure 队列存储
[Azure 队列存储](https://azure.microsoft.com/services/storage/queues/)是存储大量消息的服务。 您可以使用 HTTP 或 HTTPS 通过经过身份验证的调用从世界任何地方访问消息。 队列消息大小最大可为 64 KB。 队列可能包含数百万条消息，最高为存储帐户的总容量限制。 队列通常用于创建要异步处理的积压工作 (backlog)。

**引用**
- [Azure 门户](https://portal.azure.com)
- [Azure 队列存储文档](/azure/storage/queues/)
- [快速入门](/azure/storage/queues/storage-quickstart-queues-portal)
- [示例](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
