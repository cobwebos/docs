---
title: "连接到 Azure Analysis Services 所需的客户端库 | Microsoft Docs"
description: "介绍了客户端应用程序和工具连接 Azure Analysis Services 时所需的客户端库"
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
ms.date: 04/14/2016
ms.author: owend
ms.translationtype: Human Translation
ms.sourcegitcommit: e72275ffc91559a30720a2b125fbd3d7703484f0
ms.openlocfilehash: 6457b7d30b12a7bb138f9912e21418742814be09
ms.contentlocale: zh-cn
ms.lasthandoff: 05/05/2017


---

# <a name="client-libraries-for-connecting-to-azure-analysis-services"></a>用于连接到 Azure Analysis Services 的客户端库

客户端应用程序和工具连接到 Analysis Services 服务器时需要使用客户端库。 

Analysis Services 使用三个客户端库。 ADOMD.NET 和 Analysis Services 管理对象 (AMO) 都是托管型客户端库。 Analysis Services OLE DB 提供程序 (MSOLAP DLL) 是本机客户端库。 通常，所有三个库会同时安装。 Azure Analysis Services 需要最新版本。 

Microsoft 客户端应用程序（例如 Power BI Desktop 和 Excel）会安装所有这三个客户端库。 不过，安装的客户端库可能没有更新到 Azure Analysis Service 所需的最新版本，具体取决于 Excel 的版本，或者 Excel 和 Power BI Desktop 是否每月进行更新。 这同样适用于自定义应用程序或其他界面，例如 AsCmd、TOM、ADOMD.NET。 这些应用程序需要手动安装库。 可以手动安装的客户端库作为可分发包随附在 SQL Server 功能包中；但是，这些提供程序与 SQL Server 版本绑定，可能不是最新的。  

进行客户端连接的客户端库不同于从 Azure Analysis Services 服务器连接到数据源时需要的数据提供程序。 若要详细了解数据源连接，请参阅[数据源连接](analysis-services-datasource.md)。

## <a name="download-the-latest-preview-client-libraries"></a>下载最新**预览**客户端库  
使用以下客户端库来获取最新 bug 修补程序和更新。 在连接到 Azure Analysis Services 或 SQL Server 2017 Analysis Services 时，建议使用这些客户端库。

[MSOLAP (amd64) 预览版](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.500.170/amd64/SQL_AS_OLEDB.msi)</br>
[MSOLAP (x86) 预览版](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.500.170/x86/SQL_AS_OLEDB.msi)</br>
[AMO 预览版](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.500.170/SQL_AS_AMO.msi)</br>
[ADOMD 预览版](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.500.170/SQL_AS_ADOMD.msi)</br>

## <a name="download-the-latest-rtm-client-libraries"></a>下载最新 **RTM** 客户端库  
如果处于生产环境中，且需要完全发布和受支持的版本，则使用以下客户端库。

[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)</br>
[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)</br>
[AMO](https://go.microsoft.com/fwlink/?linkid=829578)</br>
[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)</br>

## <a name="next-steps"></a>后续步骤
[连接到 Azure Analysis Services 服务器](analysis-services-connect.md)。

