---
title: include 文件
description: include 文件
services: search
author: HeidiSteen
ms.service: search
ms.topic: include
ms.date: 05/06/2019
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: b298c87d802314bd865bd6f38c35e4361eb69f3f
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "67173330"
---
存储受磁盘空间限制，或者受索引、文档或其他高级资源的*最大数目*的硬性限制，具体取决于哪一个限制先实施。 下表描述了存储限制。 有关索引、文档和其他对象的最大限制，请参阅[按资源限制](../articles/search/search-limits-quotas-capacity.md#index-limits)。

| Resource | 免费 | 基本<sup>1</sup> | S1 | S2 | S3 | S3&nbsp;HD<sup>2</sup> | L1 | L2 |
| -------- | --- | --- | --- | --- | --- | --- | --- | --- |
| 服务级别协议（SLA）<sup>3</sup>  |否 |是 |是 |是 |是 |是 |是 |是 |
| 每个分区的存储 |50 MB |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| 每个服务的分区数 |不可用 |1 |12 |12 |12 |3 |12 |12 |
| 分区大小 |不可用 |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| 副本 |不可用 |3 |12 |12 |12 |12 |12 |12 |

<sup>1</sup> 基本层有一个固定分区。 在此层上，附加的搜索单元用于分配更多的副本以提高查询工作负荷。

<sup>2</sup> S3 HD 的硬性限制为3个分区，低于 S3 的分区限制。 施加的分区限制较低是因为 S3 HD 的索引计数要高得多。 由于计算资源（存储和处理）和内容（索引和文档）都存在服务限制，因此会先达到内容限制。

<sup>3</sup>针对专用资源的计费服务提供服务级别协议。 免费服务和预览功能没有 SLA。 对于可计费服务，SLA 将在用户为服务预配足够冗余时生效。 查询（读取） Sla 需要两个或多个副本。 查询和索引（读写） Sla 需要三个或更多的副本。 分区数不是 SLA 注意事项。 