---
title: 脱机备份概述
description: 了解脱机备份的组件。 它们包括基于 Azure Data Box 的脱机备份和基于 Azure 导入/导出服务的脱机备份。
ms.topic: conceptual
ms.date: 1/28/2020
ms.custom: references_regions
ms.openlocfilehash: c5e0f4e722e2dd15b7277a484af2a101844344e6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86503619"
---
# <a name="overview-of-offline-backup"></a>脱机备份概述

本文将简要介绍脱机备份。

与只传输增量更改的后续备份相比，到 Azure 的初始完整备份通常会在线传输大量数据，并且需要更多的网络带宽。 某些地区的远程办公室或数据中心不一定总有足够的网络带宽。 因此，这些初始备份需要几天时间。 在此期间，备份将继续使用为本地数据中心中运行的应用程序预配的同一网络。

Azure 备份支持脱机备份，即在不使用网络带宽的情况下脱机传输初始备份数据。 它提供了一种可将备份数据复制到物理存储设备的机制。 然后，这些设备将发送到附近的 Azure 数据中心并上传到恢复服务保管库。 此过程可确保在不使用任何网络带宽的情况下可靠地传输备份数据。

## <a name="offline-backup-options"></a>脱机备份选项

根据存储设备的所有权，脱机备份有两种模式：

- 基于 Azure Data Box 的脱机备份（预览版）
- 基于 Azure 导入/导出服务的脱机备份

## <a name="offline-backup-based-on-azure-data-box-preview"></a>基于 Azure Data Box 的脱机备份（预览版）

Microsoft Azure 恢复服务 (MARS) 代理当前支持此模式（处于预览阶段）。 此选项利用 [Azure Data Box](https://azure.microsoft.com/services/databox/) 将安全且防篡改的 Microsoft 专有传输设备（带 USB 连接器）连接到数据中心或远程办公室。 备份数据直接写入这些设备。 此选项减少了购买自己的 Azure 兼容磁盘和连接器或将临时存储预配为临时位置所需的工作量。 Microsoft 还处理端到端的传输物流，你可以通过 Azure 门户跟踪。

这里显示了介绍使用此选项移动备份数据的体系结构。

![Azure 备份 Data Box 体系结构](./media/offline-backup-overview/azure-backup-databox-architecture.png)

下面是这些体系结构的摘要：

1. Azure 备份直接将备份数据复制到这些预配的设备。
2. 然后，可以将这些设备送回 Azure 数据中心。
3. Azure Data Box 将数据复制到客户拥有的存储帐户上。
4. Azure 备份自动将备份数据从存储帐户复制到指定的恢复服务保管库。 已计划增量联机备份。

若要使用基于 Azure Data Box 的脱机备份，请参阅[使用 Azure Data Box 的脱机备份](offline-backup-azure-data-box.md)。

## <a name="offline-backup-based-on-the-azure-importexport-service"></a>基于 Azure 导入/导出服务的脱机备份

Microsoft Azure 备份服务器 (MABS)、System Center Data Protection Manager (DPM) DPM-A 和 MARS 代理支持此选项。 它使用 [Azure 导入/导出服务](../storage/common/storage-import-export-service.md)。 你可以使用自己的 Azure 兼容磁盘和连接器将初始备份数据传输到 Azure。 这种方法要求你预配称为暂存位置的临时存储，并使用预先生成的实用程序将备份数据格式化并复制到客户拥有的磁盘上。

这里显示了介绍使用此选项移动备份数据的体系结构。

![Azure 备份导入/导出服务体系结构](./media/offline-backup-overview/azure-backup-import-export.png)

下面是这些体系结构的摘要：

1. 不是通过网络发送备份数据，Azure 备份将备份数据写入暂存位置。
2. 暂存位置中的数据通过使用自定义实用程序写入一个或多个 SATA 磁盘。
3. 在准备过程中，实用工具将创建 Azure 导入作业。 将 SATA 驱动器传送到最近的 Azure 数据中心，并引用导入作业来连接活动。
4. 在 Azure 数据中心，磁盘上的数据将复制到 Azure 存储帐户。
5. Azure 备份将备份数据从存储帐户复制到恢复服务保管库。 已计划增量备份。

若要将基于 Azure 导入/导出服务的脱机备份与 MARS 代理一起使用，请参阅 [Azure 备份中的脱机备份工作流](./backup-azure-backup-import-export.md)。

若要将其与 MAB 或 DPM-A 一起使用，请参阅 [DPM 和 Azure 备份服务器的脱机备份工作流](./backup-azure-backup-server-import-export.md)。

## <a name="offline-backup-support-summary"></a>脱机备份支持摘要

下表比较了两个可用选项，你可以根据方案做出适当的选择。

| **注意事项**                                            | **基于 Azure Data Box 的脱机备份**                     | **基于 Azure 导入/导出服务的脱机备份**                |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Azure 备份部署模型                              | MARS 代理（预览）                                              | MARS 代理、MABS、DPM-A                                           |
| 每个服务器 (MARS) 或每个保护组（MABS、DPM-A）的最大备份数据 | [Azure Data Box 磁盘](../databox/data-box-disk-overview.md) - 7.2 TB <br> [Azure Data Box](../databox/data-box-overview.md) - 80 TB       | 80 TB（最多 10 个磁盘，每个磁盘 8 TB）                          |
| 安全性（数据、设备和服务）                           | [数据](../databox/data-box-security.md#data-box-data-protection) - 已加密 AES 256 位 <br> [设备](../databox/data-box-security.md#data-box-device-protection) - 用于复制数据的外壳坚固且基于凭据的专有接口 <br> [服务](../databox/data-box-security.md#data-box-service-protection) - 受 Azure 安全功能保护 | 数据 - BitLocker 已加密                                 |
| 临时暂存位置预配                     | 不是必需                                                | 大于或等于估计的备份数据大小        |
| 支持的区域                                           | [Azure Data Box 磁盘区域](../databox/data-box-disk-overview.md#region-availability) <br> [Azure Data Box 区域](../databox/data-box-disk-overview.md#region-availability) | [Azure 导入/导出服务区域](../storage/common/storage-import-export-service.md#region-availability) |
| 跨国家/地区传送                                     | 不支持  <br>    源地址和目标 Azure 数据中心必须位于同一国家/地区* | 支持                                                    |
| 传输物流（发送、传输、分拣）           | 完全由 Microsoft 托管                                     | 由客户管理                                            |
| 定价                                                      | [Azure Data Box 定价](https://azure.microsoft.com/pricing/details/databox/) <br> [Azure Data Box 磁盘定价](https://azure.microsoft.com/pricing/details/databox/disk/) | [Azure 导入/导出服务定价](https://azure.microsoft.com/pricing/details/storage-import-export/) |

*如果你的国家/地区没有 Azure 数据中心，则需要将磁盘发送到其他国家/地区的 Azure 数据中心。

## <a name="next-steps"></a>后续步骤

- [使用 Azure Data Box 进行 Azure 备份脱机备份](offline-backup-azure-data-box.md#backup-data-size-and-supported-data-box-skus)
- [Azure 备份中的脱机备份工作流](backup-azure-backup-import-export.md)
- [DPM 和 Azure 备份服务器的脱机备份工作流](backup-azure-backup-server-import-export.md)
