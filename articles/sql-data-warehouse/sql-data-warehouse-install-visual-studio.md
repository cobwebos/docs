---
title: "安装 Visual Studio 和 SSDT 适用于 SQL 数据仓库 |Microsoft 文档"
description: "安装 Visual Studio 和 SQL Server 开发 Tools (SSDT) 适用于 Azure SQL 数据仓库"
services: sql-data-warehouse
documentationcenter: NA
author: antvgski
manager: jhubbard
editor: 
ms.assetid: 0ed9b406-9b42-4fe6-b963-fe0a5b48aac1
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: connect
ms.date: 03/30/2017
ms.author: anvang;barbkess
ms.openlocfilehash: f7023b78c241a7bc8014276cd0bfa455165b42cc
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="install-visual-studio-and-ssdt-for-sql-data-warehouse"></a>安装 Visual Studio 和 SSDT 适用于 SQL 数据仓库
开发应用程序的 SQL 数据仓库，我们建议使用带有最新版本的 SQL Server Data Tools (SSDT) 的 Visual Studio 的最新版本。  使用 SSDT 的 visual Studio 2013 Update 5 还支持向后兼容。  

使用 SSDT 的 Visual Studio 将允许你使用 SQL Server 对象资源管理器来直观地浏览表、 视图、 存储的过程和 SQL 数据仓库中的许多详细对象以及运行查询。

> [!NOTE]
> SQL 数据仓库尚不支持 Visual Studio 数据库项目。  此功能将添加的未来版本中。
> 
> 

## <a name="step-1-install-visual-studio"></a>步骤 1： 安装 Visual Studio
单击下面的链接以下载并安装 Visual Studio。 如果你尚未安装 Visual Studio 2013 或更高版本安装，则可以跳到步骤 2，安装 SSDT。

1. [下载 Visual Studio][]。
2. 请按照[安装 Visual Studio] [ Installing Visual Studio] MSDN 上的指导，并选择默认配置。

## <a name="step-2-install-ssdt"></a>步骤 2： 安装 SSDT
若要安装适用于 Visual Studio 的 SSDT 只需检查的 SSDT 更新从 Visual Studio 中通过执行以下步骤。

1. Visual Studio 中单击**工具** / **扩展和更新...** / **更新**
2. 选择**产品更新**，然后查找**程序数据库工具的 Microsoft SQL Server 更新**

如果未找到更新，你应已安装的最新版本。  若要确认安装 SSDT，请单击**帮助** / **有关 Microsoft Visual Studio** ，然后在列表中查找 SQL Server Data Tools。  最新版本的 SSDT 是 14.0.60525.0。  如果安装的选项不可用从 Visual Studio，或者你可以访问[SSDT 下载][ SSDT Download]页后，可以下载并手动安装 SSDT。

## <a name="next-steps"></a>后续步骤
现在，你拥有最新版本的 SSDT，你就到[连接][ connect]到 SQL 数据仓库。

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[connect]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[下载 Visual Studio]: https://www.visualstudio.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
