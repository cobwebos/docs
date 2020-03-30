---
title: include 文件
description: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/05/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 726fc9cc0e4eb5a8f9bed5cca8dd4d3df588ac7c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371540"
---
Azure 存储提供多种类型的存储帐户。 每个类型支持不同的功能，并具有自身的定价模型。 在创建存储帐户之前，请考虑到这些差异，以确定最适合应用程序的帐户类型。 存储帐户的类型包括：

- **通用 v2 帐户**：Blob、文件、队列和表的基本存储帐户类型。 建议在大多数情况下使用 Azure 存储。
- **通用 v1 帐户**：Blob、文件、队列和表的旧帐户类型。 如果可能，请改用常规用途 v2 帐户。
- **块 Blob 存储帐户**：具有块 blob 和追加 blob 的高级性能特征的存储帐户。 建议适用于事务速率高的方案，或使用较小对象或需要始终低存储延迟的方案。
- **文件存储帐户**：仅文件存储帐户具有高级性能特征。 推荐用于企业或高性能扩展应用程序。
- **Blob 存储帐户**：仅存储的旧存储帐户。 如果可能，请改用常规用途 v2 帐户。

下表描述了存储帐户的类型及其功能：

| 存储帐户类型 | 支持的服务                       | 支持的性能层      | 支持的访问层         | 复制选项               | 部署模型<div role="complementary" aria-labelledby="deployment-model"><sup>1</sup></div> | 加密<div role="complementary" aria-labelledby="encryption"><sup>2</sup></div> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------|-----------------------------------|------------------------------|------------------------|
| 常规用途 V2   | Blob、文件、队列、表、磁盘和 Data Lake Gen2<div role="complementary" aria-labelledby="data-lake-gen2"><sup>6</sup></div>      | 标准、高级<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | 热、冷、存档<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS、GRS、RA-GRS、ZRS、GZRS（预览）、RA-GZRS（预览）<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div> | 资源管理器             | 加密              |
| 常规用途 V1   | Blob、文件、队列、表和磁盘       | 标准、高级<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | 空值                            | LRS、GRS、RA-GRS                  | 资源管理器、经典    | 加密              |
| 块 Blob 存储   | Blob（仅限块 Blob 和追加 Blob） | Premium                       | 空值                            | LRS， ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | 资源管理器             | 加密              |
| 文件存储   | 仅文件 | Premium                       | 空值                            | LRS， ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | 资源管理器             | 加密              |
| BlobStorage         | Blob（仅限块 Blob 和追加 Blob） | Standard                      | 热、冷、存档<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS、GRS、RA-GRS                  | 资源管理器             | 加密              |

<div id="deployment-model"><sup>1</sup>建议使用 Azure 资源管理器部署模型。 仍将在某些位置创建使用经典部署模型的存储帐户，继续支持现有的经典帐户。 有关详细信息，请参阅 <a href="https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model">Azure 资源管理器与经典部署：了解部署模型和资源状态</a>。</div><br/>

<div id="encryption"><sup>2</sup>使用针对静态数据的存储服务加密 (SSE) 来加密所有存储帐户。 有关详细信息，请参阅<a href="https://docs.microsoft.com/azure/storage/common/storage-service-encryption">静态数据的 Azure 存储服务加密</a>。</div><br/>

<div id="archive"><sup>3</sup>存档存储和 blob 级分层仅支持块 Blob。 存档层仅在单个 Blob 级别可用，而不是在存储帐户级别。 有关详细信息，请参阅 <a href="https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers">Azure Blob 存储：热存储层、冷存储层和存档存储层</a>。</div><br/>

<div id="zone-redundant-storage"><sup>4</sup>区域冗余存储 （ZRS） 和地理位置区域冗余存储 （GZRS/RA-GZRS） （预览）仅适用于特定区域的标准通用 V2、BlockBlob 存储和文件存储帐户。 有关 Azure 存储冗余选项的详细信息，请参阅<a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy">Azure 存储冗余</a>。</div><br/>

<div id="premium-performance"><sup>5</sup>通用 v2 和通用 v1 帐户的高级性能仅适用于磁盘和页面 Blob。 块或追加 blob 的高级性能仅在块 Blob 存储帐户上可用。 文件的高级性能仅在文件存储帐户上可用。</div><br/>

<div id="data-lake-gen2"><sup>6</sup>Azure 数据湖存储 Gen2 是一组专用于 Azure Blob 存储的大型数据分析功能。 数据湖存储 Gen2 仅在启用分层命名空间的通用 V2 存储帐户上受支持。 有关数据存储第 2 代的详细信息，请参阅<a href="https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction">Azure 数据存储湖存储第 2 代简介</a>。</div>
