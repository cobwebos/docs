---
title: 安装 Visual Studio 2019
description: 安装用于 SQL 分析的可视化工作室和 SQL 服务器开发工具 （SSDT）
services: synapse-analytics
ms.custom: vs-azure, azure-synapse
ms.workload: azure-vs
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: d0a1772706fa838f51322d5f5d5bd1b46eb9144a
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351625"
---
# <a name="getting-started-with-visual-studio-2019"></a>开始使用视觉工作室 2019
Visual Studio **2019** SQL Server Data Tools (SSDT) 是一个工具，可用于执行以下操作：

- 连接、查询和开发应用程序
- 利用对象资源管理器直观地浏览数据模型中的所有对象，包括表、视图、存储过程等。
- 为对象生成 T-SQL 数据定义语言 (DDL) 脚本
- 对 SSDT 数据库项目使用基于状态的方法来开发数据仓库
- 将数据库项目与源代码管理系统（例如 Git 和 Azure Repos）集成
- 通过自动化服务器（如 Azure DevOps）设置持续集成和部署管道

## <a name="install-visual-studio-2019"></a>安装 Visual Studio 2019
请参阅[下载 Visual Studio 2019][] 以下载并安装 Visual Studio **16.3 及更高版本**。 在安装过程中，选择“数据存储和处理”工作负载。 Visual Studio 2019 不再需要单独安装 SSDT。

## <a name="unsupported-features-in-ssdt"></a>SSDT 中不支持的功能

有时 SQL 分析的功能版本可能不包括对 SSDT 的支持。 目前不支持以下功能：

- [具体化视图](https://docs.microsoft.com/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest)（正在开发）
- [有序聚集列存储索引](https://docs.microsoft.com/sql/t-sql/statements/create-columnstore-index-transact-sql?view=azure-sqldw-latest#examples--and-)（正在开发）
- [COPY 语句](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest)（正在开发）
- [工作负荷管理](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-workload-management) - 工作负荷组和分类器（正在开发）
- [行级安全性](https://docs.microsoft.com/sql/relational-databases/security/row-level-security?view=sql-server-ver15)
  - 在此处提交支持票证[或投票以获得](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040057-ssdt-row-level-security)支持功能。
- [动态数据屏蔽](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking?toc=%2Fazure%2Fsql-data-warehouse%2Ftoc.json&view=sql-server-2017#defining-a-dynamic-data-mask)
  - 在此处提交支持票证[或投票以获得](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040048-ssdt-support-dynamic-data-masking)支持功能。 
- [PREDICT](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=sql-server-ver15&viewFallbackFrom=azure-sqldw-latest) 函数 
- [带约束的表](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-table-constraints#table-constraints)不受支持。 对于这些表对象，请将生成操作设为“无”。

## <a name="next-steps"></a>后续步骤

现在，您已经拥有最新版本的 SSDT，就可以[连接到](sql-data-warehouse-query-visual-studio.md)SQL 池了。




<!--Other-->

[下载 Visual Studio 2019]: https://visualstudio.microsoft.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
