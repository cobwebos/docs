---
title: include 文件
description: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/26/2018
ms.author: jeking
ms.custom: include file
ms.openlocfilehash: efa593d0ff0043d81574b67192deed30933e1e40
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60516281"
---
异地冗余存储 (GRS) 通过将数据复制到距主要区域数百英里以外的次要区域，用于在给定的一年内至少为对象提供 99.99999999999999%（16 个 9）的持久性。 如果存储帐户启用了 GRS，则即使遇到区域完全停电或导致主区域不可恢复的灾难，数据也能持久保存。

如果选择启用 GRS，可从两个相关选项中进行选择：

* GRS 将数据复制到次要区域中的另一个数据中心，但仅当 Microsoft 发起了从主要区域到次要区域的故障转移时，才可读取这些数据。 
* 读取访问异地冗余存储 (RA-GRS) 基于 GRS。 RA-GRS 将数据复制到次要区域中的另一个数据中心，同时还提供从次要区域读取数据的选项。 如果使用 RA-GRS，无论 Microsoft 是否发起从主要区域到次要区域的故障转移，都能从次要区域读取数据。 

对于已启用 GRS 或 RA-GRS 的存储帐户，首先会使用本地冗余存储 (LRS) 复制所有数据。 首先将更新提交到主要位置，并使用 LRS 复制更新。 然后，使用 GRS 以异步方式将更新复制到次要区域。 将数据写入次要位置后，还会使用 LRS 在该位置复制数据。 

主要和次要区域在一个存储缩放单元内管理跨单独的容错域和升级域管理副本。 存储缩放单元是数据中心内的基本复制单元。 此级别的复制由 LRS 提供；有关详细信息，请参阅[本地冗余存储 (LRS)：Azure 存储的低成本数据冗余](../articles/storage/common/storage-redundancy-lrs.md)。

确定要使用哪个复制选项时，请记住以下几点：

* 区域冗余存储 (ZRS) 提供高可用性和同步复制，在某些情况下可能比 GRS 或 RA-GRS 更有利。 有关 ZRS 的详细信息，请参阅 [ZRS](../articles/storage/common/storage-redundancy-zrs.md)。
* 对于异步复制，从数据写入到主要区域到数据复制到次要区域，这之间存在延迟。 发生区域性灾难时，如果无法从主要区域中恢复数据，则尚未复制到次要区域的更改可能会丢失。
* 使用 GRS 时，副本不可用于读取或写入访问，除非 Microsoft 启动到次要区域的故障转移。 如果发生故障转移，则在故障转移完成后，你将具有对该数据的读取和写入访问权限。 有关详细信息，请参阅[灾难恢复指南](../articles/storage/common/storage-disaster-recovery-guidance.md)。
* 如果应用程序需要从次要区域读取数据，请启用 RA-GRS。