---
title: 连接到 Azure Analysis Services 服务器 | Microsoft Docs
description: 了解如何连接到 Azure 中的 Analysis Services 服务器并从中获取数据。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions
ms.openlocfilehash: 93b1e9a2ea052a39e891cf9084b632f434c341df
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92013689"
---
# <a name="connecting-to-servers"></a>连接到服务器

本文介绍如何通过将数据建模和管理应用程序（例如 SQL Server Management Studio (SSMS) 或 Visual Studio）与 Analysis Services 项目或客户端报告应用程序（例如Microsoft Excel、Power BI Desktop 或自定义应用程序）一起使用来连接服务器。 使用 HTTPS 连接到 Azure Analysis Services。

## <a name="client-libraries"></a>客户端库

[获取最新的客户端库](/analysis-services/client-libraries?view=azure-analysis-services-current)

与服务器的所有连接（无论连接类型）都需要更新后的 AMO、ADOMD.NET 和 OLEDB 客户端库才能连接到 Analysis Services 服务器。 对于 SSMS、Visual Studio、Excel 2016 及更高版本和 Power BI，最新的客户端库会与每月发布一起安装或更新。 但是在某些情况下，应用程序可能不是最新版本。 例如，当策略延迟更新时，或者 Microsoft 365 更新位于延迟通道上时。

> [!NOTE]
> 客户端库无法通过需要用户名和密码的代理服务器连接到 Azure Analysis Services。 

## <a name="server-name"></a>服务器名称

在 Azure 中创建 Analysis Services 服务器时，可以指定唯一名称以及要在其中创建服务器的区域。 在连接中指定服务器名称时，服务器命名方案为：

```
<protocol>://<region>/<servername>
```
 其中，协议是字符串 **asazure**，区域是在其中创建服务器的 URI（例如 westus.asazure.windows.net），服务器名称是该区域中的唯一服务器名称。

### <a name="get-the-server-name"></a>获取服务器名称

在 **Azure 门户**中，单击“服务器”>“概述” > “服务器名称”，并复制整个服务器名称。 如果组织中的其他用户也要连接此服务器，则可以将此服务器名称与他们共享。 指定服务器名称时，必须使用完整路径。

![在 Azure 中获取服务器名称](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

> [!NOTE]
> 美国东部2地区的协议为 **aspaaseastus2**。

## <a name="connection-string"></a>连接字符串

使用表格对象模型连接到 Azure Analysis Services 时，使用以下连接字符串格式：

###### <a name="integrated-azure-active-directory-authentication"></a>集成的 Azure Active Directory 身份验证

集成的身份验证将选取 Azure Active Directory 凭据缓存（如果可用）。 如果不可用，则会显示 Azure 登录窗口。

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;"
```


###### <a name="azure-active-directory-authentication-with-username-and-password"></a>使用用户名和密码进行 Azure Active Directory 身份验证

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;User ID=<user name>;Password=<password>;Persist Security Info=True; Impersonation Level=Impersonate;";
```

###### <a name="windows-authentication-integrated-security"></a>Windows 身份验证（集成安全性）

使用运行当前进程的 Windows 帐户。

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>; Integrated Security=SSPI;Persist Security Info=True;"
```

## <a name="connect-using-an-odc-file"></a>使用 .odc 文件进行连接

在较旧版本的 Excel 中，用户可以使用 Office 数据连接 (.odc) 文件连接到 Azure Analysis Services 服务器。 若要了解详细信息，请参阅[创建 Office 数据连接 (.odc) 文件](analysis-services-odc.md)。

## <a name="connect-as-a-linked-server-from-sql-server"></a>从 SQL Server 以链接服务器的形式连接

可以通过指定 MSOLAP 作为数据源提供程序，将 SQL Server 以[链接服务器](/sql/relational-databases/linked-servers/create-linked-servers-sql-server-database-engine)的形式连接到 Azure Analysis Services 资源。 配置链接服务器连接之前，请确保安装最新的 [MSOLAP 客户端库](/analysis-services/client-libraries?view=azure-analysis-services-current)（提供程序）。 

要使链接服务器可以连接到 Azure Analysis Services，MSOLAP 提供程序必须在 SQL Server 进程外实例化。 配置链接服务器选项时，请确保“允许进程内”选项未选中。

如果选择了“允许进程内”并且在 SQL Server 进程中实例化提供程序，则将返回以下错误：

```
OLE DB provider "MSOLAP" for linked server "(null)" returned message "The following system error occurred: ".

OLE DB provider "MSOLAP" for linked server "(null)" returned message "The connection failed because user credentials are needed and Sign-In UI is not allowed.".

Msg 7303, Level 16, State 1, Line 2
Cannot initialize the data source object of OLE DB provider "MSOLAP" for linked server "(null)".
```



## <a name="next-steps"></a>后续步骤

[使用 Excel 进行连接](analysis-services-connect-excel.md)    
[使用 Power BI 进行连接](analysis-services-connect-pbi.md)   
[管理服务器](analysis-services-manage.md)