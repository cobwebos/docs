---
title: 确定何时使用 Azure Blob、Azure 文件或 Azure 磁盘
description: 了解可通过哪些不同的方式在 Azure 中存储和访问数据，以帮助确定要使用哪种技术。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 4b1a42e25a6d8c7b4a3c24dffcb858ffe63dd10b
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2019
ms.locfileid: "71671051"
---
# <a name="deciding-when-to-use-azure-blobs-azure-files-or-azure-disks"></a>确定何时使用 Azure Blob、Azure 文件或 Azure 磁盘

Microsoft Azure 在 Azure 存储中提供多种功能用于在云中存储和访问数据。 本文介绍 Azure 文件、Blob 和磁盘，旨在帮助用户选择合适的功能。

## <a name="scenarios"></a>方案

下表比较了文件、Blob 和磁盘，并显示每种技术适合的示例情景。

| 功能 | 描述 | 使用时机 |
|--------------|-------------|-------------|
| Azure 文件 | 提供 SMB 接口、客户端库，以及一个用于从任意位置访问所存储的文件的 [REST 接口](/rest/api/storageservices/file-service-rest-api)。 | 可让你将某个已使用本机文件系统 API 在自身与在 Azure 中运行的其他应用程序之间共享数据的应用程序“即时转移”到云中。<br/><br/>可让你存储需要从许多虚拟机访问的开发和调试工具。 |
| **Azure Blob** | 提供客户端库，以及一个可用于在块 blob 中大规模存储和访问非结构化数据的 [REST 接口](/rest/api/storageservices/blob-service-rest-api)。<br/><br/>还支持 [Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md)，用于企业大数据分析解决方案。 | 使应用程序能够支持流式处理和随机访问方案。<br/><br/>可让你从任意位置访问应用程序数据。<br/><br/>想要在 Azure 上生成企业数据湖并执行大数据分析。 |
| **Azure 磁盘** | 提供客户端库和 [REST 接口](/rest/api/compute/manageddisks/disks/disks-rest-api)，借助该接口可通过附加的虚拟硬盘永久地存储和访问数据。 | 可让你即时转移使用本机文件系统 API 在持久性磁盘中读取和写入数据的应用程序。<br/><br/>可让你存储不需要从磁盘附加到的虚拟机外部访问的数据。 |


## <a name="next-steps"></a>后续步骤

决定如何存储和访问数据时，还应考虑到相关的成本。 有关详细信息，请参阅 [Azure 存储定价](https://azure.microsoft.com/pricing/details/storage/)。
  
某些 SMB 功能不适用于云。 有关详细信息，请参阅 [Azure 文件服务不支持的功能](/rest/api/storageservices/features-not-supported-by-the-azure-file-service)。
 
有关 Azure Blob 的详细信息，请参阅[什么是 Azure Blob 存储？](../blobs/storage-blobs-overview.md)一文。

有关磁盘存储的详细信息，请参阅[托管磁盘简介](../../virtual-machines/windows/managed-disks-overview.md)。

有关 Azure 文件存储的详细信息，请参阅 [Azure 文件存储部署规划](../files/storage-files-planning.md)一文。