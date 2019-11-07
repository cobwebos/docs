---
title: 使用 Azure 数据工厂将数据从 data lake 和数据仓库迁移到 Azure
description: 使用 Azure 数据工厂将数据从 Data Lake 和数据仓库迁移到 Azure。
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 7/30/2019
ms.openlocfilehash: 0be9cbc9c5af2e0778654ef70c5350b48f10c35d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73675757"
---
# <a name="use-azure-data-factory-to-migrate-data-from-your-data-lake-or-data-warehouse-to-azure"></a>使用 Azure 数据工厂将数据从 Data Lake 或数据仓库迁移到 Azure

如果要将 data lake 或企业数据仓库（EDW）迁移到 Microsoft Azure，请考虑使用 Azure 数据工厂。 Azure 数据工厂非常适合以下场景：

- 将大数据工作负荷从 Amazon 简单存储服务 (Amazon S3) 或本地 Hadoop 分布式文件系统 (HDFS) 迁移到 Azure
- 将 EDW 从 Oracle Exadata、Netezza、Teradata 或 Amazon Redshift 迁移到 Azure

在迁移 Data Lake 时，Azure 数据工厂可以移动千万亿字节 (PB) 量级的数据；在迁移数据仓库时，它可以移动万亿字节 (TB) 量级的数据。

## <a name="why-azure-data-factory-can-be-used-for-data-migration"></a>为何可以使用 Azure 数据工厂来迁移数据

- Azure 数据工厂可以轻松增大处理能力，能以无服务器方式移动数据，并保持较高的性能、复原能力和可伸缩性。 你只需为使用的资源付费。 另请注意以下几点： 
  - Azure 数据工厂对数据量或文件数没有限制。
  - Azure 数据工厂可以充分利用网络和存储带宽，在环境中实现最大的数据移动吞吐量。
  - Azure 数据工厂使用即用即付方法，只需为实际用于运行数据迁移到 Azure 的时间付费。  
- Azure 数据工厂可以执行一次性的历史数据加载和计划的增量加载。
- Azure 数据工厂使用 Azure 集成运行时 (IR) 在可公开访问的 Data Lake 与仓库终结点之间移动数据。 它还可以使用自承载 IR 来移动位于 Azure 虚拟网络 (VNet) 内部或防火墙后面的 Data Lake 和仓库终结点的数据。
- Azure 数据工厂具有企业级安全性：你可以使用 Windows Installer （MSI）或服务标识进行安全的服务到服务集成，或使用 Azure Key Vault 进行凭据管理。
- Azure 数据工厂提供无代码创作体验，以及丰富的内置监视仪表板。  

## <a name="online-vs-offline-data-migration"></a>联机与脱机数据迁移

Azure 数据工厂是用于通过网络（Internet、ER 或 VPN）传输数据的标准联机数据迁移工具。 而使用脱机数据迁移时，用户需要将数据传输设备的实物从其组织寄送到 Azure 数据中心。  

在联机与脱机迁移方法之间选择时，请注意三个要点：  

- 要迁移的数据大小
- 网络带宽
- 迁移时限

例如，假设你打算使用 Azure 数据工厂在两周（迁移时限）内完成数据迁移。 请注意下表中的粉红色/蓝色切割线。 在任意给定列中，最下面的粉红色单元格显示其迁移时限最接近（但不到）两周的数据大小/网络带宽对。 （蓝色单元格中的任何大小/带宽对的联机迁移时限超过两周。） 

![联机与脱机迁移](media/data-migration-guidance-overview/online-offline.png)此表可帮助你根据数据大小和可用网络带宽，确定是否可以通过联机迁移（Azure 数据工厂）来满足预期的迁移时限。 如果联机迁移时限超过两周，则需要使用脱机迁移。

> [!NOTE]
> 使用联机迁移，可以通过一个工具端到端地实现历史数据加载和增量馈送。  通过此方法，数据在整个迁移时限内可在现有存储与新存储之间保持同步。 这意味着，可以使用刷新的数据在新存储中重新生成 ETL 逻辑。


## <a name="next-steps"></a>后续步骤

- [将数据从 AWS S3 迁移到 Azure](data-migration-guidance-s3-azure-storage.md)
- [将数据从本地 Hadoop 群集迁移到 Azure](data-migration-guidance-hdfs-azure-storage.md)
- [将数据从本地 Netezza 服务器迁移到 Azure](data-migration-guidance-netezza-azure-sqldw.md)
