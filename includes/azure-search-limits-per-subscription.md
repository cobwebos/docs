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
ms.openlocfilehash: 0da7ad35f6efc031a52ef43caa514559c08c94fe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61464349"
---
可以创建一个订阅中的多个服务。 每个可以在特定层预配。 限制是仅允许每个层的服务数。 例如，在同一订阅中，最多可以在基本层创建 12 个服务，在 S1 层也创建 12 个服务。 有关层的详细信息，请参阅[为 Azure 搜索选择 SKU 或层](../articles/search/search-sku-tier.md)。

最大服务数限制可以根据请求提高。 如果需要在同一订阅中的更多服务，请联系 Azure 支持。

| 资源            | 免费<sup>1</sup> | 基本 | S1  | S2 | S3 | S3&nbsp;HD | L1 | L2 |
| ------------------- | ---- | ----- | --- | -- | -- | ----- | -- | -- |
| 最大服务数    |第     | 12    | 12  | 6  | 6  | 6     | 6  | 6  |
| 搜索单位 (SU) 中的最大缩放<sup>2</sup> |不适用 |3 SU |36 个 SU |36 个 SU |36 个 SU |36 个 SU |36 个 SU |36 个 SU |

<sup>1</sup> 免费层基于共享资源，而不基于专用资源。 共享资源不支持纵向扩展。

<sup>2</sup>搜索单位即计费单位，为分配*副本*或*分区*。 进行存储、索引和查询操作同时需要这两个资源。 若要了解有关 SU 计算的详细信息，请参阅[缩放查询和索引工作负荷的资源级别](../articles/search/search-capacity-planning.md)。 