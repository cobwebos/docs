---
title: 功能限制
description: Azure SQL 数据库功能限制改善了数据库的安全性，因为它可以限制攻击者利用数据库中的功能来获取对数据库中信息的访问权限。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: barmichal
ms.author: mibar
ms.reviewer: vanto
ms.date: 03/22/2019
ms.openlocfilehash: ce10daca23299f838e4086426fa89d9cade314ea
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823431"
---
# <a name="azure-sql-database-feature-restrictions"></a>Azure SQL 数据库功能限制

利用可访问数据库的 Web 应用程序是一种常见的 SQL Server 攻击手段，多种形式的 SQL 注入攻击都是使用这种方式来搜集有关数据库的信息。  在理想情况下，部署的应用程序代码不应允许 SQL 注入。  但是，在包含传统代码和外部代码的大规模基础代码中，开发人员可能永远不能确保解决所有问题，因此，SQL 注入是一个即成的事实，我们必须予以防范。  功能限制旨在防止某些形式的 SQL 注入泄漏有关数据库的信息，即使 SQL 注入成功。

## <a name="enabling-feature-restrictions"></a>启用功能限制

按如下所示使用 `sp_add_feature_restriction` 存储过程启用功能限制：

```sql
EXEC sp_add_feature_restriction <feature>, <object_class>, <object_name>
```

可限制以下功能：

| 功能          | 说明 |
|------------------|-------------|
| N'ErrorMessages' | 启用限制时，会将错误消息中的任何用户数据掩码。 请参阅[错误消息功能限制](#error-messages-feature-restriction) |
| N'Waitfor'       | 启用限制时，该命令将立即返回，而不会延迟。 请参阅 [WAITFOR 功能限制](#waitfor-feature-restriction) |

`object_class` 的值可以是 `N'User'` 或 `N'Role'`，表示 `object_name` 是数据库中的用户名还是角色名称。

以下示例会导致掩码用户 `MyUser` 的所有错误消息：

```sql
EXEC sp_add_feature_restriction N'ErrorMessages', N'User', N'MyUser'
```

## <a name="disabling-feature-restrictions"></a>禁用功能限制

按如下所示使用 `sp_drop_feature_restriction` 存储过程禁用功能限制：

```sql
EXEC sp_drop_feature_restriction <feature>, <object_class>, <object_name>
```

以下示例禁用用户 `MyUser` 的错误消息掩码：

```sql
EXEC sp_drop_feature_restriction N'ErrorMessages', N'User', N'MyUser'
```

## <a name="viewing-feature-restrictions"></a>查看功能限制

`sys.sql_feature_restrictions` 视图会显示当前对数据库定义的所有功能限制。 其中包含以下列：

| 列名称 | 数据类型 | 说明 |
|-------------|-----------|-------------|
| class       | nvarchar(128) | 应用限制的对象的类 |
| 对象      | nvarchar(256) | 应用限制的对象的名称 |
| feature     | nvarchar(128) | 限制的功能 |

## <a name="feature-restrictions"></a>功能限制

### <a name="error-messages-feature-restriction"></a>错误消息功能限制

一种常见的 SQL 注入攻击手段是注入导致出错的代码。  通过查看错误消息，攻击者可以了解有关系统的信息，从而展开更有针对性的攻击。  当应用程序未显示查询结果，但显示错误消息时，这种攻击特别有效。

假设某个 Web 应用程序使用以下格式的请求：

```html
http://www.contoso.com/employee.php?id=1
```

它执行以下数据库查询：

```sql
SELECT Name FROM EMPLOYEES WHERE Id=$EmpId
```

如果将作为 `id` 参数传递给 Web 应用程序请求的值复制到数据库查询中的 $EmpId，则攻击者可能会发出以下请求：

```html
http://www.contoso.com/employee.php?id=1 AND CAST(DB_NAME() AS INT)=0
```

将返回以下错误，因此攻击者便知道了数据库的名称：

```sql
Conversion failed when converting the nvarchar value 'HR_Data' to data type int.
```

为数据库中的应用程序用户启用错误消息功能限制后，返回的错误消息将会掩码，这样就不会透漏有关数据库的内部信息：

```sql
Conversion failed when converting the ****** value '******' to data type ******.
```

同样，攻击者可能会发出以下请求：

```html
http://www.contoso.com/employee.php?id=1 AND CAST(Salary AS TINYINT)=0
```

将返回以下错误，因此攻击者便知道了员工的薪水：

```sql
Arithmetic overflow error for data type tinyint, value = 140000.
```

如果使用错误消息功能限制，数据库将返回：

```sql
Arithmetic overflow error for data type ******, value = ******.
```

### <a name="waitfor-feature-restriction"></a>WAITFOR 功能限制

如果应用程序不会向攻击者提供注入的 SQL 的结果或错误消息，但攻击者可以通过构造一个条件查询并在其中创建两个花费不同时间执行的条件分支来推断数据库中的信息，那么，他们就可以展开 SQL 盲注攻击。 通过比较响应时间，攻击者可能会知道执行了哪个分支，从而了解有关系统的信息。 此攻击的最简单变种是使用 `WAITFOR` 语句引入延迟。

假设某个 Web 应用程序使用以下格式的请求：

```html
http://www.contoso.com/employee.php?id=1
```

它执行以下数据库查询：

```sql
SELECT Name FROM EMPLOYEES WHERE Id=$EmpId
```

如果将作为 id 参数传递给 Web 应用程序请求的值复制到数据库查询中的 $EmpId，则攻击者可能会发出以下请求：

```html
http://www.contoso.com/employee.php?id=1; IF SYSTEM_USER='sa' WAITFOR DELAY '00:00:05'
```

如果使用的是 `sa` 帐户，该查询需要额外花费 5 秒。 如果在数据库中禁用 `WAITFOR` 功能限制，则会忽略 `WAITFOR` 语句，因此在受到这种攻击时不会透露信息。