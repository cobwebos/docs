---
title: 应用 SQL 转换
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习中的 "应用 SQL 转换" 模块对输入数据集运行 SQLite 查询以转换数据。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 2e44a4861e2522b766aab9c7151d76c471dd2d8c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456528"
---
# <a name="apply-sql-transformation"></a>应用 SQL 转换

本文介绍 Azure 机器学习设计器（预览）的模块。

使用 "应用 SQL 转换" 模块，您可以：
  
-   创建结果表，并将数据集保存在可移植数据库中。  
  
-   对数据类型执行自定义转换或创建聚合。  
  
-   执行 SQL 查询语句筛选或更改数据，并以数据表形式返回查询结果。  

> [!IMPORTANT]
> 此模块中使用的 SQL 引擎是**SQLite**。 有关 SQLite 语法的详细信息，请参阅[Sqlite 理解的 SQL](https://www.sqlite.org/index.html) 。  

## <a name="how-to-configure-apply-sql-transformation"></a>如何配置应用 SQL 转换  

该模块最多可采用三个数据集作为输入。 引用连接到每个输入端口的数据集时，必须使用名称 `t1`、`t2`和 `t3`。 表编号指示输入端口的索引。  
  
剩余参数为 SQL 查询，它使用 SQLite 语法。 在 " **SQL 脚本**" 文本框中键入多行时，请使用分号终止每个语句。 否则，分行符将转换为空格。  

此模块支持 SQLite 语法的所有标准语句。 有关不支持的语句的列表，请参阅[技术说明](#technical-notes)部分。

##  <a name="technical-notes"></a>技术说明  

本部分包含实现详细信息、提示以及常见问题的解答。

-   端口1上始终需要输入。  
  
-   对于包含空格或其他特殊字符的列标识符，请在引用 `SELECT` 或 `WHERE` 子句中的列时，始终将列标识符用方括号或双引号引起来。  
  
### <a name="unsupported-statements"></a>语句不受支持  

尽管 SQLite 支持很多 ANSI SQL 标准，但不包括商业关系数据库系统支持的许多功能。 有关详细信息，请参阅[SQLite 理解的 SQL](http://www.sqlite.org/lang.html)。 另外，在创建 SQL 语句时，请注意以下限制：  
  
- SQLite 使用动态类型的值，而不是像大多数关系数据库系统那样为列分配类型。 它弱化类型使用，并允许进行隐式类型转换。  
  
- `LEFT OUTER JOIN` 实现但不 `RIGHT OUTER JOIN` 或 `FULL OUTER JOIN`。  

- 可以在 `RENAME TABLE` 命令中使用 `ADD COLUMN` 和 `ALTER TABLE` 语句，但不支持其他子句，包括 `DROP COLUMN`、`ALTER COLUMN` 和 `ADD CONSTRAINT`。  
  
- 可以在 SQLite 内创建一个视图，但此后视图都是只读的。 不能对视图执行 `DELETE`、`INSERT` 或 `UPDATE` 语句。 但是，可以创建一个尝试对视图进行 `DELETE`、`INSERT` 或 `UPDATE` 操作的触发器，然后在触发器的正文执行其他操作。  
  

除了在官方 SQLite 网站上提供的不受支持的函数的列表之外，以下 wiki 还提供了其他不支持的功能的列表： [SQLite 不](http://www2.sqlite.org/cvstrac/wiki?p=UnsupportedSql)支持的 SQL  
    
## <a name="next-steps"></a>后续步骤

查看可用于 Azure 机器学习[的模块集](module-reference.md)。 
