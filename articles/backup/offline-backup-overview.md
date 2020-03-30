---
title: 脱机备份概述
description: 了解脱机备份的组件。 它们包括基于 Azure 数据框的脱机备份和基于 Azure 导入/导出服务的脱机备份。
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 4dae68c46e0072a726bba13139e405b44be0f008
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78196142"
---
# <a name="overview-of-offline-backup"></a>脱机备份概述

本文概述了脱机备份。

与仅传输增量更改的后续备份相比，对 Azure 的初始完整备份通常在线传输大量数据，并需要更多的网络带宽。 某些地区的远程办公室或数据中心并不总是有足够的网络带宽。 因此，这些初始备份需要几天时间。 在此期间，备份持续使用为在本地数据中心中运行的应用程序预配的相同网络。

Azure 备份支持脱机备份，该备份脱机传输初始备份数据，而无需使用网络带宽。 它提供了将备份数据复制到物理存储设备的机制。 然后，这些设备将发送到附近的 Azure 数据中心，并上载到恢复服务保管库。 此过程可确保在不使用任何网络带宽的情况下可靠传输备份数据。

## <a name="offline-backup-options"></a>脱机备份选项

根据存储设备的所有权，以两种模式提供脱机备份：

- 基于 Azure 数据框的脱机备份（预览）
- 基于 Azure 导入/导出服务的脱机备份

## <a name="offline-backup-based-on-azure-data-box-preview"></a>基于 Azure 数据框的脱机备份（预览）

当前 Microsoft Azure 恢复服务 （MARS） 代理在预览版中支持此模式。 此选项利用 Azure[数据盒](https://azure.microsoft.com/services/databox/)将 Microsoft 专有、安全和防篡改传输设备与 USB 连接器运送到数据中心或远程办公室。 备份数据直接写入这些设备。 此选项可节省采购自己的 Azure 兼容磁盘和连接器或将临时存储预配为暂存位置所需的工作量。 Microsoft 还处理端到端传输物流，您可以通过 Azure 门户跟踪该物流。 

此处显示了一种使用此选项描述备份数据移动的体系结构。

![Azure 备份数据框体系结构](./media/offline-backup-overview/azure-backup-databox-architecture.png)

以下是体系结构的摘要：

1. Azure 备份直接将备份数据复制到这些预配置的设备。
2. 然后，可以将这些设备运回 Azure 数据中心。
3. Azure 数据盒将数据复制到客户拥有的存储帐户。
4. Azure 备份会自动将备份数据从存储帐户复制到指定的恢复服务保管库。 计划增量联机备份。

要使用基于 Azure 数据框的脱机备份，请参阅[使用 Azure 数据框脱机备份](offline-backup-azure-data-box.md)。

## <a name="offline-backup-based-on-the-azure-importexport-service"></a>基于 Azure 导入/导出服务的脱机备份

Microsoft Azure 备份服务器 （MABS）、系统中心数据保护管理器 （DPM） DPM-A 和 MARS 代理支持此选项。 它使用[Azure 导入/导出服务](https://docs.microsoft.com/azure/storage/common/storage-import-export-service)。 可以使用自己的与 Azure 兼容的磁盘和连接器将初始备份数据传输到 Azure。 此方法要求您预配临时存储（称为暂存位置）并使用预构建的实用程序格式化备份数据并将其复制到客户拥有的磁盘上。 

此处显示了一种使用此选项描述备份数据移动的体系结构。

![Azure 备份导入/导出服务体系结构](./media/offline-backup-overview/azure-backup-import-export.png)

以下是体系结构的摘要：

1. Azure 备份不是通过网络发送备份数据，而是将备份数据写入暂存位置。
2. 过渡位置中的数据通过使用自定义实用程序写入一个或多个 SATA 磁盘。
3. 作为准备工作的一部分，实用程序将创建 Azure 导入作业。 SATA 驱动器将发送到最近的 Azure 数据中心，并引用导入作业来连接活动。
4. 在 Azure 数据中心，磁盘上的数据将复制到 Azure 存储帐户。
5. Azure 备份将备份数据从存储帐户复制到恢复服务保管库。 计划增量备份。

要将基于 Azure 导入/导出服务与 MARS 代理的脱机备份使用，请参阅[Azure 备份 中的脱机备份工作流](https://docs.microsoft.com/azure/backup/backup-azure-backup-import-export)。

要与 MABS 或 DPM-A 一起使用，请参阅[DPM 和 Azure 备份服务器的脱机备份工作流](https://docs.microsoft.com/azure/backup/backup-azure-backup-server-import-export-)。

## <a name="offline-backup-support-summary"></a>脱机备份支持摘要

下表比较了两个可用选项，以便您可以根据方案做出适当的选择。

| **注意事项**                                            | **基于 Azure 数据框的脱机备份**                     | **基于 Azure 导入/导出服务的脱机备份**                |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Azure 备份部署模型                              | MARS 代理（预览版）                                              | MARS 代理，MABS，DPM-A                                           |
| 每个服务器 （MARS） 或每个保护组的最大备份数据（MABS、DPM-A） | [Azure 数据盒磁盘](https://docs.microsoft.com/azure/databox/data-box-disk-overview)- 7.2 TB <br> [Azure 数据框](https://docs.microsoft.com/azure/databox/data-box-overview)- 80 TB       | 80 TB（每个磁盘最多 10 个，每个磁盘 8 TB）                          |
| 安全性（数据、设备和服务）                           | [数据](https://docs.microsoft.com/azure/databox/data-box-security#data-box-data-protection)- AES 256 位加密 <br> [设备](https://docs.microsoft.com/azure/databox/data-box-security#data-box-device-protection)- 坚固的外壳、专有的、基于凭据的接口，用于复制数据 <br> [服务](https://docs.microsoft.com/azure/databox/data-box-security#data-box-service-protection)- 受 Azure 安全功能保护 | 数据 - 已加密的位锁                                 |
| 临时暂存位置预配                     | 不是必需                                                | 大于或等于估计的备份数据大小        |
| 支持的区域                                           | [Azure 数据框磁盘区域](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) <br> [Azure 数据框区域](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) | [Azure 导入/导出服务区域](https://docs.microsoft.com/azure/storage/common/storage-import-export-service#region-availability) |
| 跨国运输                                     | 不支持  <br>    源地址和目标 Azure 数据中心必须位于同一国家/地区* | 支持                                                    |
| 转运物流（送货、运输、取件）           | 完全微软管理                                     | 由客户管理                                            |
| 定价                                                      | [Azure 数据框定价](https://azure.microsoft.com/pricing/details/databox/) <br> [Azure 数据盒磁盘定价](https://azure.microsoft.com/pricing/details/databox/disk/) | [Azure 导入/导出服务定价](https://azure.microsoft.com/pricing/details/storage-import-export/) |

•如果您的国家/地区没有 Azure 数据中心，则需要将磁盘运送到另一个国家/地区的 Azure 数据中心。

## <a name="next-steps"></a>后续步骤

* [使用 Azure 数据框进行 Azure 备份脱机备份](offline-backup-azure-data-box.md#backup-data-size-and-supported-data-box-skus)
* [Azure 备份中的脱机备份工作流](backup-azure-backup-import-export.md) 
* [DPM 和 Azure 备份服务器的脱机备份工作流](backup-azure-backup-server-import-export-.md)
