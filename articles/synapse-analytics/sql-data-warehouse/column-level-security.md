---
title: Azure 突触的列级安全性是什么？
description: 列级安全性允许客户根据用户的执行上下文或组成员身份控制对数据库表列的访问，简化应用程序中安全设计和编码，并允许您对列访问实施限制。
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/05/2020
ms.author: jrasnick
ms.reviewer: igorstan, carlrab
ms.custom: seo-lt-2019
tags: azure-synapse
ms.openlocfilehash: 61a3e2eadaf79cdb30a931b31cff709298d0a22c
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631299"
---
# <a name="column-level-security"></a>列级别安全性

列级安全性允许客户根据用户的执行上下文或组成员身份控制对表列的访问。

> [!VIDEO https://www.youtube.com/embed/OU_ESg0g8r8]
自发布此视频以来[，行级别安全性](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)已可用于 Azure 同步。

列级安全性简化了应用程序中安全性的设计和编码，允许您限制列访问以保护敏感数据。 例如，确保具体用户只能访问表中与其部门相关的特定列。 访问限制逻辑位于数据库层中，而不是在另一个应用层中远离数据。 每次尝试从任何层访问数据时，数据库都会应用访问限制。 通过减少整体安全系统的表面积，此限制使您的安全性更加可靠和可靠。 此外，列级安全性还消除了引入视图以筛选出列以对用户施加访问限制的需要。

您可以使用[GRANT](/sql/t-sql/statements/grant-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) T-SQL 语句实现列级安全性。 借助此机制，SQL 和 Azure Active Directory (AAD) 身份验证同时受支持。

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

下面的示例演示如何限制`TestUser`访问`SSN``Membership`表的列：

使用`Membership`用于存储社会保险号的 SSN 列创建表：

```sql
CREATE TABLE Membership
  (MemberID int IDENTITY,
   FirstName varchar(100) NULL,
   SSN char(9) NOT NULL,
   LastName varchar(100) NOT NULL,
   Phone varchar(12) NULL,
   Email varchar(100) NULL);
```

允许`TestUser`访问除 SSN 列之外的所有列，该列具有敏感数据：

```sql
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;
```

如果查询包含`TestUser`SSN 列，则执行的查询将失败：

```sql
SELECT * FROM Membership;

Msg 230, Level 14, State 1, Line 12
The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="use-cases"></a>用例

当前如何使用列级安全性的一些示例：

- 金融服务公司只允许客户经理访问客户的社会安全号码 (SSN)、电话号码和其他个人身份信息 (PII)。
- 医疗保健提供商只允许医生和护士访问敏感的医疗记录，同时阻止计费部门的成员查看此数据。
