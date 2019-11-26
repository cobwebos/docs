---
title: Machine Learning Services with R (preview)
description: This article describes Azure SQL Database Machine Learning Services (with R) and explains how it works.
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
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2019
ms.locfileid: "74462351"
---
# <a name="azure-sql-database-machine-learning-services-with-r-preview"></a>Azure SQL Database Machine Learning Services with R (preview)

机器学习服务是 Azure SQL 数据库的一项功能，用于执行数据库中的 R 脚本。 该功能包括用于高性能预测分析和机器学习的 Microsoft R 包。 关系数据可以通过存储过程、包含 R 语句的 T-SQL 脚本或包含 T-SQL 的 R 代码在 R 脚本中使用。

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

> [!NOTE]
> The preview is available for single databases and elastic pools using the vCore-based purchasing model in the **general purpose** and **business critical** service tiers. In this initial preview, the **hyperscale** service tier and the **managed instance** deployment option are not supported. 目前，R 是唯一受支持的语言。 目前不支持 Python。
>
> The preview is currently available in the following regions: West Europe, North Europe, West US 2, East US, South Central US, North Central US, Canada Central, Southeast Asia, India South, and Australia Southeast.

## <a name="what-you-can-do-with-r"></a>R 的功能

使用 R 语言的强大功能在数据库中提供高级分析和机器学习。 该功能将计算和处理功能带到了数据所在的位置，不需要通过网络拉取数据。 Also, you can leverage the power of enterprise R packages to deliver advanced analytics at scale.

机器学习服务包括了 R 的基本发行版，其中包含 Microsoft 提供的企业 R 包。 Microsoft 的 R 函数和算法是为规模和实用性而设计的，提供预测分析、统计建模、数据可视化以及先进的机器学习算法。

### <a name="r-packages"></a>R 包

Most common open-source R packages are pre-installed in Machine Learning Services. 此外还包括 Microsoft 提供的下述 R 包：

| R 程序包 | 描述|
|-|-|
| [Microsoft R Open](https://mran.microsoft.com/rro) | Microsoft R Open 是 Microsoft 提供的增强型发行版 R。 It is a complete open-source platform for statistical analysis and data science. 它基于 R 且 100% 兼容 R，并且包括其他可以增强性能和可再现性的功能。 |
| [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler) | RevoScaleR 是可缩放 R 的主库。该库中的函数属于使用最广泛的函数。 这些库中包含的函数可用于数据转换和操作、统计汇总、可视化以及许多形式的建模和分析。 另外，这些库中的函数可以自动地跨可用核心分发工作负荷，以便对其进行并行分析，并且可以处理由计算引擎协调和管理的大块数据。 |
| [MicrosoftML (R)](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-microsoftml) | MicrosoftML 增加了机器学习算法，可以创建进行文本分析、图像分析和情绪分析的自定义模型。 |

In addition to the pre-installed packages, you can [install additional packages](sql-database-machine-learning-services-add-r-packages.md).

<a name="signup"></a>

## <a name="sign-up-for-the-preview"></a>注册预览版

> [!IMPORTANT]
> Sign up for Azure SQL Database Machine Learning Services (preview) is currently closed.

Machine Learning Services with R is not recommended for production workload during the preview.

## <a name="next-steps"></a>后续步骤

- See the [key differences from SQL Server Machine Learning Services](sql-database-machine-learning-services-differences.md).
- To learn how to use R to query Azure SQL Database Machine Learning Services (preview), see the [Quickstart guide](sql-database-connect-query-r.md).
- To get started with some simple R scripts, see [Create and run simple R scripts in Azure SQL Database Machine Learning Services (preview)](sql-database-quickstart-r-create-script.md).
