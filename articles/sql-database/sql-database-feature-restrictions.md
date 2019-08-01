---
title: Azure SQL 数据库功能限制 |Microsoft Docs
description: Azure SQL 数据库的功能限制通过限制数据库中的功能, 攻击者能够获取对其中信息的访问权限, 从而提高数据库的安全性。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: vainolo
ms.author: arib
ms.reviewer: vanto
ms.date: 03/22/2019
ms.openlocfilehash: 5f5123624b5b9388baf799b48127b5b796eec21b
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568217"
---
# <a name="azure-sql-database-feature-restrictions"></a>Azure SQL 数据库功能限制

SQL Server 攻击的一个常见原因是 web 应用程序可访问数据库, 其中使用各种形式的 SQL 注入攻击搜集有关数据库的信息。  理想情况下, 应用程序代码已开发, 因此不允许 SQL 注入。  但在包含旧代码和外部代码的大型代码库中, 可能永远不能确定所有案例都已得到解决, 因此 SQL 注入是我们必须防范的一种生活。  功能限制的目标是防止某些形式的 SQL 注入泄露有关数据库的信息, 即使 SQL 注入成功也是如此。

## <a name="enabling-feature-restrictions"></a>启用功能限制

使用`sp_add_feature_restriction`存储过程可以实现功能限制, 如下所示:

```sql
EXEC sp_add_feature_restriction <feature>, <object_class>, <object_name>
```

可以限制以下功能:

| 功能          | 描述 |
|------------------|-------------|
| N'ErrorMessages' | 如果受到限制, 将屏蔽错误消息中的任何用户数据。 请参阅[错误消息功能限制](#error-messages-feature-restriction) |
| N'Waitfor'       | 当受到限制时, 该命令将立即返回, 而不会出现任何延迟。 请参阅[WAITFOR 功能限制](#waitfor-feature-restriction) |

的`object_class`值可以`N'User'`是, 也`N'Role'`可以是, 表示`object_name`数据库中是用户名还是角色名称。

下面的示例将导致用户`MyUser`屏蔽所有错误消息:

```sql
EXEC sp_add_feature_restriction N'ErrorMessages', N'User', N'MyUser'
```

## <a name="disabling-feature-restrictions"></a>禁用功能限制

禁用功能限制`sp_drop_feature_restriction`的方式如下:

```sql
EXEC sp_drop_feature_restriction <feature>, <object_class>, <object_name>
```

下面的示例为用户`MyUser`禁用错误消息掩码:

```sql
EXEC sp_drop_feature_restriction N'ErrorMessages', N'User', N'MyUser'
```

## <a name="viewing-feature-restrictions"></a>查看功能限制

`sys.sql_feature_restrictions`视图显示对数据库的所有当前定义的功能限制。 它包含以下列:

| 列名 | 数据类型 | 描述 |
|-------------|-----------|-------------|
| 类       | nvarchar(128) | 应用限制的对象的类 |
| object      | nvarchar(256) | 应用限制的对象的名称 |
| 功能     | nvarchar(128) | 受限制的功能 |

## <a name="feature-restrictions"></a>功能限制

### <a name="error-messages-feature-restriction"></a>错误消息功能限制

一种常见的 SQL 注入攻击方法是注入导致错误的代码。  通过查看错误消息, 攻击者可以了解有关系统的信息, 从而实现更有针对性的攻击。  当应用程序未显示查询结果, 但却显示错误消息时, 这种攻击会特别有用。

假设有一个 web 应用程序, 该应用程序的请求格式为:

```html
http://www.contoso.com/employee.php?id=1
```

这会执行以下数据库查询:

```sql
SELECT Name FROM EMPLOYEES WHERE Id=$EmpId
```

如果将作为`id`参数传递给 web 应用程序请求的值复制到数据库查询中的 $EmpId, 则攻击者可能会发出以下请求:

```html
http://www.contoso.com/employee.php?id=1 AND CAST(DB_NAME() AS INT)=0
```

将返回以下错误, 从而允许攻击者了解数据库的名称:

```sql
Conversion failed when converting the nvarchar value 'HR_Data' to data type int.
```

在为数据库中的应用程序用户启用错误消息后, 将屏蔽返回的错误消息, 这样就不会泄漏有关数据库的内部信息:

```sql
Conversion failed when converting the ****** value '******' to data type ******.
```

同样, 攻击者可能会发出以下请求:

```html
http://www.contoso.com/employee.php?id=1 AND CAST(Salary AS TINYINT)=0
```

将返回以下错误, 以允许攻击者了解员工的薪水:

```sql
Arithmetic overflow error for data type tinyint, value = 140000.
```

使用错误消息功能限制, 数据库将返回:

```sql
Arithmetic overflow error for data type ******, value = ******.
```

### <a name="waitfor-feature-restriction"></a>WAITFOR 功能限制

当应用程序不向攻击者提供注入的 SQL 或错误消息的结果时, 可以使用直接的 SQL 注入, 但攻击者可以通过构造一个条件查询来从数据库中推断出信息, 其中两个条件分支需要花费不同的时间来执行。 通过比较响应时间, 攻击者可以知道哪个分支已执行, 从而了解有关系统的信息。 此攻击的最简单变体是使用`WAITFOR`语句引入延迟。

假设有一个 web 应用程序, 该应用程序的请求格式为:

```html
http://www.contoso.com/employee.php?id=1
```

这会执行以下数据库查询:

```sql
SELECT Name FROM EMPLOYEES WHERE Id=$EmpId
```

如果将作为 id 参数传递给 web 应用程序请求的值复制到数据库查询中的 $EmpId, 则攻击者可以发出以下请求:

```html
http://www.contoso.com/employee.php?id=1; IF SYSTEM_USER='sa' WAITFOR DELAY '00:00:05'
```

如果正在使用该`sa`帐户, 则查询将需要额外的5秒。 如果`WAITFOR`在数据库中禁用功能限制, 则将`WAITFOR`忽略该语句, 并且不会使用此攻击泄漏信息。