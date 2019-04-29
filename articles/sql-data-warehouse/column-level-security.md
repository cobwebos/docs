---
title: Azure SQL 数据仓库列级别安全性 | Microsoft Docs
description: 借助列级别安全性 (CLS)，客户可以根据用户的执行上下文或其组成员身份，控制对数据库表列的访问。 CLS 简化了应用程序中的安全性设计和编程。 借助 CLS，可以实现列访问限制。
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/02/2019
ms.author: kavithaj
ms.reviewer: igorstan, carlrab
ms.openlocfilehash: aa91bd586e064239d0e05c754427947963c9ee3a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61082790"
---
# <a name="column-level-security"></a>列级别安全性
借助列级别安全性 (CLS)，客户可以根据用户的执行上下文或其组成员身份，控制对数据库表列的访问。

> [!VIDEO https://www.youtube.com/embed/OU_ESg0g8r8]

CLS 简化了应用程序中的安全性设计和编程。 借助 CLS，可以实现列访问限制，从而保护敏感数据。 例如，确保具体用户只能访问表中与其部门相关的特定列。 访问限制逻辑位于数据库层，而不会脱离另一应用程序层中的数据。 每当有任何一层的数据访问请求，数据库就会应用访问限制。 此限制会减少整个安全系统的外围应用，从而提高安全系统的可靠性。 此外，使用 CLS 还无需引入用于筛选掉列的视图以限制用户访问。

可以使用 [GRANT](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql) T-SQL 语句实现 CLS。 借助此机制，SQL 和 Azure Active Directory (AAD) 身份验证同时受支持。

![cls](./media/column-level-security/cls.png)

## <a name="syntax"></a>语法

```sql
GRANT <permission> [ ,...n ] ON
    [ OBJECT :: ][ schema_name ]. object_name [ ( column [ ,...n ] ) ]
    TO <database_principal> [ ,...n ]
    [ WITH GRANT OPTION ]
    [ AS <database_principal> ]
<permission> ::=
    SELECT
  | UPDATE
<database_principal> ::=
      Database_user
    | Database_role
    | Database_user_mapped_to_Windows_User
    | Database_user_mapped_to_Windows_Group
```

## <a name="example"></a>示例
下面的示例展示了如何限制“TestUser”访问“Membership”表的“SSN”列：

创建“Membership”表，其中包含用于存储社会安全号码的“SSN”列：

```sql
CREATE TABLE Membership
  (MemberID int IDENTITY,
   FirstName varchar(100) NULL,
   SSN char(9) NOT NULL,
   LastName varchar(100) NOT NULL,
   Phone varchar(12) NULL,
   Email varchar(100) NULL);
```

允许“TestUser”访问所有列，包含敏感数据的“SSN”列除外：

```sql
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;
```

如果包含“SSN”列，以“TestUser”身份执行的查询便会失败：

```sql
SELECT * FROM Membership;

Msg 230, Level 14, State 1, Line 12
The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="use-cases"></a>用例
下面是目前的一些 CLS 用例示例：
- 金融服务公司只允许客户经理访问客户的社会安全号码 (SSN)、电话号码和其他个人身份信息 (PII)。
- 医疗保健提供商只允许医生和护士访问敏感病史档案，不允许计费部门成员查看此类数据。
