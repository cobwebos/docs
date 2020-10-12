---
title: 管理服务器 Azure 门户-Azure Database for MySQL 灵活服务器
description: 了解如何从 Azure 门户管理 Azure Database for MySQL 灵活的服务器。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 7a01863b3a0c29e94550be67ca957655cff32660
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90933393"
---
# <a name="manage-an-azure-database-for-mysql---flexible-server-preview-using-azure-portal"></a>使用 Azure 门户管理 Azure Database for MySQL 灵活的服务器 (预览版) 


> [!IMPORTANT]
> Azure Database for MySQL 灵活服务器当前以公共预览版提供。

本文介绍如何 (预览版) 管理 Azure Database for MySQL 灵活的服务器。 管理任务包括计算和存储缩放、rest 服务器管理员密码和删除服务器。

## <a name="sign-in"></a>登录
登录 [Azure 门户](https://portal.azure.com)。 在 Azure 门户中转到您的灵活服务器资源。

## <a name="scale-compute-and-storage"></a>缩放计算和存储

创建服务器后，可以根据需求的变化在不同 [定价层](https://azure.microsoft.com/pricing/details/mysql/) 之间进行缩放。 还可以通过增加或减少 Vcore 来增加或减少计算和内存。

1. 在 Azure 门户中选择服务器。 选择位于 "**设置**" 部分的 "**计算 + 存储**"。

2. 你可以使用更高的计算**层来更改****计算层**， **vCore**，或通过将存储或 vcore 增加到所需的值来扩展服务器。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/howto-manage-server-portal/scale-server.png" alt-text="扩展存储灵活服务器":::

   > [!Important]
   > - 存储无法纵向缩减。
   > - 缩放 vCore 会导致服务器重启。

3. 选择“确定”以保存更改。 

## <a name="reset-admin-password"></a>重置管理员密码

可以通过 Azure 门户更改管理员角色的密码。

1. 在 Azure 门户中选择服务器。 在 " **概述** " 窗口中，选择 " **重置密码**"。

2. 输入新密码并确认。 文本框会就密码复杂性要求提示你。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/howto-manage-server-portal/reset-password.png" alt-text="扩展存储灵活服务器" 以保存新密码。

## <a name="delete-a-server"></a>删除服务器

不再需要服务器时，可以将其删除。

1. 在 Azure 门户中选择服务器。 在“概览”窗口中，选择“删除”。  

2. 在输入框中键入服务器的名称，确认要删除该服务器。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/howto-manage-server-portal/delete-server.png" alt-text="扩展存储灵活服务器":::

   > [!NOTE]
   > 删除服务器的操作不可逆。

3. 选择“删除”。 

## <a name="next-steps"></a>后续步骤
- [了解如何启动或停止服务器](how-to-stop-start-server-portal.md)
- [了解如何还原服务器](how-to-restore-server-portal.md)
- [排查连接问题](how-to-troubleshoot-common-connection-issues.md)

