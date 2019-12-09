---
title: 将数据从 data lake 和数据仓库迁移到 Azure
description: 使用 Azure 数据工厂将数据从 data lake 和数据仓库迁移到 Azure。
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 7/30/2019
ms.openlocfilehash: aaf1593cc049e8b23f8ebe36fea022b3029ccd04
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930805"
---
# <a name="use-azure-data-factory-to-migrate-data-from-your-data-lake-or-data-warehouse-to-azure"></a>使用 Azure 数据工厂将数据从 data lake 或数据仓库迁移到 Azure

如果要将 data lake 或企业数据仓库（EDW）迁移到 Microsoft Azure，请考虑使用 Azure 数据工厂。 Azure 数据工厂非常适合以下方案：

- 从 Amazon 简单存储服务（Amazon S3）或本地 Hadoop 分布式文件系统（HDFS）迁移到 Azure 的大数据工作负荷
- EDW 从 Oracle Exadata、Netezza、Teradata 或 Amazon Redshift 迁移到 Azure

Azure 数据工厂可移动用于 data lake 迁移的 pb 数据和数万 tb （TB）的数据以进行数据仓库迁移。

## <a name="why-azure-data-factory-can-be-used-for-data-migration"></a>Azure 数据工厂可用于数据迁移的原因

- Azure 数据工厂可以轻松扩展以无服务器方式移动数据的处理能力，从而提高性能、复原能力和可伸缩性。 你只需为你使用的内容付费。 另请注意以下几点： 
  - Azure 数据工厂对数据量或文件数量没有限制。
  - Azure 数据工厂可以在你的环境中充分利用你的网络和存储带宽来实现最大的数据移动吞吐量。
  - Azure 数据工厂使用即用即付方法，只需为实际用于运行数据迁移到 Azure 的时间付费。  
- Azure 数据工厂可以同时执行一次性的历史负载和计划的增量加载。
- Azure 数据工厂使用 Azure 集成运行时（IR）在可公开访问的 data lake 和仓库终结点之间移动数据。 它还可以使用自承载 IR 在 Azure 虚拟网络（VNet）或防火墙后面移动 data lake 和仓库终结点的数据。
- Azure 数据工厂具有企业级安全性：你可以使用 Windows Installer （MSI）或服务标识进行安全的服务到服务集成，或使用 Azure Key Vault 进行凭据管理。
- Azure 数据工厂提供无代码创作体验和丰富的内置监视仪表板。  

## <a name="online-vs-offline-data-migration"></a>联机和脱机数据迁移

Azure 数据工厂是一种标准的在线数据迁移工具，用于通过网络（internet、ER 或 VPN）传输数据。 虽然脱机数据迁移，但用户会将数据传输设备从其组织物理交付到 Azure 数据中心。  

在联机和脱机迁移方法之间做出选择时，有三个关键注意事项：  

- 要迁移的数据的大小
- 网络带宽
- 迁移窗口

例如，假设你计划使用 Azure 数据工厂在两周（*迁移时段*）内完成数据迁移。 请注意下表中的粉红色/蓝色切削线。 任何给定列的最小粉红色单元显示的是数据大小/网络带宽配对，其迁移窗口最接近但不到两周。 （蓝色单元格中的任何大小/带宽配对都有超过两周的在线迁移窗口。） 

![联机与脱机](media/data-migration-guidance-overview/online-offline.png) 此表可帮助你根据你的数据大小和可用网络带宽，确定你是否可以通过联机迁移（Azure 数据工厂）来满足你的预期迁移窗口。 如果联机迁移窗口超过两周，则需要使用脱机迁移。

> [!NOTE]
> 通过使用联机迁移，你可以通过单个工具实现端到端的历史数据加载和增量源。  通过此方法，你的数据可以在整个迁移时段内保持在现有存储和新存储之间同步。 这意味着，你可以在新存储中重新生成包含刷新数据的 ETL 逻辑。


## <a name="next-steps"></a>后续步骤

- [将数据从 AWS S3 迁移到 Azure](data-migration-guidance-s3-azure-storage.md)
- [将数据从本地 hadoop 群集迁移到 Azure](data-migration-guidance-hdfs-azure-storage.md)
- [将数据从本地 Netezza 服务器迁移到 Azure](data-migration-guidance-netezza-azure-sqldw.md)
