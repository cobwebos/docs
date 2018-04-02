---
title: Blob 存储简介 - Azure 中的对象存储 | Microsoft Docs
description: Azure Blob 存储用于存储巨量的非结构化对象数据，例如文本或二进制数据。 应用程序可以通过 PowerShell、Azure CLI、代码、Azure 存储客户端库或 REST 来访问 Blob 存储中的对象。
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: overview
ms.date: 03/27/2018
ms.author: tamram
ms.openlocfilehash: 0fff0032ec2452413bcd1df3175634b14a64208f
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2018
---
# <a name="introduction-to-blob-storage"></a>Blob 存储简介

Azure Blob 存储是面向数据对象的 Microsoft 云存储解决方案。 Azure Blob 存储可以存储巨量的非结构化对象数据，例如文本或二进制数据。 可以通过 HTTP 或 HTTPS 从任何位置访问 Blob 存储中的数据。 可以使用 Blob 存储向外公开数据，或者私下存储应用程序数据。

Blob 存储的常见用途包括：

* 直接向浏览器提供图像或文档
* 存储文件以供分布式访问
* 对视频和音频进行流式处理
* 存储用于备份和还原、灾难恢复及存档的数据。
* 存储数据以供本地或 Azure 托管服务执行分析
* 存储用于 Azure 虚拟机的 VHD

## <a name="blob-service-concepts"></a>Blob 服务概念

Blob 服务包含以下组件：

![Blob 体系结构](./media/storage-blobs-introduction/blob1.png)

* **存储帐户：** 对 Azure 存储服务的所有访问都要通过存储帐户来完成。 此存储账户可以是“通用存储帐户 (v1 or v2)”，也可以是“Blob 存储帐户”。 有关详细信息，请参阅[关于 Azure 存储帐户](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

* **容器：** 一个容器包含一组 blob 集。 所有 blob 必须位于相应的容器中。 一个帐户可以包含无限个容器。 一个容器可以存储无限个 Blob。 请注意，容器名称必须小写。

* **Blob：** 任何类型和大小的文件。 Azure 存储提供三种类型的 Blob：块 Blob、[页 Blob](storage-blob-pageblob-overview.md) 和追加 Blob。
  
    *块 Blob* 特别适用于存储短的文本或二进制文件，例如文档和媒体文件。 *追加 Blob* 类似于块 Blob，因为它们是由块组成的，但针对追加操作对它们进行了优化，因此它们适用于日志记录方案。 单个块 Blob 可以包含最多 50000 个块，每个块最大 100 MB，总大小稍微大于 4.75 TB (100 MB X 50000)。 单个追加 Blob 可以包含最多 50000 个块，每个块最大 4 MB，总大小稍微大于 195 GB (4 MB X 50000)。
  
    页 Blob 最大可达 8 TB 大小，并且对于频繁的读/写操作更加高效。 Azure 虚拟机使用页 Blob 作为 OS 和数据磁盘。
  
    有关命名容器和 Blob 的详细信息，请参阅 [命名和引用容器、Blob 和元数据](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata)。

## <a name="next-steps"></a>后续步骤

* [创建存储帐户](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [通过 .NET 开始使用 Blob 存储](storage-dotnet-how-to-use-blobs.md)
* [使用 .NET 的 Azure 存储示例](../common/storage-samples-dotnet.md)
* [使用 Java 的 Azure 存储示例](../common/storage-samples-java.md)
