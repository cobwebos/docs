---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 5b7aa618df63bf813e0ab0d77025cf57cfe55c3a
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/26/2018
ms.locfileid: "50964530"
---
| 资源 | 目标 |
|----------|---------------|
| 单个表的最大大小 | 500 TiB |
| 表实体的最大大小 | 1 MiB |
| 表实体中属性的最大数目 | 255（包括 3 个系统属性：PartitionKey、RowKey 和 Timestamp） |
| 每个表存储的访问策略的最大数目 | 5 |
| 每个存储帐户的最大请求速率 | 20,000 事务/秒（假定实体大小为 1 KiB） |
| 单个表分区的目标吞吐量（1 KiB 实体） | 每秒最多 2000 个实体 |
