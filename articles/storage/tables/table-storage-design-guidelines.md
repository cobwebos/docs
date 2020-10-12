---
title: Azure 存储表设计准则 | Microsoft Docs
description: 了解设计 Azure 存储表服务的相关准则，以便有效地支持读写操作。
services: storage
ms.service: storage
author: tamram
ms.author: tamram
ms.topic: article
ms.date: 04/23/2018
ms.subservice: tables
ms.openlocfilehash: f84707e454a8b1f5d5947478fe65108a142a9757
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88236312"
---
# <a name="guidelines-for-table-design"></a>表设计准则

设计用于 Azure 存储表服务的表与关系数据库的设计注意事项大不相同。 本文介绍设计表服务解决方案以实现高效读取和高效写入的准则。

## <a name="design-your-table-service-solution-to-be-read-efficient"></a>设计表服务解决方案，实现高效读取

* ***专用于查询读取操作繁重的应用程序。*** 设计表时，在考虑将如何更新实体之前，请先考虑将执行的查询（特别是延迟敏感的查询）。 这通常会产生一个高效且高性能的解决方案。  
* ***在查询中指定 PartitionKey 和 RowKey。*** *点查询* 此类查询是最高效的表服务查询。  
* ***请考虑存储实体的重复副本。*** 表存储比较便宜，因此请考虑多次存储同一实体（使用不同键）以实现更高效的查询。  
* ***请考虑反规范化处理数据。*** 表存储比较便宜，因此请考虑反规范化数据。 例如，存储摘要实体，以便对聚合数据的查询只需访问单个实体。  
* ***使用复合键值。*** 具有的唯一键是 **PartitionKey** 和 **RowKey**。 例如，使用复合键值来启用对实体的备用键控访问路径。  
* ***使用查询投影。*** 可使用仅选择所需字段的查询，减少网络上传输的数据量。  

## <a name="design-your-table-service-solution-to-be-write-efficient"></a>设计表服务解决方案，实现高效写入  

* ***不要创建热分区。*** 选择在任何时间点都能够将请求散布到多个分区的键。  
* ***避免出现流量高峰。*** 使流量在合理的时间段内均匀分布，并避免出现流量高峰。
* ***无需为每种实体类型创建一个单独的表。*** 当需要对多种实体类型执行原子事务操作时，可以将这些实体类型存储在同一个表的同一个分区中。
* ***请考虑必须实现的最大吞吐量。*** 必须了解表服务的伸缩性目标，并确保设计不会导致超出目标。  

在阅读本指南时，会看到将所有这些原则付诸实践的示例。 

## <a name="next-steps"></a>后续步骤

- [表设计模式](table-storage-design-patterns.md)
- [针对查询的设计](table-storage-design-for-query.md)
- [对表数据进行加密](table-storage-design-encrypt-data.md)
- [针对数据修改的设计](table-storage-design-for-modification.md)
