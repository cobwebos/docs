---
title: 优化 Gen2 缓存
description: 了解如何通过 Azure 门户监视 Gen2 缓存。
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: performance
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 257b33802ea95138ef8149ef9302b14ca379e7ac
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195532"
---
# <a name="how-to-monitor-the-gen2-cache"></a>如何监视 Gen2 缓存

本文介绍如何监视和排查查询性能下降的问题，只需确定工作负荷是否在充分利用 Gen2 缓存即可。

Gen2 存储体系结构自动将最常查询的列存储段归类到特定的缓存中，该缓存位于基于 NVMe 且专为 Gen2 数据仓库设计的 SSD 中。 如果查询检索驻留在缓存中的段，则可提高性能。
 
## <a name="troubleshoot-using-the-azure-portal"></a>使用 Azure 门户进行故障排除

可以使用 Azure Monitor 来查看 Gen2 缓存指标，以便排查查询性能问题。 首先，请单击 "Azure 门户"，然后单击 "**监视器**"、"**度量值**" 和 "**选择作用域**"：

![Azure Monitor](./media/sql-data-warehouse-cache-portal/cache-0.png)

使用 "搜索" 和下拉栏定位到数据仓库。 然后选择 "应用"。

![Azure Monitor](./media/sql-data-warehouse-cache-portal/cache-1.png)

排查 Gen2 缓存问题时，关键指标是“缓存命中百分比”和“缓存使用百分比”。 选择 "**缓存命中百分比**"，然后使用 "**添加度量值**" 按钮添加**使用的缓存百分比**。 

![缓存指标](./media/sql-data-warehouse-cache-portal/cache-2.png)

## <a name="cache-hit-and-used-percentage"></a>缓存命中和使用百分比

下表根据缓存指标的值对场景进行了说明：

|                                | **缓存命中百分比高** | **缓存命中百分比低** |
| :----------------------------: | :---------------------------: | :--------------------------: |
| **缓存使用百分比高** |          方案 1           |          方案 2          |
| **缓存使用百分比低**  |          方案 3           |          方案 4          |

**场景 1：** 你的缓存使用已优化。 [排查](sql-data-warehouse-manage-monitor.md)可能会减慢查询速度的其他方面。

**场景 2：** 当前工作数据集不适合放置在缓存中，引发物理读取困难，导致缓存命中百分比低。 考虑提升性能级别并重新运行工作负荷，以便填充缓存。

**场景 3：** 查询运行速度慢的原因可能与缓存无关。 [排查](sql-data-warehouse-manage-monitor.md)可能会减慢查询速度的其他方面。 也可以考虑[缩减实例](sql-data-warehouse-manage-monitor.md)，通过缩减缓存大小来节省成本。 

**场景 4：** 你使用了冷缓存，这可能是查询速度慢的原因。 考虑重新运行查询，因此工作数据集现在应该位于缓存中。 

> [!IMPORTANT]
> 如果在重新运行工作负荷后未更新缓存命中百分比或缓存已用百分比，则工作集可能已驻留在内存中。 仅缓存聚集列存储表。

## <a name="next-steps"></a>后续步骤
有关常规查询性能优化的详细信息，请参阅[监视查询执行](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md#monitor-query-execution)。
