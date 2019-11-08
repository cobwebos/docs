---
title: 安装 Visual Studio 2019
description: 安装适用于 Azure SQL 数据仓库的 Visual Studio 和 SQL Server 开发工具 (SSDT)
services: sql-data-warehouse
ms.custom: seo-lt-2019
ms.workload: azure-vs
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 11/06/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 96d66e6190732d88ba9a33094b6c27bbadfd4aae
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747804"
---
# <a name="getting-started-with-visual-studio-2019-for-sql-data-warehouse"></a>适用于 SQL 数据仓库的 Visual Studio 2019 入门
Visual Studio **2019** SQL Server Data Tools (SSDT) 是一个工具，可用于执行以下操作：

- 连接、查询和开发 SQL 数据仓库的应用程序 
- 利用对象资源管理器直观地浏览数据模型中的所有对象，包括表、视图、存储过程等。
- 为对象生成 T-SQL 数据定义语言 (DDL) 脚本
- 对 SSDT 数据库项目使用基于状态的方法来开发数据仓库
- 将数据库项目与 Azure Repos 的 Git 等源代码管理系统集成
- 通过自动化服务器（如 Azure DevOps）设置持续集成和部署管道

## <a name="install-visual-studio-2019"></a>安装 Visual Studio 2019
请参阅[下载 Visual studio 2019][]下载并安装 visual studio **16.3 及更高**版本。 在安装过程中，选择“数据存储和处理”工作负载。 Visual Studio 2019 不再需要单独安装 SSDT。

## <a name="next-steps"></a>后续步骤
安装最新版本的 SSDT 后，便可以[连接][connect]到 SQL 数据仓库。

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[connect]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[下载 Visual Studio 2019]: https://visualstudio.microsoft.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
