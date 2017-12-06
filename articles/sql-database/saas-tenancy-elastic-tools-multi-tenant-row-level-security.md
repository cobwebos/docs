---
title: "使用 RLS 和弹性数据库工具的多租户应用 | Microsoft Docs"
description: "使用弹性数据库工具以及行级安全性构建具有高可伸缩性数据层的应用程序。"
metakeywords: azure sql database elastic tools multi tenant row level security rls
services: sql-database
documentationcenter: 
manager: jhubbard
author: tmullaney
ms.assetid: e72d3cfe-e9be-4326-b776-9c6d96c0a18e
ms.service: sql-database
ms.custom: scale out apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2017
ms.author: thmullan;torsteng
ms.openlocfilehash: f874800e8647eac6ed94945d79e904df86207b0f
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2017
---
# <a name="multi-tenant-applications-with-elastic-database-tools-and-row-level-security"></a>具有弹性数据库工具和行级安全性的多租户应用程序

[弹性数据库工具](sql-database-elastic-scale-get-started.md)和[行级别安全性 (RLS)][rls] 一起使用时，可通过 Azure SQL 数据库缩放多租户应用程序的数据层。 将这些技术综合在一起，即可生成数据层高度可缩放的应用程序。 该数据层支持多租户分片，并使用 **ADO.NET SqlClient** 或**实体框架**。 有关详细信息，请参阅[具有 Azure SQL 数据库的多租户 SaaS 应用程序的设计模式](saas-tenancy-app-design-patterns.md)。

- **弹性数据库工具**可让开发人员使用 .NET 库和 Azure 服务模板通过标准分片做法横向扩展数据层。 使用[弹性数据库客户端库][s-d-elastic-database-client-library]管理分片有助于自动化和简化通常与分片关联的许多基础结构任务。
- **行级安全性**允许开发人员将多个租户的数据安全地存储在同一数据库中。 RLS 安全策略筛选掉不属于执行查询的租户的行。 将筛选逻辑集中在数据库中可简化维护过程，降低发生安全错误的风险。 替代方法是依赖所有客户端代码来强制实施安全措施，这是很危险的。

将这些功能一起使用，应用程序可以在同一个分片数据库中存储多个租户的数据。 当租户共享数据库时，每个租户的成本会降低。 不过，该应用程序也可为高级租户提供使用单租户分片专用付费版的选择。 单租户隔离的一大优势是更好的性能保证。 在单租户数据库中，没有其他租户来竞争资源。

这样做的目的是使用弹性数据库客户端库[数据依赖路由](sql-database-elastic-scale-data-dependent-routing.md) API，将每个指定的租户自动连接到正确的分片数据库。 只有一个分片包含适用于该指定租户的特定 TenantId 值。 TenantId 是分片键。 建立连接后，就会执行数据库中的 RLS 安全策略，确保指定租户只能访问那些包含其 TenantId 的数据行。

> [!NOTE]
> 租户标识符可能由多个列组成。 在本讨论中，为方便起见，我们采用单列 TenantId（非正式）。

![应用程序体系结构博客][1]

## <a name="download-the-sample-project"></a>下载示例项目

### <a name="prerequisites"></a>先决条件

- 使用 Visual Studio（2012 或更高版本） 
- 创建三个 Azure SQL 数据库 
- 下载示例项目：[Azure SQL 的弹性数据库工具 - 多租户分片](http://go.microsoft.com/?linkid=9888163)
  - 在 **Program.cs** 的开头填入数据库的信息 

此项目通过添加对多租户分片数据库的支持，扩展了 [Azure SQL 的弹性数据库工具 - 实体框架集成](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)中所述的项目。 此项目生成一个简单的控制台应用程序，用于创建博客和文章。 此项目包括四个租户，以及两个多租户分片数据库。 上图说明了此配置。 

构建并运行应用程序。 此运行会引导弹性数据库工具的分片映射管理器，并进行以下测试： 

1. 使用实体框架和 LINQ 创建新博客，并显示每个租户的所有博客文章
2. 使用 ADO.NET SqlClient 显示租户的所有博客文章
3. 尝试插入错误租户的博客，以验证是否会引发错误  

请注意，由于 RLS 尚未在分片数据库中启用，其中的每个测试都会揭露一个问题：租户能够看到不属于他们的博客，并且系统不会阻止应用程序插入错误租户的博客。 本文的余下部分介绍如何通过使用 RLS 强制隔离租户来解决这些问题。 执行以下两个步骤： 

1. **应用程序层**：打开连接后，将应用程序代码修改为始终设置 SESSION\_CONTEXT 中的当前 TenantId。 示例项目已通过这种方式设置 TenantId。 
2. **数据层**：在每个分片数据库中创建一个 RLS 安全策略，以根据 SESSION\_CONTEXT 中存储的 TenantId 筛选行。 针对每个分片数据库创建一个 策略，否则不会筛选多租户分片中的行。 

## <a name="1-application-tier-set-tenantid-in-the-sessioncontext"></a>1.应用程序层：在 SESSION\_CONTEXT 中设置 TenantId

首先，使用弹性数据库客户端库的数据依赖路由 API 连接到分片数据库。 应用程序仍需告知数据库，哪个 TenantId 在使用连接。 TenantId 告知 RLS 安全策略，必须筛选掉哪些属于其他租户的行。 将当前 TenantId 存储在连接的 [SESSION\_CONTEXT](https://docs.microsoft.com/sql/t-sql/functions/session-context-transact-sql) 中。

可以使用 [CONTEXT\_INFO](https://docs.microsoft.com/sql/t-sql/functions/context-info-transact-sql) 来替代 SESSION\_CONTEXT。 不过，SESSION\_CONTEXT 是较好的选择。 SESSION\_CONTEXT 更易于使用，默认情况下返回 NULL，并支持键值对。

### <a name="entity-framework"></a>实体框架

对于使用实体框架的应用程序，最简单的方法是根据[使用 EF DbContext 进行数据相关的路由](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md#data-dependent-routing-using-ef-dbcontext)中所述，在 ElasticScaleContext 重写中设置 SESSION\_CONTEXT。 创建和执行 SqlCommand，将 SESSION\_CONTEXT 中的 TenantId 设置为为连接指定的 shardingKey。 然后，返回通过数据依赖路由进行中转的连接。 这样，只需编写代码一次就能设置 SESSION\_CONTEXT。 

```csharp
// ElasticScaleContext.cs 
// Constructor for data-dependent routing.
// This call opens a validated connection that is routed to the
// proper shard by the shard map manager.
// Note that the base class constructor call fails for an open connection 
// if migrations need to be done and SQL credentials are used.
// This is the reason for the separation of constructors.
// ...
public ElasticScaleContext(ShardMap shardMap, T shardingKey, string connectionStr)
    : base(
        OpenDDRConnection(shardMap, shardingKey, connectionStr),
        true)  // contextOwnsConnection
{
}

public static SqlConnection OpenDDRConnection(
    ShardMap shardMap,
    T shardingKey,
    string connectionStr)
{
    // No initialization.
    Database.SetInitializer<ElasticScaleContext<T>>(null);

    // Ask shard map to broker a validated connection for the given key.
    SqlConnection conn = null;
    try
    {
        conn = shardMap.OpenConnectionForKey(
            shardingKey,
            connectionStr,
            ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey
        // to enable Row-Level Security filtering.
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText =
            @"exec sp_set_session_context
                @key=N'TenantId', @value=@shardingKey";
        cmd.Parameters.AddWithValue("@shardingKey", shardingKey);
        cmd.ExecuteNonQuery();

        return conn;
    }
    catch (Exception)
    {
        if (conn != null)
        {
            conn.Dispose();
        }
        throw;
    }
} 
// ... 
```

现在，每当调用 ElasticScaleContext 时，系统就会自动将 SESSION\_CONTEXT 设置为指定的 TenantId： 

```csharp
// Program.cs 
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() => 
{   
    using (var db = new ElasticScaleContext<int>(
        sharding.ShardMap, tenantId, connStrBldr.ConnectionString))   
    {     
        var query = from b in db.Blogs
                    orderby b.Name
                    select b;

        Console.WriteLine("All blogs for TenantId {0}:", tenantId);     
        foreach (var item in query)     
        {       
            Console.WriteLine(item.Name);     
        }   
    } 
}); 
```

### <a name="adonet-sqlclient"></a>ADO.NET SqlClient

对于使用 ADO.NET SqlClient 的应用程序，请创建一个围绕 ShardMap.OpenConnectionForKey 方法的包装器函数。 让包装器将 SESSION\_CONTEXT 中的 TenantId 自动设置为当前的 TenantId，然后再返回一个连接。 为确保始终设置 SESSION\_CONTEXT，应只使用此包装器函数打开连接。

```csharp
// Program.cs
// Wrapper function for ShardMap.OpenConnectionForKey() that
// automatically sets SESSION_CONTEXT with the correct
// tenantId before returning a connection.
// As a best practice, you should only open connections using this method
// to ensure that SESSION_CONTEXT is always set before executing a query.
// ...
public static SqlConnection OpenConnectionForTenant(
    ShardMap shardMap, int tenantId, string connectionStr)
{
    SqlConnection conn = null;
    try
    {
        // Ask shard map to broker a validated connection for the given key.
        conn = shardMap.OpenConnectionForKey(
            tenantId, connectionStr, ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey
        // to enable Row-Level Security filtering.
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText =
            @"exec sp_set_session_context
                @key=N'TenantId', @value=@shardingKey";
        cmd.Parameters.AddWithValue("@shardingKey", tenantId);
        cmd.ExecuteNonQuery();

        return conn;
    }
    catch (Exception)
    {
        if (conn != null)
        {
            conn.Dispose();
        }
        throw;
    }
}

// ...

// Example query via ADO.NET SqlClient.
// If row-level security is enabled, only Tenant 4's blogs are listed.
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (SqlConnection conn = OpenConnectionForTenant(
        sharding.ShardMap, tenantId4, connStrBldr.ConnectionString))
    {
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"SELECT * FROM Blogs";

        Console.WriteLine(@"--
All blogs for TenantId {0} (using ADO.NET SqlClient):", tenantId4);

        SqlDataReader reader = cmd.ExecuteReader();
        while (reader.Read())
        {
            Console.WriteLine("{0}", reader["Name"]);
        }
    }
});

```

## <a name="2-data-tier-create-row-level-security-policy"></a>2.数据层：创建行级安全策略

### <a name="create-a-security-policy-to-filter-the-rows-each-tenant-can-access"></a>创建安全策略，以便筛选每个租户可以访问的行

由于应用程序在查询之前会将 SESSION\_CONTEXT 设置为当前 TenantId，因此 RLS 安全策略可以筛选查询并排除使用其他 TenantId 的行。  

RLS 在 Transact-SQL 中实现。 用户定义的函数用于定义访问逻辑，安全策略可将此函数绑定到任意数量的表。 对于此项目：

1. 该函数将验证应用程序是否已连接到数据库，以及存储在 SESSION\_CONTEXT 中的 TenantId 是否与给定行的 TenantId 相匹配。
    - 连接的是应用程序而不是某个其他的 SQL 用户。

2. 对于 SELECT、UPDATE 和 DELETE 查询，FILTER 谓词允许符合 TenantId 筛选器条件的行通过。
    - BLOCK 谓词阻止系统对不符合筛选器条件的行执行 INSERT 或 UPDATE 操作。
    - 如果未设置 SESSION\_CONTEXT，此函数会返回 NULL，将无法查看或插入任何行。 

若要在所有分片上启用 RLS，请执行以下 T-SQL，只需使用项目中包括的 Visual Studio (SSDT)、SSMS 或 PowerShell 脚本即可。 或者，如果使用[弹性数据库作业](sql-database-elastic-jobs-overview.md)，则可自动在所有分片上执行此 T-SQL。

```sql
CREATE SCHEMA rls; -- Separate schema to organize RLS objects.
GO

CREATE FUNCTION rls.fn_tenantAccessPredicate(@TenantId int)     
    RETURNS TABLE     
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS fn_accessResult
        -- Use the user in your application’s connection string.
        -- Here we use 'dbo' only for demo purposes!
        WHERE DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo')
        AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId;
GO

CREATE SECURITY POLICY rls.tenantAccessPolicy
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts;
GO 
```

> [!TIP]
> 在复杂的项目中，可能需要在数百个表中添加谓词，这可能很单调乏味。 可以通过一个帮助程序存储过程来自动生成安全策略，并在架构的所有表中添加谓词。 有关详细信息，请参阅 [Apply Row-Level Security to all tables - helper script](http://blogs.msdn.com/b/sqlsecurity/archive/2015/03/31/apply-row-level-security-to-all-tables-helper-script)（向所有表应用行级安全性 - 帮助器脚本）博客中的文章。

现在，如果再次运行示例应用程序，租户只能看到属于他们的行。 此外，应用程序只能插入当前已连接到分片数据库的租户的行，而不能插入属于其他租户的行。 而且，应用不能更新任何可见行中的 TenantId。 如果应用尝试执行上述任一操作，则会引发 DbUpdateException。

如果以后添加了新表，请通过 ALTER 操作更改安全策略，在新表中添加 FILTER 和 BLOCK 谓词。

```sql
ALTER SECURITY POLICY rls.tenantAccessPolicy     
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable;
GO 
```

### <a name="add-default-constraints-to-automatically-populate-tenantid-for-inserts"></a>添加默认约束以自动填充 INSERT 的 TenantId

可以对每个表施加默认约束，以便在插入行时向 TenantId 自动填充当前存储在 SESSION\_CONTEXT 中的值。 以下是一个示例。 

```sql
-- Create default constraints to auto-populate TenantId with the
-- value of SESSION_CONTEXT for inserts.
ALTER TABLE Blogs     
    ADD CONSTRAINT df_TenantId_Blogs      
    DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId;
GO

ALTER TABLE Posts     
    ADD CONSTRAINT df_TenantId_Posts      
    DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId;
GO 
```

现在，应用程序在插入行时不需要指定 TenantId： 

```csharp
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() => 
{   
    using (var db = new ElasticScaleContext<int>(
        sharding.ShardMap, tenantId, connStrBldr.ConnectionString))
    {
        // The default constraint sets TenantId automatically!
        var blog = new Blog { Name = name };
        db.Blogs.Add(blog);     
        db.SaveChanges();   
    } 
}); 
```

> [!NOTE]
> 如果对实体框架项目使用默认约束，则建议不要在 EF 数据模型中包括 TenantId 列。 之所以提供此建议，是因为实体框架查询会自动提供默认值，而这些值会重写在 T-SQL 中创建的使用 SESSION\_CONTEXT 的默认约束。
> 举例来说，要在示例项目中使用默认约束，应该从 DataClasses.cs 中删除 TenantId（并在 Package Manager Console 中运行 Add-Migration），然后使用 T-SQL 来确保该字段仅存在于数据库表中。 这样，在插入数据时，EF 会自动提供不正确的默认值。

### <a name="optional-enable-a-superuser-to-access-all-rows"></a>（可选）启用“超级用户”来访问所有行

某些应用程序可能需要创建一个可访问所有行的超级用户。 超级用户可以启用跨所有分片上的所有租户进行报告的功能。 超级用户还可以在分片上执行拆分-合并操作，此类操作涉及在数据库之间移动租户行。

若要启用超级用户，请在每个分片数据库中创建新的 SQL 用户（在本例中为 `superuser`）。 然后，使用允许此用户访问所有行的新谓词函数更改安全策略。 接下来会提供此类函数。

```sql
-- New predicate function that adds superuser logic.
CREATE FUNCTION rls.fn_tenantAccessPredicateWithSuperUser(@TenantId int)
    RETURNS TABLE
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS fn_accessResult 
        WHERE 
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo') -- Replace 'dbo'.
            AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId
        ) 
        OR
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('superuser')
        );
GO

-- Atomically swap in the new predicate function on each table.
ALTER SECURITY POLICY rls.tenantAccessPolicy
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER BLOCK  PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts,
    ALTER BLOCK  PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts;
GO
```


### <a name="maintenance"></a>维护

- **添加新分片**：对所有新分片执行 T-SQL 脚本以启用 RLS，否则，不会筛选针对这些分片的查询。
- **添加新表**：每当创建新表时，将 FILTER 和 BLOCK 谓词添加到所有分片上的安全策略， 否则不会筛选对新表的查询。 根据 [Apply Row-Level Security automatically to newly created tables](http://blogs.msdn.com/b/sqlsecurity/archive/2015/05/22/apply-row-level-security-automatically-to-newly-created-tables.aspx)（自动向新建的表应用行级安全性）（博客）中所述，此添加操作可以使用 DDL 触发器来自动完成。

## <a name="summary"></a>摘要

可将弹性数据库工具和行级安全性一起使用，以扩大支持多租户和单租户分片的应用程序的数据层。 可以使用多租户分片来提高数据存储效率。 如果大量的租户只有几行数据，则此效率提升很显著。 单租户分片可以支持其性能和隔离要求更严格的高级租户。  有关详细信息，请参阅[行级安全性参考][rls]。

## <a name="additional-resources"></a>其他资源

- [什么是 Azure 弹性池？](sql-database-elastic-pool.md)
- [Scaling out with Azure SQL Database（使用 Azure SQL 数据库进行扩展）](sql-database-elastic-scale-introduction.md)
- [包含 Azure SQL 数据库的多租户 SaaS 应用程序的设计模式](saas-tenancy-app-design-patterns.md)
- [使用 Azure AD 和 OpenID Connect 的多租户应用身份验证](../guidance/guidance-multitenant-identity-authenticate.md)
- [Tailspin Surveys 应用程序](../guidance/guidance-multitenant-identity-tailspin.md)

## <a name="questions-and-feature-requests"></a>问题和功能请求

如有问题，请在 [SQL 数据库论坛](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)上联系我们。 可以向 [SQL 数据库反馈论坛](https://feedback.azure.com/forums/217321-sql-database/)提交功能请求。


<!--Image references-->
[1]: ./media/saas-tenancy-elastic-tools-multi-tenant-row-level-security/blogging-app.png
<!--anchors-->
[rls]: https://docs.microsoft.com/sql/relational-databases/security/row-level-security
[s-d-elastic-database-client-library]: sql-database-elastic-database-client-library.md

