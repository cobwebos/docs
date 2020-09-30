---
title: 停止/启动-Azure 门户-Azure Database for MySQL 灵活的服务器
description: 本文介绍如何通过 Azure 门户在 Azure Database for MySQL 中停止/启动操作。
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 09/29/2020
ms.openlocfilehash: e3e08ae9bbf00e1c9a44e6ba913cac1d842928b7
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91567465"
---
# <a name="stopstart-an-azure-database-for-mysql---flexible-server-preview"></a>停止/启动 Azure Database for MySQL 灵活的服务器 (预览版) 

> [!IMPORTANT]
> Azure Database for MySQL 灵活服务器当前以公共预览版提供。

本文提供了执行停止和启动灵活服务器的分步过程。

## <a name="prerequisites"></a>先决条件

若要完成本操作指南，需要：

-   您必须具有 Azure Database for MySQL 灵活的服务器。

## <a name="stop-a-running-server"></a>停止正在运行的服务器

1.  在 [Azure 门户](https://portal.azure.com/)中，选择要停止的灵活服务器。

2.  从 " **概述** " 页上，单击工具栏中的 " **停止** " 按钮。
    
    :::image type="content" source="media/how-to-stop-start-server-portal/stop-server.png" alt-text="停止灵活的服务器。"::: 

3.  单击 **"是"** 以确认停止服务器。

    :::image type="content" source="media/how-to-stop-start-server-portal/confirm-stop.png" alt-text="停止灵活的服务器。"::: 

> [!NOTE]
> 停止服务器后，其他管理操作将不能用于灵活的服务器。

## <a name="start-a-stopped-server"></a>启动停止的服务器

1.  在 [Azure 门户](https://portal.azure.com/)中，选择要启动的灵活服务器。

2.  从 " **概述** " 页上，单击工具栏中的 " **开始** " 按钮。

    :::image type="content" source="media/how-to-stop-start-server-portal/start-server.png" alt-text="停止灵活的服务器。":::  

> [!NOTE]
> 服务器启动后，所有管理操作现在都可用于灵活的服务器。

## <a name="next-steps"></a>后续步骤
- 详细了解 [Azure Database for MySQL 灵活服务器中的网络](./concepts-networking.md)
- [使用 Azure 门户创建和管理 Azure Database for MySQL 灵活服务器虚拟网络](./how-to-manage-virtual-network-portal.md)。

