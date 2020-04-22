---
title: 来自 StorSimple 8000 系列设备的数据迁移选项
description: 概述了从 StorSimple 8000 系列迁移数据的选项。
services: storsimple
author: priestlg
ms.service: storsimple
ms.topic: article
ms.date: 03/25/2020
ms.author: v-grpr
ms.openlocfilehash: 974bcc657b811a10e28b41150439e83d26a208d2
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767060"
---
# <a name="options-to-migrate-data-from-storsimple-8000-series"></a>从 StorSimple 8000 系列迁移数据的选项

> [!IMPORTANT]
> 2022 年 12 月 31 日，StorSimple 8000 系列将到达支持 （EOS） 状态结束。 我们建议 StorSimple 8000 系列客户迁移到文档中描述的备选方案之一。

StorSimple 8000 系列在 2022 年 12 月即将[结束支持](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series)。 运行 StorSimple 8000 系列的客户可以选择升级到其他 Azure 第一方混合服务。 本文介绍可用于迁移数据的 Azure 混合选项。

## <a name="migration-options"></a>迁移选项

使用 StorSimple 8000 系列的客户具有 Azure 或第三方选项。

### <a name="azure-options"></a>Azure 选项

#### <a name="migrate-to-azure-file-sync"></a>迁移到 Azure 文件同步

此全新的迁移选项使客户能够将组织的文件共享存储在 Azure 文件中。 然后可以集中这些文件共享，以使用 Azure 文件同步 (AFS) 进行本地访问。 可在 Windows Server 主机上部署 AFS。 然后，可以通过主机复制或迁移工具执行实际的数据迁移。

有关如何将数据迁移到 Azure 文件同步的详细信息，请访问[StorSimple 8100 和 8600 迁移到 Azure 文件同步](https://docs.microsoft.com/azure/storage/files/storage-files-migration-storsimple-8000)。

### <a name="third-party-options"></a>第三方选项

#### <a name="migrate-to-panzura-freedom-nas"></a>迁移到 Panzura Freedom NAS

StorSimple 5000-7000 系列和 StorSimple 8000 系列客户可以选择迁移到 Panzura 自由 NAS 以将其数据保存在 Azure 中。 Panzura 自由解决方案提供覆盖数据中心、办公室、公共云和私有云的 NAS 解决方案。 该解决方案支持 NFS、SMB 和移动客户端的本地、混合和云内数据工作流。

Panzura 支持此迁移，客户可以通过在 [Panzura 网站](https://panzura.com/migrate-storsimple-panzura/)请求迁移支持来开始。

<!-- 04/09/2020 v-grpr (priestlg) - As per request, commenting out this section because the information that will go into this section is forthcoming
#### Migrate to Cohesity

Cohesity enables you to migrate data from your current StorSimple 5000–7000 to the Cohesity Data Platform on Azure. The Cohesity Data Platform is a software-defined web-scale solution that consolidates files, backups, objects, and VMs onto a single cloud-native solution. After migration to the Data Platform, you can manage, protect, and provision data and apps from cloud to core through a single pane of glass. With Cohesity, start with as few as three nodes. 

Learn more on [migration to the Cohesity Data Platform](https://info.cohesity.com/migrate-from-storsimple-to-cohesity.html).

#### Migrate to Nasuni

Nasuni makes it easy for StorSimple 5000-7000 customers to migrate and keep their data in Azure.  Nasuni is a leading Azure-based NAS storage solution, giving customers the performance and security they expect from on-prem solutions, with cloud economics and scale.  In addition to high performance file storage, Nasuni and Azure handle backup and DR, while allowing you to share and collaborate on your data around the globe with centralized file storage management. 

Nasuni has the experience to make your migration easy – get started today: https://info.nasuni.com/nasuni-storsimple-migration

#### Migrate to Talon FAST

Talon makes it easy for StorSimple 5000-7000 customers to continue to leverage the benefits they valued so much in the StorSimple platform (small on-site footprint backed by unlimited cloud resources) with even greater function.  With the Talon FAST solution, customers can migrate and keep their data in Azure, while now having an even smaller software-only onsite footprint and adding benefits such as global file locking, global namespace, and multi-site collaboration.  Talon is a leading Azure ecosystem solution, working with global customers to migrate their on-premises file server workloads into a consolidated, Azure-based footprint without compromising user workflow or experience.  

Learn more about how to evolve to a cloud-consolidated enterprise at https://www.talonstorage.com/alliances/microsoft-storsimple.
-->

## <a name="migration---frequently-asked-questions"></a>迁移 - 常见问题解答

### <a name="q-when-do-the-storsimple-8000-series-devices-reach-end-of-service"></a>Q. StorSimple 8000 系列设备何时达到服务结束？

A. StorSimple 8000 系列在 2022 年 12 月达到[支持结束](https://support.microsoft.com/[lifecycle/search?alpha=Azure%20StorSimple%208000%20Series)。 支持的结束意味着 Microsoft 在 2022 年 12 月之后将不再能够为这些设备的硬件和软件提供支持。 我们强烈建议立即开始制定计划，以从这些设备迁移数据。

### <a name="q-what-happens-to-the-data-i-have-stored-in-azure"></a>Q. 在 Azure 中存储的数据会发生什么情况？  

A. 迁移到新服务后，可以继续使用 Azure 中的数据。

### <a name="q-what-happens-to-the-data-i-have-stored-locally-on-my-storsimple-device"></a>Q. StorSimple 设备本地存储的数据会发生什么情况？

A. 可根据迁移文档中所述，将本地设备上的数据复制到新服务。

### <a name="q-what-happens-if-i-want-to-keep-my-storsimple-8000-series-appliance"></a>Q. 如果我想保留 StorSimple 8000 系列设备，会发生什么情况？

A. 尽管服务可以继续工作，但 Microsoft 不再能够提供硬件和软件支持。 为实现业务连续性，我们强烈建议迁移。

### <a name="q-what-options-are-available-to-migrate-data-from-storsimple-8000-series-devices"></a>Q. 有哪些选项可用于从 StorSimple 8000 系列设备迁移数据？

A. 根据其方案，StorSimple 8000 系列用户具有以下迁移选项：

* **迁移到 Azure 文件同步**：若要切换到 Azure 本机格式，请使用此选项。 可以使用 Azure 文件同步来集中管理文件共享。

* **其他选项**：您可以联系 Microsoft 支持部门，讨论此处未列出的迁移选项。

### <a name="q-is-migration-to-other-storage-solutions-supported"></a>Q. 是否支持迁移到其他存储解决方案？

A. 是的。 支持迁移到使用数据主机复制的其他存储解决方案。

### <a name="q-is-migration-supported-by-microsoft"></a>Q. Microsoft 是否为迁移提供支持？

A. 从 8000 系列迁移是完全支持的操作。 事实上，Microsoft 建议在开始迁移之前联系支持人员。 迁移当前是一项辅助的操作。 如果要从 StorSimple 8000 系列设备迁移数据，[请与 StorSimple 支持 。](mailto:storsimp@microsoft.com)

### <a name="q-what-is-the-pricing-model-for-migration-to-azure-file-sync"></a>Q. 迁移到 Azure 文件同步的定价模型是什么？

A. 使用 Azure 文件同步时，可能会收取服务的订阅费用。 客户还必须支付持续的存储成本。 有关估计，请参阅[AFS 定价]( https://azure.microsoft.com/pricing/details/storage/files/)。

### <a name="q-how-long-does-it-take-to-complete-a-migration"></a>Q. 完成迁移需要多长时间？

A. 迁移数据所需的时间取决于数据量和选择的升级选项。

## <a name="next-steps"></a>后续步骤

* [将数据从 StorSimple 8000 系列迁移到 Azure 文件同步](https://docs.microsoft.com/azure/storage/files/storage-files-migration-storsimple-8000)
