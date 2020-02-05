---
title: 脱机备份概述
description: 了解脱机备份的各种组件，包括基于 Azure Data Box 的脱机备份和 Azure 导入/导出的脱机备份。
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 35bc15488624f3648bdc765a36d10607b4ca2de9
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77027012"
---
# <a name="overview-of-offline-backup"></a>脱机备份概述

本文提供脱机备份的概述。

与仅传输增量更改的后续备份相比，对 Azure 进行的初始完整备份通常会联机传输大量数据，并且需要更多的网络带宽。 某些地理区域中的远程办公室或数据中心不一定有足够的网络带宽。 因此，这些初始备份需要几天时间，在这段时间内持续使用为本地数据中心内运行的应用程序预配的同一网络。

Azure 备份支持 "脱机备份"，允许脱机传输初始备份数据，而无需使用网络带宽。 它提供了一种机制，用于将备份数据复制到物理存储设备上，然后将设备发送到附近的 Azure 数据中心并上载到恢复服务保管库。 此过程可确保无需使用任何网络带宽就能可靠地传输备份数据。

## <a name="offline-backup-options"></a>脱机备份选项

脱机备份是以两种模式提供的，具体取决于存储设备的所有权。

1. 基于 Azure Data Box 的脱机备份（预览版）
2. Azure 导入/导出基于脱机备份

## <a name="azure-data-box-based-offline-backup-preview"></a>基于 Azure Data Box 的脱机备份（预览版）

在预览版中，MARS 代理目前支持此模式。 此选项利用[Azure Data Box 服务](https://azure.microsoft.com/services/databox/)向数据中心或远程办公室发送 Microsoft 专有、安全和防篡改的传输设备，并将其直接写入到这些设备。 **此选项保存购买你自己的 Azure 兼容磁盘和连接器，或将临时存储设置为暂存位置所需的工作量。** 此外，Microsoft 还处理端到端传输物流，你可以通过该 Azure 门户进行跟踪。 下面显示了使用此选项描述备份数据移动的体系结构。

![Azure 备份 Data Box 体系结构](./media/offline-backup-overview/azure-backup-databox-architecture.png)

下面是体系结构的摘要：

1. Azure 备份会直接将备份数据复制到这些预配置的设备。
2. 然后，可以将这些设备送回 Azure 数据中心。
3. Azure Data Box 服务将数据复制到客户拥有的存储帐户。
4. Azure 备份会自动将备份数据从存储帐户复制到指定的恢复服务保管库，并计划增量联机备份。

若要使用基于 Azure Data Box 的脱机备份，请参阅[此文](offline-backup-azure-data-box.md)。

## <a name="azure-importexport-based-offline-backup"></a>Azure 导入/导出基于脱机备份

Azure 备份服务器（MABS）/DPM-A/MARS 代理支持此选项。 它利用[Azure 导入/导出服务](https://docs.microsoft.com/azure/storage/common/storage-import-export-service)，通过它可以将初始备份数据传输到 azure，方法是使用自己的与 azure 兼容的磁盘和连接器。 此方法要求预配临时存储，该存储称为**暂存位置**，并使用预建的实用程序来设置备份数据的格式并将其复制到客户拥有的磁盘上。 描述如何使用此选项移动备份数据的体系结构如下所示：

![Azure 备份导入/导出体系结构](./media/offline-backup-overview/azure-backup-import-export.png)

下面是体系结构的摘要：

1. Azure 备份将备份数据写入暂存位置，而不是通过网络发送备份数据。
2. 使用自定义实用工具将暂存位置中的数据写入一个或多个 SATA 磁盘。
3. 作为准备工作的一部分，该实用工具将创建 Azure 导入作业。 将 SATA 驱动器寄送到最近的 Azure 数据中心，并引用导入作业来连接活动。
4. 在 Azure 数据中心，磁盘上的数据将复制到 Azure 存储帐户。
5. Azure 备份将备份数据从存储帐户复制到恢复服务保管库，并计划增量备份。

若要将 Azure 导入/导出脱机备份用于 MARS 代理，请参阅[此文](https://docs.microsoft.com/azure/backup/backup-azure-backup-import-export)。

若要将与 MABS/DPM 一起使用，请参阅[此文](https://docs.microsoft.com/azure/backup/backup-azure-backup-server-import-export-)。

## <a name="offline-backup-support-summary"></a>脱机备份支持摘要

下表比较了两个可用的选项，因此你可以根据方案做出适当的选择。

| **注意事项**                                            | **基于 Azure Data Box 的脱机备份**                     | **Azure 导入/导出基于脱机备份**                |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Azure 备份部署模型                              | MARS 代理（预览）                                              | MARS 代理，Azure 备份服务器（MABS），DPM-A                                           |
| 每个服务器（MARS）或每个保护组的最大备份数据（MABS） | [Azure Data Box Disk](https://docs.microsoft.com/azure/databox/data-box-disk-overview) -7.2 TB <br> [Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-overview) -80 TB       | 80 TB （每个 8 TB 最多10个磁盘）                          |
| 安全（data，device & service）                           | [数据](https://docs.microsoft.com/azure/databox/data-box-security#data-box-data-protection)-AES-256 位已加密 <br> 用于复制数据的基于凭据的专用接口的[设备](https://docs.microsoft.com/azure/databox/data-box-security#data-box-device-protection)耐用大小写 <br> 由 Azure 安全功能保护的[服务](https://docs.microsoft.com/azure/databox/data-box-security#data-box-service-protection) | 数据-BitLocker 已加密                                 |
| 临时暂存位置预配                     | 不需要                                                | 大于或等于估计的备份数据大小        |
| 支持的区域                                           | [Azure Data Box Disk 区域](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) <br> [Azure Data Box 区域](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) | [Azure 导入/导出区域](https://docs.microsoft.com/azure/storage/common/storage-import-export-service#region-availability) |
| 跨国家/地区发货 *                                     | **不支持**  <br>    *源地址 & 目标 Azure 数据中心必须位于同一国家/地区* | 受支持                                                    |
| 传输逻辑（传递、传输、选择）           | 完全托管的 Microsoft                                     | 客户托管                                            |
| 价格                                                      | [Azure Data Box 定价](https://azure.microsoft.com/pricing/details/databox/) <br> [Azure Data Box Disk 定价](https://azure.microsoft.com/pricing/details/databox/disk/) | [Azure 导入/导出定价](https://azure.microsoft.com/pricing/details/storage-import-export/) |

* *如果你的国家/地区没有 Azure 数据中心，则需要将磁盘寄送到其他国家/地区的 Azure 数据中心。*

## <a name="next-steps"></a>后续步骤

* [MARS 代理的基于 Azure Data Box 的脱机备份](offline-backup-azure-data-box.md#backup-data-size-and-supported-data-box-skus)
* [适用于 MARS 代理的 Azure 导入/导出脱机备份](backup-azure-backup-import-export.md)  
* [Azure 导入/导出基于 MABS/DPM 的脱机备份-A](backup-azure-backup-server-import-export-.md)
