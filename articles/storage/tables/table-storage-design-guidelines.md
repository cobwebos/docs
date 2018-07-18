---
title: Azure 存储表设计准则 | Microsoft Docs
description: 将 Azure 表服务设计为高效地支持读取操作。
services: storage
documentationcenter: na
author: SnehaGunda
manager: kfile
ms.assetid: 8e228b0c-2998-4462-8101-9f16517393ca
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/23/2018
ms.author: sngun
ms.openlocfilehash: 5329d33aee1bb1a55e9982b1ba9e3e8329246980
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34659241"
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