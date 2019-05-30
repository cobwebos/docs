---
title: Azure SQL 数据库功能限制 |Microsoft Docs
description: Azure SQL 数据库功能限制通过限制攻击者能够访问其中的信息可以是在数据库中的功能，提高了数据库安全性。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: vainolo
ms.author: arib
ms.reviewer: vanto
manager: craigg
ms.date: 03/22/2019
ms.openlocfilehash: ac7a074e78def504a10b4daa07971f919f414a88
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2019
ms.locfileid: "66259447"
---
# <a name="azure-sql-database-feature-restrictions"></a>Azure SQL 数据库功能限制

SQL Server 的攻击的一个常见原因是通过访问数据库的 web 应用程序使用各种形式的 SQL 注入式攻击来搜集有关数据库的信息。  理想情况下，因此不允许 SQL 注入被开发的应用程序代码。  但是，大型-在基本代码中包含旧的和外部代码，其中一个永远无法确定已解决所有情况下，因此 SQL 注入是争的事实，我们必须防范。  功能限制的目的是防止某些形式的泄漏有关数据库的信息，即使 SQL 注入是成功的 SQL 注入。

## <a name="enabling-feature-restrictions"></a>启用功能的限制

可以启用功能限制使用`sp_add_feature_restriction`存储过程，如下所示：

```sql
EXEC sp_add_feature_restriction <feature>, <object_class>, <object_name>
```

以下功能可以限制：

| Feature          | 描述 |
|------------------|-------------|
| N'ErrorMessages' | 限制时，将屏蔽的错误消息中的任何用户数据。 请参阅[错误消息功能限制](#error-messages-feature-restriction) |
| N'Waitfor       | 限制时，该命令将立即返回不使用任何延迟。 请参阅[WAITFOR 功能限制](#waitfor-feature-restriction) |

值`object_class`可以是`N'User'`或`N'Role'`来表示是否`object_name`是用户名称或数据库中的角色名称。

下面的示例将导致用户的所有错误消息`MyUser`将要屏蔽：

```sql
EXEC sp_add_feature_restriction N'ErrorMessages', N'User', N'MyUser'
```

## <a name="disabling-feature-restrictions"></a>禁用功能限制

完成禁用功能限制使用`sp_drop_feature_restriction`存储过程，如下所示：

```sql
EXEC sp_drop_feature_restriction <feature>, <object_class>, <object_name>
```

下面的示例禁用用户的错误消息屏蔽`MyUser`:

```sql
EXEC sp_drop_feature_restriction N'ErrorMessages', N'User', N'MyUser'
```

## <a name="viewing-feature-restrictions"></a>查看功能限制

`sys.sql_feature_restrictions`视图显示数据库上的所有当前定义的功能限制。 它包含以下列：

| 列名称 | 数据类型 | 描述 |
|-------------|-----------|-------------|
| class       | nvarchar(128) | 此限制适用的对象的类 |
| 对象      | nvarchar(256) | 此限制适用的对象的名称 |
| 功能     | nvarchar(128) | 是受限制的功能 |

## <a name="feature-restrictions"></a>功能限制

### <a name="error-messages-feature-restriction"></a>错误消息功能限制

一个常见的 SQL 注入攻击方法是将导致错误的代码注入。  通过检查错误消息，攻击者可以了解有关系统的信息启用更多更有针对性的攻击。  在应用程序不会显示查询的结果，但会显示错误消息，此类攻击可能非常有用。

请考虑具有请求的窗体中的 web 应用程序：

```html
http://www.contoso.com/employee.php?id=1
```

它将执行以下数据库查询：

```sql
SELECT Name FROM EMPLOYEES WHERE Id=$EmpId
```

如果该值作为传递`id`复制到 web 应用程序请求的参数替换 $EmpId 数据库查询中的，攻击者可以进行以下请求：

```html
http://www.contoso.com/employee.php?id=1 AND CAST(DB_NAME() AS INT)=0
```

并将返回以下错误，从而能够了解数据库的名称：

```sql
Conversion failed when converting the nvarchar value 'HR_Data' to data type int.
```

启用错误消息功能对数据库中的应用程序用户的限制后，返回的错误消息被屏蔽，以便数据库有关的任何内部信息泄露：

```sql
Conversion failed when converting the ****** value '******' to data type ******.
```

同样，攻击者可以发出以下请求：

```html
http://www.contoso.com/employee.php?id=1 AND CAST(Salary AS TINYINT)=0
```

并将返回以下错误，从而能够了解雇员的薪金：

```sql
Arithmetic overflow error for data type tinyint, value = 140000.
```

使用错误消息功能限制，将返回数据库：

```sql
Arithmetic overflow error for data type ******, value = ******.
```

### <a name="waitfor-feature-restriction"></a>WAITFOR 功能限制

密件的 SQL 注入是当应用程序不提供攻击者注入 SQL 结果或错误消息，但攻击者可以通过构造中的条件查询推断在数据库中的信息时两个条件分支需要在不同时间执行。 通过比较响应时间，攻击者可以知道哪一个分支已执行，从而了解有关系统的信息。 使用此类攻击的最简单变体`WAITFOR`语句引入延迟。

请考虑具有请求的窗体中的 web 应用程序：

```html
http://www.contoso.com/employee.php?id=1
```

它将执行以下数据库查询：

```sql
SELECT Name FROM EMPLOYEES WHERE Id=$EmpId
```

如果复制作为 id 参数传递给 web 应用程序请求的值来替换 $EmpId 数据库查询中的，攻击者可以进行以下请求：

```html
http://www.contoso.com/employee.php?id=1; IF SYSTEM_USER='sa' WAITFOR DELAY '00:00:05'
```

该查询将花费更多的 5 秒，如果和`sa`帐户已被使用。 如果`WAITFOR`在数据库中，禁用功能限制`WAITFOR`语句将被忽略，且不是信息泄露使用这种攻击。