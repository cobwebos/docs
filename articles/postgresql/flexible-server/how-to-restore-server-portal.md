---
title: Azure 门户-Azure Database for PostgreSQL-灵活服务器
description: 本文介绍如何通过 Azure 门户 Azure Database for PostgreSQL 执行还原操作。
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: e69bcb3d9e4dca4c45bf9a6fe8ed4d54e7f4a8cd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90934221"
---
# <a name="point-in-time-restore-of-a-flexible-server"></a>灵活服务器的时间点还原

> [!IMPORTANT]
> Azure Database for PostgreSQL 灵活服务器以预览版提供

本文提供使用备份在灵活的服务器中执行时间点恢复的分步过程。 可以在保留期内执行到最早的还原点或自定义的还原点。

## <a name="pre-requisites"></a>先决条件

若要完成本操作指南，需要：

-   您必须具有 Azure Database for PostgreSQL 灵活的服务器。 同样的过程也适用于配置了区域冗余的灵活服务器。

## <a name="restoring-to-the-earliest-restore-point"></a>还原到最早的还原点

按照这些步骤操作，使用最早的现有备份还原你的灵活服务器。

1.  在 [Azure 门户](https://portal.azure.com/)中，选择要从中还原备份的灵活服务器。

2.  单击左侧面板中的 "**概述**"，然后单击 "**还原**"
   
   :::image type="content" source="./media/how-to-restore-server-portal/restore-overview.png" alt-text="还原概述&quot;:::

3.  &quot;还原&quot; 页将显示一个选项，用于在最早的还原点和自定义还原点之间进行选择。

4.  选择 &quot; **最早还原点** &quot; 并在 &quot; **还原到新服务器** " 字段中提供新的服务器名称。 显示可以还原到的最早时间戳。 
   
   :::image type="content" source="./media/how-to-restore-server-portal/restore-earliest.png" alt-text="还原概述&quot;:::

3.  &quot;还原&quot; 页将显示一个选项，用于在最早的还原点和自定义还原点之间进行选择。

4.  选择 &quot; **最早还原点** &quot; 并在 &quot; **还原到新服务器** " **确定**"。

6.  将显示一条通知，指出已启动还原操作。

## <a name="restoring-to-a-custom-restore-point"></a>还原到自定义的还原点

按照这些步骤操作，使用最早的现有备份还原你的灵活服务器。

1.  在 [Azure 门户](https://portal.azure.com/)中，选择要从中还原备份的灵活服务器。

2.  在 "概述" 页上，单击 " **还原**"。
 :::image type="content" source="./media/how-to-restore-server-portal/restore-overview.png" alt-text="还原概述&quot;:::

3.  &quot;还原&quot; 页将显示一个选项，用于在最早的还原点和自定义还原点之间进行选择。

4.  选择 &quot; **最早还原点** &quot; 并在 &quot; **还原到新服务器** " 字段中提供新的服务器名称。 
   
:::image type="content" source="./media/how-to-restore-server-portal/restore-custom.png" alt-text="还原概述&quot;:::

3.  &quot;还原&quot; 页将显示一个选项，用于在最早的还原点和自定义还原点之间进行选择。

4.  选择 &quot; **最早还原点** &quot; 并在 &quot; **还原到新服务器** " **确定**"。

7.  将显示一条通知，指出已启动还原操作。

## <a name="next-steps"></a>后续步骤

-   了解 [业务连续性](./concepts-business-continuity.md)
-   了解 [区域冗余高可用性](./concepts-high-availability.md)
-   了解 [备份和恢复](./concepts-backup-restore.md)
