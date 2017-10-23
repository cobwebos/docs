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
ms.date: 08/15/2017
ms.author: owend
ms.openlocfilehash: a96e7fe671dc051da35168fa7b49ecba53b4c4a5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="client-libraries-for-connecting-to-azure-analysis-services"></a>用于连接到 Azure Analysis Services 的客户端库

客户端应用程序和工具连接到 Analysis Services 服务器时需要使用客户端库。 

Analysis Services 使用三个客户端库。 ADOMD.NET 和 Analysis Services 管理对象 (AMO) 都是托管型客户端库。 Analysis Services OLE DB 提供程序 (MSOLAP DLL) 是本机客户端库。 通常，所有三个库会同时安装。 Azure Analysis Services 需要最新版本。 

Microsoft 客户端应用程序（例如 Power BI Desktop 和 Excel）会安装所有这三个客户端库。 但是，客户端库可能不是 Azure Analysis Services 所需要的最新版本，具体取决于更新的版本或频率。 这同样适用于自定义应用程序或其他界面，例如 AsCmd、TOM、ADOMD.NET。 这些应用程序需要手动安装库。 用于手动安装的客户端库作为可分发程序包包含在 SQL Server 功能包中。 但是，这些客户端库与 SQL Server 版本关联，可能不是最新的。  

进行客户端连接的客户端库不同于从 Azure Analysis Services 服务器连接到数据源时需要的数据提供程序。 若要详细了解数据源连接，请参阅[数据源连接](analysis-services-datasource.md)。

## <a name="download-the-latest-client-libraries"></a>下载最新客户端库  
如果处于生产环境中，且需要完全发布和受支持的版本，则使用以下客户端库。

[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)</br>
[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)</br>
[AMO](https://go.microsoft.com/fwlink/?linkid=829578)</br>
[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)</br>

## <a name="next-steps"></a>后续步骤
[使用 Excel 进行连接](analysis-services-connect-excel.md)    
[使用 Power BI 进行连接](analysis-services-connect-pbi.md)
