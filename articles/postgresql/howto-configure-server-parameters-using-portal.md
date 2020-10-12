---
title: 配置服务器参数-Azure 门户-Azure Database for PostgreSQL-单一服务器
description: 本文介绍如何通过 Azure 门户在 Azure Database for PostgreSQL 中配置 Postgres 参数。
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 02/28/2018
ms.openlocfilehash: e1b40e3116d56e87a2f397350ef2ba5510e04c0c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91707687"
---
# <a name="configure-server-parameters-in-azure-database-for-postgresql---single-server-via-the-azure-portal"></a>通过 Azure 门户在 Azure Database for PostgreSQL - 单一服务器中配置服务器参数 
可以通过 Azure 门户列出、显示和更新 Azure Database for PostgreSQL 的配置参数。

## <a name="prerequisites"></a>必备条件
若要逐步执行本操作方法指南，需要：
- [Azure Database for PostgreSQL 服务器](quickstart-create-server-database-portal.md)

## <a name="viewing-and-editing-parameters"></a>查看和编辑参数
1. 打开 [Azure 门户](https://portal.azure.com)。

2. 选择你的 Azure Database for PostgreSQL 服务器。

3. 在“设置”部分下，选择“服务器参数”。   该页显示了参数的列表及其值和说明。
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/3-overview-of-parameters.png" alt-text="参数概述页":::

4. 选择**下拉**按钮查看 client_min_messages 等枚举类型的参数的可能值。
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/4-enum-drop-down.png" alt-text="参数概述页":::

5. 选择“i”（信息）按钮或将鼠标悬停于其上，查看 cpu_index_tuple_cost 等数字参数的可能值范围。 
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/4-information-button.png" alt-text="参数概述页":::

6. 如果需要，可使用**搜索框**缩小特定参数的搜索范围。 搜索根据参数的名称和说明执行。
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/5-search.png" alt-text="参数概述页":::

7. 更改想要调整的参数值。 在此会话中所做的更改将以紫色突出显示。 更改值之后，可选择“保存”。  也可以放弃所做的更改  。
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/6-save-and-discard-buttons.png" alt-text="参数概述页":::

8. 保存参数的新值后，随时可以通过选择“全部重置为默认设置”，将所有设置还原为默认值。 
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/7-reset-to-default-button.png" alt-text="参数概述页":::

## <a name="next-steps"></a>后续步骤
学习内容：
- [Azure Database for PostgreSQL 中的服务器参数概述](concepts-servers.md)
- [使用 Azure CLI 配置参数](howto-configure-server-parameters-using-cli.md)
