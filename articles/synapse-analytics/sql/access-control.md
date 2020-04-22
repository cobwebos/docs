---
title: 管理对工作区、数据和管道的访问
description: 了解如何在 Azure Synapse Analytics 工作区（预览版）中管理对工作区、数据和管道的访问控制。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 89d2105ab080309639c4341072c3f5f36608dfce
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421101"
---
# <a name="manage-access-to-workspaces-data-and-pipelines"></a>管理对工作区、数据和管道的访问

了解如何在 Azure Synapse Analytics 工作区（预览版）中管理对工作区、数据和管道的访问控制。

> [!NOTE]
> 在正式版 (GA) 中，RBAC 将会引入特定于 Synapse 的 Azure RBAC 角色，因此其功能会更完备。

## <a name="access-control-for-workspace"></a>对工作区的访问控制

若要在 Azure Synapse 工作区中进行生产部署，我们建议组织好环境，以便轻松预配用户和管理员。

> [!NOTE]
> 本文采用的方法是创建多个安全组，然后将工作区配置为以一致方式使用这些安全组。 设置安全组后，管理员只需组中的成员身份。

### <a name="step-1-set-up-security-groups-with-names-following-this-pattern"></a>步骤 1：使用遵循此模式的名称设置安全组

1. 创建名为 `Synapse_WORKSPACENAME_Users` 的安全组
2. 创建名为 `Synapse_WORKSPACENAME_Admins` 的安全组
3. 为 `ProjectSynapse_WORKSPACENAME_Users` 添加了 `Synapse_WORKSPACENAME_Admins`

### <a name="step-2-prepare-the-default-adls-gen2-account"></a>步骤 2：准备默认的 ADLS Gen2 帐户

预配工作区时，必须为工作区要使用的文件系统选取 ADLSGEN2 帐户和容器。

1. 打开 [Azure 门户](https://portal.azure.com)
2. 导航到 ADLSGEN2 帐户
3. 导航到为 Azure Synapse 工作区选取的容器（文件系统）
4. 单击“访问控制(标识和访问管理)” 
5. 分配以下角色：
   1. “读取者”角色：  `Synapse_WORKSPACENAME_Users`
   2. “存储 Blob 数据所有者”角色：  `Synapse_WORKSPACENAME_Admins`
   3. “存储 Blob 数据参与者”角色：  `Synapse_WORKSPACENAME_Users`
   4. “存储 Blob 数据所有者”角色：  `WORKSPACENAME`
  
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

### <a name="step-5-add-and-remove-users-and-admins-to-security-groups"></a>步骤 5：在安全组中添加和删除用户与管理员

1. 将需要管理访问权限的用户添加到 `Synapse_WORKSPACENAME_Admins`
2. 将所有其他用户添加到 `Synapse_WORKSPACENAME_Users`

## <a name="access-control-to-data"></a>对数据的访问控制

对基础数据的访问控制划分为三个部分：

- 对存储帐户的数据平面访问权限（已在上面的步骤 2 中配置）
- 对 SQL 数据库的数据平面访问权限（适用于 SQL 池和 SQL 按需版本）
- 通过存储帐户创建 SQL 按需版本数据库的凭据

## <a name="access-control-to-sql-databases"></a>对 SQL 数据库的访问控制

> [!TIP]
> 需要对每个 SQL 数据库运行以下步骤，才能向用户授予对所有 SQL 数据库的访问权限。 

### <a name="sql-on-demand"></a>SQL 按需版本

若要向用户授予对单个 SQL 按需版本数据库的访问权限，请执行此示例中的步骤： 

1. 创建登录名

    ```sql
    use master
    go
    CREATE LOGIN [John.Thomas@microsoft.com] FROM EXTERNAL PROVIDER;
    go
    ```

2. 创建用户

    ```sql
    use yourdb -- Use your DB name
    go
    CREATE USER john FROM LOGIN [John.Thomas@microsoft.com];
    ```

3. 将用户添加到指定角色的成员

    ```sql
    use yourdb -- Use your DB name
    go
    alter role db_owner Add member john -- Type USER name from step 2
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

创建用户后，验证 SQL 按需版本是否可以查询存储帐户：

- 针对 SQL 按需版本的 master 数据库运行以下命令： 

    ```sql
    CREATE CREDENTIAL [https://<storageaccountname>.dfs.core.windows.net]
    WITH IDENTITY='User Identity';
    ```

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

有关 Synapse SQL 中的访问和控制的概述，请参阅 [Synapse SQL 访问控制](../sql/access-control.md)。 若要详细了解数据库主体，请参阅[主体](https://msdn.microsoft.com/library/ms181127.aspx)。 可以在[数据库角色](https://msdn.microsoft.com/library/ms189121.aspx)一文中找到有关数据库角色的更多信息。
