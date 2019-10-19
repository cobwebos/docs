---
title: 安装适用于 SQL 数据仓库的 Visual Studio 2019 |Microsoft Docs
description: 安装适用于 Azure SQL 数据仓库的 Visual Studio 和 SQL Server 开发工具 (SSDT)
services: sql-data-warehouse
ms.custom: vs-azure
ms.workload: azure-vs
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 10/17/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 81c709e7705e16484438ab684a6b1591e5e624ba
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553518"
---
# <a name="getting-started-with-visual-studio-2019-for-sql-data-warehouse"></a>适用于 SQL 数据仓库的 Visual Studio 2019 入门
Visual Studio **2019** SQL SERVER DATA TOOLS （SSDT）是一个工具，可用于执行以下操作：

- 为 SQL 数据仓库连接、查询和开发应用程序 
- 利用对象资源管理器以可视化方式浏览数据模型中的所有对象，包括表、视图、存储过程等。
- 为对象生成 T-sql 数据定义语言（DDL）脚本
- 使用基于状态的方法和 SSDT 数据库项目来开发数据仓库
- 将数据库项目与源代码管理系统（如 Git）与 Azure DevOps 存储库集成
- 通过自动化服务器（如 Azure DevOps）设置持续集成和部署管道

> [!NOTE]
> 目前，Visual Studio SSDT 数据库项目处于预览阶段。 若要接收有关此功能的定期更新，请在 [UserVoice] 上投票。

## <a name="install-visual-studio-2019-preview"></a>安装 Visual Studio 2019 预览版
请参阅[下载 Visual studio 2019][]下载并安装 visual studio **16.3 及更高**版本。 在安装过程中，选择数据存储和处理工作负荷。 Visual Studio 2019 中不再需要单独的 SSDT 安装。

## <a name="reporting-issues-with-ssdt-visual-studio-2019-preview"></a>报告 SSDT Visual Studio 2019 （预览版）的问题
若要在将 SSDT 与 SQL 数据仓库配合使用时报告问题，请通过电子邮件发送以下电子邮件通讯组列表： <sqldwssdtpreview@service.microsoft.com>

## <a name="next-steps"></a>后续步骤
现在，你已安装了最新版本的 SSDT，接下来可以[连接][connect]到 SQL 数据仓库。

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[connect]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[下载 Visual Studio 2019]: https://visualstudio.microsoft.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
