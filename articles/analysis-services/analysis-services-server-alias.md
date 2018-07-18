---
title: Azure Analysis Services 别名服务器名称 | Microsoft Docs
description: 介绍了如何创建和使用服务器名称别名。
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/03/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: d0aebbe115be9e9af697b5d93d158a263fefe55c
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2018
ms.locfileid: "37448022"
---
# <a name="alias-server-names"></a>别名服务器名称

通过使用服务器名称别名，用户可以使用较短的“别名”而非服务器名称来连接到 Azure Analysis Services 服务器。 从客户端应用程序进行连接时，将使用 **link://** 协议格式将别名指定为终结点。 然后，终结点返回实际的服务器名称以便进行连接。

别名服务器名称在下列方面具有优势：

- 在不影响用户的情况下在服务器之间迁移模型。 
- 友好的服务器名称更方便用户记忆。 
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
