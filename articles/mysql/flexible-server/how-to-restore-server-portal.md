---
title: Azure 门户-Azure Database for MySQL-灵活服务器
description: 本文介绍如何通过 Azure 门户 Azure Database for MySQL 执行还原操作。
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: 1c81ddad8a11cbad361ff84caf6f7200a0c010d5
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90933387"
---
# <a name="point-in-time-restore-of-a-azure-database-for-mysql---flexible-server-preview"></a>Azure Database for MySQL 灵活的服务器 (预览版的时间点还原) 


> [!IMPORTANT]
> Azure Database for MySQL 灵活服务器当前以公共预览版提供。

本文提供使用备份在灵活的服务器中执行时间点恢复的分步过程。

## <a name="prerequisites"></a>先决条件

若要完成本操作指南，需要：

-   您必须具有 Azure Database for MySQL 灵活的服务器。

## <a name="restore-to-the-latest-restore-point"></a>还原到最新的还原点

按照这些步骤操作，使用最早的现有备份还原你的灵活服务器。

1.  在 [Azure 门户](https://portal.azure.com/)中，选择要从中还原备份的灵活服务器。

2.  单击左侧面板中的 " **概述** "。

3.  在 "概述" 页上，单击 " **还原**"。

    Placeholder

4.  此时将显示 "还原" 页，其中包含在 **最新的还原点** 和自定义还原点之间进行选择的选项。

5.  选择 **最新的还原点**。


6.  在 " **还原到新服务器** " 字段中提供新的服务器名称。

    :::image type="content" source="./media/concept-backup-restore/restore-blade-latest.png" alt-text="最早还原时间":::

8.  单击“确定”。 

9.  将显示一条通知，指出已启动还原操作。

## <a name="restoring-to-a-custom-restore-point"></a>还原到自定义的还原点

按照这些步骤操作，使用最早的现有备份还原你的灵活服务器。

1.  在 [Azure 门户](https://portal.azure.com/)中，选择要从中还原备份的灵活服务器。

2.  在 "概述" 页上，单击 " **还原**"。

    Placeholder

3.  "还原" 页将显示一个选项，用于在最早的还原点和自定义还原点之间进行选择。

4.  选择 " **自定义还原点**"。

5.  选择 "日期和时间"。

6.  在 " **还原到新服务器** " 字段中提供新的服务器名称。

6.  在 " **还原到新服务器** " 字段中提供新的服务器名称。 
   
    :::image type="content" source="./media/concept-backup-restore/restore-blade-custom.png" alt-text="查看概述":::
 
7.  单击“确定”。 

8.  将显示一条通知，指出已启动还原操作。

## <a name="next-steps"></a>后续步骤

占位符
