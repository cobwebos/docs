---
title: "如何连接到数据源 | Microsoft Docs"
description: "操作指南文章重点说明如何使用 Azure 数据目录连接到发现的数据源。"
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 4e6b27a5-cf75-4012-b88c-333c1fe638e8
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 05/15/2017
ms.author: maroche
ms.translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 5db1a6f8b3ff0369817e69abaa4d95af843cd7f2
ms.contentlocale: zh-cn
ms.lasthandoff: 11/17/2016


---
# <a name="how-to-connect-to-data-sources"></a>如何连接到数据源
## <a name="introduction"></a>介绍
**Microsoft Azure 数据目录**是一个完全托管的云服务，充当企业数据源的注册系统和发现系统。 换而言之，**Azure 数据目录**旨在帮助人们发现、了解和使用数据源，并帮助组织从其现有数据中获取更多价值。 此方案的一个重要方面在于使用数据 - 用户发现数据源并了解其用途后，下一步就是连接到数据源将其数据投入使用。

## <a name="data-source-locations"></a>数据源位置
在数据源注册期间，**Azure 数据目录**会接收有关数据源的元数据。 此元数据包括数据源位置的详细信息。 位置的详细信息因数据源而不同，但始终包含连接所需的信息。 例如，SQL Server 表的位置包括服务器名称、数据库名称、架构名称和表名，而 SQL Server Reporting Services 报表的位置包括服务器名称和报表的路径。 其他数据源类型的位置会反映源系统的结构和功能。

## <a name="integrated-client-tools"></a>集成客户端工具
要连接到数据源，最简单的方法是使用 “Azure 数据目录”门户中的"打开方式..."菜单。 此菜单会显示一份列表，列表上列有用于连接到所选数据资产的选项。
使用默认磁贴视图时，此菜单在每个磁贴上可用。

 ![从数据资产磁贴在 Excel 中打开 SQL Server 表](./media/data-catalog-how-to-connect/data-catalog-how-to-connect1.png)

使用列表视图时，菜单将显示在门户窗口顶部的搜索栏。

 ![从搜索栏在报表管理器中打开 SQL Server Reporting Services 报表](./media/data-catalog-how-to-connect/data-catalog-how-to-connect2.png)

## <a name="supported-client-applications"></a>支持的客户端应用程序
在 Azure 数据目录门户中为数据源 使用“打开方式...”菜单时，正确的客户端应用程序必须安装在客户端计算机上。

| 在应用程序中打开 | 文件扩展名/协议 | 支持的应用程序版本 |
| --- | --- | --- |
| Excel |.odc |Excel 2010 或更高版本 |
| Excel（前 1000 个） |.odc |Excel 2010 或更高版本 |
| Power Query |.xlsx |安装了 Power Query for Excel 外接程序的Excel 2016 或 Excel 2010 或 Excel 2013 |
| Power BI Desktop |.pbix |Power BI Desktop 2016 年 7 月版或更高版本 |
| SQL Server Data Tools |vsweb:// |安装了 SQL Server 工具的 Visual Studio 2013 Update 4 或更高版本 |
| 报表管理器 |http:// |请参阅 [SQL Server Reporting Services 的浏览器要求](https://technet.microsoft.com/en-us/library/ms156511.aspx) |

## <a name="your-data-your-tools"></a>你的数据，你的工具
菜单中可用的选项取决于当前所选的数据资产类型。 当然，并非所有可能的工具都包括在“打开方式...” 菜单中，但仍可以使用任何客户端工具轻松连接到数据源。 在“Azure 数据目录”门户中选中某数据资产时，会在属性窗格中显示其完整位置。

 ![SQL Server 表的连接信息](./media/data-catalog-how-to-connect/data-catalog-how-to-connect3.png)

连接信息的详细信息因数据源类型而不同，但门户中包含的信息将提供在任何客户端工具中连接到数据源所需的一切内容。 用户可以复制他们使用 **Azure 数据目录**所发现的数据源的连接详细信息，让他们能够使用在所选工具中处理数据。

## <a name="connecting-and-data-source-permissions"></a>连接和数据源权限
尽管 **Azure 数据目录**使得数据源可被发现，但访问数据源本身仍受数据源所有者或管理员控制。 在 **Azure 数据目录**中发现数据源不会向用户提供任何访问数据源本身的权限。

为了方便发现了数据源但没有权限访问其数据的用户，在对数据源进行批注时，用户可以在“请求访问”属性中提供信息。 此处提供的信息（包括过程链接或用于获取数据源访问权限的联系点）与门户中的数据源位置信息一起显示。

 ![包含提供的请求访问指令的连接信息](./media/data-catalog-how-to-connect/data-catalog-how-to-connect4.png)

## <a name="summary"></a>摘要
使用 **Azure 数据目录**注册数据源，将结构性元数据和描述性元数据从数据源复制到目录服务，使数据可被发现。 注册并发现数据源后，用户可以从“Azure 数据目录”门户使用“打开方式...”菜单， 或者使用用户自己选择的数据工具连接到数据源。

## <a name="see-also"></a>另请参阅
* [Azure 数据目录入门](data-catalog-get-started.md)教程，了解有关如何连接到数据源的分步详细说明。

