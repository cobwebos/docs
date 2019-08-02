---
title: 使用 Azure 数据工厂将数据从 data lake 和数据仓库迁移到 Azure |Microsoft Docs
description: 使用 Azure 数据工厂将数据从 data lake 和数据仓库迁移到 Azure。
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
ms.openlocfilehash: 780b9ae6e4664af86fa655c9136193bed58526d9
ms.sourcegitcommit: 6ad03fa28a0f60cb6dce6144f728c2ceb56ff6e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2019
ms.locfileid: "68708493"
---
# <a name="use-azure-data-factory-to-migrate-data-from-your-data-lake-or-data-warehouse-to-azure"></a>使用 Azure 数据工厂将数据从 data lake 或数据仓库迁移到 Azure 

如果要将 data lake 或企业数据仓库 (EDW) 迁移到 Azure, 则可以通过 Azure 数据工厂来完成数据迁移。 Data lake 迁移和数据仓库迁移与以下方案相关: 

- 大数据工作负荷从 AWS S3 本地 Hadoop 文件系统迁移到 Azure。 
- EDW 从 Oracle Exadata、Netezza、Teradata、AWS Redshift 迁移到 Azure。 

Azure 数据工厂可以移动 PBs 的数据以进行 data lake 迁移, 为数据仓库迁移移动数十 TB 的数据。 

## <a name="why-azure-data-factory-can-be-used-for-data-migration"></a>Azure 数据工厂可用于数据迁移的原因 

- Azure 数据工厂可以轻松地增加能力, 以无服务器的方式移动数据, 实现高性能、复原能力和可伸缩性, 只需为所使用的内容付费。  
  - Azure 数据工厂对数据量和文件数量没有限制。
  - Azure 数据工厂可以 100%, 利用你的网络和存储带宽在你的环境中实现最高的数据移动吞吐量。   
  - Azure 数据工厂采用 "即用即付" 策略, 因此只需支付使用 Azure 数据工厂执行数据迁移到 Azure 的时间。  
- Azure 数据工厂能够执行一次性的历史负载和计划的增量加载。 
- Azure 数据工厂使用 Azure IR 在可公开访问的 data lake/仓库终结点之间移动数据, 或者使用自承载 IR 在 VNet 或防火墙后面的数据中移动数据。 
- Azure 数据工厂具有企业级安全性: 使用 MSI 或服务标识进行安全的服务到服务集成, 或者利用 Azure Key Vault 进行凭据管理。 
- Azure 数据工厂提供无代码创作体验和丰富的内置监视仪表板。  

## <a name="online-vs-offline-data-migration"></a>联机和脱机数据迁移

Azure 数据工厂是一种典型的在线数据迁移工具, 用于通过网络 (Internet、ER 或 VPN) 传输数据, 在这种情况下, 离线数据迁移可让用户以物理方式将数据传输设备从你的组织交付到 Azure 数据中心。  

选择联机和脱机迁移方法时, 有三个关键注意事项:  

- 要迁移的数据的大小。 
- 网络带宽。 
- 迁移窗口。   

如果要在两周内完成数据迁移 ("迁移" 窗口), 可以在下图中看到一条切削线, 以显示使用具有不同数据大小和网络带宽的在线迁移工具 (Azure 数据工厂) 的情况。   

![联机与脱机](media/data-migration-guidance-overview/online-offline.png)

> [!NOTE]
> 在线迁移方法的好处是, 你可以通过一个工具来实现历史数据加载和增量源端到端。  这样一来, 在整个迁移期间, 数据可以保持在现有和新存储之间同步, 以便您可以在新存储中使用刷新后的数据重新生成 ETL 逻辑。 


## <a name="next-steps"></a>后续步骤

- [使用 Azure 数据工厂复制多个容器中的文件](solution-template-copy-files-multiple-containers.md)