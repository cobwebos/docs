---
title: 停止/启动-Azure 门户-Azure Database for MySQL 服务器
description: 本文介绍如何在 Azure Database for MySQL 中停止/启动操作。
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: 95be6aa576d9d059ce419443f8c7e32af5ff397a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91826208"
---
# <a name="stopstart-an-azure-database-for-mysql"></a>停止/启动 Azure Database for MySQL

> [!IMPORTANT]
> Azure Database for MySQL 的停止/启动功能当前为公共预览版。

本文提供了执行停止和启动单一服务器的分步过程。

## <a name="prerequisites"></a>先决条件

若要完成本操作指南，需要：

-   您必须具有 Azure Database for MySQL 单个服务器。

> [!NOTE]
> 请参阅使用[停止/启动](concepts-servers.md#limitations-of-stopstart-operation)的限制

## <a name="how-to-stopstart-the-azure-database-for-mysql-using-azure-portal"></a>如何使用 Azure 门户停止/启动 Azure Database for MySQL

### <a name="stop-a-running-server"></a>停止正在运行的服务器

1.  在 [Azure 门户](https://portal.azure.com/)中，选择要停止的 MySQL 服务器。

2.  从 " **概述** " 页上，单击工具栏中的 " **停止** " 按钮。

    :::image type="content" source="./media/howto-stop-start-server/mysql-stop-server.png" alt-text="Azure Database for MySQL 停止服务器":::

    > [!NOTE]
    > 停止服务器后，不能为单一服务器提供其他管理操作。

### <a name="start-a-stopped-server"></a>启动停止的服务器

1.  在 [Azure 门户](https://portal.azure.com/)中，选择要启动的单一服务器。

2.  从 " **概述** " 页上，单击工具栏中的 " **开始** " 按钮。

    :::image type="content" source="./media/howto-stop-start-server/mysql-start-server.png" alt-text="Azure Database for MySQL 停止服务器":::

    > [!NOTE]
    > 服务器启动后，所有管理操作现在都可用于单一服务器。

## <a name="how-to-stopstart-the-azure-database-for-mysql-using-cli"></a>如何使用 CLI 停止/启动 Azure Database for MySQL

### <a name="stop-a-running-server"></a>停止正在运行的服务器

1.  在 [Azure 门户](https://portal.azure.com/)中，选择要停止的 MySQL 服务器。

2.  从 " **概述** " 页上，单击工具栏中的 " **停止** " 按钮。

    ```azurecli-interactive
    az mysql server stop --name <server-name> -g <resource-group-name>
    ```
    > [!NOTE]
    > 停止服务器后，不能为单一服务器提供其他管理操作。

### <a name="start-a-stopped-server"></a>启动停止的服务器

1.  在 [Azure 门户](https://portal.azure.com/)中，选择要启动的单一服务器。

2.  从 " **概述** " 页上，单击工具栏中的 " **开始** " 按钮。

    ```azurecli-interactive
    az mysql server start --name <server-name> -g <resource-group-name>
    ```
    > [!NOTE]
    > 服务器启动后，所有管理操作现在都可用于单一服务器。

## <a name="next-steps"></a>后续步骤
了解 [如何创建有关指标的警报](howto-alert-on-metric.md)。
