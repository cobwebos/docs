---
title: 基础结构双加密-Azure 门户-Azure Database for PostgreSQL
description: 了解如何为 Azure Database for PostgreSQL 设置和管理基础结构双加密。
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: 362c051cf1dd7e97430bd6afaf4821a9c960b71d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90901543"
---
# <a name="infrastructure-double-encryption-for-azure-database-for-postgresql"></a>Azure Database for PostgreSQL 的基础结构双加密

了解如何使用为你的 Azure Database for PostgreSQL 设置和管理基础结构双加密的方式。

## <a name="prerequisites"></a>必备条件

* 必须有一个 Azure 订阅，并且是该订阅的管理员。

## <a name="create-an-azure-database-for-postgresql-server-with-infrastructure-double-encryption---portal"></a>使用基础结构双加密创建 Azure Database for PostgreSQL 服务器-门户

按照以下步骤创建一个 Azure Database for MySQL 服务器，其中包含 Azure 门户的基础结构双加密：

1. 在门户左上角选择“创建资源”  (+)。

2. 选择“数据库” > “用于 PostgreSQL 的 Azure 数据库”。 您还可以在搜索框中输入 PostgreSQL 来查找该服务。 启用了 **单一服务器** 部署选项。

   :::image type="content" source="./media/quickstart-create-database-portal/1-create-database.png" alt-text="菜单中的 Azure Database for PostgreSQL&quot;:::

3. 提供服务器的基本信息。 选择 &quot; **其他设置** &quot;，并启用 &quot; **基础结构双加密** " 复选框，以设置参数。

    :::image type="content" source="./media/howto-infrastructure-double-encryption/infrastructure-encryption-selected.png" alt-text="菜单中的 Azure Database for PostgreSQL&quot;:::

3. 提供服务器的基本信息。 选择 &quot; **其他设置** &quot;，并启用 &quot; **基础结构双加密** ":::

4. 选择“查看 + 创建”  以预配服务器。

    :::image type="content" source="./media/howto-infrastructure-double-encryption/infrastructure-encryption-summary.png" alt-text="菜单中的 Azure Database for PostgreSQL&quot;:::

3. 提供服务器的基本信息。 选择 &quot; **其他设置** &quot;，并启用 &quot; **基础结构双加密** " 边栏选项卡中的状态来验证基础结构双加密。

    :::image type="content" source="./media/howto-infrastructure-double-encryption/infrastructure-encryption-validation.png" alt-text="菜单中的 Azure Database for PostgreSQL&quot;:::

3. 提供服务器的基本信息。 选择 &quot; **其他设置** &quot;，并启用 &quot; **基础结构双加密** ":::

## <a name="create-an-azure-database-for-postgresql-server-with-infrastructure-double-encryption---cli"></a>使用基础结构双加密创建 Azure Database for PostgreSQL 服务器-CLI

按照以下步骤创建一个 Azure Database for MySQL 服务器，其中包含来自 CLI 的基础结构双加密：

此示例在位置创建名为的资源组 `myresourcegroup` `westus` 。

```azurecli-interactive
az group create --name myresourcegroup --location westus
```
以下示例在名为 "PostgreSQL 11 服务器" 的资源组中创建名为的 `mydemoserver` " `myresourcegroup` 服务器管理员登录名" `myadmin` 。 这是第 4 代常规用途服务器，带有 2 个 vCore**** ********。 这还会为创建的服务器启用基础结构双加密。 用自己的值替换 `<server_admin_password>`。

```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen4_2 --version 11 --infrastructure-encryption >Enabled/Disabled>
```

## <a name="next-steps"></a>后续步骤

若要了解有关数据加密的详细信息，请参阅 [Azure Database for PostgreSQL 数据基础结构双加密](concepts-Infrastructure-double-encryption.md)"。

