---
title: 利用 Azure 存储构建高度安全、持久、可缩放的应用程序的云存储
description: 了解在云中存储大型结构化和非结构化移动应用程序数据的服务。
author: elamalani
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 53754976a6e1f6d893cf7d6ab73d57c95dae193f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453249"
---
# <a name="cloud-storage-for-highly-secure-durable-scalable-apps-with-azure-storage"></a>适用于 Azure 存储的高度安全、持久、可缩放的应用程序的云存储
[Azure 存储](https://azure.microsoft.com/services/storage/)是 Microsoft 的云存储解决方案，适用于为数据对象提供高度可缩放的对象存储、适用于云的文件系统服务、用于可靠消息传送的消息存储和 NoSQL 存储。 Azure 存储：
- **持久且高度可用：** 冗余确保在发生暂时性的硬件故障时，数据是安全的。 还可以选择在各个数据中心或地理区域之间复制数据，从而在发生本地灾难或自然灾害时提供额外的保护。 以此方式复制的数据在发生意外中断时将保持高可用性。
- **安全：** 写入 Azure 存储的所有数据均由该服务加密。 Azure 存储可以精细地控制谁可以访问你的数据。
- **可伸缩：** 服务的设计可高度可扩展，以满足当今应用程序的数据存储和性能需求。
- **托管：** Azure 会处理硬件维护、更新和关键问题。
- **可访问：** 数据可通过 HTTP 或 HTTPS 从世界上的任何位置进行访问。 Microsoft 提供多种语言的客户端库，如 .NET、Java、node.js、Python、PHP、Ruby 和中转，以及成熟的 REST API。 Azure PowerShell 或 Azure CLI 支持脚本。 Azure 门户和 Azure 存储资源管理器提供易于使用的可视化解决方案来处理数据。

使用以下服务在移动应用中启用云存储。

## <a name="azure-blob-storage"></a>Azure Blob 存储
[Azure Blob 存储](https://azure.microsoft.com/services/storage/blobs/)为云提供了一个对象存储解决方案。 Blob 存储经过优化，可存储大量非结构化数据，这些数据不符合特定的数据模型或定义，如文本或二进制。 它支持客户端库使用的各种语言。 Blob 存储旨在：
- 直接向浏览器提供图像或文档。
- 存储用于分布式访问的文件。
- 流式传输视频和音频。
- 写入日志文件。
- 存储数据以用于备份和还原、灾难恢复和存档。
- 存储数据以供本地或 Azure 托管服务进行分析。

**参考**
- [Azure 门户](https://portal.azure.com)
- [Azure Blob 存储文档](/azure/storage/blobs/storage-blobs-introduction)
- [快速入门](/azure/storage/blobs/storage-quickstart-blobs-portal)
- [示例](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

## <a name="azure-table-storage"></a>Azure 表存储
[Azure 表存储](https://azure.microsoft.com/services/storage/tables/)是一项用于在云中存储结构化 NoSQL 数据的服务，并提供具有无架构设计的密钥或属性存储。 Azure 表存储可存储大量结构化数据。 该服务是一个 NoSQL 数据存储，它接受来自 Azure 云内部和外部的经过身份验证的呼叫。 Azure 表非常适合用于存储结构化的非关系数据。 表存储通常用于：
- 存储数 tb 的结构化数据，能够为 web 规模应用程序提供服务。
- 存储无需复杂联接、外键或存储过程，并且可以对其进行非规范化以实现快速访问的数据集。
- 使用聚集索引快速查询数据。
- 使用 OData 协议和 LINQ 查询和 Windows Communication Foundation （WCF）数据服务 .NET 库访问数据。

可以使用表存储来存储和查询大型结构化、非关系数据集。 表随着需求的增加而扩展。

**参考**
- [Azure 门户](https://portal.azure.com)
- [Azure 表存储文档](/azure/storage/tables/table-storage-overview)
- [示例](/azure/cosmos-db/tutorial-develop-table-dotnet?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fstorage%2Ftables%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
- [快速入门](/azure/storage/tables/table-storage-quickstart-portal)

## <a name="azure-files"></a>Azure 文件
使用[Azure 文件](https://azure.microsoft.com/services/storage/files/)，可以设置高可用网络文件共享，使用标准服务器消息块（SMB）协议可以访问该共享。 多个 Vm 可以共享具有读取和写入访问权限的相同文件。 你还可以使用 REST 接口或存储客户端库来读取文件。 你可以通过使用指向文件的 URL 并包含共享访问签名（SAS）令牌，从世界上的任何位置访问文件。 可以生成 SAS 令牌。 它们允许特定的一段时间内对私有资产进行特定访问。

Azure 文件共享可用于：
- **替换或补充本地文件服务器：** 常用操作系统（例如 Windows、macOS 和 Linux）可以直接在世界各地装载 Azure 文件共享。 此外，可以使用 Azure 文件同步将 Azure 文件共享复制到本地或云中的 Windows Server，以便在使用位置对数据进行高性能的分布式缓存。
- **提升和移动应用程序：** 将应用程序迁移到需要文件共享来存储文件应用程序或用户数据的云。
- **简化云开发：** 还可以通过多种方式使用 Azure 文件来简化新的云开发项目。 例如：
    - **共享应用程序设置：** 分布式应用程序的一种常见模式是将配置文件置于可从多个应用程序实例访问的集中位置。 应用程序实例可以通过文件 REST API 加载其配置。 用户可以根据需要通过在本地装载 SMB 共享来访问这些资源。
    - **诊断共享：** Azure 文件共享是云应用程序编写日志、指标和故障转储的方便位置。 应用程序实例可以通过文件 REST API 来写入日志。 开发人员可以通过在本地计算机上安装文件共享来访问这些文件。 此功能实现了极大的灵活性。 开发人员可以采用云开发，而不必放弃他们知道的现有工具。

**参考**
- [Azure 门户](https://portal.azure.com)
- [Azure 文件文档](/azure/storage/files/storage-files-introduction)
- [快速入门](/azure/storage/files/storage-files-quick-create-use-windows)

## <a name="azure-queue-storage"></a>Azure 队列存储
[Azure 队列存储](https://azure.microsoft.com/services/storage/queues/)是用于存储大量消息的服务。 可以通过使用 HTTP 或 HTTPS 的经过身份验证的呼叫，从世界上的任何地方访问消息。 队列消息大小最大可为 64 KB。 队列可能包含数百万条消息，直至达到存储帐户的总容量限制。 队列通常用于创建要异步处理的积压工作 (backlog)。

**参考**
- [Azure 门户](https://portal.azure.com)
- [Azure 队列存储文档](/azure/storage/queues/)
- [快速入门](/azure/storage/queues/storage-quickstart-queues-portal)
- [示例](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
