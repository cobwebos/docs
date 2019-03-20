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
ms.openlocfilehash: a466ea29fa31c4c628724f3d5138a1612ef7a0f4
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553297"
---
可以创建一个订阅中的多个服务。 每个可以在特定层预配。 限制是仅允许每个层的服务数。 例如，在同一订阅中，最多可以在基本层创建 12 个服务，在 S1 层也创建 12 个服务。 有关层的详细信息，请参阅[为 Azure 搜索选择 SKU 或层](../articles/search/search-sku-tier.md)。

最大服务数限制可以根据请求提高。 如果需要在同一订阅中的更多服务，请联系 Azure 支持。

| 资源            | 免费<sup>1</sup> | 基本 | S1  | S2 | S3 | S3&nbsp;HD |
| ------------------- | ---- | ----- | --- | -- | -- | ----- |
| 最大服务数    |第     | 12    | 12  | 6  | 6  | 6     |
| 搜索单位 (SU) 中的最大缩放<sup>2</sup> |不适用 |3 SU |36 个 SU |36 个 SU |36 个 SU |36 个 SU |

<sup>1</sup>免费层基于共享的不基于专用资源。 共享资源不支持纵向扩展。

<sup>2</sup>搜索单位即计费单位，为分配*副本*或*分区*。 进行存储、索引和查询操作同时需要这两个资源。 若要了解有关 SU 计算的详细信息，请参阅[缩放查询和索引工作负荷的资源级别](../articles/search/search-capacity-planning.md)。 