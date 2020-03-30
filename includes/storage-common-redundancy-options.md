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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77157206"
---
存储帐户的冗余选项包括：

* 本地冗余存储 （LRS）：一种简单、低成本的冗余策略。 数据在主区域内同步复制三次。
* 区域冗余存储 （ZRS）：需要高可用性的方案的冗余。 数据在主区域中的三个 Azure 可用性区域中同步复制。
* 异地冗余存储 （GRS）：跨区域冗余，以防止区域中断。 数据在主区域中同步复制三次，然后异步复制到辅助区域。 若要对次要区域中的数据进行读取访问，请启用读取访问异地冗余存储 (RA-GRS)。
* 地理区域冗余存储 （GZRS） （预览）：需要高可用性和最大耐用性的方案的冗余性。 数据在主区域中的三个 Azure 可用性区域上同步复制，然后异步复制到辅助区域。 对于对辅助区域中数据的读取访问，启用读取访问地理区域冗余存储 （RA-GZRS）。

有关 Azure 存储中的冗余选项的详细信息，请参阅[Azure 存储冗余](../articles/storage/common/storage-redundancy.md)。
