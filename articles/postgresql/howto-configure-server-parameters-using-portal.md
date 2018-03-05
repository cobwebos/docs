---
title: "通过 Azure 门户在 Azure Database for PostgreSQL 中配置服务器参数"
description: "本文介绍如何通过 Azure 门户在 Azure Database for PostgreSQL 中配置服务器参数。"
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 6d43cac79c19e117385549b1678a464dc5731bd7
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2018
---
# <a name="configure-server-parameters-in-azure-portal"></a>在 Azure 门户中配置服务器参数
可以通过 Azure 门户列出、显示和更新 Azure Database for PostgreSQL 的配置参数。

## <a name="prerequisites"></a>先决条件
若要逐步执行本操作方法指南，需要：
- [Azure Database for PostgreSQL 服务器](quickstart-create-server-database-portal.md)

## <a name="viewing-and-editing-parameters"></a>查看和编辑参数
1. 打开 [Azure 门户](https://portal.azure.com)。

2. 选择你的 Azure Database for PostgreSQL 服务器。

3. 在“设置”部分下，选择“服务器参数”。 该页显示了参数的列表及其值和说明。
![参数概述页](./media/howto-configure-server-parameters-in-portal/3-overview-of-parameters.png)

4. 选择**下拉**按钮查看 client_min_messages 等枚举类型的参数的可能值。
![枚举下拉按钮](./media/howto-configure-server-parameters-in-portal/4-enum-drop-down.png)

5. 选择“i”（信息）按钮或将鼠标悬停于其上，查看 cpu_index_tuple_cost 等数字参数的可能值范围。
![信息按钮](./media/howto-configure-server-parameters-in-portal/4-information-button.png)

6. 如果需要，可使用**搜索框**缩小特定参数的搜索范围。 搜索根据参数的名称和说明执行。
![搜索结果](./media/howto-configure-server-parameters-in-portal/5-search.png)

7. 更改想要调整的参数值。 在此会话中所做的更改将以紫色突出显示。 更改值之后，可选择“保存”。 也可以放弃所做的更改。
![保存或放弃更改](./media/howto-configure-server-parameters-in-portal/6-save-and-discard-buttons.png)

8. 保存参数的新值后，随时可以通过选择“全部重置为默认设置”，将所有设置还原为默认值。
![全部重置为默认设置](./media/howto-configure-server-parameters-in-portal/7-reset-to-default-button.png)

## <a name="next-steps"></a>后续步骤
学习内容：
- [Azure Database for PostgreSQL 中的服务器参数概述](concepts-servers.md)
- [使用 Azure CLI 配置参数](howto-configure-server-parameters-using-cli.md)
