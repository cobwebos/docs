---
title: Azure Synapse 的列级安全性是什么？
description: 列级安全性使客户能够根据用户的执行上下文或组成员身份来控制对数据库表列的访问，从而简化应用程序中的安全性设计和编码，并使你能够对列实施限制access.
services: sql-data-warehouse
author: julieMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 02/05/2020
ms.author: jrasnick
ms.reviewer: igorstan, carlrab
ms.custom: seo-lt-2019
tags: azure-synapse
ms.openlocfilehash: aa9791f019436cc5c7effc9bce197d89131a6557
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78199965"
---
# <a name="column-level-security"></a>列级别安全性

列级安全性允许客户根据用户的执行上下文或组成员身份控制对表列的访问。


> [!VIDEO https://www.youtube.com/embed/OU_ESg0g8r8]
由于此视频已发布，因此[行级别安全性](/sql/relational-databases/security/row-level-security?toc=%2Fazure%2Fsql-data-warehouse%2Ftoc&view=sql-server-2017)可用于 Azure Synapse。 

列级安全性简化了应用程序中安全性的设计和编码，使你能够限制列访问权限以保护敏感数据。 例如，确保具体用户只能访问表中与其部门相关的特定列。 访问限制逻辑位于数据库层中，而不是在另一个应用层中远离数据。 每次尝试从任何层进行数据访问时，数据库将应用访问限制。 此限制通过减少整体安全系统的外围应用，使你的安全性更可靠、更可靠。 此外，列级安全性还消除了引入视图的需要，以筛选出用于对用户施加访问限制的列。

可以用[GRANT](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql) t-sql 语句实现列级安全性。 借助此机制，SQL 和 Azure Active Directory (AAD) 身份验证同时受支持。

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
下面的示例演示如何限制 `TestUser` 访问 `Membership` 表的 `SSN` 列：

创建 `Membership` 表，其中包含用于存储社会安全号码的 SSN 列：

```sql
CREATE TABLE Membership
  (MemberID int IDENTITY,
   FirstName varchar(100) NULL,
   SSN char(9) NOT NULL,
   LastName varchar(100) NOT NULL,
   Phone varchar(12) NULL,
   Email varchar(100) NULL);
```

允许 `TestUser` 访问除 SSN 列之外的所有列，其中包含敏感数据：

```sql
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;
```

如果查询包含 SSN 列，则执行 `TestUser` 将失败：

```sql
SELECT * FROM Membership;

Msg 230, Level 14, State 1, Line 12
The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="use-cases"></a>用例

有关当前如何使用列级安全性的一些示例：

- 金融服务公司只允许客户经理访问客户的社会安全号码 (SSN)、电话号码和其他个人身份信息 (PII)。
- 医疗保健提供商仅允许医生和医护人员访问敏感的医疗记录，同时阻止计费部门的成员查看此数据。
