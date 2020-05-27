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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "77157206"
---
存储帐户的冗余选项包括：

* 本地冗余存储 (LRS)：简单的低成本冗余策略。 数据将在主要区域中同步复制三次。
* 区域冗余存储（ZRS）：冗余，适用于需要高可用性的方案。 在主要区域的三个 Azure 可用性区域之间同步复制数据。
* 异地冗余存储（GRS）：跨区域冗余以防范区域性服务中断。 数据在主要区域中同步复制三次，然后异步复制到次要区域。 若要对次要区域中的数据进行读取访问，请启用读取访问异地冗余存储 (RA-GRS)。
* 区域冗余存储（GZRS）（预览版）：冗余，适用于要求高可用性和最大持续性的方案。 数据在主要区域的三个 Azure 可用性区域之间同步复制，然后异步复制到次要区域。 若要对次要区域中的数据进行读取访问，请启用读取访问权限异地冗余存储（GZRS）。

有关 Azure 存储中冗余选项的详细信息，请参阅[Azure 存储冗余](../articles/storage/common/storage-redundancy.md)。
