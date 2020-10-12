---
title: 停止/启动-Azure 门户-Azure Database for PostgreSQL 灵活的服务器
description: 本文介绍如何通过 Azure 门户在 Azure Database for PostgreSQL 中停止/启动操作。
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 4c393e0048a0058ebe0fbf2b0ee65f6ae2e184c5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90934217"
---
# <a name="stopstart-an-azure-database-for-postgresql---flexible-server-preview"></a>停止/启动 Azure Database for PostgreSQL 灵活的服务器 (预览版) 

> [!IMPORTANT]
> Azure Database for PostgreSQL-可伸缩服务器目前处于预览阶段。

本文提供了有关停止和启动灵活服务器的分步说明。

## <a name="pre-requisites"></a>先决条件

若要完成本操作指南，需要：

-   您必须具有 Azure Database for PostgreSQL 灵活的服务器。

## <a name="stop-a-running-server"></a>停止正在运行的服务器

1.  在 [Azure 门户](https://portal.azure.com/)中，选择要停止的灵活服务器。

2.  从 " **概述** " 页上，单击工具栏中的 " **停止** " 按钮。

> [!NOTE]
> 停止服务器后，其他管理操作将不能用于灵活的服务器。

请注意，停止的服务器将在七天后自动重新启动。 下一次启动服务器时，将应用任何挂起的维护更新。

## <a name="start-a-stopped-server"></a>启动停止的服务器

1.  在 [Azure 门户](https://portal.azure.com/)中，选择要启动的灵活服务器。

2.  从 " **概述** " 页上，单击工具栏中的 " **开始** " 按钮。

> [!NOTE]
> 服务器启动后，所有管理操作现在都可用于灵活的服务器。

## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure Database for PostgreSQL 灵活的服务器中的计算和存储选项](./concepts-compute-storage.md)。
