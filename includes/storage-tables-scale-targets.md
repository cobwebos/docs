---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: a8b4e3038bfa6a2e937de91804159e340ed13224
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553327"
---
| 资源 | 目标 |
|----------|---------------|
| 单个表的最大大小 | 500 TiB |
| 表实体的最大大小 | 1 MiB |
| 中的表实体属性的最大数目 | 255，其中包括三个系统属性：PartitionKey、 RowKey 和 Timestamp |
| 每个表的存储的访问策略的最大数目 | 5 |
| 每个存储帐户的最大请求速率 | 20,000 事务 / 秒，假定实体大小为 1 KiB |
| 单个表分区 （1 KiB 实体） 的目标吞吐量 | 最多 2,000 条实体数 / 秒 |
