---
title: Blob 存储简介 - Azure 中的对象存储
description: Azure Blob 存储可存储巨量的非结构化对象数据，例如文本或二进制数据。 Azure Blob 存储高度可缩放且高度可用。 客户端可通过 PowerShell 或 Azure CLI、以编程方式通过 Azure Storage 客户端库或者使用 REST 访问 Blob 存储中的数据对象。
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 01/03/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 498dd3be75cb27263c811d9db541b2e040599156
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2019
ms.locfileid: "65788026"
---
# <a name="introduction-to-azure-blob-storage"></a>Azure Blob 存储简介

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

## <a name="blob-storage-resources"></a>Blob 存储资源

Blob 存储提供了三种类型的资源：

- 存储帐户。 
- 存储帐户中的容器
- 容器中的 Blob 

以下图示显示了这些资源之间的关系。

![Blob 存储体系结构的图示](./media/storage-blob-introduction/blob1.png)

### <a name="storage-accounts"></a>存储帐户

存储帐户在 Azure 中为数据提供唯一的命名空间。 在 Azure 存储中存储的每个对象都具有一个包含唯一帐户名称的地址。 帐户名称与 Azure 存储 blob 终结点的组合构成了存储帐户中对象的基址。

例如，如果存储帐户名为 mystorageaccount，则 Blob 存储的默认终结点为：

```
http://mystorageaccount.blob.core.windows.net 
```

若要创建存储帐户，请参阅[创建存储帐户](../common/storage-quickstart-create-account.md)。 若要了解存储帐户的详细信息，请参阅 [Azure 存储帐户概述](../common/storage-account-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

### <a name="containers"></a>容器

容器对一组 blob 进行组织，类似于文件系统中的目录。 一个存储帐户可以包含无限数量的容器，一个容器可以存储无限数量的 Blob。 

  > [!NOTE]
  > 容器名称必须小写。 有关命名容器的详细信息，请参阅[命名和引用容器、Blob 和元数据](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata)。

### <a name="blobs"></a>Blob
 
Azure 存储支持三种类型的 Blob：

* 块 Blob 存储文本和二进制数据，最多约为 4.7 TB。 块 Blob 由可以分别管理的数据块构成。
* 与块 Blob 一样，追加 Blob 也由块构成，但针对追加操作进行了优化。 追加 Blob 非常适用于诸如记录来自虚拟机的数据之类的场景。
* 页 Blob 用于存储最大 8 TB 的随机访问文件。 页 Blob 存储虚拟硬盘 (VHD) 文件作为 Azure 虚拟机的磁盘。 有关页 Blob 的更多信息，请参阅 [Azure 页 Blob 概述](storage-blob-pageblob-overview.md)

有关不同类型 Blob 的详细信息，请参阅 [Understanding Block Blobs, Append Blobs, and Page Blobs](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)（了解块 Blob、追加 Blob 和页 Blob）。

## <a name="move-data-to-blob-storage"></a>将数据移至 Blob 存储

存在许多用于将现有数据迁移到 Blob 存储的解决方案：

- AzCopy 是一种易于使用的适用于 Windows 和 Linux 的命令行工具，可以跨容器或跨存储帐户将数据复制到 Blob 存储，以及从 Blob 存储复制数据。 有关 AzCopy 的详细信息，请参阅[使用 AzCopy v10（预览版）传输数据](../common/storage-use-azcopy-v10.md)。 
- Azure 存储数据移动库是一个 .NET 库，用于在 Azure 存储服务之间移动数据。 AzCopy 实用程序是使用数据移动库生成的。 有关详细信息，请参阅数据移动库的[参考文档](/dotnet/api/microsoft.azure.storage.datamovement)。 
- Azure 数据工厂支持使用 Azure 资源身份验证的帐户密钥、共享访问签名、服务主体或托管标识向/从 Blob 存储复制数据。 有关详细信息，请参阅[使用 Azure 数据工厂向/从 Azure Blob 存储复制数据](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。 
- Blobfuse 是 Azure Blob 存储的虚拟文件系统驱动程序。 可以使用 Blobfuse 通过 Linux 文件系统访问存储帐户中的现有块 Blob 数据。 有关详细信息，请参阅[如何使用 Blobfuse 将 Blob 存储装载为文件系统](storage-how-to-mount-container-linux.md)。
- Azure Data Box Disk 是一项服务，如果大型数据集或网络限制导致通过网络上传数据不可行，此服务可将本地数据传输到 Blob 存储。 可以使用 [Azure Data Box Disk](../../databox/data-box-disk-overview.md) 从 Microsoft 请求固态磁盘 (SSD)。 然后，你可以将数据复制到这些磁盘中，将磁盘寄回 Microsoft，我们就会将数据上传到 Blob 存储中。
- Azure 导入/导出服务提供了一种方法，可将大量数据从存储帐户导出到你提供的硬盘，然后 Microsoft 会将数据寄回给你。 有关详细信息，请参阅[使用 Microsoft Azure 导入/导出服务将数据传输到 Blob 存储中](../common/storage-import-export-service.md)。

## <a name="next-steps"></a>后续步骤

* [创建存储帐户](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Azure 存储可伸缩性和性能目标](../common/storage-scalability-targets.md)
