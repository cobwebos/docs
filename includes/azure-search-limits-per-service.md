---
title: include 文件
description: include 文件
services: search
author: HeidiSteen
ms.service: search
ms.topic: include
ms.date: 04/04/2018
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: fc0451aa89da9b84a5a01a0762425f7533dded3c
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553341"
---
存储受磁盘空间限制，或者受索引、文档或其他高级资源的*最大数目*的硬性限制，具体取决于哪一个限制先实施。 下表描述了存储限制。 有关索引、 文档和其他对象的最大限制，请参阅[按资源限制](../articles/search/search-limits-quotas-capacity.md#index-limits)。

| 资源 | 免费 | 基本<sup>1</sup> | S1 | S2 | S3 | S3&nbsp;HD<sup>2</sup> |
| -------- | --- | --- | --- | --- | --- | --- |
| 服务级别协议 (SLA)<sup>3</sup>  |否 |是 |是 |是 |是 |是 |
| 每个分区的存储 |50 MB |2 GB |25 GB |100 GB |200 GB |200 GB |
| 每个服务的分区数 |不适用 |第 |12 |12 |12 |3 |
| 分区大小 |不适用 |2 GB |25 GB |100 GB |200 GB |200 GB |
| 副本 |不适用 |3 |12 |12 |12 |12 |

<sup>1</sup>基本层有一个固定的分区。 在此层中，为提高的查询工作负荷分配更多副本使用附加搜索单位。

<sup>2</sup>S3 HD 的硬限制为三个分区，低于 S3 的分区限制。 施加的分区限制较低是因为 S3 HD 的索引计数要高得多。 由于计算资源（存储和处理）和内容（索引和文档）都存在服务限制，因此会先达到内容限制。

<sup>3</sup>服务级别协议提供专用资源上的可计费服务。 免费服务和预览功能没有 SLA。 对于可计费服务，SLA 将在用户为服务预配足够冗余时生效。 查询 （读取） Sla 需要两个或多个副本。 用于查询和索引 （读-写） Sla 需要三个或多个副本。 分区数不是一个 SLA 的考虑因素。 