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
ms.openlocfilehash: 1e147e8bd9260cd1ece60b70641968a229995ec1
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "67173329"
---
可以在一个订阅中创建多个服务。 每一个服务都可以在特定层上进行预配。 你仅受每个层允许的服务数量限制。 例如，在同一订阅中，最多可以在基本层创建 12 个服务，在 S1 层也创建 12 个服务。 有关层的详细信息，请参阅[为 Azure 搜索选择 SKU 或层](../articles/search/search-sku-tier.md)。

最大服务数限制可以根据请求提高。 如果需要在同一订阅中使用更多服务，请与 Azure 支持部门联系。

| Resource            | 免费<sup>1</sup> | 基本 | S1  | S2 | S3 | S3&nbsp;HD | L1 | L2 |
| ------------------- | ---- | ----- | --- | -- | -- | ----- | -- | -- |
| 最大服务数    |1     | 16    | 16  | 8  | 6  | 6     | 6  | 6  |
| 搜索单位 (SU) 的最大规模<sup>2</sup> |不可用 |3 SU |36 个 SU |36 个 SU |36 个 SU |36 个 SU |36 个 SU |36 个 SU |

<sup>1</sup> 免费层基于共享资源，而不基于专用资源。 共享资源不支持纵向扩展。

<sup>2</sup> 搜索单位是计费单位，以副本或分区形式分配。 进行存储、索引和查询操作同时需要这两个资源。 若要了解有关 SU 计算的详细信息，请参阅[缩放查询和索引工作负荷的资源级别](../articles/search/search-capacity-planning.md)。 