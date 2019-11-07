---
title: Azure Analysis Services 别名服务器名称 | Microsoft Docs
description: 了解如何创建 Azure Analysis Services 服务器名称别名。 然后，用户可以使用较短的别名，而不是服务器名称连接到服务器。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 5e7017fad90e32cb8c4b952987fe248e463e4d03
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73572293"
---
# <a name="alias-server-names"></a>别名服务器名称

通过使用服务器名称别名，用户可以使用较短的“别名”而非服务器名称来连接到 Azure Analysis Services 服务器。 从客户端应用程序进行连接时，可以使用 **link://** 协议格式将别名指定为终结点。 然后，终结点会返回进行连接所需的真实的服务器名称。

别名服务器名称在下列方面具有优势：

- 在不影响用户的情况下在服务器之间迁移模型。 
- 用户更容易记住友好的服务器名称。 
- 在一天中的不同时间将用户定向到不同的服务器。 
- 将不同区域中的用户定向到在地理上更近的实例，就像使用 Azure 流量管理器时一样。 

任何返回有效的 Azure Analysis Services 服务器名称的 HTTPS 终结点都可以充当别名。 终结点必须通过端口 443 支持 HTTPS，且不得在 URI 中指定端口。

![使用链接格式的别名](media/analysis-services-alias/aas-alias-browser.png)

从客户端进行连接时，将使用 **link://** 协议格式输入别名服务器名称。 例如，在 Power BI Desktop 中：

![Power BI Desktop 连接](media/analysis-services-alias/aas-alias-connect-pbid.png)

## <a name="create-an-alias"></a>创建别名

若要创建别名终结点，可以使用可返回有效的 Azure Analysis Services 服务器名称的任何方法。 例如，引用 Azure Blob 存储中包含实际服务器名称的一个文件，或者创建并发布一个 ASP.NET Web 窗体应用程序。

在此示例中，ASP.NET Web 窗体应用程序是在 Visual Studio 中创建的。 母版页引用和用户控件已从 Default.aspx 页面中删除。 Default.aspx 的内容只是以下 Page 指令：

```
<%@ Page Title="Home Page" Language="C#" AutoEventWireup="true" CodeBehind="Default.aspx.cs" Inherits="FriendlyRedirect._Default" %>
```

Default.aspx.cs 中的 Page_Load 事件使用 Response.Write() 方法返回 Azure Analysis Services 服务器名称。

```
protected void Page_Load(object sender, EventArgs e)
{
    this.Response.Write("asazure://<region>.asazure.windows.net/<servername>");
}
```

## <a name="see-also"></a>另请参阅

[客户端库](analysis-services-data-providers.md)   
[从 Power BI Desktop 进行连接](analysis-services-connect-pbi.md)
