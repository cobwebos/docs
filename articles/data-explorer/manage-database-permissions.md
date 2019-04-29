---
title: 在 Azure 数据资源管理器中管理数据库权限
description: 本文介绍了 Azure 数据资源管理器中数据库和表基于角色的访问控制。
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 36e1bb77be1e825e42f0e5d25457214a8b5f882d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60758731"
---
# <a name="manage-azure-data-explorer-database-permissions"></a>管理 Azure 数据资源管理器数据库权限

通过 Azure 数据资源管理器，可以使用基于角色的访问控制模型来控制对数据库和表的访问。 在此模型下，主体（用户、组和应用）将映射到角色。 主体可以根据分配的角色访问资源。

本文介绍可用角色以及如何使用 Azure 门户和 Azure 数据资源管理器管理命令将主体分配给这些角色。

## <a name="roles-and-permissions"></a>角色和权限

Azure 数据资源管理器具有以下角色：

|角色                       |权限                                                                        |
|---------------------------|-----------------------------------------------------------------------------------|
|数据库管理员             |可以在特定数据库的范围内执行任何操作。|
|数据库用户              |可以读取数据库中的所有数据和元数据。 此外，他们可以创建表（成为该表的表管理员）和数据库中的函数。|
|数据库查看器            |可以读取数据库中的所有数据和元数据。|
|数据库引入器          |可以将数据提取到数据库中的所有现有表中，但不能查询数据。|
|数据库监视器           |可以在数据库及其子实体的上下文中执行“.show ...”命令。|
|表管理员                |可以在特定表的范围内执行任何操作。 |
|表引入器             |可以在特定表的范围内引入数据，但不能查询数据。|

## <a name="manage-permissions-in-the-azure-portal"></a>在 Azure 门户中管理权限

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 导航到 Azure 数据资源管理器群集。

1. 在“概述”部分中，选择想要管理权限的数据库。

    ![选择数据库](media/manage-database-permissions/select-database.png)

1. 选择“权限”，然后选择“添加”。

    ![数据库权限](media/manage-database-permissions/database-permissions.png)

1. 在“添加数据库权限”下，选择要分配给主体的角色，然后选择“选择主体”。

    ![添加数据库权限](media/manage-database-permissions/add-permission.png)

1. 查找主体，请选择它，然后选择“选择”。

    ![在 Azure 门户中管理权限](media/manage-database-permissions/new-principals.png)

1. 选择“保存”。

    ![在 Azure 门户中管理权限](media/manage-database-permissions/save-permission.png)

## <a name="manage-permissions-with-management-commands"></a>使用管理命令管理权限

1. 登录到 [https://dataexplorer.azure.com](https://dataexplorer.azure.com)，如果尚不可用，请添加群集。

1. 在左侧窗格中，选择相应的数据库。

1. 使用 `.add` 命令将主体分配到角色：`.add database databasename rolename ('aaduser | aadgroup=user@domain.com')`。 若要将用户添加到数据库用户角色，请运行以下命令，替换数据库名称和用户。

    ```Kusto
    .add database <TestDatabase> users ('aaduser=<user@contoso.com>')
    ```

    该命令的输出显示现有用户的列表以及他们在数据库中分配的角色。

## <a name="next-steps"></a>后续步骤

[编写查询](write-queries.md)
