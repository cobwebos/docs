---
title: include 文件
description: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/26/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: b965fea3d4f166b1a801dda7cafd8e4190790c68
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2018
ms.locfileid: "45739498"
---
存储帐户的复制选项包括：

* [本地冗余存储 (LRS)](../articles/storage/common/storage-redundancy-lrs.md)：简单、成本低廉的复制策略。 数据在单个存储缩放单元中进行复制。
* [区域冗余存储 (ZRS)](../articles/storage/common/storage-redundancy-zrs.md)：可用性和持续性较高的复制选项。 数据跨三个可用性区域以同步方式进行复制。 
* [异地冗余存储 (GRS)](../articles/storage/common/storage-redundancy-grs.md)：为了防范区域性的不可用而提供的跨区域复制。
* [读取访问异地冗余存储 (RA-GRS)](../articles/storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)：可以对副本进行读取访问的跨区域复制。