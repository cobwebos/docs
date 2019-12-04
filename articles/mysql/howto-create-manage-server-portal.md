---
title: 管理服务器-Azure 门户-Azure Database for MySQL
description: 了解如何从 Azure 门户管理 Azure Database for MySQL 服务器。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 553234410a7b8b798d26f0e2fee6132a2602b1cc
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770503"
---
# <a name="manage-an-azure-database-for-mysql-server-using-the-azure-portal"></a>使用 Azure 门户管理 Azure Database for MySQL 服务器
本文介绍如何管理战火 Database for MySQL 服务器。 管理任务包括计算和存储缩放、管理员密码重置和查看服务器详细信息。

## <a name="sign-in"></a>登录
登录到 [Azure 门户](https://portal.azure.com)。

## <a name="create-a-server"></a>创建服务器
访问[快速入门](quickstart-create-mysql-server-database-using-azure-portal.md)，了解如何创建 Azure Database for MySQL 服务器并开始使用。

## <a name="scale-compute-and-storage"></a>缩放计算和存储

创建服务器后，可以根据需求的变化在常规用途和内存优化层之间进行缩放。 还可以通过增加或减少 Vcore 来缩放计算和内存。 可以扩展存储（不过，不能缩小存储空间）。

### <a name="scale-between-general-purpose-and-memory-optimized-tiers"></a>在常规用途和内存优化层之间进行缩放

可以从常规用途缩放到内存优化，反之亦然。 不支持在创建服务器后在基本层之间进行更改。 

1. 在 Azure 门户中选择你的服务器。 选择位于 "**设置**" 部分的 "**定价层**"。

2. 根据要缩放的内容，选择 "**常规用途**" 或 "**内存优化**"。 

    ![更改-定价层](./media/howto-create-manage-server-portal/change-pricing-tier.png)

    > [!NOTE]
    > 更改层会导致服务器重启。

4. 选择 **"确定"** 保存更改。


### <a name="scale-vcores-up-or-down"></a>向上或向下缩放 Vcore

1. 在 Azure 门户中选择你的服务器。 选择位于 "**设置**" 部分的 "**定价层**"。

2. 通过将滑块移动到所需的值来更改“vCore”设置。

    ![scale-compute](./media/howto-create-manage-server-portal/scaling-compute.png)

    > [!NOTE]
    > 缩放 Vcore 导致服务器重启。

3. 选择 **"确定"** 保存更改。


### <a name="scale-storage-up"></a>向上缩放存储

1. 在 Azure 门户中选择你的服务器。 选择位于 "**设置**" 部分的 "**定价层**"。

2. 将滑块移动到所需的值，以更改**存储**设置。

    ![scale-storage](./media/howto-create-manage-server-portal/scaling-storage.png)

    > [!NOTE]
    > 无法缩减存储空间。

3. 选择 **"确定"** 保存更改。


## <a name="update-admin-password"></a>更新管理员密码
您可以使用 Azure 门户更改管理员角色的密码。

1. 在 Azure 门户中选择你的服务器。 在 "**概述**" 窗口中，选择 "**重置密码**"。

   ![概览](./media/howto-create-manage-server-portal/overview-reset-password.png)

2. 输入新密码并确认。 文本框将提示您输入密码复杂性要求。

   ![reset-password](./media/howto-create-manage-server-portal/reset-password.png)

3. 选择 **"确定"** 保存新密码。


## <a name="delete-a-server"></a>删除服务器

如果不再需要服务器，可以将其删除。 

1. 在 Azure 门户中选择你的服务器。 在 "**概述**" 窗口中选择 "**删除**"。

    ![delete](./media/howto-create-manage-server-portal/overview-delete.png)

2. 在输入框中键入服务器的名称，以确认这是要删除的服务器。

    ![confirm-delete](./media/howto-create-manage-server-portal/confirm-delete.png)

    > [!NOTE]
    > 删除服务器是不可逆的。

3. 选择“删除”。


## <a name="next-steps"></a>后续步骤
- 了解[备份和服务器还原](howto-restore-server-portal.md)
- 了解[Azure Database for MySQL 中的优化和监视选项](concepts-monitoring.md)