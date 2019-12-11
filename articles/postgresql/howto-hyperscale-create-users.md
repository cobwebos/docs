---
title: 创建用户-超大规模（Citus）-Azure Database for PostgreSQL
description: 本文介绍如何创建新的用户帐户，以与 Azure Database for PostgreSQL 超大规模（Citus）进行交互。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: d093d4c23fcc44e7e9f3461f875607926f4b612d
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977567"
---
# <a name="create-users-in-azure-database-for-postgresql---hyperscale-citus"></a>在 Azure Database for PostgreSQL 中创建用户超大规模（Citus）

本文介绍如何创建超大规模（Citus）服务器组内的用户。 若要改为了解 Azure 订阅用户及其权限，请访问[azure 基于角色的访问控制（RBAC）一文](../role-based-access-control/built-in-roles.md)，或查看[如何自定义角色](../role-based-access-control/custom-roles.md)。

## <a name="the-server-admin-account"></a>服务器管理员帐户

新创建的超大规模（Citus）服务器组附带了几个预定义的角色：

* [默认 PostgreSQL 角色](https://www.postgresql.org/docs/current/default-roles.html)
* *azure_pg_admin*
* postgres
* *citus*

PostgreSQL 引擎使用权限来控制对数据库对象的访问权限，如 [PostgreSQL 产品文档](https://www.postgresql.org/docs/current/static/sql-createrole.html)中所述。
你的服务器管理员用户*citus*是*azure_pg_admin*角色的成员。
但是，它不是*postgres* （超级用户）角色的一部分。  由于超大规模是托管的 PaaS 服务，因此只有 Microsoft 是超级用户角色的一部分。 *Citus*用户的权限有限，无法创建新数据库。

## <a name="how-to-create-additional-users"></a>如何创建其他用户

*Citus*管理员帐户没有创建其他用户的权限。 若要添加用户，请使用 Azure 门户接口。

1. 中转到超大规模服务器组的 "**角色**" 页，然后单击 " **+ 添加**"：

   !["角色" 页](media/howto-hyperscale-create-users/1-role-page.png)

2. 输入 "角色名称" 和 "密码"。 单击“保存”。

   ![添加角色](media/howto-hyperscale-create-users/2-add-user-fields.png)

将在服务器组的协调器节点上创建用户，并将其传播到所有工作节点。

## <a name="how-to-delete-a-user-or-change-their-password"></a>如何删除用户或更改其密码

中转到超大规模服务器组的 "**角色**" 页，然后单击 "用户" 旁边**的省略号 "..."** 。 省略号将打开一个菜单以删除用户或重置其密码。

   ![编辑角色](media/howto-hyperscale-create-users/edit-role.png)

*Citus*角色具有特权，不能删除。

## <a name="how-to-modify-privileges-for-role"></a>如何修改角色的权限

新角色通常用于提供具有受限权限的数据库访问权限。 若要修改用户权限，请使用 PgAdmin 或 psql 等工具的标准 PostgreSQL 命令。 （请参阅在超大规模（Citus）快速入门中[连接 with psql](quickstart-create-hyperscale-portal.md#connect-to-the-database-using-psql) 。）

例如，若要允许*db_user*读取*mytable*，请授予权限：

```sql
GRANT SELECT ON mytable TO db_user;
```

超大规模（Citus）通过整个群集传播单表 GRANT 语句，并将其应用于所有工作节点。 但是，需要在每个日期节点上运行系统范围的授予（例如，对于架构中的所有表）。  使用*run_command_on_workers （）* helper 函数：

```sql
-- applies to the coordinator node
GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;

-- make it apply to workers as well
SELECT run_command_on_workers(
  'GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;'
);
```

## <a name="next-steps"></a>后续步骤

为新用户计算机的 IP 地址打开防火墙，使其能够连接：[使用 Azure 门户创建和管理超大规模（Citus）防火墙规则](howto-hyperscale-manage-firewall-using-portal.md)。

有关数据库用户帐户管理的详细信息，请参阅 PostgreSQL 产品文档：

* [数据库角色和特权](https://www.postgresql.org/docs/current/static/user-manag.html)
* [GRANT 语法](https://www.postgresql.org/docs/current/static/sql-grant.html)
* [权限](https://www.postgresql.org/docs/current/static/ddl-priv.html)
