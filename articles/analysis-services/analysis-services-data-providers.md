---
title: "连接到 Azure Analysis Services 所需的客户端库 | Microsoft Docs"
description: "介绍了客户端应用程序和工具连接 Azure Analysis Services 时所需的客户端库"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 12/14/2017
ms.author: owend
ms.openlocfilehash: 870d430d1926859894f452e0af812d794272a9e6
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2017
---
# <a name="client-libraries-for-connecting-to-azure-analysis-services"></a>用于连接到 Azure Analysis Services 的客户端库

客户端应用程序和工具连接到 Analysis Services 服务器时需要使用客户端库。 

## <a name="download-the-latest-client-libraries"></a>下载最新客户端库  

|下载  |版本  | 
|---------|---------|
|[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)    |    14.0.801.241      |
|[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)     |    14.0.801.241      |
|[AMO](https://go.microsoft.com/fwlink/?linkid=829578)     |   14.0.800.117      |
|[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)     |    14.0.801.241      |

## <a name="understanding-client-libraries"></a>了解客户端库

Analysis Services 利用三个客户端库（也称为数据提供程序）。 ADOMD.NET 和 Analysis Services 管理对象 (AMO) 都是托管型客户端库。 Analysis Services OLE DB 提供程序 (MSOLAP DLL) 是本机客户端库。 通常，所有三个库会同时安装。 **Azure Analysis Services 要求安装所有三个库的最新版本**。 

Microsoft 客户端应用程序（例如 Power BI Desktop 和 Excel）会安装所有这三个客户端库，有新版本可用时，会更新这些库。 某些客户端库可能不是 Azure Analysis Services 所需要的最新版本，具体取决于更新的版本或频率。 这同样适用于自定义应用程序或其他界面，例如 AsCmd、TOM、ADOMD.NET。 这些应用程序需要手动或以编程方式安装库。 用于手动安装的客户端库作为可分发程序包包含在 SQL Server 功能包中。 但是，这些客户端库与 SQL Server 版本关联，可能不是最新的。  

进行客户端连接的客户端库不同于从 Azure Analysis Services 服务器连接到数据源时需要的数据提供程序。 若要详细了解数据源连接，请参阅[数据源连接](analysis-services-datasource.md)。

## <a name="client-library-types"></a>客户端库类型

### <a name="analysis-services-ole-db-provider-msolap"></a>Analysis Services OLE DB 提供程序 (MSOLAP) 

 Analysis Services OLE DB 提供程序 (MSOLAP) 是用于建立 Analysis Services 数据库连接的本机客户端库。 ADOMD.NET 和 AMO 间接使用它向数据提供程序委托连接请求。 也可以直接从应用程序代码调用 OLE DB 提供程序。  
  
 用于访问 Analysis Services 数据库的大多数工具和客户端应用程序可自动安装 Analysis Services OLE DB 提供程序。 必须将它安装在用于访问 Analysis Services 数据的计算机上。  
  
 OLE DB 提供程序通常在连接字符串中指定。 Analysis Services 连接字符串使用不同的命名法来引用 OLE DB 提供程序：MSOLAP.\<版本>.dll。

### <a name="amo"></a>AMO  

 AMO 是用于服务器管理和数据定义的托管客户端库。 它由工具和客户端应用程序安装和使用。 例如，SQL Server Management Studio (SSMS) 使用 AMO 连接到 Analysis Services。  
  
 使用 AMO 的连接通常非常精简，由 `“data source=\<servername>”` 组成。 建立连接后，可以使用 API 来处理数据库集合和主要对象。 SSDT 和 SSMS 都使用 AMO 连接到 Analysis Services 实例。  

  
### <a name="adomd"></a>ADOMD

 ADOMD.NET 是用于查询 Analysis Services 数据的托管数据客户端库。 它由工具和客户端应用程序安装和使用。 
  
 连接到数据库时，所有三个库的连接字符串属性相似。 使用 [Microsoft.AnalysisServices.AdomdClient.AdomdConnection.ConnectionString](https://msdn.microsoft.com/library/microsoft.analysisservices.adomdclient.adomdconnection.connectionstring.aspx) 为 ADOMD.NET 定义的几乎任何连接字符串同样适用于 AMO 和 Analysis Services OLE DB 提供程序 (MSOLAP)。 若要了解详细信息，请参阅[连接字符串属性 &#40;Analysis Services&#41;](https://docs.microsoft.com/sql/analysis-services/instances/connection-string-properties-analysis-services)。  

  
##  <a name="bkmk_LibUpdate"></a>如何确定客户端库版本   
  
### <a name="oleddb-msolap"></a>OLEDDB (MSOLAP)  
  
1.  转到 `C:\Program Files\Microsoft Analysis Services\AS OLEDB\140`。 如果有多个文件夹，请选择较大的数字。
  
2.  右键单击“msolap140.dll” > ，选择“属性” > “详细信息”。  
    
    ![客户端库详细信息](media/analysis-services-data-providers/aas-msolap-details.png)
  
### <a name="amo"></a>AMO

1. 转到 `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices\v4.0_14.0.0.0__89845dcd8080cc91`。
2. 右键单击“Microsoft.AnalysisServices” > ，选择“属性” > “详细信息”。  

### <a name="adomd"></a>ADOMD

1. 转到 `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices.AdomdClient\v4.0_14.0.0.0__89845dcd8080cc91`。
2. 右键单击“Microsoft.AnalysisServices.AdomdClient” > ，选择“属性” > “详细信息”。  


## <a name="next-steps"></a>后续步骤
[使用 Excel 进行连接](analysis-services-connect-excel.md)    
[使用 Power BI 进行连接](analysis-services-connect-pbi.md)
