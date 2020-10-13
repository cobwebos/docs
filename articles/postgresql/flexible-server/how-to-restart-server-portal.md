---
title: 重新启动-Azure 门户 Azure Database for PostgreSQL-灵活的服务器
description: 本文介绍如何通过 Azure 门户 Azure Database for PostgreSQL 执行重新启动操作。
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 952bd6dddf9f276ed1a4a18f03799147f1902198
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90933327"
---
# <a name="restart-azure-database-for-postgresql---flexible-server"></a>重新启动 Azure Database for PostgreSQL-灵活服务器

> [!IMPORTANT]
> Azure Database for PostgreSQL 灵活服务器以预览版提供

本文提供了执行灵活服务器重启的分步过程。 此操作可用于应用需要重新启动数据库服务器的任何静态参数更改。 对于配置了区域冗余高可用性的服务器，该过程是相同的。 

> [!IMPORTANT]
> 如果配置了高可用性，则会同时重新启动主服务器和备用服务器。

## <a name="pre-requisites"></a>先决条件

若要完成本操作指南，需要：

-   您必须具有灵活的服务器。

## <a name="restart-your-flexible-server"></a>重新启动你的灵活服务器

请按照以下步骤重启你的灵活服务器。

1.  在 [Azure 门户](https://portal.azure.com/)中，选择要重新启动的灵活服务器。

2.  单击左侧面板中的 " **概述** "，然后单击 " **重新启动**"。
   
     :::image type="content" source="./media/how-to-restart-server-portal/restart-base-page.png" alt-text="重新启动选定内容":::

3.  将显示一个弹出的确认消息。

4.  如果要继续，请单击 **"是"** 。
   
     :::image type="content" source="./media/how-to-restart-server-portal/restart-pop-up.png" alt-text="重新启动选定内容":::
 
6.  将显示一条通知，指出已启动重新启动操作。

## <a name="next-steps"></a>后续步骤

-   了解 [业务连续性](./concepts-business-continuity.md)
-   了解 [区域冗余高可用性](./concepts-high-availability.md)
