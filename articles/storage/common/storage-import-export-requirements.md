---
title: Azure 导入/导出服务的要求 | Microsoft Docs
description: 了解 Azure 导入/导出服务的软件和硬件要求。
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 07/19/2018
ms.author: alkohli
ms.component: common
ms.openlocfilehash: 10e8fb6ac5bcce278de3924ebd3a0d9f90392217
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2018
ms.locfileid: "39528048"
---
# <a name="azure-importexport-system-requirements"></a>Azure 导入/导出系统要求

本文介绍了 Azure 导入/导出服务的重要要求。 建议在使用导入/导出服务之前仔细查看这些信息，并且在操作过程中根据需要重新参阅。

## <a name="supported-operating-systems"></a>支持的操作系统

为了使用 WAImportExport 工具准备硬盘驱动器，以下**支持 BitLocker 驱动器加密的 64 位操作系统**受支持。


|平台 |版本 |
|---------|---------|
|Windows     | Windows 7 企业版、Windows 7 旗舰版 <br> Windows 8 专业版、Windows 8 企业版、Windows 8.1 专业版、Windows 8.1 企业版 <br> Windows 10        |
|Windows Server     |Windows Server 2008 R2 <br> Windows Server 2012、Windows Server 2012 R2         |


## <a name="supported-storage-accounts"></a>支持的存储帐户

Azure 导入/导出服务支持以下 [Azure 存储帐户](storage-account-options.md)。
- 常规用途 v1 存储帐户（经典部署或 Azure 资源管理器部署）
- Blob 存储帐户
- 常规用途 v2 存储帐户

每个作业只能用于将数据传输到一个存储帐户或者从一个存储帐户传输数据。 换言之，一个导入/导出作业不能跨多个存储帐户。 有关创建新存储帐户的信息，请参阅[如何创建存储帐户](storage-create-storage-account.md#create-a-storage-account)。

> [!IMPORTANT] 
> Azure 导入导出服务不支持已启用[虚拟网络服务终结点](../../virtual-network/virtual-network-service-endpoints-overview.md)功能的存储帐户。 

## <a name="supported-storage-types"></a>支持的存储类型

下面列出的存储类型都受 Azure 导入/导出服务支持。


|作业  |存储服务 |支持  |不支持  |
|---------|---------|---------|---------|
|导入     |  Azure Blob 存储 <br><br> Azure 文件存储       | 支持块 Blob 和页 Blob <br><br> 支持文件          |
|导出     |   Azure Blob 存储       | 支持块 Blob、页 Blob 和追加 Blob         | 不支持 Azure 文件


## <a name="supported-hardware"></a>支持的硬件 

对于 Azure 导入/导出服务，需要使用支持的磁盘复制数据。

### <a name="supported-disks"></a>支持的磁盘

下面列出的存储类型均受支持，可与 Azure 导入/导出服务配合使用。


|磁盘类型  |大小  |支持 |不支持  |
|---------|---------|---------|---------|
|SSD    |   2.5"      |         |         |
|HDD     |  2.5"<br>3.5"       |SATA II、SATA III         |带有内置 USB 适配器的外部 HDD <br> 外部 HDD 外壳内的磁盘         |


单个导入/导出作业可以有：
- 最多 10 个 HDD/SSD。
- 任意大小的 HDD/SSD 的组合。

大量驱动器可以分布在多个作业上，并且可创建的作业数没有限制。 对于导入作业，仅处理驱动器上的第一个数据卷。 该数据卷必须使用 NTFS 进行格式化。

当使用 WAImportExport 工具准备硬盘驱动器并复制数据时，可以使用外部 USB 适配器。 大多数现成的 USB 3.0 或更高版本的适配器应可以工作。 


## <a name="next-steps"></a>后续步骤

* [设置 WAImportExport 工具](storage-import-export-tool-how-to.md)
* [使用 AzCopy 命令行实用程序传输数据](storage-use-azcopy.md)
* [Azure 导入/导出 REST API 示例](https://azure.microsoft.com/documentation/samples/storage-dotnet-import-export-job-management/)

