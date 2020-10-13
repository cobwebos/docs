---
title: 规模操作-Azure 门户 Azure Database for PostgreSQL-灵活服务器
description: 本文介绍如何通过 Azure 门户 Azure Database for PostgreSQL 执行缩放操作。
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 1542bba53b51ffdf2129953a81e5d13975ade434
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90934373"
---
# <a name="scale-operations-in-flexible-server"></a>灵活服务器中的缩放操作

> [!IMPORTANT]
> Azure Database for PostgreSQL 灵活服务器以预览版提供

本文提供了执行计算和存储的缩放操作的步骤。 你将能够在可突增、常规用途和内存优化 Sku 之间更改计算层，包括选择适用于运行应用程序的 Vcore 数量。 还可以纵向扩展存储。 预期的 IOPS 基于计算层 Vcore 和存储容量来显示。 还会根据你的选择显示成本估算。

> [!IMPORTANT]
> 不能缩小存储空间。

## <a name="pre-requisites"></a>先决条件

若要完成本操作指南，需要：

-   您必须具有 Azure Database for PostgreSQL 灵活的服务器。 同样的过程也适用于配置了区域冗余的灵活服务器。
> [!IMPORTANT]
> 如果配置了高可用性，则不能选择可突增 SKU。 在缩放操作期间，首先会将待机调整为所需大小，主服务器已故障转移，并且主服务器已缩放。 

## <a name="scaling-the-compute-tier-and-size"></a>缩放计算层和大小

请按照以下步骤选择计算层。
 
1.  在 [Azure 门户](https://portal.azure.com/)中，选择要从中还原备份的灵活服务器。

2.  单击 " **计算 + 存储**"。

3.  将显示具有当前设置的页面。
 :::image type="content" source="./media/how-to-scale-compute-storage-portal/click-compute-storage.png" alt-text="计算 + 存储视图":::

4.  可以选择可突增、通用和内存优化层之间的计算类。
   :::image type="content" source="./media/how-to-scale-compute-storage-portal/list-compute-tiers.png" alt-text="计算 + 存储视图" 下拉列表，然后从列表中单击所需的 vcore 数。
    
    - 可突增计算层： :::image type="content" source="./media/how-to-scale-compute-storage-portal/compute-burstable-dropdown.png" alt-text="计算 + 存储视图":::

    - 常规用途计算层： :::image type="content" source="./media/how-to-scale-compute-storage-portal/compute-general-purpose-dropdown.png" alt-text="计算 + 存储视图":::

    - 内存优化计算层： :::image type="content" source="./media/how-to-scale-compute-storage-portal/compute-memory-optimized-dropdown.png" alt-text="计算 + 存储视图":::

7.  单击 **“保存”** 。 
8.  将显示确认消息。 如果要继续，请单击 **"确定"** 。 
9.  有关正在进行中的缩放操作的通知。


## <a name="scaling-storage-size"></a>缩放存储大小

请按照以下步骤提高存储大小。

1.  在 [Azure 门户](https://portal.azure.com/)中，选择要为其增加存储大小的灵活服务器。
2.  单击 " **计算 + 存储**"。

3.  将显示具有当前设置的页面。
   
:::image type="content" source="./media/how-to-scale-compute-storage-portal/click-compute-storage.png" alt-text="计算 + 存储视图":::
4.  带有滑动条的 **GiB 中的字段存储大小** 显示为当前大小。

5.  将滚动条滑至所需大小。 将显示相应的 IOPS 数。 IOPS 依赖于计算层和大小。 还显示了成本信息。 

 :::image type="content" source="./media/how-to-scale-compute-storage-portal/storage-scaleup.png" alt-text="计算 + 存储视图" **保存**"。 
7.  将显示确认消息。 如果要继续，请单击 **"确定"** 。 
8.  有关正在进行中的缩放操作的通知。

## <a name="next-steps"></a>后续步骤

-   了解 [业务连续性](./concepts-business-continuity.md)
-   了解 [高可用性](./concepts-high-availability.md)
-   了解 [备份和恢复](./concepts-backup-restore.md)
