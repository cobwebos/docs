---
title: 创建用户 - 超大规模（Citus） - 用于后格雷SQL的 Azure 数据库
description: 本文介绍如何创建新用户帐户，以便与 Azure 数据库进行后格雷SQL - 超大规模 （Citus） 的交互。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 1/8/2019
ms.openlocfilehash: 674fd4372bdf7c3782d18aaf04b48eb0067a9b2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77484921"
---
# <a name="create-users-in-azure-database-for-postgresql---hyperscale-citus"></a>在 Azure 数据库中为后格雷SQL - 超缩放（Citus） 创建用户

> [!NOTE]
> 术语"用户"是指超大规模 （Citus） 服务器组中的用户。 要了解 Azure 订阅用户及其权限，请访问[基于 Azure 角色的访问控制 （RBAC） 一文](../role-based-access-control/built-in-roles.md)或查看[如何自定义角色](../role-based-access-control/custom-roles.md)。

## <a name="the-server-admin-account"></a>服务器管理员帐户

PostgreSQL 引擎使用[角色](https://www.postgresql.org/docs/current/sql-createrole.html)来控制对数据库对象的访问，新创建的超大规模 （Citus） 服务器组附带了几个预定义的角色：

* [默认 PostgreSQL 角色](https://www.postgresql.org/docs/current/default-roles.html)
* `azure_pg_admin`
* `postgres`
* `citus`

由于 Hyperscale 是托管 PaaS 服务，因此只有 Microsoft`postgres`才能使用超级用户角色登录。 对于有限的管理访问，超大规模提供该`citus`角色。

角色的权限`citus`：

* 读取所有配置变量，甚至通常仅对超级用户可见的变量也是如此。
* 阅读所有\_pg\_ \* stat 视图并使用各种与统计信息相关的扩展-甚至视图或扩展通常只对超级用户可见。
* 执行监视功能，这些功能可能会在表上获取 ACCESS SHARE 锁，可能长时间运行。
* [创建 PostgreSQL 扩展](concepts-hyperscale-extensions.md)（因为角色是 的成员`azure_pg_admin`）。

值得注意的是，该`citus`角色有一些限制：

* 无法创建角色
* 无法创建数据库

## <a name="how-to-create-additional-user-roles"></a>如何创建其他用户角色

如前所述，`citus`管理员帐户缺少创建其他用户的权限。 要添加用户，请使用 Azure 门户界面。

1. 转到超大规模服务器组**的角色**页面，然后单击 **" 添加**：

   ![角色页](media/howto-hyperscale-create-users/1-role-page.png)

2. 输入角色名称和密码。 单击“保存”。****

   ![添加角色](media/howto-hyperscale-create-users/2-add-user-fields.png)

将在服务器组的协调器节点上创建用户，并传播到所有辅助节点。 通过 Azure 门户创建的角色具有`LOGIN`该属性，这意味着这些角色是真正的用户，可以登录到数据库。

## <a name="how-to-modify-privileges-for-user-role"></a>如何修改用户角色的权限

新的用户角色通常用于提供具有受限权限的数据库访问权限。 要修改用户权限，请使用标准 PostgreSQL 命令，使用 PgAdmin 或 psql 等工具。 （请参阅在超量程（Citus）快速启动中[连接到 psql。](quickstart-create-hyperscale-portal.md#connect-to-the-database-using-psql)

例如，要允许`db_user`读取`mytable`，请授予权限：

```sql
GRANT SELECT ON mytable TO db_user;
```

超大规模 （Citus） 通过整个群集传播单表 GRANT 语句，将它们应用于所有辅助节点。 但是，系统范围的 GRANT（例如，对于架构中的所有表）需要在每个日期节点上运行。  使用`run_command_on_workers()`帮助器功能：

```sql
-- applies to the coordinator node
GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;

-- make it apply to workers as well
SELECT run_command_on_workers(
  'GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;'
);
```

## <a name="how-to-delete-a-user-role-or-change-their-password"></a>如何删除用户角色或更改其密码

要更新用户，请访问超大规模服务器组**的角色**页面，然后单击用户旁边的省略号 **...** 椭圆将打开一个菜单以删除用户或重置其密码。

   ![编辑角色](media/howto-hyperscale-create-users/edit-role.png)

该`citus`角色是特权，无法删除。

## <a name="next-steps"></a>后续步骤

打开新用户计算机的 IP 地址的防火墙，使他们能够连接：[使用 Azure 门户创建和管理超大规模 （Citus） 防火墙规则](howto-hyperscale-manage-firewall-using-portal.md)。

有关数据库用户帐户管理的详细信息，请参阅 PostgreSQL 产品文档：

* [数据库角色和特权](https://www.postgresql.org/docs/current/static/user-manag.html)
* [格兰特语法](https://www.postgresql.org/docs/current/static/sql-grant.html)
* [特权](https://www.postgresql.org/docs/current/static/ddl-priv.html)
