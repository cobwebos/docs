---
title: include 文件
description: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/17/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 7889fbc9373cbdfdfab891bf8b1cd610523c7032
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2018
ms.locfileid: "50088085"
---
Azure Blob 存储是 Microsoft 提供的适用于云的对象存储解决方案。 Blob 存储最适合存储巨量的非结构化数据，例如文本或二进制数据。

Blob 存储最适合用于：

* 直接向浏览器提供图像或文档。
* 存储文件以供分布式访问。
* 对视频和音频进行流式处理。
* 向日志文件进行写入。
* 存储用于备份和还原、灾难恢复及存档的数据。
* 存储数据以供本地或 Azure 托管服务执行分析。

用户或客户端应用程序可以在全球任何位置使用 HTTP 或 HTTPS 通过 URL、[Azure 存储 REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api)、[Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage)、[Azure CLI](https://docs.microsoft.com/cli/azure/storage) 或 Azure 存储客户端库访问 Blob 存储对象。 存储客户端库以各种语言提供，包括 [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/client)、[Java](https://docs.microsoft.com/java/api/overview/azure/storage/client)、[Node.js](http://azure.github.io/azure-storage-node)、[Python](https://docs.microsoft.com/python/azure/)、[PHP](http://azure.github.io/azure-storage-php/) 和 [Ruby](http://azure.github.io/azure-storage-ruby)。

## <a name="blob-service-concepts"></a>Blob 服务概念

Blob 存储公开了三种资源：存储帐户、帐户中的容器，以及容器中的 blob。 以下图示显示了这些资源之间的关系。

![Blob（对象）存储体系结构的图示](./media/storage-blob-concepts-include/blob1.png)

### <a name="storage-account"></a>存储帐户

对 Azure 存储中所有数据对象的访问都是通过存储帐户进行的。 有关详细信息，请参阅 [Azure 存储帐户概述](../articles/storage/common/storage-account-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

### <a name="container"></a>容器

容器对一组 blob 进行组织，类似于文件系统中的文件夹。 所有 Blob 都驻留在容器中。 一个存储帐户可以包含无限数量的容器，一个容器可以存储无限数量的 Blob。 

  > [!NOTE]
  > 容器名称必须小写。

### <a name="blob"></a>Blob
 
Azure 存储提供三种类型的 Blob&mdash;块 Blob、追加 Blob 和[页 Blob](../articles/storage/blobs/storage-blob-pageblob-overview.md)（用于 VHD 文件）。

* 块 Blob 存储文本和二进制数据，最多约为 4.7 TB。 块 Blob 由可以分别管理的数据块构成。
* 与块 Blob 一样，追加 Blob 也由块构成，但针对追加操作进行了优化。 追加 Blob 非常适用于诸如记录来自虚拟机的数据之类的场景。
* 页 Blob 用于存储最大 8 TB 的随机访问文件。 页 Blob 存储着为 VM 提供支撑的 VHD 文件。

所有 Blob 都驻留在容器中。 容器类似于文件系统中的文件夹。 可以进一步将 Blob 组织到虚拟目录中，并导航它们，就像对待文件系统一样。 

有时候，由于数据集很大且存在网络限制，无法通过线缆向 Blob 存储上传数据。 可以使用 [Azure Data Box Disk](../articles/databox/data-box-disk-overview.md) 从 Microsoft 请求固态磁盘 (SSD)。 然后，你可以将数据复制到这些磁盘中，将磁盘寄回 Microsoft，我们就会将数据上传到 Blob 存储中。

如果需要从存储帐户导出大量数据，请参阅[使用 Microsoft Azure 导入/导出服务将数据传输到 Blob 存储中](../articles/storage/common/storage-import-export-service.md)。
  
有关命名容器和 Blob 的详细信息，请参阅[命名和引用容器、Blob 和元数据](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata)。
