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
ms.openlocfilehash: ca223de2bc0b26e4968d400ea418761a399dacae
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79268933"
---
# <a name="azure-sql-database-machine-learning-services-with-r-preview"></a>Azure SQL Database 机器学习服务 R （预览版）

机器学习服务是 Azure SQL 数据库的一项功能，用于执行数据库中的 R 脚本。 该功能包括用于高性能预测分析和机器学习的 Microsoft R 包。 关系数据可以通过存储过程、包含 R 语句的 T-SQL 脚本或包含 T-SQL 的 R 代码在 R 脚本中使用。

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

> [!NOTE]
> 预览版适用于单一数据库和弹性池，在**常规用途**和**业务关键**服务层中使用基于 vCore 的购买模型。 在此初始预览中，不支持**超大规模**服务层和**托管实例**部署选项。 目前，R 是唯一受支持的语言。 目前不支持 Python。
>
> 预览版目前在以下区域中提供：西欧、北欧、美国西部2、美国东部、美国中南部、美国中北部、加拿大中部、东南亚、印度南部和澳大利亚东南部。

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

## <a name="sign-up-for-the-preview"></a>注册预览版

> [!IMPORTANT]
> 注册 Azure SQL Database 机器学习服务（预览版）当前已关闭。

预览期间，不建议在生产工作负荷中使用 R 机器学习服务。

## <a name="next-steps"></a>后续步骤

- 请参阅[SQL Server 机器学习服务的主要区别](sql-database-machine-learning-services-differences.md)。
- 若要了解如何使用 R 查询 Azure SQL 数据库机器学习服务（预览版），请参阅[快速入门指南](sql-database-connect-query-r.md)。
- 若要开始了解一些简单的 R 脚本，请参阅[在 AZURE SQL 数据库中创建和运行简单的 r 脚本机器学习服务（预览版）](sql-database-quickstart-r-create-script.md)。
