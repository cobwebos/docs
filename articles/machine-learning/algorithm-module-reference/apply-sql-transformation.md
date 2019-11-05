---
title: 应用 SQL 转换
titleSuffix: Azure Machine Learning service
description: 了解如何使用 Azure 机器学习服务中的 "应用 SQL 转换" 模块对输入数据集运行 SQLite 查询以转换数据。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 62c61d589324fe8364fe4630b3cf2cc64e1860b1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493912"
---
# <a name="apply-sql-transformation"></a>应用 SQL 转换

本文介绍 Azure 机器学习设计器（预览）的模块。

使用 "应用 SQL 转换" 模块，您可以：
  
-   创建结果表，并将数据集保存在可移植数据库中。  
  
-   对数据类型执行自定义转换，或创建聚合。  
  
-   执行 SQL 查询语句以筛选或更改数据，并以数据表形式返回查询结果。  

> [!IMPORTANT]
> 此模块中使用的 SQL 引擎是**SQLite**。 有关 SQLite 语法的详细信息，请参阅[Sqlite 理解的 SQL](https://www.sqlite.org/index.html) 。  

## <a name="how-to-configure-apply-sql-transformation"></a>如何配置应用 SQL 转换  

模块最多可以使用三个数据集作为输入。 引用连接到每个输入端口的数据集时，必须使用名称 `t1`、`t2`和 `t3`。 表号指示输入端口的索引。  
  
剩余的参数是使用 SQLite 语法的 SQL 查询。 在 " **SQL 脚本**" 文本框中键入多行时，请使用分号终止每个语句。 否则，换行符将转换为空格。  

此模块支持 SQLite 语法的所有标准声明。 有关不支持的语句的列表，请参阅[技术说明](#technical-notes)部分。

##  <a name="technical-notes"></a>技术说明  

本部分包含实现详细信息、提示以及常见问题的解答。

-   端口1上始终需要输入。  
  
-   对于包含空格或其他特殊字符的列标识符，请在引用 `SELECT` 或 `WHERE` 子句中的列时，始终将列标识符用方括号或双引号引起来。  
  
### <a name="unsupported-statements"></a>语句不受支持  

尽管 SQLite 支持很多 ANSI SQL 标准，但它不包含商业关系数据库系统支持的许多功能。 有关详细信息，请参阅[SQLite 理解的 SQL](http://www.sqlite.org/lang.html)。 另外，在创建 SQL 语句时，请注意以下限制：  
  
- SQLite 使用动态类型化值，而不是像在大多数关系数据库系统中那样将类型分配给列。 它是弱类型，允许隐式类型转换。  
  
- `LEFT OUTER JOIN` 实现但不 `RIGHT OUTER JOIN` 或 `FULL OUTER JOIN`。  

- 可以将 `RENAME TABLE` 和 `ADD COLUMN` 语句与 `ALTER TABLE` 命令一起使用，但不支持其他子句，包括 `DROP COLUMN`、`ALTER COLUMN`和 `ADD CONSTRAINT`。  
  
- 可以在 SQLite 内创建视图，但此后视图是只读的。 不能对视图执行 `DELETE`、`INSERT`或 `UPDATE` 语句。 但是，你可以创建一个触发器，该触发器将尝试对视图 `DELETE`、`INSERT`或 `UPDATE`，并在触发器的正文中执行其他操作。  
  

除了在官方 SQLite 网站上提供的不受支持的函数的列表之外，以下 wiki 还提供了其他不支持的功能的列表： [SQLite 不](http://www2.sqlite.org/cvstrac/wiki?p=UnsupportedSql)支持的 SQL  
    
## <a name="next-steps"></a>后续步骤

查看可用于 Azure 机器学习服务[的模块集](module-reference.md)。 
