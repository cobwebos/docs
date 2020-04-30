---
title: 机器学习服务 R （预览）
description: 本文介绍了 Azure SQL Database 机器学习服务（使用 R），并说明了其工作原理。
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 11/20/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 46ca4661d06b52c861431a680a69297575ac99b0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81461406"
---
# <a name="azure-sql-database-machine-learning-services-with-r-preview"></a>使用 R 的 Azure SQL 数据库机器学习服务（预览版）

机器学习服务是 Azure SQL 数据库的一项功能，用于执行数据库中的 R 脚本。 该功能包括用于高性能预测分析和机器学习的 Microsoft R 包。 关系数据可以通过存储过程、包含 R 语句的 T-SQL 脚本或包含 T-SQL 的 R 代码在 R 脚本中使用。

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="what-you-can-do-with-r"></a>R 的功能

使用 R 语言的强大功能在数据库中提供高级分析和机器学习。 该功能将计算和处理功能带到了数据所在的位置，不需要通过网络拉取数据。 此外，还可以利用企业 R 包的强大功能提供大规模的高级分析。

机器学习服务包括了 R 的基本发行版，其中包含 Microsoft 提供的企业 R 包。 Microsoft 的 R 函数和算法是为规模和实用性而设计的，提供预测分析、统计建模、数据可视化以及先进的机器学习算法。

### <a name="r-packages"></a>R 包

大多数常见的开源 R 包都预安装在机器学习服务中。 还包括 Microsoft 提供的以下 R 包：

| R 程序包 | 说明|
|-|-|
| [Microsoft R Open](https://mran.microsoft.com/rro) | Microsoft R Open 是 Microsoft 提供的增强型发行版 R。 它是用于统计分析和数据科学的完整开源平台。 它基于 R，并 100% 与 R 兼容，包括很多其他功能，可以带来更高的性能和可再现性。 |
| [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler) | RevoScaleR 是可缩放 R 的主库。该库中的函数属于使用最广泛的函数。 这些库中包含的函数可用于数据转换和操作、统计汇总、可视化以及许多形式的建模和分析。 另外，这些库中的函数可以自动地跨可用核心分发工作负荷，以便对其进行并行分析，并且可以处理由计算引擎协调和管理的大块数据。 |
| [MicrosoftML (R)](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-microsoftml) | MicrosoftML 增加了机器学习算法，可以创建进行文本分析、图像分析和情绪分析的自定义模型。 |

除了预安装的包外，还可以[安装其他包](sql-database-machine-learning-services-add-r-packages.md)。

<a name="signup"></a>

## <a name="sign-up-for-the-preview-closed"></a>注册预览（已关闭）

> [!IMPORTANT]
> 注册 Azure SQL Database 机器学习服务（预览版）当前**已关闭**。

## <a name="next-steps"></a>后续步骤

- 请参阅[SQL Server 机器学习服务的主要区别](sql-database-machine-learning-services-differences.md)。
- 若要了解如何使用 R 查询 Azure SQL 数据库机器学习服务（预览版），请参阅[快速入门指南](sql-database-connect-query-r.md)。
- 若要开始了解一些简单的 R 脚本，请参阅[在 AZURE SQL 数据库中创建和运行简单的 r 脚本机器学习服务（预览版）](sql-database-quickstart-r-create-script.md)。
