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
ms.date: 02/27/2016
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: 03720b926e3a75c5443308f510b38132144318ff
ms.lasthandoff: 03/04/2017


---

# <a name="data-providers-for-connecting-to-azure-analysis-services"></a>用于连接到 Azure Analysis Services 的数据提供程序

数据提供程序，也称客户端库，是客户端应用程序连接到 Analysis Services 服务器所必需的。 

Analysis Services 使用三个数据提供程序。 ADOMD.NET 和 Analysis Services 管理对象 (AMO) 都是托管型数据提供程序。 Analysis Services OLE DB 提供程序 (MSOLAP DLL) 是本机数据提供程序。 通常，所有三个提供程序会同时安装。 Azure Analysis Services 需要最新版数据提供程序。 

Microsoft 客户端应用程序（例如 Power BI Desktop 和 Excel）会安装所有这三个数据提供程序。 不过，安装的数据提供程序可能没有更新到 Azure Analysis Service 所需的最新版本，具体取决于 Excel 的版本，或者 Excel 和 Power BI Desktop 是否每月进行更新。 这同样适用于自定义应用程序或其他界面，例如 AsCmd、TOM、ADOMD.NET。 这些应用程序需要手动安装提供程序。 可以手动安装的数据提供程序作为可分发包随附在 SQL Server 功能包中；但是，这些提供程序与 SQL Server 版本绑定，可能不是最新的。  

进行客户端连接的数据提供程序不同于从 Azure Analysis Services 服务器连接到数据源时需要的数据提供程序。 若要详细了解数据源连接，请参阅[数据源连接](analysis-services-datasource.md)。

## <a name="download-the-latest-preview-data-providers"></a>下载最新**预览**数据提供程序  
使用以下数据提供程序来获取最新 bug 修补程序和更新。 在连接到 Azure Analysis Services 预览版或 SQL Server vNext Analysis Services 时，建议使用这些数据提供程序。

[MSOLAP (amd64) 预览版](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.304.138/1033/x64/SQL_AS_OLEDB.msi)</br>
[MSOLAP (x86) 预览版](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.304.138/1033/x86/SQL_AS_OLEDB.msi)</br>
[AMO 预览版](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.304.138/1033/x64/SQL_AS_AMO.msi)</br>
[ADOMD 预览版](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.304.138/1033/x64/SQL_AS_ADOMD.msi)</br>

## <a name="download-the-latest-rtm-data-providers"></a>下载最新 **RTM** 数据提供程序  
如果处于生产环境中，且需要完全发布和受支持的版本，则使用以下数据提供程序。

[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)</br>
[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)</br>
[AMO](https://go.microsoft.com/fwlink/?linkid=829578)</br>
[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)</br>

## <a name="next-steps"></a>后续步骤
安装最新的数据提供程序以后，即可通过客户端应用程序连接到服务器。 若要详细了解如何从客户端连接，请参阅[从 Azure Analysis Services 获取数据](analysis-services-connect.md)。

