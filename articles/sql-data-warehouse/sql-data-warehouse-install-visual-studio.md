---
title: 安装 Visual Studio 2019
description: 安装适用于 SQL Analytics 的 Visual Studio 和 SQL Server 开发工具（SSDT）
services: sql-data-warehouse
ms.custom: azure-synapse
ms.workload: azure-vs
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: b9ad0f4aedc5a095bfa70dec1b67e52cd4d152e0
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195333"
---
# <a name="getting-started-with-visual-studio-2019"></a>Visual Studio 2019 入门
Visual Studio **2019** SQL SERVER DATA TOOLS （SSDT）是一个工具，可用于执行以下操作：

- 连接、查询和开发应用程序
- 利用对象资源管理器以可视化方式浏览数据模型中的所有对象，包括表、视图、存储过程等。
- 为对象生成 T-sql 数据定义语言（DDL）脚本
- 使用基于状态的方法和 SSDT 数据库项目来开发数据仓库
- 将数据库项目与 Azure Repos 的 Git 等源代码管理系统集成
- 通过自动化服务器（如 Azure DevOps）设置持续集成和部署管道

## <a name="install-visual-studio-2019"></a>安装 Visual Studio 2019
请参阅[下载 Visual studio 2019][]下载并安装 visual studio **16.3 及更高**版本。 在安装过程中，选择数据存储和处理工作负荷。 Visual Studio 2019 中不再需要单独的 SSDT 安装。

## <a name="unsupported-features-in-ssdt"></a>SSDT 中不支持的功能

有时，SQL Analytics 的功能版本可能不包括对 SSDT 的支持。 当前不支持以下功能：

- [具体化视图](https://docs.microsoft.com/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest)（正在进行）
- [有序聚集列存储索引](https://docs.microsoft.com/sql/t-sql/statements/create-columnstore-index-transact-sql?view=azure-sqldw-latest#examples--and-)（进行中）
- [COPY 语句](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest)（进行中）
- [工作负荷管理](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-workload-management)-工作负荷组和分类器（进行中）
- [行级安全](https://docs.microsoft.com/sql/relational-databases/security/row-level-security?view=sql-server-ver15)
  - 提交支持票证或[在此处](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040057-ssdt-row-level-security)投票以获取支持的功能。
- [动态数据屏蔽](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking?toc=%2Fazure%2Fsql-data-warehouse%2Ftoc.json&view=sql-server-2017#defining-a-dynamic-data-mask)
  - 提交支持票证或[在此处](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040048-ssdt-support-dynamic-data-masking)投票以获取支持的功能。 
- [PREDICT](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=sql-server-ver15&viewFallbackFrom=azure-sqldw-latest)函数 
- 不支持[具有约束的表](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-table-constraints#table-constraints)。 对于这些表对象，请将生成操作设置为 "无"。

## <a name="next-steps"></a>后续步骤

既然已安装了最新版本的 SSDT，就可以[连接][connect]到 SQL 池了。

<!--Anchors-->

<!--Image references-->

<!--Articles-->

[connect]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->

[下载 Visual Studio 2019]: https://visualstudio.microsoft.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
