---
title: 管理服务器 Azure 门户-Azure Database for PostgreSQL-灵活的服务器
description: 了解如何从 Azure 门户管理 Azure Database for PostgreSQL 灵活的服务器。
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.custom: mvc
ms.openlocfilehash: 1ac418d855696138341115412dc7e2601d4cf3a1
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91961402"
---
# <a name="manage-an-azure-database-for-postgresql---flexible-server-using-the-azure-portal"></a>使用 Azure 门户管理 Azure Database for PostgreSQL 灵活的服务器

> [!IMPORTANT]
> Azure Database for PostgreSQL 灵活服务器以预览版提供

本文介绍如何管理 Azure Database for PostgreSQL 灵活的服务器。 管理任务包括计算和存储缩放、管理员密码重置，以及查看服务器详细信息。

## <a name="sign-in"></a>登录

登录 [Azure 门户](https://portal.azure.com)。 在 Azure 门户中转到您的灵活服务器资源。

## <a name="scale-compute-and-storage"></a>缩放计算和存储

创建服务器后，可以根据需求的变化在不同 [定价层](https://azure.microsoft.com/pricing/details/postgresql/) 之间进行缩放。 还可以通过增加或减少 Vcore 来增加或减少计算和内存。

> [!NOTE]
> 存储不能缩小到较小的值。

1. 在 Azure 门户中选择服务器。 选择位于 "**设置**" 部分的 "**计算 + 存储**"。
2. 你可以使用更高的计算**层来更改****计算层** **vCore**，或者通过将存储或 vcore 增加到所需值，在同一层内向上扩展服务器。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/howto-manage-server-portal/scale-server.png" alt-text="扩展存储灵活服务器":::

> [!Important]
> - 存储无法纵向缩减。
> - 缩放 vCore 会导致服务器重启。

3. 选择“确定”以保存更改。 

## <a name="reset-admin-password"></a>重置管理员密码

可以通过 Azure 门户更改管理员角色的密码。

1. 在 Azure 门户中选择服务器。 在“概览”窗口中，选择“重置密码”   。
2. 输入新密码并确认。 文本框会就密码复杂性要求提示你。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/howto-manage-server-portal/reset-password.png" alt-text="扩展存储灵活服务器" 以保存新密码。

## <a name="delete-a-server"></a>删除服务器

不再需要服务器时，可以将其删除。

1. 在 Azure 门户中选择服务器。 在“概览”窗口中，选择“删除”。  
2. 在输入框中键入服务器的名称，确认要删除该服务器。

   :::image type="content" source="./media/howto-manage-server-portal/delete-server.png" alt-text="扩展存储灵活服务器":::

   > [!IMPORTANT]
   > 删除服务器的操作不可逆。

  > [!div class="mx-imgBorder"]
  > ![删除灵活的服务器](./media/howto-manage-server-portal/delete-server.png)  

3. 选择“删除” 。

## <a name="next-steps"></a>后续步骤

- [了解备份和还原概念](concepts-backup-restore.md)
- [优化和监视服务器](concepts-monitoring.md)
