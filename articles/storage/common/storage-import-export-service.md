---
title: 使用 Azure 导入/导出将数据传入/传出 Azure 存储 | Microsoft Docs
description: 了解如何在 Azure 门户中创建导入和导出作业，以便将数据传入/传出到 Azure 存储。
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/06/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 120dbe9c4b79755105ba634c68606edfbfdc4ad2
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872437"
---
# <a name="what-is-azure-importexport-service"></a>什么是 Azure 导入/导出服务？

使用 Azure 导入/导出服务，可将磁盘驱动器寄送到 Azure 数据中心，从而安全地将大量数据导出到 Azure Blob 存储和 Azure 文件。 此外，还可以使用此服务将数据从 Azure Blob 存储传输到磁盘驱动器，然后再寄送到本地站点。 可将单个或多个磁盘驱动器中的数据导入到 Azure Blob 存储或 Azure 文件。

提供你自己的磁盘驱动器，然后使用 Azure 导入/导出服务传输数据。 也可以使用由 Microsoft 提供的磁盘驱动器。

如果要使用由 Microsoft 提供的磁盘驱动器来传输数据，可以使用 [Azure Data Box 磁盘](../../databox/data-box-disk-overview.md)将数据导入 Azure。 Microsoft 通过区域运营商向你的数据中心发运每个订单最多 5 个加密固态磁盘驱动器 (SSD)，总容量为 40 TB。 你可以快速配置磁盘驱动器，通过 USB 3.0 连接将数据复制到磁盘驱动器，然后将磁盘驱动器发运回 Azure。 有关详细信息，请转到 [Azure Data Box 磁盘概述](../../databox/data-box-disk-overview.md)。

## <a name="azure-importexport-use-cases"></a>Azure 导入/导出用例

如果通过网络上传或下载数据速度太慢，或者获取额外的网络带宽因成本过高而受到限制，则可考虑使用 Azure 导入/导出服务。 该服务用于以下方案：

* **将数据迁移到云**：将大量数据快速且经济高效地转移到 Azure。
* **内容分发**：将数据快速发送到客户站点。
* **备份**：备份本地数据以将其存储在 Azure 存储中。
* **数据恢复**：恢复存储在存储中的大量数据，并将其传送到本地位置。

## <a name="importexport-components"></a>“导入/导出”组件

“导入/导出”服务使用以下组件：

* **导入/导出服务**：该服务在 Azure 门户中提供，可帮助用户创建和跟踪数据导入（上传）和导出（下载）作业。  

* WAImportExport 工具****：这是命令行工具，可执行以下任务：
  * 准备要寄送的磁盘驱动器，以便进行导入。
  * 在将数据导入到驱动器的过程中提供辅助。
  * 用 AES 256 位 BitLocker 加密驱动器上的数据。 你可以使用外部密钥保护程序保护你的 BitLocker 密钥。
  * 生成导入创建过程中使用的驱动器日志文件。
  * 帮助确定导出作业所需的驱动器数。

> [!NOTE]
> WAImportExport 工具有两个版本：版本 1 和 2。 建议：
>
> * 使用版本 1 将数据导入/导出到 Azure Blob 存储。
> * 使用版本 2 将数据导入到 Azure 文件。
>
> WAImportExport 工具仅兼容 64 位 Windows 操作系统。 有关支持的特定 OS 版本，请转到 [Azure 导入/导出要求](storage-import-export-requirements.md#supported-operating-systems)。

* **磁盘驱动器**：可以将固态驱动器 (SSD) 或硬盘驱动器 (HDD) 发送到 Azure 数据中心。 创建导入作业时，需要发送包含数据的磁盘驱动器。 创建导出作业时，需要将空驱动器发送到 Azure 数据中心。 有关特定磁盘类型的信息，请转到[支持的磁盘类型](storage-import-export-requirements.md#supported-hardware)。

## <a name="how-does-importexport-work"></a>“导入/导出”的工作原理是什么？

Azure“导入/导出”服务通过创建作业，将数据传输到 Azure Blob 和 Azure 文件。 使用 Azure 门户或 Azure 资源管理器 REST API 创建作业。 每个作业都与单个存储帐户相关联。

作业可以为导入或导出作业。 导入作业可将数据导入到 Azure Blob 或 Azure 文件，导出作业可从 Azure Blob 导出数据。 对于导入作业，需要寄送包含数据的驱动器。 创建导出作业时，需要将空驱动器寄送到 Azure 数据中心。 每种情况下，每个作业最多可以寄送 10 个磁盘驱动器。

### <a name="inside-an-import-job"></a>关于导入作业

概括而言，导入作业包括以下步骤：

1. 确定要导入的数据、所需的驱动器数以及 Azure 存储中数据的目标 blob 位置。
2. 使用 WAImportExport 工具将数据复制到磁盘驱动器。 使用 BitLocker 加密磁盘驱动器。
3. 在 Azure 门户中目标存储帐户下创建导入作业。 上传驱动器日志文件。
4. 请提供回寄地址以及快递商帐户号码，以便我们将驱动器寄回给你。
5. 将磁盘驱动器寄送到在创建作业时获得的寄送地址。
6. 在导入作业详细信息中更新快递跟踪号码，并提交导入作业。
7. Azure 数据中心在收到驱动器后会对其进行处理。
8. 该中心会使用快递商帐户将驱动器寄送到在导入作业中提供的回寄地址。

> [!NOTE]
> 对于本地（在数据中心的国家/地区）装运，请共享国内运营商帐户。
>
> 对于国外（外部数据中心国家/地区）装运，请共享国际运营商帐户。

 ![图 1：导入作业流](./media/storage-import-export-service/importjob.png)

有关数据导入分步说明，请转到：

* [将数据导入到 Azure Blob](storage-import-export-data-to-blobs.md)
* [将数据导入到 Azure 文件](storage-import-export-data-to-files.md)

### <a name="inside-an-export-job"></a>关于导出作业

> [!IMPORTANT]
> 该服务仅支持导出 Azure Blob。 不支持导出 Azure 文件。

概括而言，导出作业包括以下步骤：

1. 确定要导出的数据、所需的驱动器数以及 Blob 存储中数据的源 blob 或容器路径。
2. 在 Azure 门户中源存储帐户下创建导出作业。
3. 指定要导出数据的源 blob 或容器路径。
4. 请提供回寄地址以及快递商帐户号码，以便我们将驱动器寄回给你。
5. 将磁盘驱动器寄送到在创建作业时获得的寄送地址。
6. 在导出作业详细信息中更新快递跟踪号码，并提交导出作业。
7. Azure 数据中心在收到驱动器后会对其进行处理。
8. 驱动器使用 BitLocker 加密；密钥通过 Azure 门户提供。  
9. 该中心会使用快递商帐户将驱动器寄送到在导入作业中提供的回寄地址。

> [!NOTE]
> 对于本地（在数据中心的国家/地区）装运，请共享国内运营商帐户。
>
> 对于国外（外部数据中心国家/地区）装运，请共享国际运营商帐户。
  
 ![图 2：导出作业流](./media/storage-import-export-service/exportjob.png)

有关数据导出的分步说明，请转到[从 Azure Blob 导出数据](storage-import-export-data-from-blobs.md)。

## <a name="region-availability"></a>上市区域

Azure 导入/导出服务支持将数据复制到所有 Azure 存储帐户，以及从后者进行复制。 可以将磁盘驱动器寄送到列出的其中一个位置。 如果存储帐户位于此处未指定的 Azure 位置，创建作业时会提供一个备用寄送位置。

### <a name="supported-shipping-locations"></a>支持的寄送位置

|国家/地区  |国家/地区  |国家/地区  |国家/地区  |
|---------|---------|---------|---------|
|美国东部    | 北欧        | 印度中部        |US Gov 爱荷华州         |
|美国西部     |西欧         | 印度南部        | US DoD 东部        |
|美国东部 2    | 东亚        |  印度西部        | US DoD 中部        |
|美国西部 2     | 东南亚        | 加拿大中部        | 中国东部         |
|美国中部     | 澳大利亚东部        | 加拿大东部        | 中国北部        |
|美国中北部     |  澳大利亚东南部       | 巴西南部        | 英国南部        |
|美国中南部     | 日本西部        |韩国中部         | 德国中部        |
|美国中西部     |  日本东部       | US Gov 弗吉尼亚州        | 德国东北部        |

## <a name="security-considerations"></a>安全注意事项

使用 AES 256 位 BitLocker 驱动器加密对驱动器上的数据进行加密。 此加密会在运送过程中保护数据。

对于导入作业，驱动器有两种加密方式。  

* 在运行 WAImportExport 工具准备驱动器时，使用 dataset.csv 文件指定该选项**。

* 手动对驱动器启用 BitLocker 加密。 在驱动器准备期间运行 WAImportExport 命令行工具时，在 driveset.csv 文件中指定加密密钥**。 可以使用外部密钥保护程序（也称为 Microsoft 托管密钥）或客户托管密钥来进一步保护 BitLocker 加密密钥。 有关详细信息，请参阅如何[使用客户托管密钥保护 BitLocker 密钥](storage-import-export-encryption-key-portal.md)。

对于导出作业，在将数据复制到驱动器以后，此服务会使用 BitLocker 加密驱动器，然后再将驱动器寄回给你。 加密密钥是通过 Azure 门户提供的。 需要使用 WAImporExport 工具通过密钥解锁驱动器。

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]

### <a name="pricing"></a>定价

**驱动器处理费用**

在导入或导出作业的过程中，处理每个驱动器都需要支付驱动器处理费用。 请参阅有关 [Azure 导入/导出定价](https://azure.microsoft.com/pricing/details/storage-import-export/)的详细信息。

**寄送费用**

将驱动器寄送到 Azure 时，需要向快递商支付寄送费用。 当 Microsoft 将驱动器寄回给你时，寄送费用由你在创建作业时提供的快递商帐户支付。

**事务成本**

在导入和导出数据的过程中，将应用[标准存储事务费用](https://azure.microsoft.com/pricing/details/storage/)。 从 Azure 存储导出数据时，标准传出费用也适用于存储事务费用。 有关出口成本的详细信息，请参阅[数据传输定价。](https://azure.microsoft.com/pricing/details/data-transfers/)

## <a name="next-steps"></a>后续步骤

了解如何使用“导入/导出”服务执行以下任务：

* [将数据导入到 Azure Blob](storage-import-export-data-to-blobs.md)
* [从 Azure Blob 中导出数据](storage-import-export-data-from-blobs.md)
* [将数据导入到 Azure 文件](storage-import-export-data-to-files.md)
