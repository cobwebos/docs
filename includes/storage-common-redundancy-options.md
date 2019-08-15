---
title: include 文件
description: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/28/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 5df0fe23183b53f13d6c6545e248724324434cde
ms.sourcegitcommit: df7942ba1f28903ff7bef640ecef894e95f7f335
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2019
ms.locfileid: "69029744"
---
存储帐户的复制选项包括：

* [本地冗余存储 (LRS)](../articles/storage/common/storage-redundancy-lrs.md)：一种简单、低成本的复制策略。 数据在主要区域内同步复制三次。
* [区域冗余存储 (ZRS)](../articles/storage/common/storage-redundancy-zrs.md)：需要高可用性的情况下进行复制。 在主要区域的三个 Azure 可用性区域之间同步复制数据。
* [异地冗余存储 (GRS)](../articles/storage/common/storage-redundancy-grs.md)：跨区域复制以防范区域性服务中断。 数据在主要区域中同步复制三次, 然后异步复制到次要区域。 若要对次要区域中的数据进行读取访问, 请启用读取访问异地冗余存储 (GRS)。
* [区域冗余存储 (GZRS) (预览版)](../articles/storage/common/storage-redundancy-gzrs.md):针对需要高可用性和最大持久性的方案进行复制。 数据在主要区域的三个 Azure 可用性区域之间同步复制, 然后异步复制到次要区域。 若要对次要区域中的数据进行读取访问, 请启用读取访问权限异地冗余存储 (GZRS)。
