---
title: 管理对工作区、数据和管道的访问
description: 了解如何在 Azure Synapse Analytics 工作区（预览版）中管理对工作区、数据和管道的访问控制。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 52cf3af4b4ab84c12b2e1b5d7f076c43e652157d
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2020
ms.locfileid: "90031307"
---
# <a name="manage-access-to-workspaces-data-and-pipelines"></a>管理对工作区、数据和管道的访问

了解如何在 Azure Synapse Analytics 工作区（预览版）中管理对工作区、数据和管道的访问控制。

> [!NOTE]
> 在正式版 (GA) 中，RBAC 将会引入特定于 Synapse 的 Azure 角色，因此其功能会更完备

## <a name="access-control-for-workspace"></a>对工作区的访问控制

若要在 Azure Synapse 工作区中进行生产部署，我们建议组织好环境，以便轻松预配用户和管理员。

> [!NOTE]
> 本文采用的方法是创建多个安全组，然后将工作区配置为以一致方式使用这些安全组。 设置组后，管理员只需管理安全组中的成员身份。

### <a name="step-1-set-up-security-groups-with-names-following-this-pattern"></a>步骤 1：使用遵循此模式的名称设置安全组

1. 创建名为 `Synapse_WORKSPACENAME_Users` 的安全组
2. 创建名为 `Synapse_WORKSPACENAME_Admins` 的安全组
3. 为 `Synapse_WORKSPACENAME_Users` 添加了 `Synapse_WORKSPACENAME_Admins`

> [!NOTE]
> 通过[本文](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal)了解如何创建安全组。
>
> 通过[本文](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-membership-azure-portal)了解如何添加另一个安全组中的安全组。
>
> WORKSPACENAME - 应将此部分替换为实际的工作区名称。

### <a name="step-2-prepare-the-default-adls-gen2-account"></a>步骤 2：准备默认的 ADLS Gen2 帐户

预配工作区时，必须为工作区要使用的文件系统选取 [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction) 帐户和容器。

1. 打开 [Azure 门户](https://portal.azure.com)
2. 导航到 Azure Data Lake Storage Gen2 帐户
3. 导航到为 Azure Synapse 工作区选取的容器（文件系统）
4. 单击“访问控制(标识和访问管理)”
5. 分配以下角色：
   1. 将“读取者”角色分配到：`Synapse_WORKSPACENAME_Users`
   2. 将“存储 Blob 数据所有者”角色分配到：`Synapse_WORKSPACENAME_Admins`
   3. 将“存储 Blob 数据参与者”角色分配到：`Synapse_WORKSPACENAME_Users`
   4. 将“存储 Blob 数据所有者”角色分配到：`WORKSPACENAME`

> [!NOTE]
> WORKSPACENAME - 应将此部分替换为实际的工作区名称。

### <a name="step-3-configure-the-workspace-admin-list"></a>步骤 3：配置工作区管理员列表

1. 转到 [Azure Synapse Web UI](https://web.azuresynapse.net)
2. 转到“管理”  > “安全性” > “访问控制”  
3. 单击“添加管理员”，然后选择 `Synapse_WORKSPACENAME_Admins`

### <a name="step-4-configure-sql-admin-access-for-the-workspace"></a>步骤 4：配置对工作区的 SQL 管理员访问权限

1. 转到 [Azure 门户](https://portal.azure.com)
2. 导航到你的工作区
3. 转到“设置” > “Active Directory 管理员” 
4. 单击“设置管理员”
5. 选择 `Synapse_WORKSPACENAME_Admins`
6. 单击“选择”
7. 单击“保存”

> [!NOTE]
> WORKSPACENAME - 应将此部分替换为实际的工作区名称。

### <a name="step-5-add-and-remove-users-and-admins-to-security-groups"></a>步骤 5：在安全组中添加和删除用户与管理员

1. 将需要管理访问权限的用户添加到 `Synapse_WORKSPACENAME_Admins`
2. 将所有其他用户添加到 `Synapse_WORKSPACENAME_Users`

> [!NOTE]
> 通过[本文](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-members-azure-portal)了解如何将用户作为成员添加到安全组
> 
> WORKSPACENAME - 应将此部分替换为实际的工作区名称。

## <a name="access-control-to-data"></a>对数据的访问控制

对基础数据的访问控制划分为三个部分：

- 对存储帐户的数据平面访问权限（已在上面的步骤 2 中配置）
- 对 SQL 数据库的数据平面访问权限（适用于 SQL 池和 SQL 按需版本）
- 通过存储帐户创建 SQL 按需版本数据库的凭据

## <a name="access-control-to-sql-databases"></a>对 SQL 数据库的访问控制

> [!TIP]
> 需要为每个 SQL 数据库运行以下步骤，以向用户授予对所有 SQL 数据库的访问权限（[服务器级别权限](#server-level-permission)部分中的除外，其中可为用户分配 sysadmin 角色）。

### <a name="sql-on-demand"></a>SQL 按需版本

在本部分中，可以找到有关如何向用户授予对特定数据库的权限或完全服务器权限的示例。

#### <a name="database-level-permission"></a>数据库级别权限

若要向用户授予对单个 SQL 按需版本数据库的访问权限，请执行此示例中的步骤：

1. 创建登录名

    ```sql
    use master
    go
    CREATE LOGIN [alias@domain.com] FROM EXTERNAL PROVIDER;
    go
    ```

2. 创建用户

    ```sql
    use yourdb -- Use your DB name
    go
    CREATE USER alias FROM LOGIN [alias@domain.com];
    ```

3. 将用户添加到指定角色的成员

    ```sql
    use yourdb -- Use your DB name
    go
    alter role db_owner Add member alias -- Type USER name from step 2
    ```

> [!NOTE]
> 将别名替换为要向其授予访问权限的用户的别名，并将域名替换为正在使用的公司域。

#### <a name="server-level-permission"></a>服务器级别权限

若要向用户授予对所有 SQL 按需版本数据库的完全访问权限，请执行此示例中的步骤：

```sql
CREATE LOGIN [alias@domain.com] FROM EXTERNAL PROVIDER;
ALTER SERVER ROLE  sysadmin  ADD MEMBER [alias@domain.com];
```

### <a name="sql-pools"></a>SQL 池

若要向用户授予对单个 SQL 数据库的访问权限，请执行以下步骤：

1. 针对上下文选择器（用于选择数据库的下拉列表）中的所需数据库运行以下命令，在数据库中创建用户：

    ```sql
    --Create user in SQL DB
    CREATE USER [<alias@domain.com>] FROM EXTERNAL PROVIDER;
    ```

2. 向用户授予有权访问数据库的角色：

    ```sql
    --Create user in SQL DB
    EXEC sp_addrolemember 'db_owner', '<alias@domain.com>';
    ```

> [!IMPORTANT]
> 如果授予 db_owner 权限不符合需要，可为 db_datareader 和 db_datawriter 授予读取/写入权限。  
> 要使 Spark 用户能够直接使用 Spark 在 SQL 池中读取/写入数据，需要授予 db_owner 权限。

创建用户后，验证 SQL 按需版本是否可以查询存储帐户。

## <a name="access-control-to-workspace-pipeline-runs"></a>对工作区管道运行的访问控制

### <a name="workspace-managed-identity"></a>工作区托管标识

> [!IMPORTANT]
> 若要成功运行包含引用 SQL 池的数据集或活动的管道，需要直接向工作区标识授予对 SQL 池的访问权限。

针对每个 SQL 池运行以下命令，以允许工作区托管标识对 SQL 池数据库运行管道：

```sql
--Create user in DB
CREATE USER [<workspacename>] FROM EXTERNAL PROVIDER;

--Granting permission to the identity
GRANT CONTROL ON DATABASE::<SQLpoolname> TO <workspacename>;
```

针对同一个 SQL 池运行以下脚本可以删除此权限：

```sql
--Revoking permission to the identity
REVOKE CONTROL ON DATABASE::<SQLpoolname> TO <workspacename>;

--Deleting the user in the DB
DROP USER [<workspacename>];
```

## <a name="next-steps"></a>后续步骤

有关 Synapse 工作区托管标识的概述，请参阅 [Azure Synapse 工作区托管标识](../security/synapse-workspace-managed-identity.md)。 若要详细了解数据库主体，请参阅[主体](https://msdn.microsoft.com/library/ms181127.aspx)。 可以在[数据库角色](https://msdn.microsoft.com/library/ms189121.aspx)一文中找到有关数据库角色的更多信息。
