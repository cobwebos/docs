---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/20/2019
ms.author: tamram
ms.openlocfilehash: 5ab03b682dd0ed1dc7b198e89c86e7a74c6275cd
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "67457236"
---
| Resource | 目标 |
|----------|---------------|
| 单个表的最大大小 | 500 TiB |
| 表实体的最大大小 | 1 MiB |
| 表实体中属性的最大数目 | 255，包含三个系统属性：PartitionKey、RowKey 和 Timestamp |
| 实体中属性值的最大总大小 | 1 MiB |
| 实体中单个属性的最大总大小 | 根据属性类型而变化。 有关详细信息，请参阅[了解表服务数据模型](/rest/api/storageservices/understanding-the-table-service-data-model)中的**属性类型**。 |
| 每个表存储的访问策略的最大数目 | 5 |
| 每个存储帐户的最大请求速率 | 20,000 事务/秒，假定实体大小为 1-KiB |
| 单个表分区的目标吞吐量（1 KiB 实体） | 每秒最多 2,000 个实体 |