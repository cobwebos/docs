---
title: "确定何时使用 Azure Blob、Azure 文件或 Azure 数据磁盘"
description: "了解可通过哪些不同的方式在 Azure 中存储和访问数据，以帮助确定要使用哪种技术。"
services: storage
documentationcenter: 
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: robinsh
ms.translationtype: Human Translation
ms.sourcegitcommit: fc27849f3309f8a780925e3ceec12f318971872c
ms.openlocfilehash: e282a8a7bec1cb6e48110e7c9859a3a19ab8a11e
ms.contentlocale: zh-cn
ms.lasthandoff: 06/14/2017

---

# <a name="deciding-when-to-use-azure-blobs-azure-files-or-azure-data-disks"></a>确定何时使用 Azure Blob、Azure 文件或 Azure 数据磁盘

Microsoft Azure 在 Azure 存储中提供多种功能用于在云中存储和访问数据。 本文介绍 Azure 文件、Blob 和数据磁盘，旨在帮助你选择合适的功能。

## <a name="scenarios"></a>方案

下表比较了文件、Blob 和数据磁盘，并显示每种技术适合的示例情景。

| 功能 | 说明 | 使用时机 |
|--------------|-------------|-------------|
| **Azure 文件** | 提供 SMB 接口、客户端库，以及一个用于从任意位置访问所存储的文件的 [REST 接口](/rest/api/storageservices/file-service-rest-api)。 | 可让你将某个已使用本机文件系统 API 在自身与在 Azure 中运行的其他应用程序之间共享数据的应用程序“即时转移”到云中。<br/><br/>可让你存储需要从许多虚拟机访问的开发和调试工具。 |
| **Azure Blob** | 提供客户端库，以及一个可用于在块 Blob 中批量存储和访问非结构化数据的 [REST 接口](/rest/api/storageservices/blob-service-rest-api)。 | 使应用程序能够支持流式处理和随机访问方案。<br/><br/>可让你从任意位置访问应用程序数据。 |
| **Azure 数据磁盘** | 提供客户端库，以及一个可用于通过附加的虚拟硬盘一致性地存储和访问数据的 [REST 接口](/rest/api/compute/virtualmachines/virtualmachines-create-or-update)。 | 可让你即时转移使用本机文件系统 API 在持久性磁盘中读取和写入数据的应用程序。<br/><br/>可让你存储不需要从磁盘附加到的虚拟机外部访问的数据。 |

## <a name="comparison-files-and-blobs"></a>比较：文件和 Blob

下表将 Azure 文件和 Azure Blob 进行比较。  
  
||||  
|-|-|-|  
|**属性**|**Azure Blob**|**Azure 文件**|  
|持久性选项|LRS、ZRS、GRS（以及用于提高可用性的 RA-GRS）|LRS、GRS|  
|可访问性|REST API|REST API<br /><br /> SMB 2.1 和 SMB 3.0（标准文件系统 API）|  
|连接|REST API - 全球|REST API - 全球<br /><br /> SMB 2.1 - 区域内部<br /><br /> SMB 3.0 - 全球|  
|终结点|`http://myaccount.blob.core.windows.net/mycontainer/myblob`|`\\myaccount.file.core.windows.net\myshare\myfile.txt`<br /><br /> `http://myaccount.file.core.windows.net/myshare/myfile.txt`|  
|目录|平面命名空间|真正目录对象|  
|名称区分大小写|区分大小写|不区分大小写，但保留大小写|  
|容量|高达 500 TB 的容器|5 TB 文件共享|  
|吞吐量|每个块 Blob 高达 60 MB/秒|每个共享高达 60 MB/秒|  
|对象大小|每个块 Blob 高达 200 GB|每个文件高达 1 TB|  
|计费容量|基于写入的字节数|基于文件大小|  
|客户端库|多语言|多语言|  
  
## <a name="comparison-files-and-data-disks"></a>比较：文件和数据磁盘

Azure 文件是对 Azure 数据磁盘的补充。 一个数据磁盘每次只能附加到一个 Azure 虚拟机。 数据磁盘是作为页 Blob 存储在 Azure 存储中的固定格式 VHD，由虚拟机用来存储持久性数据。 可以像访问本地磁盘一样访问 Azure 文件中的文件共享（使用本机文件系统 API），后者可在多个虚拟机之间共享。  
 
下表将 Azure 文件和 Azure 数据磁盘进行比较。  
 
||||  
|-|-|-|  
|**属性**|**Azure 数据磁盘**|**Azure 文件**|  
|范围|专供单个虚拟机使用|可在多个虚拟机之间进行共享访问|  
|快照和复制|是|否|  
|配置|启动虚拟机时进行连接|启动虚拟机后进行连接|  
|身份验证|内置|使用 net use 进行设置|  
|清理|自动|手动|  
|使用 REST 访问|无法访问 VHD 中的文件|可以访问共享中存储的文件|  
|最大大小|1 TB 磁盘|5 TB 文件共享，共享中可保存 1 TB 文件|  
|最大 8KB IOps|500 IOps|1000 IOps|  
|吞吐量|每个磁盘高达 60 MB/秒|每个文件共享高达 60 MB/秒|  

## <a name="next-steps"></a>后续步骤

决定如何存储和访问数据时，还应考虑到相关的成本。 有关详细信息，请参阅 [Azure 存储定价](https://azure.microsoft.com/pricing/details/storage/)。
  
某些 SMB 功能不适用于云。 有关详细信息，请参阅 [Azure 文件服务不支持的功能](/rest/api/storageservices/features-not-supported-by-the-azure-file-service)。
  
有关数据磁盘的详细信息，请参阅[管理磁盘和映像](storage-about-disks-and-vhds-linux.md)以及[如何将数据磁盘附加到 Windows 虚拟机](../virtual-machines/windows/classic/attach-disk.md)。
