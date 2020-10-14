---
title: include 文件
description: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/14/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 73c2b742ede21a4e86d717d994f8ebc4f16389c9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "77157206"
---
存储帐户的冗余选项包括：

* 本地冗余存储 (LRS)：简单的低成本冗余策略。 数据将在主要区域中同步复制三次。
* 区域冗余存储 (ZRS)：针对需要高可用性的方案的冗余。 跨主要区域中的 3 个 Azure 可用性区域同步复制数据。
* 异地冗余存储 (GRS)：用于防范区域性服务中断的跨区域冗余。 在主要区域同步复制数据三次，然后将数据异步复制到次要区域。 若要对次要区域中的数据进行读取访问，请启用读取访问异地冗余存储 (RA-GRS)。
* 异地区域冗余存储 (GZRS)（预览版）：针对需要高可用性和最大持续性的方案的冗余。 跨主要区域中的 3 个 Azure 可用性区域同步复制数据，然后将数据异步复制到次要区域。 若要对次要区域进行读取访问，可启用读取访问异地区域冗余存储 (RA-GZRS)。

若要详细了解 Azure 存储中的冗余选项，请参阅 [Azure 存储冗余](../articles/storage/common/storage-redundancy.md)。
