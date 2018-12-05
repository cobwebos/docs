---
title: 使用 Azure 门户重启 Azure Database for MySQL 服务器
description: 本文介绍如何使用 Azure 门户重启 Azure Database for MySQL 服务器。
services: mysql
author: ajlam
ms.author: andrela
editor: jasonwhowell
manager: kfile
ms.service: mysql
ms.topic: article
ms.date: 11/16/2018
ms.openlocfilehash: 278b535e57dc4d3e79c1ca4d08c89f3e4d833325
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2018
ms.locfileid: "52166670"
---
# <a name="restart-azure-database-for-mysql-server-using-azure-portal"></a>使用 Azure 门户重启 Azure Database for MySQL 服务器
本主题介绍如何重启 Azure Database for MySQL 服务器。 出于维护原因，可能需要重启服务器，这会在服务器执行操作时导致短暂中断。

如果服务处于繁忙状态，则会阻止重启服务器。 例如，服务可以处理缩放 vCores 等先前请求的操作。

完成重启所需的时间取决于 MySQL 恢复过程。 若要减少重启时间，建议在重启之前尽量减少服务器上发生的活动量。

## <a name="prerequisites"></a>先决条件
若要完成本操作指南，需要：
- [Azure Database for MySQL 服务器和数据库](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="perform-server-restart"></a>执行服务器重启

可通过以下步骤重启 MySQL 服务器：

1. 在 Azure 门户中，选择 Azure Database for MySQL 服务器。

2. 在服务器“概述”页的工具栏中，单击“重启”。

   ![Azure Database for MySQL -“概述”-“重启”按钮](./media/howto-restart-server-portal/2-server.png)

3. 单击“是”以确认重启服务器。 

   ![Azure Database for MySQL - 重启确认 ](./media/howto-restart-server-portal/3-restart-confirm.png)

4. 观察到服务器状态更改为“正在重启”。

   ![Azure Database for MySQL - 重启状态 ](./media/howto-restart-server-portal/4-restarting-status.png)

5. 确认服务器重启成功。

   ![Azure Database for MySQL - 重启成功 ](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>后续步骤

[快速入门：使用 Azure 门户创建 Azure Database for MySQL 服务器](./quickstart-create-mysql-server-database-using-azure-portal.md)