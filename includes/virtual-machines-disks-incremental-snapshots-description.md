---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 1f094cd78e6708fec4bda1e5510379b11df14dcd
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79299143"
---
增量快照是托管磁盘的时间点备份，在这种情况下，只包含自上一快照后的所有更改。 尝试下载或使用增量快照时，将使用完整的 VHD。 托管磁盘快照的这一新功能可能会使它们更加经济高效，因为除非你选择，否则不需要在每个单独的快照中存储整个磁盘。 与常规快照一样，增量快照可用于创建完全托管的磁盘，或使用常规快照。

增量快照和常规快照之间存在一些差异。 增量快照始终使用标准 Hdd 存储，而不考虑磁盘的存储类型，而常规快照可以使用高级 Ssd。 如果你在高级存储上使用常规快照来扩展 VM 部署，我们建议你在[共享映像库](../articles/virtual-machines/linux/shared-image-galleries.md)中的标准存储上使用自定义映像。 它将帮助你以较低的成本获得更大的规模。 此外，增量快照可能会通过[区域冗余存储](../articles/storage/common/storage-redundancy-zrs.md)（ZRS）提供更好的可靠性。 如果 ZRS 在所选区域中可用，则增量快照将自动使用 ZRS。 如果 ZRS 在区域中不可用，则快照将默认为[本地冗余存储](../articles/storage/common/storage-redundancy-lrs.md)（LRS）。 您可以重写此行为，并手动选择一个，但我们不建议这样做。

增量快照还提供了差异功能，仅适用于托管磁盘。 它们使你能够在相同的托管磁盘的两个增量快照之间进行更改，减小到块级别。 跨区域复制快照时，可以使用此功能来减少数据占用量。
