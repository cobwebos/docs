---
title: "客户端需要数据提供程序才能连接到 Azure Analysis Services | Microsoft 文档"
description: "说明客户端连接 Azure Analysis Services 时需要的数据提供程序"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 01/20/2016
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 4dbd2c87d648f6923772b070fdfe7379de22e6c5
ms.openlocfilehash: e1adeaca702de3bad1c2e572f89ade9eba753173


---

# <a name="data-providers-for-connecting-to-azure-analysis-services"></a>用于连接到 Azure Analysis Services 的数据提供程序

数据提供程序，也称客户端库，是客户端应用程序连接到 Analysis Services 服务器所必需的。 

Analysis Services 使用三个数据提供程序。 ADOMD.NET 和 Analysis Services 管理对象 (AMO) 都是托管型数据提供程序。 Analysis Services OLE DB 提供程序 (MSOLAP DLL) 是本机数据提供程序。 通常，所有三个提供程序会同时安装。 Azure Analysis Services 需要最新版数据提供程序。 

Microsoft 客户端应用程序（例如 Power BI Desktop 和 Excel）会安装所有这三个数据提供程序。 不过，安装的数据提供程序可能没有更新到 Azure Analysis Service 所需的最新版本，具体取决于 Excel 的版本，或者 Excel 和 Power BI Desktop 是否每月进行更新。 这同样适用于自定义应用程序或其他界面，例如 AsCmd、TOM、ADOMD.NET。 这些应用程序需要手动安装提供程序。 可以手动安装的数据提供程序作为可分发包随附在 SQL Server 功能包中；但是，这些提供程序与 SQL Server 版本绑定，可能不是最新的。  

进行客户端连接的数据提供程序不同于从 Azure Analysis Services 服务器连接到数据源时需要的数据提供程序。 若要详细了解数据源连接，请参阅[数据源连接](analysis-services-datasource.md)。
 
## <a name="download-the-latest-data-providers"></a>下载最新数据提供程序  

[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)</br>
[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)</br>
[AMO](https://go.microsoft.com/fwlink/?linkid=829578)</br>
[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)</br>

## <a name="next-steps"></a>后续步骤
安装最新的数据提供程序以后，即可通过客户端应用程序连接到服务器。 若要详细了解如何进行连接，请参阅[从 Azure Analysis Services 获取数据](analysis-services-connect.md)。



<!--HONumber=Jan17_HO1-->


